.. _all_in_one_intro:

All-in-one introduction to Godot
================================

If you've come here, you must want to create a game. You know the idea
of what you want to build, but not *how* to build it *in Godot*. If so,
you've come to the right place.

Dynamic object-oriented design
------------------------------

.. image:: img/all_in_one_objects.png

Godot Engine uses an Object-Oriented game framework. Users, like you, make
a "class" which is a template of things to create. These are mostly just
ideas rather than actual things. Then you "instantiate" the class to
create an actual instance of that thing. This instance is called an "object".

You can create many objects, but each one will only be tied to one class.
In the same way, you are a Human. You cannot be a Bird or a Fish. Humans
are many and varied, but they all maintain a similar form and behavior.

This makes it easier to keep track of the elements each object in your world
has. Don't know if an object can do something you want? Check its class! If
the class can do it, then the object can. Updating a class will also update
every instance of that class in the world.

Godot has many kinds of classes, but the one that new users must learn first
is the base :ref:`Object <class_Object>` class.

Every Object in Godot has certain elements that can be defined for it...

- variables

    - These are pieces of data.
    - When associated with a class, variables are called "properties".

- functions

    - These are pieces of behavior.
    - When associated with a class, functions are called "methods".

- constants

    - These are static and unchanging pieces of data.
    - The engine's classes can only store integers as constants, but most
      scripting languages enable your classes to store any kind of data as a
      constant.

- signals

    - These are declarations that an event has occurred.
    - Objects can "connect" to a signal, on them or another object, to
      plug in their own behavior whenever that event happens.
    - We'll cover this in more detail later.

A key detail though is that each class ``extends`` another class. This
allows it to have all of the elements defined by its base class, also known as
"Inheritance."

So, while you are still a Human, you are also a Primate, an Animal, and a
Life. Humans inherit the unique elements of all of these abstract ideas.

Now, not all classes in Godot extend the Object class. This means that you can have
objects (lowercase 'o'), complex things with their own data and features, that
are not Objects (capital 'O'). The different thing about Objects is that they
all have a special property called :ref:`script <class_Object_property_script>`.

``script`` stores a :ref:`Script <class_Script>`. These are a type of
file you can create that describes how to ``extend`` an Object with custom
elements. Objects themselves are unchanging. But, when asked for an element,
all Objects will first ask their script if it can handle the request.

This is where you come in. You will create Scripts that extend a Godot class.
If you create an Object and an Object-extending script, then attaching that
script to it, will enable you to intercept, add to, and/or replace the Object's
elements.

.. warning::

  Because non-Object classes don't have a ``script``, you cannot override
  their behavior. Their features are fixed in place by the engine.
  
  Examples mostly include basic mathematic and organizational data structures:
  Vector2, Vector3, Transform, Array, Dictionary, etc.

Below is an example script that defines a new Object class with each type of
element.

