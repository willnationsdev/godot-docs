.. _doc_editor_inspector_header:

EditorInspector header
======================

The first thing we will do is break down the EditorInspector's header file.
This file includes all of the declarations that the C++ code uses. Think of
it as the C++ code telling the rest of the engine what class names, properties,
and methods it owns so that other classes can be aware.

An important part to note about header files is that they can define more
than just 1 class per file. It isn't like GDScript where the file is a single
class and the declared classes are "subclasses" of the file's class. Here,
everything is declared at the top level. Often, the header does not initialize
properties or implement any methods.

.. note::
  
  We will need to prevent naming conflicts with the engine's actual editor
  code. We'll bundle GDScript classes under a ``Editor`` "namespace" script
  class, and C# classes under a C# namespace.

Creating class files
--------------------

Before we can get started "stubbing out" our GDScript classes with
empty functions and features, we must figure out what all classes we need.

The header file helps us out by having each class name declared with a ``class``
keyword. Let's run a search for every instance of "class ".

.. tabs::
  .. code-tab:: cpp

    class UndoRedo;

    class EditorPropertyRevert { ... };

    class EditorProperty : public Container {
        ...
        friend class EditorInspectorPlugin;
        ...
    };

    class EditorInspectorPlugin : public Reference {
        ...
        friend class EditorInspector;
        ...
    };

    class EditorInspectorCategory : public Control {
        ...
        friend class EditorInspector;
        ...
    };

    class EditorInspectorSection : public Control { ... };

    class EditorInspector : public ScrollContainer { ... };

  .. code-tab:: gdscript GDScript

    # editor_namespace.gd
    extends Reference
    class_name Editor
    const PropertyRevert = preload("property_revert.gd")
    const Property = preload("property.gd")
    const InspectorPlugin = preload("inspector_plugin.gd")
    const InspectorCategory = preload("inspector_category.gd")
    const InspectorSection = preload("inspector_section.gd")
    const Inspector = preload("inspector.gd")

    # property_revert.gd
    extends Reference

    # property.gd
    extends Container

    # inspector_plugin.gd
    extends Reference

    # inspector_category.gd
    extends Control

    # inspector_section.gd
    extends Control

    # inspector.gd
    extends ScrollContainer

  .. code-tab:: csharp

    // Assuming all of these have the following at the top, with one class per
    // file:
    using System;
    using GD;

    namespace Editor {
        public class ...
    }

    // PropertyRevert.cs
    public class PropertyRevert { ... };

    // Property.cs
    public class Property : Container { ... };

    // InspectorPlugin.cs
    public class InspectorPlugin : Reference { ... };

    // InspectorCategory.cs
    public class InspectorCategory : Control { ... };

    // InspectorSection.cs
    public class InspectorSection : Control { ... };

    // Inspector.cs
    public class Inspector : ScrollContainer { ... };

This strategy allows us to use ``Editor`` as a namespace for the classes
we've set up. In GDScript, we've created an Editor script, but there's no need
to instantiate it. Since they are constants, we need only access the values
on the global variable Script.

Handling it this way allows us to do the following:

.. tabs::
  .. code-tab:: gdscript GDScript
    
    # access constant values in editor_namespace.gd
    var inspector = Editor.Inspector.new()
  
  .. code-tab:: csharp

    // Access declared types in the "Editor" C# namespace
    Editor.Inspector inspector = new Editor.Inspector();


Now, let's outline the differences here.

To begin with, the ``class UndoRedo;`` declaration is missing from the GDScript
and C# versions. C++ declares this class in order to use the name. It is
specific to C++ and is referred to as a
:ref:`forward declaration <https://stackoverflow.com/questions/4757565/what-are-forward-declarations-in-c>`__.

GDScript and C# both operate as a layer on top of the engine API. As such,
they can already see all engine classes (GDScript sees them as global types,
C# imports them through the `using GD;` statement). There is no need for them
to access them one name or file at a time like C++ needs to.

C++ also has several instances of ``friend class`` statements within classes.
These are needed in C++ because, by default, C++ classes don't expose their
data to other classes. They have access modifiers applied to sections of their
code (``private``, ``protected``, and ``public``, exactly like the access
modifiers present in C#). C#
:ref:`has no 'friend' equivalent <https://stackoverflow.com/questions/204739/what-is-the-c-sharp-equivalent-of-friend>`__.
GDScript makes everything public and has no access modifiers. But, the editor
does hide properties and methods with leading underscores since that is the
convention for ``private`` naming content.

Translating properties
----------------------

The next step is to translate properties within each class into their
respective scripting equivalents. There are a number of generalizations we can
make as we go about this process:

1. In GDScript, private and protected properties should be the same as any
   other property, only with a leading underscore added if it doesn't already
   have one. Example: ``label`` -> ``_label``.

2. Replace StringNames with Strings. StringName is a class that hashes strings
   and stores the character data in a shared dataset. This prevents the engine
   from allocating new memory for every copy of a commonly-occurring string.
   StringNames are useful for things like class names.

2. Replace Vectors and Lists with Arrays. Keep track of what templated types
   they use so that the script can accommodate it.
   
   GDScript will require the
   use of documentation to maintain it since the static typing doesn't pierce
   through Array or Dictionary records in Godot 3.1.
   
   C# has the extra option of using arrays or generics.

   .. tabs::
     .. code-tab:: cpp

       Vector<Control *> focusables;

     .. code-tab:: gdscript GDScript

       # Array<Control>
       var focusables: Array = []

     .. code-tab:: csharp

       // Enforces Control types, but can resize itself as needed. Or...
       private List<Control> focusables = new List<Control>();
       // Does not enforce types. Accepts everything. Is the C# equivalent to Array. Uses a fixed size.
       private object[] focusables = new object[10];
   
   What's the difference between Vectors, Lists, and Arrays? You can read more about
   that :ref:`here <doc_data_preferences>`.

3. Replace Maps and HashMaps with Dictionaries. Again, keep track of template
   parameters. GDScript will use comments for documenting the type expectations
   until static typing can catch up.
   
   .. tabs::
     .. code-tab:: cpp

       Map<String, List<EditorProperty *> > editor_property_map;

     .. code-tab:: gdscript GDScript

       # Dictionary<String, Array<Editor.Property> >
       var editor_property_map: Dictionary = {}

     .. code-tab:: csharp

       // Does not support external access
       private var editorPropertyMap = new Dictionary<String, List<Editor.Property> >();

       // Supports external access
       public GD.Dictionary EditorPropertyMap = new GD.Dictionary();
   
4. Replace Sets with Dictionaries that have ``null`` values. This allows us to
   perform lookups within the container like we would with a Set.

   .. tabs::
     .. code-tab:: cpp

       Set<StringName> pending;
       if (pending.has("test")) {}

     .. code-tab:: gdscript GDScript

       # Set<String>
       var pending: Dictionary = {}
       func _ready():
           pending["test"] = null
           if pending.has("test"):
               pass

     .. code-tab:: csharp

       // Does not support external access
       private var editorPropertyMap = new Dictionary<String, List<Editor.Property> >();

       // Supports external access
       public GD.Dictionary EditorPropertyMap = new GD.Dictionary();

Translating methods
-------------------

When translating methods, parameter type hints and return values will follow
the same type alterations made during the property translations above.