Getting Started
===============

Installing Mods
^^^^^^^^^^^^^^^

Installing the API
~~~~~~~~~~~~~~~~~~
Too install mods that use the modding API you first need to install the Modding API from the the google drive or build it yourself.

The Assembly-CSharp.dll should overwrite your vanilla one at :code:`~\Hollow Knight\hollow_knight_Data\Managed\Assembly-CSharp.dll`.

.. note::
   I'd recommend backing up your vanilla assembly-csharp.dll so you can easily uninstall the API without having to reinstall your game.

After installing the API Assembly-CSharp.dll, the API is installed.

Now you can install mods, mods will be zipped and will either contain :code:`modname.dll` file or a :code:`hollow_knight_Data` folder.

Installing DLL mods
~~~~~~~~~~~~~~~~~~~

The dll file contained in the zip needs to be moved into :code:`~\Hollow Knight\hollow_knight_Data\Managed\Mods` 

To uninstall the mod just remove it from this folder.

Installing folder mods
~~~~~~~~~~~~~~~~~~~~~~

Most mods will come in a folder named :code:`hollow_knight_Data` and this folder should just be moved to the :code:`~\Hollow Knight` folder to overwrite the vanilla one.

To uninstall these you can just find the dll file it installed in your :code:`~\Hollow Knight\hollow_knight_Data\Managed\Mods` folder and remove it from the folder, this will stop the API from loading the mod and consequentally any additional files it may have required to run.

Installing via Crossroads
~~~~~~~~~~~~~~~~~~~~~~~~~

??? I don't know I've never done it, someone with more experience with that please fill this in :^)

Creating Mods
^^^^^^^^^^^^^

1. Install the API as directed above.
2. Either download an example mod from github_ or create a new C# Class Library Project, I'd recommend downloading an example so you know the structure of mods.
3. Ensure that your project has references to :code:`~\Hollow Knight\hollow_knight_Data\Managed\Assembly-CSharp.dll` and :code:`~\Hollow Knight\hollow_knight_Data\Managed\UnityEngine.dll`, you may also want to reference :code:`~\Hollow Knight\hollow_knight_Data\Managed\UnityEngine.UI.dll` if you intend to use Unity's Canvas in your mod.
4. Ensure you have a class in your project that extends the :code:`Mod` class, and that it contains a :code:`public override void Initialize()` function
5. Assuming you've done everything correctly you should now be able to build your mod and install it by moving the compiled DLL into :code:`~\Hollow Knight\hollow_knight_Data\Managed\Mods` if the API is installed you should see your mods assembly name at the top left followed by what you made :code:`public override string GetVersion()` return.
6. To add more functionality you will have to add delegates to the hooks that the ModdingAPI/Unity exposes, there is a list of them, roughly when they are called and what they may be useful for.

.. note::
   If you downloaded a example mod, your mod will have an assembly name like ExampleMod, you will probably want to change this by right clicking on your project in the Solution Explorer and selecting Properties, Assembly name will be the top left most option on the screen that appears.

.. _github: https://github.com/seanpr96/HollowKnight.Modding/tree/master/ExampleMods