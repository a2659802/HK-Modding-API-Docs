Hook Reference
==============

Hooks are the main way of interacting with Hollow Knights code, and we can use them to wait for an event to happen in game, then intercept data related to that event and trigger some code and potentially return different data to what we recieved.

Hooks are set up like so :code:`ModHooks.Instance.SetPlayerBoolHook += MyCodeThatRunsWhenPlayerDataBoolsAreSet` if put this code in :code:`public override void Initialize()` your MyCodeThatRunsWhenPlayerDataBoolsAreSet function will be ran everytime the game tries to set any of the bool variables in PlayerData

.. note::
   Visual Studio and most other IDEs will autocomplete hook functions for you, in Visual Studio you just need to write :code:`ModHooks.Instance.SetPlayerBoolHook +=` and then press tab and it'll create a function with the correct type signature.

SetPlayerBool
^^^^^^^^^^^^^
:code:`ModHooks.Instance.SetPlayerBoolHook += PlayerBoolSet`

:code:`public void PlayerBoolSet( string target, bool val )`

This code can be used to check when certain events happen in game such as obtaining a charm by checking if :code:`target == "gotCharm_X"` where X is the id of the charm

GetPlayerBool
^^^^^^^^^^^^^
:code:`ModHooks.Instance.GetPlayerBoolHook += PlayerBoolGet`

:code:`public bool PlayerBoolGet( string target )`

This can be used to override the games request for bools stored in saves, such as charm equipped states.

.. code-block:: c#
    
    //This snippet makes Wayward Compass be permanently equipped.
    public bool PlayerBoolGet( string target ){
        if( "target" == "equippedCharm_1") return true;
        return PlayerData.instance.InternalBoolGet(target);
    }

.. warning::
   If you use PlayerData.instance.GetBool, instead of InternalBoolGet you'll call your function again and crash the game.

SetPlayerInt
^^^^^^^^^^^^
:code:`ModHooks.Instance.SetPlayerIntHook += PlayerIntSet`

:code:`public void PlayerIntSet( string target, int val )`

See `SetPlayerBool`_, but with an int instead of a bool.

GetPlayerInt
^^^^^^^^^^^^
:code:`ModHooks.Instance.GetPlayerIntHook += PlayerIntGet`

:code:`public bool PlayerIntGet( string target )`

See `SetPlayerInt`_, but with an int instead of a bool.

NewPlayerDataHook
^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.NewPlayerDataHook += NewPlayerData`

:code:`public void NewPlayerData( PlayerData instance )`

.. warning::
   Do Not Use - Due to a bug in the game this is called way too often.

BlueHealthHook
^^^^^^^^^^^^^^
:code:`ModHooks.Instance.BlueHealthHook += BlueHPRestored`

:code:`public int BlueHPRestored()`

This is called whenever :code:`PlayerData.UpdateBlueHealth()` is called, which handles lifeblood heart/core, but DOES NOT handle Joni's or lifeblood bugs.

.. code-block:: c#

    //This snippet makes the lifeblood charms twice as effective.
    public int BlueHPRestored(){
        int retValue = 0;
        if(PlayerData.instance.GetBool("equippedCharm_8")) retValue += 2;
        if(PlayerData.instance.GetBool("equippedCharm_9")) retValue += 4;
        return retValue;
    }

.. note::
   It's important to note here the use of PlayerData.instance.GetBool("equippedCharm_8") instead of the simpler PlayerData.instance.equippedCharm_8 this is to maximize mod compatibility. If another mod overrides the equippedCharm_8 for whatever reason we want to respect that.

TakeHealthHook
^^^^^^^^^^^^^^
:code:`ModHooks.Instance.TakeHealthHook += TakeDamage`

:code:`public int TakeDamage( int damage )`

This is called whenver the Knight takes damage via the :code:`HeroController.TakeHealth` function.

.. code-block:: c#

    //This snippet doubles all damage dealt to the Knight
    public int TakeDamage( int damage ){
        return damage*2;
    }

.. code-block:: c#

    //This snippet forces the Knight to die if he takes any damage.
    public int TakeDamage( int damage ){
        PlayerData.instance.health = 0;
        return damage;
    }

AfterTakeDamageHook
^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.AfterTakeDamageHook += AfterTakeDamage`

:code:`public int AfterTakeDamage( int hazardType, int damageAmount )`

This is called in :code:`HeroController.TakeDamage` after invincibility is accounted for, but before damage is applied.

.. note::
   Invincibility in this case doesn't include Baldur Shell, so that could negate damage after this hook is called.

