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

MainLoop and the SceneTree
--------------------------

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

Nodes, Node trees, and scenes
-----------------------------

Nodes are Godot's fundamental worldbuilding unit. They can each have one
parent node and many child nodes. Attaching one node to another forms a
tree. Trees are recursive structures, built out of subtrees. In fact, a tree
can even be a single node. This grants them many significant features. You
can...

1. build nodes into a tree.
2. subdivide a tree into smaller trees/nodes.
3. attach trees to other trees as children.
4. reorganize the nodes within a tree to produce a new tree.
5. masquerade a tree as a node.
6. create and delete entire trees.

As such, nodes provide the foundation of a flexible game world that you can
freely manipulate.

Nodes are also entry points for interacting with the world. The SceneTree
sends all nodes inside it *notifications* about things that happen like
advancing to a new frame or an input detection. Nodes can then opt-in to
respond to these notifications and do things in the world.

Users create a class that extends Node and which defines methods for
responding to notifications. The Node class then passes these methods
*back* to the engine so that it can *call* them at the correct time; hence,
the methods are referred to as *callbacks*. They appear as virtual methods
with leading underscores in the Class Reference. See the "Methods" table
at the top of the :ref:`Node API docs<class_Node>` for examples.

While the most frequently used notifications have their own callbacks,
Godot also has a master callback for handling any notification:
:ref:`Object._notification <class_Object_method__notification>`. As
you can see, notifications are an Object feature, so you will find them
scattered throughout the Class Reference. Search for ``NOTIFICATION_``
constants to find them.





Most other engines have you create mostly empty instances in the world
and add behaviors to them to bring those instances to life. Godot,
in constrast, makes no distinction between 

~~~~

Notifications
-------------

As mentioned, Nodes are Godot's entry point for behavior. The SceneTree sends a
:ref:`*notification* <class_Object_method__notification>` to Nodes when any
overall change to the game state occurs (new frame, input detected). Nodes
opt-in to trigger logic on these notifications, so they can be somewhat
lightweight. Each node then responds to a notification by interacting with
the world. You add behavior to the world by overriding these notification
responses.

Some notifications are so common that they have dedicated "callbacks". A
"callback" is a function that a *source* instance provides to a *target*
instance to call later. In this case, the source Node instance implements a
virtual method which the target SceneTree instance calls when it sends
notifications.

It is common practice to prefix virtual methods with an underscore to
help distinguish them. these special notification callbacks follow this
convention. A node will only opt into responding to notifications for
which you've already implemented one of these methods. For examples of
dedicated callbacks, see the underscore-prefixed methods at the top of the
:ref:`Node class <class_Node>`'s "Methods" table.

.. note::

  Notifications are an Object feature, so you will find many of them scattered
  throughout the Class Reference. Search for ``NOTIFICATION_`` to find them.

  The master callback for all notifications,
  :ref:`Object._notification <class_Object_method__notification>`, allows you
  to detect notifications that do not have dedicated callbacks.

Inheritance, Aggregation, and Scenes
------------------------------------

Inheritance is where one class *inherits* the behaviors of another class; they
have an "is-a" relationship. Aggregation is where one class instance manages
access to another class instance and *uses* it to execute behaviors for its
sake; they have a "has-a" relationship.

Godot's Node hierarchies employ a mix of the two. Related functionality that
builds on each other is often combined into an inheritance relationship. For
example:

- Node *is* a structural unit.
- Node2D *is* a Node that has a 2D position in the world. Child Node2Ds move
  relative to their Node2D parents, so trees of Node2Ds move together.

With a 2D position, we can create various chains of functionality:

- CanvasItem *is* a Node2D that can draw things.
- Sprite *is* a CanvasItem that draws an image relative to its 2D position.

or...

- PhysicsBody2D *is* a Node2D that interacts with the physical world.
- KinematicBody2D *is* a Node2D that can move with custom physics movement.

Each of these are things we may wish to use and reproduce often. They have
properties that configure how they work and methods to engage in behaviors
they support. They each have a class that builds a layer of features over
the previous one.

Now let's see this example:

- A KinematicBody2D called "Player".
- "Player" *has* a Sprite called "HeadSprite"
- "Player" *has* a Sprite called "TorsoSprite"
- "Player" *has* a Sprite called "LegsSprite"

Now, we could define a new class, but creating 

In aggregation, the owning instance delegates responsibilities to the owned
instance and takes credit for the work. Other owning instances don't know whilst other owning instances have no idea it even owns
other things



To add a feature to the world, you must embody that feature as a Node-derived
class and add that node to the SceneTree.




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