.. tabs::
  .. code-tab:: gdscript GDScript

    # All text written after a pound-sign (#) is a comment.
    # Comments do not do anything. They just let authors add notes to code.

    # You declare that this script extends Object.
    extends Object

    # You can optionally declare a class name.
    class_name MyObject

    # You use the 'signal' keyword to declare a signal named 'printed_message'
    # and you allow it to pass a value called 'msg' to recipients.
    signal printed_message(msg)

    # You use the 'const' keyword to declare a constant named 'MESSAGE_PREFIX'
    # and make it hold a starting value of "Message: ".
    const MESSAGE_PREFIX = "Message: "

    # You use the 'var' keyword to declare a property called 'message'
    # and make it hold a starting value of "Hello World!".
    var message = "Hello World!"

    # You use the 'func' keyword to declare a method called 'print_message' and
    # give it the list of parameters in parentheses (none in this case).
    # Parameters are variables we can give to functions to plug data into a behavior.
    # Then you can call it to execute this custom behavior.
    func print_message():

        # I am inside of a method, so this creates a local, temporary
        # variable, NOT a property. We use it to combine our constant and message data.
        var text = MESSAGE_PREFIX + message

        # Call the global 'print' function and pass in our text variable as a parameter.
        # Prints "Message: Hello World!".
        print(text) 

        # Notifies anyone who wants to know that we printed a message and what that message was.
        # Any object that receives the signal will get a 'msg' variable with the value of message.
        # We use 'message' here instead of 'text' because the receiver only needs to know the content of the message.
        emit_signal("printed_message", message)

  .. code-tab:: csharp

    // All text written after the two slashes (//) is a comment.
    // Comments do not do anything. They just let authors add notes to code.

    // These are necessary lines in C# to help a script find things.
    using System;
    using Godot;

    // You declare that this script, called MyClass, extends Object.
    // Note the 'public' access modifier. This allows Godot to see the class.
    // Its elements are similarly marked.
    public class MyObject : public Object
    {
        // You use the 'Signal' attribute on a C# delegate to declare a signal named 'PrintedMessage'.
        // Then you make it pass a value called 'msg' to recipients.
        [Signal]
        delegate void PrintedMessage(string msg);

        // To declare a constant...
        // You specify that it is 'static' (associated with the class, not an object instance).
        // You specify that it is a constant value with 'const'.
        // You specify the data type ('string' in this case).
        // Then you give it a name ("MESSAGE_PREFIX") and give it a starting value ("Message: ").
        public static const string MESSAGE_PREFIX = "Message: ";

        // To declare a property...
        // You specify the data type ('string' in this case).
        // Then give it a name ("Message") and give it a starting value ("Hello World!").
        public string Message = "Hello World!" { get; set; }

        // To declare a method...
        // You specify the return value's data type ('void' in this case, meaning no return value).
        // Then you call it PrintMessage and give the list of parameters in parentheses (none in this case).
        // This method is custom behavior that you can then execute at a later time.
        public void PrintMessage()
        {
            // Inside of a method, this creates a local, temporary
            // varible, NOT a property. We use it to combine our constant and message data.
            string text = MESSAGE_PREFIX + Message;

            // Call the Godot.GD namespace's 'Print' function and pass in the text variable as a parameter.
            // Prints "Message: Hello World!".
            GD.Print(text);

            // Notifies anyone who wants to know that we printed a message and what that message was.
            // Any object that receives the signal will get a 'msg' variable with the value of message.
            // We use 'Message' here instead of 'text' because the receiver only needs to know the content of the message.
            EmitSignal("printed_message", Message);
        }
    }

.. note::

  If you want to learn more about these languages' syntax, click the links below:

  - :ref:`GDScript syntax <doc_gdscript>`
  - :ref:`C# syntax <doc_c_sharp>`

Godot indexes these elements using a name. If you know an element's name,
then you can get it from an Object. This is true even if you don't know the
class of the Object (also known as :ref:`duck typing <doc_gdscript_advanced_duck_typing>`).

Indexing things by name alone also means that Godot does not allow name
sharing between elements inside of a class. For example, you can't have two
``health`` variables in a class. The same goes for two functions, two
signals, two constants, or a variable and a constant. It also includes
inherited elements.

---HOW TO READ THE API DOCS---

Godot's object system
---------------------

Godot Engine brings together many things. It interacts with multiple
operating systems, network protocols, scripting languages, and data types.
To help simplify the process of communicating between all of these,
Godot Engine uses a special class called :ref:`Variant <class_Variant>`.

A Variant object stores any kind of data and enables all Godot systems to
communicate with it. They enable all values to be converted into binary
and string-based formats to easily convert to files on your computer or bits
sent over the Internet. This means that all values can conceivably be saved,
loaded, or messaged to other devices automatically.

Notice that Variant isn't an Object, but an object. It exists purely,
behind-the-scenes, helping you be more productive by linking all of Godot's
systems together.

A great example of Variant in action is GDScript. Because GDScript is a dynamic
language, it can store different types of data in the same variable. It all
goes into a Variant.

.. tabs::
  .. code-tab:: gdscript GDScript

  var my_var = 42 # integer
  my_var = "Forty-two" # string
  my_var = 42.0 # real number, aka. float
  my_var = [40, "2"] # Array, supports any Variant, i.e. multiple types
  my_var = PoolIntArray([40, 2]) # PoolIntArray
  my_var = {40: 2.0, null: "4D2"} # Dictionary, supports any Variant, i.e. multiple types