BeforePlayerDeadHook
^^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.BeforePlayerDeadHook += BeforePlayerDied`

:code:`public void BeforePlayerDied()`

Called at the beginning of the :code:`GameManger.PlayerDead` function.

AfterPlayerDeadHook
^^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.AfterPlayerDeadHook += AfterPlayerDied`

:code:`public void AfterPlayerDied()`

Called at the end of the :code:`GameManger.PlayerDead` function.

AttackHook
^^^^^^^^^^
:code:`ModHooks.Instance.AttackHook += OnAttack`

:code:`public void OnAttack( AttackDirection dir )`

Called whenever the Knight attacks, this is called at the beginning of :code:`HeroController.Attack`.


DoAttackHook
^^^^^^^^^^^^
:code:`ModHooks.Instance.DoAttackHook += OnDoAttack`

:code:`public void OnDoAttack()`

Called whenever the Knight attacks, this is called at the beginning of :code:`HeroController.DoAttack`, effectively identical to AttackHook except its called before attack_cooldown is set.

AfterAttackHook
^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.AfterAttackHook += AfterAttack`

:code:`public void AfterAttackHook( AttackDirection dir )`

Called whenever the Knight attacks, this is called at the end of :code:`HeroController.Attack`, this is called after all the objects related to the slash have been created, possible uses include recolouring/resizing the Knights slash SFX or conditionally setting nail damage.

SlashHitHook
^^^^^^^^^^^^
:code:`ModHooks.Instance.SlashHitHook += OnSlashHit`

:code:`public void OnSlashHit( Collider2D otherCollider, GameObject gameObject )`

Called whenever the nail hits anything with a collider, this includes doors, levers, npcs and of course enemies. Potential uses include overriding the 0 damage feature the game has to allow 0 damage nail mods to hit levers.

CharmUpdateHook
^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.CharmUpdateHook += OnCharmUpdate`

:code:`public void OnCharmUpdate()`

This is called after any charm is equipped and if that charms effects are done in code (Which at the time of writing are Nailmaster's Glory, Fragile Heart, Joni's Blessing and Carefree Melody) it'll be called after their effects run. 

.. code-block:: c#

    //This snippet buffs Joni's Blessing back to 1.5x max HP and also fixes it to use PlayerData.instance.GetBool()
    public void OnCharmUpdate() {
        if (PlayerData.instance.GetBool("equippedCharm_27")){
            PlayerData.instance.SetInt("joniHealthBlue", (int)((float)PlayerData.instance.GetInt("maxHealthBase") * 1.5f));
            PlayerData.instance.MaxHealth();
        }
    }

HeroUpdateHook
^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.HeroUpdateHook += OnHeroUpdate`

:code:`public void OnHeroUpdate()`

This is called everyframe, as long as the Knight exists. Useful for running code every frame during gameplay without having to create a monobehaviour.

BeforeAddHealthHook
^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.BeforeAddHealthHook += BeforeAddHealth`

:code:`public int BeforeAddHealth( int amount )`

This is called before health is added to the Knights health pool, amount is the amount to be added to the health pool.

.. code-block:: c#

    //This snippet makes healing cost 100 geo.
    public int BeforeAddHealth( int amount ) {
        if( PlayerData.instance.GetBool("geo") > 100 ){
            PlayerData.instance.geo -= 100;
            return amount;
        }
        return 0;
    }

HeroUpdateHook
^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.HeroUpdateHook += OnHeroUpdate`

:code:`public void OnHeroUpdate()`

This is called everyframe, as long as the Knight exists. Useful for running code every frame during gameplay without having to create a monobehaviour.

FocusCostHook
^^^^^^^^^^^^^
:code:`ModHooks.Instance.FocusCostHook += FocusCostCalc`

:code:`public float FocusCostCalc()`

This is called when you begin to heal, and returns a float that multiplies how much soul is drained.

.. code-block:: c#

    //This snippet makes focusing use all your soul.
    //note to make this work with less than 33 soul, you will need to change the focus MP amount
    public float FocusCostCalc(){
        return (float)PlayerData.instance.GetInt("MPCharge") / 33.0f;
    }

SoulGainHook
^^^^^^^^^^^^
:code:`ModHooks.Instance.SoulGainHook += GainSoul`

:code:`public int GainSoul( int amount )`
 
This is called whenever the Knight gains souls from hitting an enemy with his nail, it passes it the amount it would gain and returns how much it will gain.

.. code-block:: c#

    //This snippet doubles soul gain from nail hits whenever the knight is on 1 hp.
    public int GainSoul( int amount ){
        if( PlayerData.instance.GetInt("health") == 1 )
           return amount * 2;
        return amount;
    }

DashVectorHook
^^^^^^^^^^^^^^
:code:`ModHooks.Instance.DashVectorHook += ChangeDashVel`

:code:`public Vector2 ChangeDashVel( Vector2 velocity )`
 
Sets the velocity of a dash, called every frame.

.. code-block:: c#
    
    //This snippet makes you accelerate during dashes
    public Vector2 ChangeDashVel( Vector2 velocity ){
        return velocity * (1 + (2*time.deltaTime));
    }

DashPressedHook
^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.DashPressedHook += DashPressed`

