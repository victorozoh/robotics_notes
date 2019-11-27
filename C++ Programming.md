---


---

<blockquote>
<p>Written with <a href="https://stackedit.io/">StackEdit</a>.</p>
</blockquote>
<h1 id="c-programming">C++ Programming</h1>
<p>A Ferrari programming language. Gives you power, control and great performance.</p>
<h2 id="variables">Variables</h2>
<h3 id="enum">Enum</h3>
<pre><code>enum Color {Red, Green, Blue};
Color c = Red;

// enum class
enum class GameState {Win, Lose};
GameState gs = GameState::Win;
</code></pre>
<h2 id="functions">Functions</h2>
<p>A means of making software more modular and implement the DRY(do not repeat yourself) guideline.</p>
<ul>
<li>Must be declared before they are called. Best to declare in a header file and include the header file in other files such as a main.cpp and the function definition file. Compiler problem means you should look in your header file. Linker error means you should look in the function definition file.</li>
<li>Must have a return type</li>
<li>Can call bey value or by reference. Use by reference when we need to swap variables or access large objects. Best to implement functions accessing large variables using a const reference. E,g<pre><code>int func(const int&amp; x)
{
	x.name = "Solo";
	...
}
</code></pre>
</li>
</ul>
<h3 id="lambda-functions">Lambda Functions</h3>
<p>C++ 11 addition. Don’t add any new capability to the language. They make things easier. They are syntactic sugar and make code more readable</p>
<ul>
<li>A lambda is an expression that represents doing something</li>
<li>Used for handing an operation or function(code) to some other operation or function. This is suitable for generic work, functional style, concurrency and readability.</li>
<li>Good to write lambda functions over multiple lines<pre><code>[](int n)
{
	std::cout &lt;&lt; n &lt;&lt; endl;
}
</code></pre>
</li>
<li>may return a value. A return statement<pre><code>[] (int n) -&gt; double {/* ... */}
</code></pre>
</li>
<li>When you write a lambda, the compiler generates an anonymous function object.</li>
<li>Lambda has member variables that hold values the lambda might use.  The member variables are const by default, but you can override that. Lambda has access to variables from the calling scope.</li>
<li>Empty [] - captures nothing, works only with function parameters</li>
<li>[x,y] - capture x and y by value. Copies are made. Lambda can be used when x and y have gone out of scope</li>
<li>[&amp;x, &amp;y] - capture x and y by reference. No copies, changes affect the originals</li>
<li>[=] - copy everything by value</li>
<li>[&amp;] - copy everything by reference</li>
</ul>
<h2 id="exceptions">Exceptions</h2>
<p>A great way to handle errors. Some errors are more predictable than others. E.g. user left a field blank or balance in a bank account is not enough to cover the amount of withdrawal.<br>
Some errors are not so predictable<br>
Different kinds of errors should be handled in different ways.</p>
<ul>
<li>Deal with the errors right where they are discovered. Sometimes, the code that finds the problem cannot deal with it. A possible solution is : We can have the function return an indication of trouble. But what if the function cannot return a value such as a Constructor.</li>
<li>Exceptions allow you to transfer the flow of execution from the problem location to somewhere that can handle it. Best practice is still to deal with things as close to the problem as possible.</li>
</ul>
<pre><code>// You can read the documentation to see what exceptions can be thrown
try 
{
...// code that can generate error
}
catch (out_of_range&amp; e) // exception&amp; can be 
{
...//react
}
catch (exception&amp; e)
{
...//react
}
</code></pre>
<ul>
<li>We can have any number of catch statements after the try statement. The first exception to catch the error is executed and the other catch statements are ignored.</li>
<li>std::exception is a base class from which other exceptions inherit.</li>
<li>Objects created on the stack and local to the try statement get automatically cleaned up in the catch statement.<br>
<strong>Unwinding the Stack</strong></li>
<li>When an exception is thrown, the code after the throw does not execute.</li>
<li>If the throw is in a function, everything local to the function goes out of scope and control returns to where that function was called from<br>
<strong>General tips for Try/Catch</strong></li>
<li>Wrap code that might throw an exception in a try block</li>
<li>try/catch as close to the problem as possible</li>
<li>Add one or more catch blocks after the try</li>
<li>Catch more specific exceptions first</li>
<li>Catch exceptions by reference. This is good for catching a derived exception so that you don’t suffer slicing where during debugging, you don’t see enough information about the exception.</li>
<li>Don’t use for data entry validation</li>
<li>More useful with deep calling hierarchy. E.g. A calls B which calls C…</li>
</ul>
<h2 id="classes">Classes</h2>
<ul>
<li>If you don’t specify a constructor, you get a default constructor.</li>
<li>Write your own copy constructors</li>
</ul>
<pre><code>// Person.h file
class Person
{
private:
	static int populationSize; // static member
	const static int foo = 0; // can initialize constant static
	int age;
	string name;
public:
	Person(const int age, const string name); // constructor
	Person(const int age);
	Person(const Person&amp; p);
	~Person(); // destructor

