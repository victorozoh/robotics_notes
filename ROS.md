---


---

<blockquote>
<p>Written with <a href="https://stackedit.io/">StackEdit</a>.</p>
</blockquote>
<h1 id="robot-operating-system">Robot Operating System</h1>
<h2 id="ros-concepts">ROS Concepts</h2>
<h3 id="packages">Packages</h3>
<ul>
<li>A collection of source code files, configuration files and libraries that perform a set of related functions</li>
<li>Must have a package.xml file and usually includes a CMakeLists.txt file</li>
<li>A package is should be in the ROS_PACKAGE_PATH environment variable</li>
</ul>
<h3 id="master">Master</h3>
<ul>
<li>A process in charge of coordinating other nodes.</li>
<li>Acts as a name server for node-to-node connections</li>
<li>the roscore command starts the master</li>
<li>Master communicates with slaves using XML remote procedure call. This is a HTTP-based protocol that doesn’t maintain connectivity</li>
</ul>
<h3 id="nodes">Nodes</h3>
<ul>
<li>A process that performs a given function.</li>
<li>Simply an executable program written in Python or C++</li>
</ul>
<h3 id="messages">Messages</h3>
<ul>
<li>Nodes send and receive data in the form of messages.</li>
<li>A data structure sent over a ROS topic</li>
<li>Messages can be of several data types such as integers, floats, strings etc</li>
<li>Can have a nested structure whereby a message contains other data types or messages</li>
<li>messages are serialized to facilitate communication over a ROS topic. Two nodes in communication with each other must know what data was serialized. ROS uses the MD5 hash algorithm.</li>
<li>There is a message file format in which the file name ends with .msg</li>
<li>.msg file format given below<pre><code>Datatype1 name1
Datatype2 name2
</code></pre>
Example of Vector3 message file in geometry_msgs package is given below<pre><code>float64 x
float64 y
float64 z
</code></pre>
</li>
</ul>
<h3 id="topics">Topics</h3>
<ul>
<li>A channel/highway over which messages are sent</li>
<li>Unidirectional communication between publisher and subscriber</li>
<li>Used for one-to-many and many-to-many communication</li>
</ul>
<h3 id="services">Services</h3>
<ul>
<li>Similar to a topic but uses a request-response communication structure</li>
<li>Synchronous bidirectional communication between a client that requests a service and the server that responds to request</li>
<li>.srv file format is given below<pre><code>
</code></pre>
</li>
</ul>
<h3 id="actions">Actions</h3>
<ul>
<li>Another communication mechanism in ROS for asynchronous bidirectional communication</li>
<li>Used where it takes longer to respond to a request and intermediate responses are needed until result</li>
<li>There is an action client that sets the goal</li>
<li>There is also Action server that performs the action specified and returns feedback and result to the action client</li>
</ul>
<h3 id="bags">Bags</h3>
<ul>
<li>File format for recording and playing back data from ROS topics</li>
<li>The file format used ends in a .bag</li>
</ul>
<h3 id="catkin">Catkin</h3>
<ul>
<li>This is the build system of ROS</li>
<li>Makes use of CMake</li>
</ul>
<h2 id="names">Names</h2>
<ul>
<li>Nodes, topics, services and parameters are referred to as graph resources.</li>
<li>Every graph resource is identified by a short string called a graph resource name.</li>
<li>There are 3 types of names.<br>
- Global names<br>
- Relative names<br>
- Private names</li>
<li><strong>Global names</strong>: Make sense anywhere they are used. They have clear, unambiguous meanings. No additional context is needed to resolve which resource is being referred to<br>
- A leading slash / identifies a global name<br>
- followed by a sequence of zero or more namespaces. A <strong>Namespace</strong> is used to group related graph resources together. Graph resources that immediately follow the slash e.g /run_id belong to the global namespace.<br>
- A base name that describes the resource itself</li>
<li><strong>Relative names</strong>: We can allow ROS supply a default namespace<br>
- lacks a leading slash<br>
- We cannot match relative names to specific graph resources unless we know the default namespace ROS is using to resolve them<br>
- To resolve a relative name, ROS prepends the default namespace to the base name.<br>
- Default namespace is tracked individually for each node rather than system wide. If you do not specify a default namespace, ROS will use the global namespace.<br>
- It is best to set the default namespace for a node or group of resources using the ns attribute in launch files.<br>
- We have the following options for setting the default namespace of a node/resource<br>
<code>__ns:=default_namespace // at the command line</code><br>
<code>export ROS_NAMESPACE=default_namespace // set up an environment variable for every program within a shell. Used when no other default namespace is set by the __ns parameter</code><br>
- Relative names gives users flexibility to insert node into other namespaces that designers did not originally intend. Best practice is to use relative names with nodes. Helps with composition.</li>
<li><strong>Private names</strong>:<br>
- Begin with a tilde(~)<br>
- They do not use the default namespace. Instead they use the name of their node as a namespace.<br>
- Often used for parameters</li>
<li><strong>Anonymous names</strong>: The purpose of an anonymous name is to make it easier for each node to have a unique name. Through a ros::init() call, a node can request a unique name be assigned automatically.<pre><code>ros::init(argc, argv, base_name, ros::init_options::AnonymousName);
</code></pre>
</li>
</ul>
<h2 id="launch-files">Launch Files</h2>
<ul>
<li>A mechanism for starting the master and multiple nodes at once.</li>
<li>A launch file is an xml file</li>
<li>All nodes in a launch file are started at approximately the same time. Hence, we cannot be sure about the order in which nodes are initialized.</li>
<li>Launch files usually end with a .launch in their names and are placed in a launch directory inside the package.</li>
<li>standard output from launched nodes is redirected to a launch file</li>
<li>All launched nodes share a terminal. We can change this behavior for a given node in the launch file. For example nodes that need console input can launch in a different terminal. Use the launch-prefix attribute</li>
<li>We can include other launch files in a launch file. The file attribute of the include tag needs the full path of the launch file.</li>
<li>Launch arguments make a launch file configurable. Arguments are not “inherited” by included launch files.</li>
<li>Command to start a launch file is given below<pre><code>roslaunch package-name launch-file-name arg2:=2
</code></pre>
</li>
<li>Example launch file<pre><code>&lt;launch&gt;
	&lt;include file="$(find package-name)/launch-file-name"&gt; &lt;!-- include other launch files --&gt;
		&lt;arg name="arg1" value="$(arg arg1)"/&gt;	
	&lt;/include&gt;
	&lt;arg name="arg1" value="$(arg arg1)"/&gt; &lt;!-- common scenario where argument is shared by including and included launch file --&gt;
	&lt;arg name="arg2" default="0" value="val" /&gt; &lt;!-- command line argument can override default but not value. Throws error if you use value in launch file and try to use value in command line as well --&gt;
	&lt;node 
		pkg="package-name" 
		type="executable-name" 
		name="node-name" &lt;!-- overrides node name given in ros::init() call --&gt;
		output="screen" &lt;!-- display output on screen --&gt;
		respawn="true" &lt;!-- restart node if it terminates --&gt;
		required="true" &lt;!-- if node terminates, all other nodes are terminated as well --&gt;
		launch-prefix="xterm -e" &lt;!-- launch node in another terminal --&gt;
		ns="namespace" &lt;!-- launch node in a separate namespace --&gt;
	/&gt;

	&lt;node&gt;
		
	&lt;/node&gt; &lt;!-- nodes with children require a closing tag --&gt;

	&lt;group ns="namespace" if="$(arg arg2)"&gt;
		&lt;node /&gt;
		&lt;node /&gt;
	&lt;/group&gt;