:code:`public bool DashPressed()`
 
Called whenever dash is pressed, return overrides vanilla inputs: true to not dash, false to dash.

.. code-block:: c#
    
    //This snippet makes it only possible to dash if you are also rising in your jump.
    public bool DashPressed(){
        return !HeroController.instance.cState.jumping;
    }

.. note::
   It might seem weird to NOT dash if you return true, but the intended functionality is for modded implementations of the entire dash, in which case you'd want to disable vanilla code entirely, and becaue of this you return true if you have a modded implementation.

SavegameLoadHook
^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.SavegameLoadHook += SaveLoaded`

:code:`public void SaveLoaded( int id )`
 
Called directly after a save is loaded, id is the save slot of the loaded file.

SavegameSaveHook
^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.SavegameSaveHook += SaveSaved`

:code:`public void SaveSaved( int id )`
 
Called directly after a save is saved, id is the save slot of the saved file.

NewGameHook
^^^^^^^^^^^
:code:`ModHooks.Instance.NewGameHook += NewGameStarted`

:code:`public void NewGameStarted( int id )`
 
Called directly after a new game is started, id is the save slot of the created file.

SavegameClearHook
^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.SavegameClearHook += SaveDeleted`

:code:`public void SaveDeleted( int id )`
 
Called directly before a save is deleted, id is the save slot of the file to be deleted.

AfterSavegameLoadHook
^^^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.AfterSavegameLoadHook += AfterSaveLoaded`

:code:`public void AfterSaveLoaded( Patches.SaveGameData data )`
 
Called directly after a save is loaded, however gives you access to the SaveGameData.

BeforeSavegameSaveHook
^^^^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.BeforeSavegameSaveHook += BeforeSave`

:code:`public void BeforeSave( Patches.SaveGameData data )`
 
Called directly before a save is saved and allows you to edit the data that will be written to the save file.

GetSaveFileNameHook
^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.GetSaveFileNameHook += SaveName`

:code:`public string SaveName( int saveSlot )`
 
Used to have custom filenames, designed to allow per mod savefiles but can also be used to disable saves or add more saveslots.

.. code-block:: c#
    
    public string SaveName( int saveSlot ){
        return "myModName" + saveSlot.toString() + ".dat";
    }

AfterSaveGameClearHook
^^^^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.AfterSaveGameClearHook += AfterSaveCleared`

:code:`public void AfterSaveCleared( int saveSlot )`
 
Called after a save has been deleted, saveSlot is the slot of the deleted save.

LanguageGetHook
^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.LanguageGetHook += LanguageGet`

:code:`public string LanguageGet( string key, string sheet )`
 
Called whenever a localized string is requested, useful for injecting custom text into the game

.. code-block:: c#
    
    public string LanguageGet( string key, string sheet )
        if( key == "myKey" )
            return "some profound text";
        return Language.GetInternal(key, sheet);
    }

.. code-block:: c#
    
    public string LanguageGet( string key, string sheet )
        return "KEY: " + key + " displays this text: " + Language.GetInternal(key, sheet);
    }


.. note::
   To find out what keys point to what strings of text, you can either use a unity asset extractor such as UABE and look for EN_*.txt, or putting logging inside your hook.

SceneChanged
^^^^^^^^^^^^
:code:`ModHooks.Instance.SceneChanged += SceneLoaded`

:code:`public void SceneLoaded( string targetScene )`
 
Called after a scene is loaded, scene is unity's internal name for levels and is any area in game aswell as several cutscenes and the main menu.

.. note::
   It's recommended to use the internal SceneLoaded hook instead of the the api's by using UnityEngine.SceneManagement.SceneManager.sceneLoaded
   However you'll probably want to delay this by a frame by using a coroutine.