	string getName() const {return this-&gt;name};
	int getAge() const {return this-&gt;age};
	void greet() const; // good practice to mark function that doesn't change a member variable as constant
	static  int  getPopulation()  {  return  populationSize;  }  // static member function
};

//Person.cpp file
#include "Person.h" // obtain the class declaration
Person::PopulationSize = 0; // initialize static member

Person::Person(const int age, const string name) 
{
	this-&gt;name = name;
	this-&gt;age = age;
	populationSize++;
}

Person::Person(const int age) : age(age) // overloaded constructor with initializer list
{
}

Person::Person(const Person&amp; p) : age(p.getAge), name(p.getName) // copy constructor
{

}

Person::~Person() // destructor
{

}

void Person::greet() const 
{
	cout &lt;&lt; "My name is " &lt;&lt; this-&gt;name &lt;&lt; endl;
}


</code></pre>
<h3 id="member-functions">Member Functions</h3>
<ul>
<li>Declare in class declaration(.h)</li>
<li>Can implement in .cpp file. Use the full name e.g Employee::GetName()</li>
<li>Can implement “inline” where declared</li>
<li>Mark as const unless they are going to change a member variable. Best practice is to mark a member function as const unless you can’t. See below for example.<br>
Declaration<pre><code>std::string GetName() const;
</code></pre>
Definition<pre><code>std::string Employee:: GetName() const
{
   return "Solo";
}
</code></pre>
</li>
<li>Constructors and Destructors do not have a return type</li>
</ul>
<h3 id="static-member-variables">Static Member Variables</h3>
<ul>
<li>Unlike normal member variables, static member variables are shared by all objects of the class.</li>
<li>You can access static member variables from objects of the class, but they are not associated with objects of the class. Static members are treated like global variables. You can still access static members even if no objects are instantiated.</li>
<li>The preferred method of accessing static members is through the class name.</li>
<li>static members aren’t subject to access controls. However, we can’t access a private static member from a main function.</li>
<li>Do not put static member definitions in the header file. You usually put static member definitions in the .cpp file where you implement your class.</li>
<li>You must initialize static members outside the class declaration. The only exception is when the static member is a const int, bool, char or enum.</li>
</ul>
<h3 id="static-member-functions">Static Member Functions</h3>
<ul>
<li>They have no “this” pointer.</li>
<li>Can only access static member variables</li>
</ul>
<h3 id="friend-functions-and-classes">Friend Functions and Classes</h3>
<p>A friend function is a function that can access the private members of a class as though it were a member of that class.</p>
<h2 id="standard-library">Standard Library</h2>
<ul>
<li>Consists of three components. Namely: Containers, Iterators and Algorithms</li>
<li>Iterators act as glue between containers and algorithms</li>
<li>In general, we have two types of containers<br>
- Sequential<br>
- Associative(key value pairs)</li>
<li>Associative containers can be further divided into<br>
- Ordered<br>
- Unordered</li>
<li>std::vector is most commonly used. It can dynamically adjust its size. Can add elements to the end</li>
<li>std::array is the only sequential container that cannot adjust its size at runtime.</li>
<li>std::deque allows you to put elements at the beginning as well</li>
<li>std::list is a doubly-linked list</li>
<li>std::forward_list is a singly linked list</li>
<li>There are eight different associative containers</li>
<li>4 ordered associative<br>
- std::set<br>
- std::map<br>
- std::multiset<br>
- std::multimap</li>
</ul>
<h2 id="templates">Templates</h2>
<h2 id="memory-management">Memory Management</h2>

