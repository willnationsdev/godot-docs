.. _doc_state_pattern:

State Pattern
=============

The State Pattern grants you the ability to change what your objects do in
response to external stimuli.

Example Use Cases:
    1. Animation: "Is my character standing, walking, talking, swimming, flying,
    or falling?"
    1. AI: "What is the player doing? Running at me? Shift to a defensive
    behavior (shielding state). Now they are strafing me? Wait till they are
    closer (bide state). They in range? Attack (attack state)!"
    1. Effects: "Okay, pressed button to swing sword. It swings once. But if I
    press and hold the button it stays stretched out. A second later, it
    charges to a first-level full-circle swing. Another second and it starts
    glowing, entering into a second-level, magical, full-circle swing. Each
    version of the attack has a custom particle effect, hitbox, and damage
    parameters.

Design:
    1. Define one or more data structures which identify the possible states.
    1. Define one state variable which assigns an entity to one of the states.
    1. Define multiple function bodies (either within the same function or
    separately) which have access to the state variable.
    1. Define one or more contexts in which the state variable may transition
    from one state to another state.

Implementations
===============

Bool flag state
---------------

.. tabs::

  .. code-tab:: gdscript GDScript

    # bool_flag_node.gd
    extends Node
    # states? true|false
    # transitions? public var. Just assign `true` or `false` directly.
    var flag = false
    func do_something():
        if flag:
            print("Function Body 1")
        else:
            print("Function Body 2")
    
    .. code-tab:: csharp

    public class BoolFlagNode : public Node
    {
        private bool _flag;
        public bool Flag { get; set; }

        public void DoSomething()
        {
            if (Flag)
            {
                GD.Print("Function Body 1");
            }
            else
            {
                GD.Print("Function Body 2");
            }
        }
    }

Enum state
----------

.. tabs::

  .. code-tab:: gdscript GDScript

    # bool_flag_node.gd
    extends Node
    var flag = false
    func do_something():
        if flag:
            print("Function Body 1")
        else:
            print("Function Body 2")

    .. code-tab:: csharp

    public class BoolFlagNode : public Node
    {
        private bool _flag;
        public bool Flag { get; set; }

        public void DoSomething()
        {
            if (Flag)
            {
                GD.Print("Function Body 1");
            }
            else
            {
                GD.Print("Function Body 2");
            }
        }
    }

   1. A bool `"flag" <https://stackoverflow.com/questions/17402125/what-is-a-flag-variable>`__
      variable which toggles on and off custom behavior.
   2. An :ref:`enum <doc_gdscript_enums>` type combined with a variable of that
      type. You could have a method which behaves differently based on which
      value the enum variable has.
   3. A full OOP pattern implementation with a different script for
      each possible state and a common set of methods shared between them.
   4. A Node for every state so that you can can configure each state visually
      from the editor.

Implementations
===============

