.. _doc_intro_engine_to_script:

Engine code to script code
==========================

This tutorial series covers how to translate engine source code into script
code. Experienced GDScript users may believe Godot's source code to be
beyond their abilities. In reality, Godot's developers went to painstaking
effort to ensure that the scripting API and C++ API are as alike as possible.
There are obvious differences between pythonic and C-based languages, but
looking closer exposes how closely related the two realms of programming are.

If readers would like to follow along, they will need to `download and/or
compile Godot Engine's source code <doc_getting_source>`. We will be working
with the Godot 3.1 stable code base. Over the course of this tutorial, we will
convert one of the more complex objects, the
:ref:`EditorInspector <https://github.com/godotengine/godot/blob/master/editor/editor_inspector.h>`,
into our own set of scripted types.