&lt;/launch&gt;
</code></pre>
</li>
<li>Remapping can provide finer level of control over name management. Can remap from the console or from the launch file<pre><code>&lt;launch&gt;
	&lt;remap from="original-name" to="new-name" /&gt; &lt;!-- affects all nodes --&gt;
	&lt;node 
		&lt;remap from="original-name" to="new-name" /&gt; &lt;!-- affects only current node --&gt;
	/&gt;
	&lt;node&gt;
		
	&lt;/node&gt;
&lt;/launch&gt;
</code></pre>
</li>
</ul>
<h2 id="parameters">Parameters</h2>
<ul>
<li>A mechanism for passing information to nodes</li>
<li>We can use the rosparam tool to get and set parameters<pre><code>rosparam get parameter-name
rosparam set parameter-name parameter-value
rosparam set /namespace "foo: red
goo: green
hoo: blue" //used to set a collection of parameters in a namespace from the command line. The newline character is required as well as the space after the colons.

rosparam dump file-name namespace //to store all the parameters in YAML format into a file
rosparam load file-name namespace //to read parameters from a file and add them to the parameter server
rosservice call /clear // this ensures that new parameter values are used after making changes
</code></pre>
</li>
<li>We can also set parameters in a launch file using the param tag<pre><code>&lt;group&gt;
	&lt;param name="param-name" value="param-value" /&gt;
