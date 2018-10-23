Introduction to Programming
===========================

This series of tutorials guides you through learning basic programming
concepts by teaching you about GDScript, Godot Engine's original programming language.
During the course, we'll build a few small scripts and game scenes to help illustrate
the topics covered.

GDScript is quite different from most other languages. It is designed specifically
for Godot and doesn't have general uses outside of that. As a result, it makes life
with Godot a *lot* easier.

Before getting started, let's jump into how GDScript fits into the larger picture of
creating and running a program.

What's a "Programming Language"?
--------------------------------

Programming is broken down into these basic steps:

1. Computers use a **Central Processing Unit** (CPU) to read and
   execute **binary** instructions, a.k.a. binary **code** (1s and 0s).

2. These instructions may be different for various platforms (Windows, OSX, Linux, iOS, Android, HTML, etc.).
   Therefore, CPU instructions can also be called **native** code.

3. A programmer doesn't want to memorize that 1101000100100011 might mean
   "start" when running an Intel CPU with Windows 64-bit Home Edition (yikes!). 
   Instead they write human-readable text files called **source code**.

4. One or more other programs translate the text into native code. *Many*
   techniques exist to do this, sometimes with intermediate steps.
    - Translating one human-readable programming language into another one is called "transpiling" the code (noun: "transpilation").
      TypeScript is an example that adds special features on top of JavaScript. One writes TypeScript files, transpiles them to JavaScript,
      and then runs the JavaScript in the web browser.
    - Translating a human-readable programming language into a non-human-readable, low-level one is called "compiling" the code (noun: "compilation").
        - C and C++ compile to an intermediate "assembly" code used by the CPU to execute native instructions.
        - Other similar languages include Rust, D, Nim, and Haskell.
    - One form of compilation is translating to an intermediate **bytecode** language that is used by a separate program to translate into
      native instructions. This offloads some of the work for developers and allows them to worry only about writing code. 
      The separate program will do the work of translating the bytecode into platform-specific code for them.
        - Some bytecode translation is done within a virtual machine (VM), i.e. a self-contained, in-memory CPU that translates the bytecode
          for the main computer's CPU. Note that these languages pre-parse and pre-compile the source into bytecode, but are only executed when
          the bytecode is handed to the virtual machine. Developers write code and compile it once. Users install the VM.
            - Java, with its Java Virtual Machine (JVM).
            - C# with its Common Language Runtime (CLR).
            - **GDScript** (the VM is bundled into **Godot Engine** and is built into executable files so that users do not need to install it separately).
        - Some bytecode translation uses a virtual machine *within* an interpreter. These programs parse, compile, and execute the code all in one go.
          These are the slowest languages, but are very quick to develop in.
            - JavaScript
            - Python

Note that every programming language has what's called a ``parser`` which is a program that reads
the syntax of the source code and breaks it down into a more efficient form that the ``compiler`` program can read.
Often times, a language's parser is bundled into its compiler to some degree (you don't download them separately).

Now, GDScript is what's known as a "scripting language". All it does is
lay out a small set of instructions that tell *another* program what to do.
These types of source code files are called "scripts" because they script
the program's behavior and serve as data input. In this case, Godot Engine
is the program and GDScript's ``.gd`` files are the scripts (e.g. ``hello_world.gd``).

..note:
  Almost any language can potentially be used as a scripting language. That is,
  scripting is a potential *use* of a language, not a quality of the language itself.
  C# for example is used to build applications with Microsoft's .NET framework, but
  is also used for scripting in both the Unity game engine and in Godot Engine.

The instructions that are valid for a scripting language to execute differ
depending on the program they are providing input to. For example, while
Godot Engine may have some instructions it accepts, those same instructions
may not work for other applications. These instructions are known as a
"scripting API" or **Application-Programmer Interface**. While you can
access Godot's API in many places, we'll explore that later in the course.

Godot Engine supports *many* scripting languages, each with their own
benefits and use-cases. GDScript is the best integrated language and the
easiest one to learn of the bunch, so that's what we'll learn.

