Classes
=======

Hollow Knight uses various classes to control different functions of the game. 
For example, enemy health is controlled through the :code:`HealthManager` class and the player's movement is controlled through the :code:`HeroController` class.
This section will not cover every single class in the game nor will it describe perfectly the classes it does feature. 
Our hope is that you will eventually be able to figure out other classes by yourself and if not, you can always ask on Discord.

.. note::
    You can use DnSpy to look at each class's code and understand what it does.

HeroController
^^^^^^^^^^^^^^
The HeroController class is attached to the player's gameobject and handles their movement as well as other things related to the control of the player such as the soul level. 

This class is a singleton which means there is always only one instance of it in the game which you can access with :code:`HeroController.instance`.
One common way to access the player's gameobject is through :code:`HeroController.instance.gameobject`.

.. note:: 
    There are times such as during scene transitions where HeroController.instance may not exist so be sure to check if it is not null when you are not sure

cState
~~~~~~
This can be used to check and set whether the player is doing a certain action. For example, :code:`HeroController.instance.cState.attacking` is a bool that tells you whether the player is slashing horizontally.

Access FSMs
~~~~~~~~~~~
You can access different FSMs (covered in the PlaymakerFSM section) related to the player such as :code:`HeroController.instance.spellControl` which gives you the FSM that handles the player's spells.

Player Constants
~~~~~~~~~~~~~~~~
Some of the player's constants like :code:`HeroController.instance.SHADOW_DASH_TIME`, which is the cooldown time between each shadow dash, can be read/changed here.

Prefabs
~~~~~~~
Some gameobjects that are related to the player such as the Grubberfly elegy beams (:code:`HeroController.instance.grubberFlyBeamPrefabL`) can be reached in HeroController.


HealthManager
^^^^^^^^^^^^^
The HealthManager class controls enemy health and death. 
Unlike the HeroController class, this class is not a singleton since each enemy has their own instance of HealthManager. 
Remember that you can obtain the HealthManager (or any Unity component) of your desired enemy using the command: :code:`yourEnemyGameObject.GetComponent<HealthManager>()`.

HP 
~~
Once you have a HealthManager, you can read/change the hp of the enemy using :code:`healthManager.hp`.

Methods
~~~~~~~
Use :code:`healthManager.Hit(HitInstance hit)` to manually force the enemy to take damage in a customized fashion. HitInstance contains parameters such as DamageDealt, AttackType, Direction, and more.

You can manually kill an enemy using :code:`healthManager.Die(float? attackDir, AttackType attType, bool ignoreEvasion)`.

OnDeath
~~~~~~~
HealthManager also contains a hook that runs when the enemy dies (healthManager.hp reaches 0). This hook works the same way as the hooks in hook section.

.. tip::
    If you are adding a new enemy and want to copy a preexisting enemy's hit effects, you can do this:

    .. code-block:: c#

        // Add HealthManager to your new enemy and get the HealthManager of old enemy.
        HealthManager newEnemyHM = newEnemyGO.AddComponent<HealthManager>();
        HealthManager oldEnemyHM = oldEnemyGO.GetComponent<HealthManager>();
        // Copies the different properties of oldEnemyHM into newEnemyHM
        foreach (FieldInfo fi in typeof(HealthManager).GetFields(BindingFlags.Instance | BindingFlags.NonPublic)
            .Where(x => x.Name.Contains("Prefab")))
        {
            fi.SetValue(newEnemyHM, fi.GetValue(oldEnemyHM));
        }

        // Add EnemyHitEffectsUninfected to your new enemy and get the EnemyHitEffectsUninfected of old enemy.
        // EnemyHitEffectsUninfected is the class that contains getting hit effects for uninfected enemies (like the mantis).
        EnemyHitEffectsUninfected newEnemyHF = newEnemyGO.AddComponent<EnemyHitEffectsUninfected>();
        EnemyHitEffectsUninfected oldEnemyHF = oldEnemyGO.GetComponent<EnemyHitEffectsUninfected>();
        // Copies the different properties of oldEnemyHF into newEnemyHF
        foreach (FieldInfo fi in typeof(EnemyHitEffectsUninfected).GetFields(BindingFlags.Instance | BindingFlags.Public))
        {
            fi.SetValue(newEnemyHF, fi.GetValue(oldEnemyHF));
        }


DamageHero
^^^^^^^^^^
Stuff3

PlayerData
^^^^^^^^^^
Stuff4