BeforeSceneLoadHook
^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.BeforeSceneLoadHook += BeforeSceneLoad`

:code:`public string BeforeSceneLoad( string sceneName )`
 
Called directly before a scene starts to load, returning a different string will load a different scene.

.. note::
   If you transition normally, the game expects a scene the Knight could possibly access from that direction, i.e. if you left a room on the right the new scene requires an entrance on the left, dreamgating is glitchy as expected, and going into dreams requires a scene with an object to say where to spawn.

CursorHook
^^^^^^^^^^
:code:`ModHooks.Instance.CursorHook += Cursor`

:code:`public void Cursor()`
 
Called whenever the game tries to show the cursor.

ColliderCreateHook
^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.ColliderCreateHook += ColliderCreated`

:code:`public void ColliderCreated( GameObject go )`
 
Called whenever a gameobject is created with both a PlayMakerFSM and a Collider2D, useful for obtaining references to gameplay objects.

ObjectPoolSpawnHook
^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.ObjectPoolSpawnHook += ObjectCreated`

:code:`public GameObject OnObjectPoolSpawn( GameObject go )`
 
Called whenever the game attempts to create a gameobject.

.. warning::
   This happens a lot and you should be careful about the complexity of code contained in your hook.

OnGetEventSenderHook
^^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.OnGetEventSenderHook += SpellCreated`

:code:`public GameObject SpellCreated( GameObject go, HutongGames.PlayMaker.Fsm fsm )`
 
Called whenever OnGetEvent fsm action is ran, this action only exists inside of attacks/spells so is a decent way to get references to them.

.. note::
   This has several issues with the games global object pool, and anything you try to do will likely not work how you intend to without major work arounds.

ApplicationQuitHook
^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.ApplicationQuitHook += ApplicationQuit`

:code:`public void ApplicationQuit()`
 
Called when the game is closed, probably no general use for this, it was implemented for the PlayerDataTracker so a websocket message could be sent whenever the game was closed.

.. note::
   This code won't run if the game unexpectedly terminates.

SetFontHook
^^^^^^^^^^^
:code:`ModHooks.Instance.SetFontHook += FontSet`

:code:`public void FontSet()`
 
Called whenever the game changes language with a different font.

TextDirectionHook
^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.TextDirectionHook += GetTextDirection`

:code:`public bool GetTextDirection( bool direction )`
 
Called when the text renderer requests write/read direction of text. Useful for fan translations to RTL languages.

HitInstanceHook
^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.HitInstanceHook += HitInstanceCreated`

:code:`public HitInstance HitInstanceCreated( HutongGames.PlayMaker.Fsm owner, HitInstance hitInst )`
 
Called whenever a hit instance is created, hit instances are used to set the damage and damage type of any collisions that occur, so you can override this to set damage on awkward to adjust values such as flukenest damage.

DrawBlackBordersHook
^^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.DrawBlackBordersHook += BlackBordersDrawn`

:code:`public void BlackBordersDrawn( List<GameObject> borders )`
 
Called when the SceneManager loads a scene, specifically as it calls DrawBlackBorders, passes references to all the objects used to create the border. Useful to delete the vignette if its not your style.

OnEnableEnemyHook
^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.OnEnableEnemyHook += EnemyEnabled`

:code:`public bool EnemyEnabled( GameObject enemy, bool isDead )`
 
Called whenever an enemy attempts to spawn, if isDead is true this enemy is persistent and has already been killed, return isDead to mimic default behaviour.

.. code-block:: c#
    
    //This snippet will make all enemies spawn regardless of if they have been previously defeated (except bosses).
    public bool EnemyEnabled( GameObject enemy, bool isDead )
        return false;
    }

OnRecieveDeathEventHook
^^^^^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.OnRecieveDeathEventHook += EnemyDied`

.. code-block:: c#

    public void EnemyDied( EnemyDeathEffects enemyDeathEffects, bool eventAlreadyRecieved,
                           ref float? attackDirection, ref bool resetDeathEvent, 
                           ref bool spellBurn, ref bool isWatery )
 
Called whenever an enemy dies.

.. note::
   Possibly triggered multiple times per death, be careful not to trigger code more often than expected.

OnRecordKillForJournalHook
^^^^^^^^^^^^^^^^^^^^^^^^^^
:code:`ModHooks.Instance.OnRecordKillForJournalHook += RecordJournalKill`

.. code-block:: c#

    public void RecordJournalKill( EnemyDeathEffects enemyDeathEffects, string playerDataName, 
                                   string killedBoolPlayerDataLookupKey, string killCountIntPlayerDataLookupKey, 
                                   string newDataBoolPlayerDataLookupKey )
 
Called whenever an enemy dies, and it's recorded to the journal. The strings are lookup values you can use with PlayerData.GetBool()/PlayerData.GetInt() to obtain relevant data from the save data.

.. highlight:: c#