Okay, GDScript Directs Godot's Behavior. Now what?
--------------------------------------------------

Let's dive into Godot Engine! First download the binary at
https://godotengine.org/download. Because we'll be using GDScript only, just grab
the 32-bit or 64-bit version at the top, whichever is appropriate for your operating
system.

When you open the editor for the first time, you will see a big central **Viewport**
that gives you a window into the world of your game. In order to actually create a
simple GDScript program, we first must put a **Node** into the world!

Click the 'Node2D' button in the Scene dock to add a Node2D root node to your scene.

TODO: add image

Godot Engine games run off of a tree-hierarchy of Nodes managed by a SceneTree.
Imagine the filesystem on your computer and how different files exist at different
places. There's the root folder (``/`` on OSX/Linux and the ``C:\\`` folder on Windows)
and then many files nested within sub-folders. The SceneTree likewise creates
"folders" of "files". In this analogy, the "folders" are **scenes** and the files
are your nodes.

Just as activating a file will run an application's logic, the nodes have embedded
logic that executes when Godot Engine activates the nodes. There are many ways that
a node can be activated. For us to slip into this process we need to write a *script*
that will let us define our own definition of an activated behavior.

Your First Program
------------------

First, click on the ``Node2D`` you created and then press the top-right icon in the Scene dock
to add a script. Call the script ``test.gd`` and click "Create".

TODO: add image

.. tabs
  ..code-tab gdscript
    extends Node2D
    
    func _ready():
        pass

This is the most basic program we can create in GDScript. There are a lot of things
happening here, but we're only going to examine them at a high level for right now. In future sections,
we'll go more in-depth.

Types of Code
-------------

First, note that programs are split into two portions. Declarative code and Imperative code.

Declarative code communicates structural information about the source code. It defines *what* it can do.

Imperative code communicates sequential instructions within the source code. It defines *how* it executes or
uses the content that has been **declared** for it by the declarative code.

Second, note that a 'type' in a programming language is a named entity that can be used to some degree by other types. While
most languages will support the invention of user-defined types, scripting languages make "scripts" precisely because
they are using the types defined by the program they give instructions to.

First Program Breakdown
-----------------------

``extends Node2D`` - A **declarative** statement that tells the VM that our script is extending Godot's ``Node2D`` type.
Notice how the type of node we created and the script that it extends are the same. If this statement were removed, the
script would extend Godot's ``Reference`` type by default. If we did this though, the script would become incompatible
with the ``Node2D`` it is currently attached to in the scene.

``func _ready():`` - This is more **declarative** code, informing the VM that this script has a **func**tion
called "_ready". Functions are re-usable segments of code that, when executed, a.k.a. "called", execute the set of
instructions associated with them. Instructions might include declaring variables (to store data), using operators (to transform
the data), or making additional function calls. Functions take in a set of input values called **parameters** and then also
provide a single, output **return value**.

The syntax of a function declaration consists of 4 parts:

1. the ``func`` keyword, which tells the program reading the GDScript source code that the following term represents a function name.

2. An **identifier**, a.k.a. a name. Identifiers consist of underscores and/or any alphanumeric sequence, so long as it doesn't start with a numeric character.
This function's identifier is ``_ready``.

3. A **parameter list**, contained in parentheses. ``()`` is empty, telling the VM reading the generated bytecode that the ``_ready`` function does not have
any inputs.

4. A colon, ``:``, telling the VM that a new ``block`` is beginning. A block is a code segment that is directly associated with
some other line of code. In this case, the script is stating that the following block is associated with the function.

5. A block detailing the instructions the function executes. GDScript and other Pythonic languages define blocks by using indents.
   If the code is indented, it is contained in the block. One terminates the block by...

   1. getting to the end of the file,
   2. by starting a new line of code at a lesser indentation, or
   3. Inserting the ``pass`` keyword to indicate an "empty" block. Without, GDScript can't know if the empty block was intentional.

``_ready`` is a special function that Godot Engine calls a "notification". This means that the engine automatically calls it.
In this case, the engine calls it when the node and all of its children are done initializing, i.e. creating themselves.