.. note::

  For those familiar, Dictionaries are implemented as
  :ref:`OrderedHashMaps <https://www.hackerearth.com/practice/data-structures/hash-tables/basics-of-hash-tables/tutorial/>`__.
  All Variants, even Objects, can hash (compute consistent integer IDs) without
  users needing to define their own custom hash methods.

A Variant can hold a variety of data types, outlined in :ref:`@GlobalScope <class_@GlobalScope_constant_TYPE_NIL>`.
You can check which data type a Variant has using :ref:`GDScript's global typeof() function <class_@GDScript_method_typeof>`.
The below code breaks ``typeof()``'s return value into its possible options and
lets you handle them separately.

.. tabs::
  .. code-tab:: gdscript GDScript
  
  match typeof(my_var):
      TYPE_INT, TYPE_REAL:
          print("my_var is a number!")
      TYPE_STRING:
          print("my_var is text!")
      TYPE_ARRAY, TYPE_INT_ARRAY:
          print("my_var is an array, i.e. a sequence of values!")
      TYPE_DICTIONARY:
          print("my_var is a dictionary, i.e. a collection of key-value pairs!")

``TYPE_OBJECT`` is one of these available types, but wait. Godot has
*many* objects extending Object! Why are there so few types listed?

Well, any time a Variant does something, it has to explicitly check each of its
possible types to see what it should do. There are just too many Objects to
check. Instead, Variant checks to see if it is an Object at all, and only when
it's sure does it do a secondary check to see what kind of Object.

You too can perform similar checks in your own code, as demonstrated below.

.. tabs::
  .. code-tab:: gdscript GDScript
  
  match typeof(my_var):
      # ...
      TYPE_OBJECT:
          match my_var.get_class():
              "Node":
                  print("my_var is an Object of type Node!")

We'll learn more about Nodes in the next section.

.. note::

  Sometimes, you don't want a variable's value to be able to change. Maybe,
  when you say something is an integer, you only ever want it to be an integer.
  At times like these, it can be helpful to use
  :ref:`GDScript's optional static typing <doc_gdscript_static_typing>`.
  This means that the type of a variable is locked when it is declared.

  Many languages in Godot Engine also support static typing, but in different
  ways:

  *GDScript* and *VisualScript* use Godot's Variants internally, but they wrap
  them in a static interface (optional for GDScript). This way, the language
  can enforce a static layer over Godot's internal dynamic scripting system.

  *C#* and *C++* each operate independently of Godot's Variant system. They are
  statically typed languages that only convert to Variants just before
  interacting with Godot, like gluing two separate things together.

  All :ref:`NativeScript <https://godotengine.org/article/dlscript-here#targetText=So%20what%20is%20GDNative%3F,has%20methods%2C%20properties%20and%20signals.>`__
  languages use the latter approach, not just C++. They also all have access to
  Godot's Variant class whenever they want it.

  C# already has built-in support for using optional dynamic values with the
  ``dynamic`` and ``object`` types. You can read more about them
  :ref:`here <https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/reference-types>`__.

  When using static typing, your code becomes clearer, but has more
  restrictions on it.

  .. tabs::
    .. code-tab:: gdscript GDScript
  
      var node := my_var as Node # will be a Node or null
      if (node): # will evaluate to false if null
          print("my_var is an Object of type Node!")

MainLoop, the SceneTree, and Nodes
----------------------------------

- Mainloop
- SceneTree
- Nodes, aggregation/composition versus inheritance
- NodePaths, $
- ``onready``
- Callbacks
- Notifications
- Signals
- Groups

Memory, references and resources
--------------------------------

- Memory management
- Reference-counting
- Serialization
- Resources
- Scripts and PackedScenes
- Custom Resources

The editor, tools, and plugins
------------------------------

- The Godot Editor is a SceneTree
- tool scripts
- Creating plugins
- EditorInspectorPlugins
