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

As a Godot user, you will create a script file that defines a base class and
a list of custom elements. You can then assign that script to an instance of
that base class to append to or, in some cases, override, the base class's
elements.

To see examples of these elements defined in Godot's scripting languages,
please see the language-specific sections of the :ref:`scripting documentation <doc_scripting>`.

To see a full description of any class, one can check the
:ref:`Class Reference <doc_classes>`, also known as the 
:ref:`API <https://en.wikipedia.org/wiki/Application_programming_interface>`__.
The :ref:`Object API docs <class_Object>` help to demonstrate the above concepts.

.. _doc_reading_the_class_api::

Reading the Class Reference
---------------------------

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
something. The right-hand column starts with the name of the method. A 
parentheses-enclosed list of variables then follows. These are the "parameters"
that you must give to the method to call it. Note that this is a guide to what
values you can feed to it, not an example of *syntax*.

Each parameter in the list follows the format
``DataType ParameterName[= OptionalDefaultValue][Comma]``. You must provide
parameters in the same order with compatible data types. If a default value is
present, then you can omit it to have Godot assume its value.

Some methods have special suffixes to more clearly indicate their behavior.

- `const`: calling the method on an instance does not modify that instance's
  data.

- `virtual`: you can write your own version of the method to plug in behavior
  somewhere else.

- `vararg`: the method accepts an unlimited number of parameters, indicated by
  a `...` at the end of the method's parameter list.

The constants section gives the name of the constant and the integer value it
corresponds to. Enumerations are groups of constants with their own
*data type*. For example, Object defines a ``ConnectFlags`` data type with
enumerated values that appear as constants starting with ``CONNECT_*``.

Some classes also have a Tutorial section that provides links to other official,
related web documentation.

.. note::

  The entire API is also available offline from the Godot Editor's
  "Search Help" utility. Press ``Shift + F1`` or go to ``Help > Search Help``
  in the main toolbar to access it.

.. note::

  If you'd like to learn how to contribute to the Class Reference, please
  see the related documentation on
  :ref:`Submitting a pull request <doc_pr_workflow>` and
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

Nodes, Node trees
-----------------

Nodes are Godot's fundamental worldbuilding unit. They can each have one
parent node and many child nodes. Attaching one node to another forms a
tree. Trees are recursive structures, built out of subtrees. In fact, a tree
can even be a single node. This grants them many significant features. You
can...

1. build nodes into a tree.
2. subdivide a tree into smaller trees.
3. attach trees to other trees as children.
4. reorganize the nodes within a tree to produce a new tree.
5. masquerade a tree as a node.
6. create and delete entire trees.

The lack of a major distinction between a tree of nodes and a single node
is what sets Godot apart. Nodes serve as the foundation of a flexible game
world that you can freely manipulate.

Notifications
-------------

Nodes are also entry points for interacting with the world. The SceneTree
sends all nodes inside it *notifications* about things that happen like
advancing to a new frame or an input detection. Nodes can then opt-in to
respond to these notifications and do things in the world.

Users create a script that extends Node and which defines methods for
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
constants to find them; they will be the value of the
``_notification`` method's parameter.

Creating scenes
---------------

So you've :ref:`downloaded <https://godotengine.org/download>`__ a copy of
Godot and :ref:`created a new project <doc_creating_a_new_project>`. How
do you actually start building your SceneTree content?

On the left side of the editor, you should see a "Scene" dock:

.. image:: /img/essentials_scene_dock_empty.png

Right now, because it is empty, it offers suggestions for a "root node" that
you could add to a "scene". But this image alone gives rise to several
questions:

What is a "scene"?

  The game world is built out of a node tree, but it wouldn't be reasonable to
  build the entire tree in one
  :ref:`hardcoded <https://stackoverflow.com/questions/1895789/what-does-hard-coded-mean>`__,
  :ref:`monolithic <https://www.quora.com/What-is-the-difference-modular-vs-monolithic-programming-for-applications>`__ node hierarchy.
  
  Instead, we use tree feature #2 and subdivide our SceneTree's
  entire node tree into subtrees. This lets us examine each
  subtree in isolation to develop it, test it, and track its dependencies.
  We call these subtrees "scenes" and save them to files:
  ``.tscn`` and ``.scn`` for text and binary, respectively.

What is a "root node"?

  Because every scene is a tree, it necessarily has a root node.

How do I decide which node to use as root?

  Due to tree feature #3, you can nest scenes within other scenes. So, which
  node you choose as the root affects how the scene interacts with others.

  For example, if you create 2D content, you can put it under a
  :ref:`Node <class_Node>` root or a :ref:`Node2D <class_Node2D>` root,
  among other things. However, doing so has a very different impact on that
  scene's relationship to its parent scene.

  A Node2D will position itself relative to its parent Node2D's transform. A
  Node, on the other hand, will ignore the parent's transform because it does
  not have one. Therefore, your scene's 2D content either will or will not
  follow its parent if that parent moves around.

  In addition, the root node dictates how other scenes perceive the current
  scene. By default, they can only see the root and interact with its internal
  nodes via its methods. This is a manifestation of
  :ref:`"Encapsulation" <https://en.wikipedia.org/wiki/Encapsulation_(computer_programming)>`__
  from Object-Oriented Programming.
  
  Scenes are, in a way, like building a class in a visual editor (or rather,
  a constructor for a class). All things in your game, even the environments,
  are classes with the potential to have their own scripted functionality
  and hidden internal structure.
  
Why would a root node ever NOT be in 3D, 2D, or UI space?

  Not every class you create will need to have a position in space. Some
  will be bundles of data or behavior that need to have
  access to the game world, but do not have a physical location themselves.
  This includes nodes that enable a behavior for something else, e.g. a Jump
  node handles configuration and calculations for jump logic. It also
  includes nodes that serve as standalone gameplay systems such as a targeting
  system singleton.

.. note::

  Most engines have you...
  
  1. create an ``entity``.
  2. add behavioral ``components`` to it
  3. save it as a reproducible ``prefab``
  4. stick many instances of this prefab inside a ``scene`` container.
  
  Godot instead just makes everything a ``node``.
  
  1. You can logically reduce a scene, the tree container for nodes, down to
  a single node via its root. Ergo, nodes handle case #4.
  2. Scenes are the serializable ``prefabs`` for game content. You can fully
  reproduce a scene via script code too. Ergo, nodes handle case #3.
  3. Whether a node is an ``entity``, or a ``component`` depends entirely on
  context. If the node is meant to be a standalone thing, then it is an entity.
  If it exists to provide features to some other thing, it's more like a
  component.

  So Godot unifies entities, components, prefabs, and levels all together into
  just the concept of defining nodes. As a result, the Godot Editor is more
  like a visual class editor rather than anything else.

~~~~

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
