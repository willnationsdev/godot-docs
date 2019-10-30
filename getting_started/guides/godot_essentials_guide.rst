.. _doc_godot_essentials:

Godot essentials guide
======================

If you've come here, you must want to create a game. You know the idea
of what you want to build, but not *how* to build it *in Godot*. If so,
you've come to the right place.

Object-oriented design
----------------------

.. image:: img/all_in_one_objects.png

Godot Engine uses an Object-Oriented design. This means it is
made up of abstract templates that allow you to build concrete things.
The abstract template is called a *class*. The things you actually interact
with are *instances* of a class.

Users can create content with Godot's large library of built-in classes, but
the first one to learn is the base Object class. It can define the
following elements:

- variables: Pieces of data. Also known as "properties" when associated
  with a class.

- functions: Pieces of behavior. Also known as "methods" when associated
  with a class.

- constants: *Static* and unchanging pieces of data. By *static*, we
  mean that the data is owned by the class, not by any one instance.

- signals: Declarations that something happened, similar to events
  in other game engines.

Every other class in Godot ``extends Object``, or extends something else that
extends Object, at some remove. Each possesses all the properties, methods,
constants, and signals of the class it extends plus those unique to
it. This chaining of elements in classes is called "Inheritance."

To see examples of these elements defined in various scripting languages,
please see the language-specific sections of the :ref:`scripting documentation <doc_scripting>`.

To see a full description of any class, one can check the
:ref:`Class Reference <doc_classes>`, also known as the 
:ref:`API <https://en.wikipedia.org/wiki/Application_programming_interface>`__.
The :ref:`Object API docs <class_Object>` help to demonstrate the above concepts.

.. _doc_reading_the_class_api::

Reading the Class API
---------------------

.. image:: img/essentials_object_api.png

Each page details the name and description of a class along with the class
it inherits, those which inherit from it, and all elements related to it.

The properties table has three columns. On the left is the
"data type". This could be a class, like ``Object``, or a primitive like int 
(``5``), float (``3.14``), or bool (``true``/``false``). In the middle is the
text identifier, i.e. name. On the right is the default value of the property.

The methods table is a bit more complex. The left-hand column has the data type
that is returned to you when you call, i.e. execute, the method. A ``void``
data type means that it does not return a value; the method itself does
something. The right-hand column starts with the name of the method followed by
a parentheses-enclosed list of the variables that must be provided to the
method, also known as "parameters." Note that this is a guide to what values
you can feed to it, not an example of *syntax*.

Each parameter in the list follows the format
``DataType ParameterName[= OptionalDefaultValue][Comma]``. When calling a
method, you must provide parameters in the same order with compatible
data types. If a default value is present, then you can omit it to have
Godot assume its value.

Some methods have special suffixes to more clearly indicate their behavior.

- `const`: calling the method on an instance does not modify that instance's
  data.

- `virtual`: you can write your own version of the method to plug in behavior
  somewhere else.

- `vararg`: the method accepts an unlimited number of parameters, indicated by
  a `...` at the end of the method's parameter list.

The constants section gives the name of the constant and the integer value it
corresponds to. Enumerations are groups of constants with their own
*data type*. For example, the Object class's ``CONNECT_*`` constants are
associated with the ``ConnectFlags`` data type that Object defines.

Some classes also have a Tutorial section that provides links to other official
web documentation related to the class.

.. note::

  The entire API is also available offline from the Godot Editor's
  "Search Help" utility. Press ``Shift + F1`` or go to ``Help > Search Help``
  in the Godot Editor to access it.

.. note::

  If you'd like to learn how to contribute to the Class Reference, please
  see the related documentation on
  :ref:`Submitting a pull request <doc_>` and
  :ref:`Updating the Class Reference <doc_updating_the_class_reference>`.

MainLoop, the SceneTree, Nodes, and notifications
-------------------------------------------------

The next class to learn about is the :ref:`MainLoop <class_MainLoop>` class. It
manages the game's core iteration loop and the OS window that it runs in. It
detects when the window is in focus, when the game crashes, and when the user
quits, among other things. The data it provides changes based on the platform
Godot runs on (desktop, web, mobile). It also handles iterative processes like
frame calculations and checking for input.

MainLoop is then inherited by the :ref:`SceneTree <class_SceneTree>` class.
The SceneTree is your actual Godot game instance. It builds on top of MainLoop
to enable users to manage a world of content, change it, organize it, and help
it communicate over networks. To be more specific, SceneTree manages a tree
hierarchy of :ref:`Nodes <class_Node>`.

Nodes are Godot's fundamental worldbuilding unit. They each can have one
parent node and many child nodes. Attaching one node to another forms a node
tree. Trees are recursive structures and thus have many significant features.
You can subdivide a tree into smaller trees, attach trees to other trees as
children (just like you can nodes), and even reorganize the nodes within a tree
to produce a new tree. Trees can be created and destroyed in bulk. As such,
nodes provide the foundation of a flexible game world that you can freely
manipulate. 

.. note::

  You can read more information about the MainLoop, SceneTree, and Node
  processing order :ref:`here <doc_scene_tree>`.

Nodes are also Godot's entry point for behavior. The SceneTree sends a
:ref:`*notification* <class_Object_method__notification>` to Nodes when any
overall change to the game state occurs (new frame, input detected). Nodes
opt-in to trigger logic on these notifications, so they can be somewhat
lightweight. Each node then responds to a notification by interacting with
the world.

Some notifications are so common that they have dedicated "callbacks". A
"callback" is a function that a *source* instance provides to a *target*
instance to call later. In this case, the source Node instance implements a
virtual method which Godot Engine, the target, calls when it sends
notifications.

It is common practice to prefix virtual methods with an underscore to
help distinguish them. these special notification callbacks follow this
convention. A node will only opt into responding to notifications for
which you've already implemented a method. For examples of dedicated
callbacks, see the underscore-prefixed methods in the
:ref:`Node class <class_Node>`'s "Methods" table.

.. note::

  Notifications are an Object feature, so you will find many of them scattered
  throughout the Class Reference. Search for ``NOTIFICATION_`` to find them.



To add a feature to the world, you must embody that feature as a Node-derived
class and add that node to the SceneTree.


---
When the engine does something
and needs an Object instance to respond, it will send it a *notification*.
The Object class has a :ref:`_notification() <class_Object_method__notification>`
method which implements the response to each notification.
---


.. image:: /img/essentials_scene_dock_empty.png

---

- Mainloop
- SceneTree
- Nodes, aggregation/composition versus inheritance
- NodePaths, $
- ``onready``
- Callbacks
- Notifications
- Signals
- Groups
- Static versus dynamic element access

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