&lt;/group&gt;
&lt;rosparam command="load" file="$(find package-name)/param-file"/&gt; &lt;!-- used to set many paramters at once--&gt;
</code></pre>
</li>
<li>parameters that are included as children of nodes in a launch file are always resolved as private names</li>
</ul>
<h2 id="ros-command-line-tools">ROS Command Line Tools</h2>
<h2 id="logging">Logging</h2>
<h2 id="gazebo">Gazebo</h2>
<ul>
<li>Physics-based high fidelity 3D simulator for robotics</li>
<li>Can simulate multiple robots and helps with testing ans rapid prototyping</li>
<li>8 main features of Gazebo<br>
- Dynamics Simulation: model robot dynamics<br>
- Advanced 3D Graphics: Render environment with high fidelity graphics<br>
- Sensors: add sensors to robot<br>
- Plugins: write a plugin to interact with world, sensor or robot<br>
- Model Database: Add an environment or robot from Gazebo library to your simulation<br>
- Socket-based Communication: Interact with Gazebo running on a remote server through socket-based communication<br>
- Cloud Simulation: Run Gazebo on a server and interact with it through a browser<br>
- Command Line Tools: Control your simulated environment through the command line tools</li>
<li>There are 6 components involved in running an instance of a Gazebo simulation<br>
- Gazebo server<br>
- Gazebo client<br>
- World Files<br>
- Model Files<br>
- Environment variables<br>
- Plugins</li>
</ul>
<h3 id="gazebo-server">Gazebo server</h3>
<ul>
<li>Responsible for parsing description files</li>
<li>Simulates the scene using he physics and sensor engine</li>
<li>Can be launched independently by using the following command on the terminal<pre><code>gzserver
</code></pre>
</li>
</ul>
<h3 id="gazebo-client">Gazebo Client</h3>
<ul>
<li>Provides the essential graphical client that connects gzserver and renders the simulation together with interactive tools. We can run gzclient from the command line with the following command<pre><code>gzclient
</code></pre>
</li>
</ul>
<h3 id="world-files">World Files</h3>
<ul>
<li>A world file contains all the elements in the simulated environment. These elements include your robot model, the environment, other objects, light sources and sensors.</li>
<li>You save a world file with a .world extension</li>
<li>A world file is formatted using the simulation description format(sdf).</li>
</ul>
<h3 id="model-files">Model Files</h3>
<ul>
<li>You can create a separate sdf file of your robot with exactly the same format as your world file. There are <a href="http://gazebosim.org/tutorials?tut=ros_roslaunch#UsingroslaunchtoSpawnURDFRobots">two ways</a> to add your robot model to gazebo. They are:<br>
- ROS Service call spawn method<br>
- Model database method (include your robot within the .world file)</li>
<li>Similarly, other objects can be added to your world file using the command below<pre><code>&lt;include&gt;
  &lt;uri&gt;model://model_file_name&lt;/uri&gt;
&lt;/include&gt;
</code></pre>
</li>
</ul>
<h3 id="environment-variables">Environment Variables</h3>
<ul>
<li>There are environment variables that Gazebo uses to primarily locate files and set up communications with the gzserver and gzclient. Some example environment variables include:<br>
- GAZEBO_MODEL_PATH: Set of directories Gazebo looks to populate a model file<br>
- GAZEBO_RESOURCE_PATH: Set of directories where Gazebo will search for other resources such as world and media files.</li>
</ul>
<h3 id="plugins">Plugins</h3>
<ul>
<li>You can write your own plugins to interact with the world or sensors. The plugins can be loaded from the command line or added to your sdf world file</li>
</ul>
<h2 id="tf">TF</h2>
<ul>
<li>TF keeps track of spatial and temporal relationships between objects in our environment. It does this using reference frames in 3D. x(red), y(green), z(blue).</li>
<li>How are TF reference frames generated? The ROS package robot_state_publisher generates and update the TF reference frames.</li>
<li>The robot_state_publisher uses two sources of information<br>
- movable joints (joint state information) from the - /joint_states topic<br>
- robot_description parameter - URDF/XACRO. Uses root link of the xacro file as a fixed frame of reference.</li>
<li>It is your responsibility to set up the info needed by the robot_state_publisher.</li>
<li>We can have multiple nodes publish to the /joint_states topic</li>
</ul>
<pre><code>&lt;!--In launch file, we can use the joint_state_publisher package to combine multiple nodes publishing on the /joint_states topic --&gt;
&lt;node pkg="joint_state_publisher" name="joint_state_publisher" type="joint_state_publisher"&gt;
&lt;rosparam="/source_list"&gt;
[/robot1/joint_states, /robot2/joint_states]&lt;/rosparam&gt;
&lt;/node&gt;
</code></pre>
<ul>
<li>The functional aspects of TF is implemented in the tf2_ros package. There is still a tf package. tf2_ros is recommended. The main advantage of the tf2_ros package is the transform buffer</li>
<li>Spatio-temporal relationships are represented by he geometry_msgs/TransformStamped message</li>
<li>/tf topic is used to aggregate transform information. The message on this topic is the tf2_msgs/TFMessage type which is an array of TransformStamped messages.</li>
</ul>
<h3 id="tf-command-line-tools">tf command line tools</h3>
<pre><code>// show transform between source and target frames
rosrun tf tf_echo &lt;source_frame&gt; &lt;target_frame&gt;

