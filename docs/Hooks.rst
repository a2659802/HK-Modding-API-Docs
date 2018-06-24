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

This can be used to override the games request for bools stored in saves, such as charm equipped states. The following Snippet would make Charm 1 (Wayward Compass) be permanently equipped.

.. code-block:: c#

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

.. warning::
   Do Not Use - Due to a bug in the game this is called way too often.

:code:`ModHooks.Instance.NewPlayerDataHook += NewPlayerData`

:code:`public void NewPlayerData( PlayerData instance )`

BlueHealthHook
^^^^^^^^^^^^^^^^^

:code:`ModHooks.Instance.BlueHealthHook += BlueHPRestored`

:code:`public int BlueHPRestored()`

This is called whenever :code:`PlayerData.UpdateBlueHealth()` is called, which handles lifeblood heart/core, but DOES NOT handle Joni's or lifeblood bugs. The following code makes the 2 lifeblood charms bonuses twice as large.

.. code-block:: c#

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

This is called whenver the Knight takes damage via the :code:`HeroController.TakeHealth` function, here is a simple snippet to double all damage taken and also a snippet to enable one hit kill.

.. code-block:: c#

    public int TakeDamage( int damage ){
        return damage*2;
    }

.. code-block:: c#

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

This is called after any charm is equipped and if that charms effects are done in code (Which at the time of writing are Nailmaster's Glory, Fragile Heart, Joni's Blessing and Carefree Melody) it'll be called after their effects run. The following snippet buffs Joni's Blessing back to 1.5x max HP and also fixes it to use PlayerData.instance.GetBool().

.. code-block:: c#

        public void OnCharmUpdate() {
            if (PlayerData.instance.GetBool("equippedCharm_27")){
                PlayerData.instance.SetInt("joniHealthBlue", (int)((float)PlayerData.instance.GetInt("maxHealthBase") * 1.5f));
                PlayerData.instance.MaxHealth();
            }
        }


.. highlight:: c#