Custom Achievements/Charms/Items
=====================================

Background
^^^^^^^^^^
You can add custom achievements, charms and items without having a good understanding of the various aspects that run Hollow Knight and its mods. 
Therefore, this guide recommends and expects that you have already worked with the following topics:

* Understanding the structure of a basic mod.
* Adding embedded resources to a mod.

.. note::
    These steps are made using Visual Studio as an IDE, if you're using Rider, the details might not match up.
    If you are unsure about details, ask in the Discord.

Load embedded images
^^^^^^^^^^^^^^^^^^^^

Embedded image resources can be loaded with:

.. note::
    Embedded resources have the naming format :code:`{Projectname}.Resources.{Filename_With_Extension}`.

.. code-block:: c#

    private void loadResources()
    {
        Assembly _asm = Assembly.GetExecutingAssembly();
        using (Stream s = _asm.GetManifestResourceStream("CharmHelperExample.Resources.Filename.png"))
        {
            if (s != null)
            {
                byte[] buffer = new byte[s.Length];
                s.Read(buffer, 0, buffer.Length);
                s.Dispose();

                //Create texture from bytes
                var tex = new Texture2D(2, 2);

                tex.LoadImage(buffer, true);

                // Create sprite from texture
                testSprite = Sprite.Create(tex, new Rect(0, 0, tex.width, tex.height), new Vector2(0.5f, 0.5f));
            }
        }
    }

Add a reference
^^^^^^^^^^^^^^^
1) After downloading SFCore from the ModInstaller, open your project and right-click :code:`References` and click on :code:`Add Reference`

2) Select :code:`Browse...` and navigate to the :code:`Mods` folder, where the mod has been installed, after which you can :code:`Add` it.

3) Click :code:`ok`.

Add custom charms
^^^^^^^^^^^^^^^^^

1) After adding the reference, you can follow the CharmHelper_Example_ code.

2) Going through the example:

3) Preparation of the save settings of the mod to hold the data for 4 charms. (Lines 13 - 20)

.. code-block:: c#

    public class CHESaveSettings : ModSettings
    {
        // insert default values here
        public List<bool> gotCharms = new List<bool>() { true, true, true, true };
        public List<bool> newCharms = new List<bool>() { false, false, false, false };
        public List<bool> equippedCharms = new List<bool>() { false, false, false, false };
        public List<int> charmCosts = new List<int>() { 1, 1, 1, 1 };
    }

4) Add the CharmHelper as a member of the main mod class (Lines 26 - 30)

.. code-block:: c#

    public class CharmHelperExample : Mod<CHESaveSettings, CHEGlobalSettings>
    {
        //private Sprite testSprite;
        public CharmHelper charmHelper { get; private set; }
    }

5) Initialize the CharmHelper with 4 custom charms and empty sprites, also initialize the callbacks needed. (Lines 54 - 66)

.. note::
    This step is the one where you can add your custom sprites. Simply load those instead of the :code:`new Sprite()`.

.. code-block:: c#

    public override void Initialize()
    {
        //loadResources();
        charmHelper = new CharmHelper();
        charmHelper.customCharms = 4;
        charmHelper.customSprites = new Sprite[] { new Sprite(), new Sprite(), new Sprite(), new Sprite() };
        //charmHelper.customSprites = new Sprite[] { testSprite, testSprite, testSprite, testSprite };

        initCallbacks();
    }

6) Initialize the callbacks needed. (Lines 83 - 93)

.. code-block:: c#

    private void initCallbacks()
    {
        ModHooks.Instance.GetPlayerBoolHook += OnGetPlayerBoolHook;
        ModHooks.Instance.SetPlayerBoolHook += OnSetPlayerBoolHook;
        ModHooks.Instance.GetPlayerIntHook += OnGetPlayerIntHook;
        ModHooks.Instance.SetPlayerIntHook += OnSetPlayerIntHook;
        ModHooks.Instance.AfterSavegameLoadHook += initSaveSettings;
        ModHooks.Instance.ApplicationQuitHook += SaveCHEGlobalSettings;
        ModHooks.Instance.LanguageGetHook += OnLanguageGetHook;
    }

7) Form the callbacks for language. (Lines 101 - 124)

.. code-block:: c#

    private string OnLanguageGetHook(string key, string sheet)
    {
        if (key.StartsWith("CHARM_NAME_"))
        {
            int charmNum = int.Parse(key.Split('_')[2]);
            if (charmHelper.charmIDs.Contains(charmNum))
            {
                return "CHARM NAME";
            }
        }
        if (key.StartsWith("CHARM_DESC_"))
        {
            int charmNum = int.Parse(key.Split('_')[2]);
            if (charmHelper.charmIDs.Contains(charmNum))
            {
                return "CHARM DESC";
            }
        }
        return Language.Language.GetInternal(key, sheet);
    }

8) Form the callbacks for boolean checks. (Lines 126 - 197)