// view the tf tree
rosrun tf view_frames
rosrun tf2_tools view_frames.py

// publish a static transform between a parent and child frame
rosrun tf2_ros static_transform_publisher &lt;trans(x y z)&gt; &lt;rot(q0 q1 q2 q3)&gt; "parent" "child"

</code></pre>
<h3 id="tf-and-query-timing">TF and query timing</h3>
<ul>
<li>robot_state_publisher is constantly updating the /tf topic. If we query for a transform that is yet to be published, we’ll get an error. If we ask for a transform that has already been published, we’ll get a warning that we are requesting an old transform.</li>
<li></li>
</ul>
<h2 id="urdf">URDF</h2>
<ul>
<li>Unified Robot Description Format. A domain specific modelling language. Enables us to store kinematics, dynamics and other meta-data</li>
<li>File format that ROS uses to store the robot body layout, appearance and extra positions. A simple text file that uses XML tags</li>
<li>robot model storage format</li>
<li>Mostly link and joint tags. Only one robot tag in URDF file</li>
</ul>
<pre><code>&lt;robot&gt;
	&lt;link name="link_1"/&gt;
	&lt;link name="link_2"/&gt;
	&lt;joint name="joint_1"&gt;
		&lt;parent link="link_1" /&gt;
		&lt;child link="link_2" /&gt;
	&lt;/joint&gt;
&lt;/robot&gt;
</code></pre>
<ul>
<li>
<p>1 type of link and 6 types of joints</p>
<ul>
<li>Fixed: rigid connection</li>
<li>Revolute: 1D rotation</li>
<li>Continuous: unlimited revolute</li>
<li>Prismatic: 1D translation</li>
<li>Planar: 2D translation</li>
<li>Floating: unlimited 6D</li>
</ul>
</li>
<li>
<p>Right handed coordinate system</p>
</li>
<li>
<p>ROS uses SI units. E.g lengths in meters, angles in radians</p>
</li>
</ul>
<h3 id="limitations-of-urdf">Limitations of URDF</h3>
<ul>
<li>Robot descriptions cannot be changed</li>
<li>Only tree structures. Joints have only a single parent and child</li>
<li>No sensor tags. But alternatives exist</li>
<li>Low re-usability</li>
<li>Solution is XACRO: Programmatic URDF generation. They accept parameters</li>
</ul>
<pre><code>
</code></pre>
<ul>
<li>
<p>Must use xacro tool to convert xacro file into URDF</p>
</li>
<li>
<p>There is a check_urdf tool for verifying the correctness of a URDF file.</p>
</li>
</ul>
<h2 id="tips">TIPS</h2>
<ul>
<li>Remember to add both joint_state_publisher and robot_state_publisher to your launch fule in order to view your robot in rviz</li>
</ul>
<h2 id="moveit">Moveit</h2>
<ul>
<li>Open source library for manipulation. Integrated with ROS</li>
<li>Used to plan and execute motions for serial link manipulations</li>
<li>Platform to configure and use various functionalities associated with manipulation</li>
</ul>
<h2 id="ros-navstack">ROS NavStack</h2>
<ul>
<li>Robot navigation means the ability to move in space without hitting an obstacle. There are two major categories<br>
- Map based navigation<br>
- Reactive Navigation</li>
<li>Answers to 3 questions
<ul>
<li>Where am  I? -Localization</li>
<li>Where am I going? Mapping is needed for robot to recognize where he has been around so far. Sensor Fusion is also performed to help build map</li>
<li>How do I get there? Path Planning. The target position must be well defined to the robot which will require appropriate addressing scheme that the robot can understand</li>
<li>Three main packages for the navigation stack
<ul>
<li>move_base: makes the robot navigate in a map and move to a goal pose with respect to a given reference frame</li>
<li>gmapping:  creates maps using the laser scan data</li>
<li>amcl: responsible for localization using an existing map</li>
</ul>
</li>
</ul>
</li>
</ul>
<h2 id="useful-packages">Useful Packages</h2>
<h3 id="sensing">Sensing</h3>
<ul>
<li>libuvc_camera</li>
<li>camera_calibration</li>
<li>cv_bridge</li>
<li>image_pipeline</li>
<li>image_transport</li>
<li>openni_launch</li>
<li>openni_tracker</li>
<li>pcl</li>
<li></li>
</ul>
<h3 id="slam">SLAM</h3>
<ul>
<li>robot_pose_ekf</li>
<li>robot_localization</li>
</ul>
<h3 id="visual-slam">Visual SLAM</h3>
<ul>
<li>RTabMap</li>
<li>Gmapping</li>
<li>Hector SLLAM</li>
<li>ORB SLAM</li>
</ul>
<h3 id="navigation">Navigation</h3>
<ul>
<li>navigation</li>
</ul>
<h3 id="planning">Planning</h3>
<ul>
<li>ompl</li>
</ul>

