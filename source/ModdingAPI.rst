Mod Class
=========

The Modding API's Mod class (found here on github_) allows you to initialize your mod and provides many other useful methods.
Your mod's main class should be set to inherit from the API's Mod class: :code:`public class ClassName : Mod, ITogglableMod`

.. note::
    Be sure to implement the interface ITogglableMod as well so that players are able to enable and disable your mod in the menu

Mod Name
^^^^^^^^^^^^^^^^^^^
By default, the name of your mod is whatever the name of your main class is. To change this, we can do the following:
:code:`public ClassName(): base("The Name You Want") { }`.

Adding Version Number
^^^^^^^^^^^^^^^^^^^^^^^
Your mod will display the version number as "UNKNOWN" if you do not give it one. We can add this by overriding the Mod class's GetVersion() method:
:code:`public override string GetVersion() => "Your.Version.Number";`.

Load Priority
^^^^^^^^^^^^^
You can change how your mod is ordered compared to other mods by overriding the LoadPriority() method. By default, this method will load by alphabetic order.
:code:`public override int LoadPriority() => Priority_Number;`.

Preload GameObjects
^^^^^^^^^^^^^^^^^^^
GetPreloadNames() is a powerful tool that obtains a copy of almost whichever gameobject you want based on the scene it comes from and its full name.
Use it in the following way:

.. code-block:: c#

    public override List<(string, string)> GetPreloadNames()
    {
        return new List<(string, string)>
        {
            ("SceneName1","Go/Path/Name/1"),
            ("SceneName2", "Go/Path/Name/2")
        };
    }

You can then use a version of the Initialize() method (covered in the subsection below) to recieve copies of these gameobjects.


Initialize
^^^^^^^^^^
This is where you finally gain control of your mod. Initialize is run once at the start of the game when the menu has been loaded. 
It has two different versions, one is :code:`public override void Initialize` and
the other is :code:`public override void Initialize(Dictionary<string, Dictionary<string, GameObject>> preloadedObjects)`. The first Initialize shouldn't need any explaining, you just put your code in that method and it runs at the start of the game.

The second is only slightly more complicated and relates to the GetPreloadNames() described in the subsection above. 
The second Initialize() gives you the gameobjects you asked for in GetPreloadNames() in the form of preloadedObjects which is a dictionary with its key 
being the name of the gameobject's scene, and the value being another dictionary with a key of the gameobject's name and a value that is the desired gameobject.
Using the same example as in the previous subsection, we get the first gameobject using the following code: 
:code:`preloadedObjects["SceneName1"]["Go/Path/Name/1"]`. Usually, we store these gameobjects in our own public dictionary so that other classes in our
project can access them.


Saving Data
^^^^^^^^^^^
Mod data comes in two different varieties, the first being SaveSettings and the other GlobalSettings. 
SaveSettings consists of data that is unique to each save file where the player unlocking an area is an exmaple of this.
GlobalSettings consists of data that should be the same across all save files, an example of this could be the difficulty setting of a boss.
I will only give an example implementation of SaveSettings since GlobalSettings would be implemented the same way pretty much.

First we want to make a ModSettings class where we store our mod's save data.

.. code-block:: c#

    public class SaveModSettings : ModSettings
    {
        public bool HasUnlockedEpicGamerBoss;
        public int NumberOfTimesDiedToEpicBoss;
    }

Now, in your main class, do the following:

.. code-block:: c#

    public SaveModSettings Settings = new SaveModSettings();
    public override ModSettings SaveSettings
    {
        get => Settings;
        set => Settings = (SaveModSettings) value;
    }

If you want to edit your settings, simply do :code:`Settings.HasUnlockedEpicGamerBoss = true`.
And you are done!

.. _github: https://github.com/seanpr96/HollowKnight.Modding/blob/master/Assembly-CSharp/Mod.cs