.. code-block:: c#

    private bool OnGetPlayerBoolHook(string target)
    {
        if (target.StartsWith("gotCharm_"))
        {
            int charmNum = int.Parse(target.Split('_')[1]);
            if (charmHelper.charmIDs.Contains(charmNum))
            {
                return Settings.gotCharms[charmHelper.charmIDs.IndexOf(charmNum)];
            }
        }
        if (target.StartsWith("newCharm_"))
        {
            int charmNum = int.Parse(target.Split('_')[1]);
            if (charmHelper.charmIDs.Contains(charmNum))
            {
                return Settings.newCharms[charmHelper.charmIDs.IndexOf(charmNum)];
            }
        }
        if (target.StartsWith("equippedCharm_"))
        {
            int charmNum = int.Parse(target.Split('_')[1]);
            if (charmHelper.charmIDs.Contains(charmNum))
            {
                return Settings.equippedCharms[charmHelper.charmIDs.IndexOf(charmNum)];
            }
        }
        return PlayerData.instance.GetBoolInternal(target);
    }
    private void OnSetPlayerBoolHook(string target, bool val)
    {
        if (target.StartsWith("gotCharm_"))
        {
            int charmNum = int.Parse(target.Split('_')[1]);
            if (charmHelper.charmIDs.Contains(charmNum))
            {
                Settings.gotCharms[charmHelper.charmIDs.IndexOf(charmNum)] = val;
                return;
            }
        }
        if (target.StartsWith("newCharm_"))
        {
            int charmNum = int.Parse(target.Split('_')[1]);
            if (charmHelper.charmIDs.Contains(charmNum))
            {
                Settings.newCharms[charmHelper.charmIDs.IndexOf(charmNum)] = val;
                return;
            }
        }
        if (target.StartsWith("equippedCharm_"))
        {
            int charmNum = int.Parse(target.Split('_')[1]);
            if (charmHelper.charmIDs.Contains(charmNum))
            {
                Settings.equippedCharms[charmHelper.charmIDs.IndexOf(charmNum)] = val;
                return;
            }
        }
        PlayerData.instance.SetBoolInternal(target, val);
    }

9) Form the callbacks for integer checks. (Lines 199 - 228)

.. code-block:: c#

    private int OnGetPlayerIntHook(string target)
    {
        if (target.StartsWith("charmCost_"))
        {
            int charmNum = int.Parse(target.Split('_')[1]);
            if (charmHelper.charmIDs.Contains(charmNum))
            {
                return Settings.charmCosts[charmHelper.charmIDs.IndexOf(charmNum)];
            }
        }
        return PlayerData.instance.GetIntInternal(target);
    }
    private void OnSetPlayerIntHook(string target, int val)
    {
        // We don't need other mods to adjust the cost of our charms, but it could be added if wanted
        PlayerData.instance.SetIntInternal(target, val);
    }

Add custom achievements
^^^^^^^^^^^^^^^^^^^^^^^

1) After adding the reference, you can follow the CharmHelper_Example_ code, but you can leave out a lot, as most things are handled by the helper.

2) Similar to step 4 of Add_custom_charms_, you add the AchievementHelper as a member of your main mod class.

.. code-block:: c#

    public class AchievementModClass : Mod
    {
        //private Sprite testSprite;
        public AchievementHelper achHelper { get; private set; }

5) Initialize the AchievementHelper with 1 custom achievement and empty sprites.

.. note::
    This step is the one where you can add your custom sprites. Simply load those instead of the :code:`new Sprite()`.

.. note::
    For the :code:`Convo`'s to work properly, you need the :code:`ModHooks.Instance.LanguageGetHook` similar to the Helper above, but only listening to the custom convo keys.

.. code-block:: c#

    public override void Initialize()
    {
        //loadResources();
        achHelper = new AchievementHelper();
        achHelper.customAchievements.Add(new s_CustomAchievement() {
            key = "YourCustomAchievementKey",
            sprite = new Sprite(),
            titleConvo = "YourCustomLanguageConvo",
            textConvo = "YourCustomLanguageConvo",
            hidden = false
        });
    }

6) Done! Now you can at some point in your mod have :code:`GameManager.instance.AwardAchievement("YourCustomAchievementKey");` to grant the player the achievement.

Add custom inventory items
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. warning::
    Don't use this yet, it only supports normal items (like Lumafly Lantern and similar), not counted items (like Rancid Eggs).

1) After adding the reference, you can follow the CharmHelper_Example_ code, but you can leave out a lot, as most things are handled by the helper.

2) Similar to step 4 of Add_custom_charms_, you add the ItemHelper as a member of your main mod class.

.. code-block:: c#

    public class ItemModClass : Mod
    {
        //private Sprite testSprite;
        public ItemHelper itemHelper { get; private set; }
    }

5) Initialize the AchievementHelper with 1 custom achievement and empty sprites.

.. note::
    This step is the one where you can add your custom sprites. Simply load those instead of the :code:`new Sprite()`.

.. note::
    For the :code:`Convo`'s to work properly, you need the :code:`ModHooks.Instance.LanguageGetHook` similar to the Helper above, but only listening to the custom convo keys.

.. note::
    For the :code:`playerdataBool` to work properly, you need the :code:`ModHooks.Instance.GetPlayerBoolHook` & :code:`ModHooks.Instance.SetPlayerBoolHook` similar to the CharmHelper, but only listening to the custom bool key.

.. code-block:: c#

    public override void Initialize()
    {
        //loadResources();
        itemHelper = new ItemHelper(new s_CustomNormalItem[] {
            new s_CustomNormalItem() {
                uniqueName = "YourUniqueStateName", // Just choose a random string tbh
                sprite = new Sprite(),
                playerdataBool = "YourCustomPlayerDataBool",
                nameConvo = "YourCustomLanguageConvo",
                descConvo = "YourCustomLanguageConvo"
            }
        });
    }

6) Done! You can now have custom Inventory Items.


.. _CharmHelper_Example: https://github.com/SFGrenade/ModdingHelper/blob/master/CharmHelper_Example.cs
.. _Add_custom_charms: https://radiance.host/apidocs/SFCore.html#add-custom-charms