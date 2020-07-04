PlayerData Reference
==========

PlayerData is the main component of holding and saving various different aspects of the player's progress throughout the game.
It holds a lot of data, most of which is used in game.

In every code block following, assume :code:`PlayerData pd = PlayerData.instance` was made.

When using these variables for computing, consider getting the values using e.g. :code:`PlayerData.instance.GetBool("gotCharm_1")`, as this way other mods can make their changes to those values without breaking anything.

This is only a list of the most important pieces of PlayerData.

Health
^^^^^^

Below are health related members and their respective default values.

.. code-block:: c#
    
    // Amount of total current masks left
    pd.health = 5;
    // Amount of max masks (execluding joni charm)
    pd.maxHealth = 5;
    // Amount of max masks
    pd.maxHealthBase = 5;
    // Amount of lifeblood masks (important for fury charm)
    pd.healthBlue = 0;
    // Likely exclusivly used withing PlayMakerFSMs
    pd.joniHealthBlue = 0;
    // Only used to check if you have all heart pieces
    pd.maxHealthCap = 9;
    // Stores previous health amount, basically unused
    pd.prevHealth = pd.health;
    
    // Likely exclusivly used withing PlayMakerFSMs
    pd.heartPieces = 0;
    // Likely exclusivly used withing PlayMakerFSMs
    pd.heartPieceCollected = false;
    // Gets set to true when pd.maxHealthBase == pd.maxHealthCap
    pd.heartPieceMax = false;
    
    // Amount of hits left that baldur charm will block
    pd.blockerHits = 4;

Collectables & Geo
^^^^^^^^^^^^^^^^^^

Below are collectable related members and their respective default values.

.. code-block:: c#
    
    // Likely exclusivly used withing PlayMakerFSMs
    pd.firstGeo = false;
    // Used as a currency in Hollow Knight, almost exclusivly used withing PlayMakerFSMs
    pd.geo = 0;
    // Likely exclusivly used withing PlayMakerFSMs
    pd.geoPool = 0;
    // Likely exclusivly used withing PlayMakerFSMs
    pd.fountainGeo = 0;
    
    // Amount of Wanderer's Journals the player has
    pd.trinket1 = 0;
    // Wether the player has found one
    pd.foundTrinket1 = false;
    // Amount of Hallownest Seals the player has
    pd.trinket2 = 0;
    // Wether the player has found one
    pd.foundTrinket2 = false;
    // Amount of King's Idols the player has
    pd.trinket3 = 0;
    // Wether the player has found one
    pd.foundTrinket3 = false;
    // Amount of Arcane Eggs the player has
    pd.trinket4 = 0;
    // Wether the player has found one
    pd.foundTrinket4 = false;
    // Wether the player doesn't have the given trinket
    pd.noTrinket1 = false;
    pd.noTrinket2 = false;
    pd.noTrinket3 = false;
    pd.noTrinket4 = false;
    // Amount of trinkets the player sold
    pd.soldTrinket1 = 0;
    pd.soldTrinket2 = 0;
    pd.soldTrinket3 = 0;
    pd.soldTrinket4 = 0;
    
    // Amount of Simple Keys the player has
    pd.simpleKeys = 0;
    // Amount of Rancid Eggs the player has
    pd.rancidEggs = 0;
    
    // Amount of essence the player has
    pd.dreamOrbs = 0;
    // Amount of essence the player has already spent
    pd.dreamOrbsSpent = 0;
    // Ghost coins? doesn't appear to be used anywhere
    pd.ghostCoins = 0;
    // Amount of Pale Ore the player has
    pd.ore = 0;

Abilities & Items
^^^^^^^^^^^^^^^^^

Below are Ability & Item related members and their respective default values.

.. code-block:: c#
    
    // Mothwing Cloak
    pd.hasDash = false;
    // Mantis Claw
    pd.hasWallJump = false;
    // Crystal Heart
    pd.hasSuperDash = false;
    // Shade Cloak
    pd.hasShadowDash = false;
    // Isma's Tear
    pd.hasAcidArmour = false;
    // Monarch Wings
    pd.hasDoubleJump = false;
    // Lumafly Lantern
    pd.hasLantern = false;
    // Tram Pass
    pd.hasTramPass = false;
    // Quill
    pd.hasQuill = false;
    // Map
    pd.hasMap = false;
    // City Crest
    pd.hasCityKey = false;
    // Shopkeeper's Key
    pd.hasSlykey = false;
    // Wether the player gave sly his key
    pd.gaveSlykey = false;
    // Elegant Key
    pd.hasWhiteKey = false;
    // Wether the player used the Elegant Key
    pd.usedWhiteKey = false;
    // Unused key to Menderbug's house
    pd.hasMenderKey = false;
    // Unused key to the Waterways
    pd.hasWaterwaysKey = false;
    // Unused key to a spa
    pd.hasSpaKey = false;
    // Love Key
    pd.hasLoveKey = false;
    // King's Brand
    pd.hasKingsBrand = false;
    // Delicate Flower
    pd.hasXunFlower = false;
    // Wether the Delicate Flower is broken
    pd.xunFlowerBroken = false;
    // How many times the Delicate Flower has been broken
    pd.xunFlowerBrokeTimes = 0;
    // Wether the Delicate Flower was delivered
    pd.xunFlowerGiven = false;
    // Godtuner
    pd.hasGodfinder = false;

    // Wether the player has any spell
    pd.hasSpell = false;
    // 1: Vengeful Spirit; 2: Shade Soul
    pd.fireballLevel = 0;
    // 1: Desolate Dive; 2: Descending Dark
    pd.quakeLevel = 0;
    // 1: Howling Wraiths; 2: Abyss Shriek
    pd.screamLevel = 0;
    // Wether the player has any nail art
    pd.hasNailArt = false;
    // Cyclone Slash
    pd.hasCyclone = false;
    // Dash Slash
    pd.hasDashSlash = false;
    // Great Slash
    pd.hasUpwardSlash = false;
    // Wether the player has all nail arts
    pd.hasAllNailArts = false;
    // Dream Nail
    pd.hasDreamNail = false;
    // Dreamgate
    pd.hasDreamGate = false;
    // Awoken Dream Nail
    pd.dreamNailUpgraded = false;

Charms
^^^^^^

Below are Charm related members and their respective default values.

.. code-block:: c#
    
    // How many charm notches the player has
    pd.charmSlots = 3;
    // How many charm notches are used
    pd.charmSlotsFilled = 0;
    // Wether the player has a charm
    pd.hasCharm = false;
    // List of equipped charms
    pd.equippedCharms = new List<int>();
    // Wether the player had the message about charms
    pd.charmBenchMsg = false;
    // Amount of charms the player has
    pd.charmsOwned = 0;
    // Wether the player can overcharm
    pd.canOvercharm = false;
    // Wether the player is overcharmed
    pd.overcharmed = false;
    
    // Continues from charm 1 all the way to 40
    // Wether the player owns the charm
    pd.gotCharm_1 = false;
    // Wether the player has the charm equipped
    pd.equippedCharm_1 = false;
    // Amount of notches the charm costs (min 0 - max 6)
    pd.charmCost_1 = 1;
    // Wether the charm is freshly obtained
    pd.newCharm_1 = true;
    
    // Wether the health charm is unbreakable
    pd.fragileHealth_unbreakable = false;
    // Wether the greed charm is unbreakable
    pd.fragileGreed_unbreakable = false;
    // Wether the strength charm is unbreakable
    pd.fragileStrength_unbreakable = false;
    // State of the Kingsoul / Void Heart charm (min 0 - max 4)
    pd.royalCharmState = 0;
    // State of the Grimmchild / Carefree Melody charm (min 1 - max 5)
	pd.grimmChildLevel = 1;

Hunter's Journal
^^^^^^^^^^^^^^^^

Below are Journal related members and their respective default values.

.. code-block:: c#
    
    // Wether the player has the Hunter's Journal
    pd.hasJournal = false;
    // Last entry looked at
    pd.lastJournalItem = 0;
    // Wether the player has seen the journal message
    pd.seenJournalMsg = false;
    // Wether the player has seen the hunter message
    pd.seenHunterMsg = false;
    // Wether the player has a full journal
    pd.fillJournal = false;
    // Amount of completed entries
    pd.journalEntriesCompleted = 0;
    // Idk if it is used
    pd.journalNotesCompleted = 0;
    // Amount of total entries
    pd.journalEntriesTotal = 0;

    // Wether you unlocked the journal entry of an enemy
    pd.killedDummy = false;
    // Amount of enemies left to kill before unlocking the entry
    pd.killsDummy = 0;
    // Wether the entry is freshly unlocked
    pd.newDataDummy = false;

Misc
^^^^

Below are other members and their respective default values.

.. code-block:: c#

    // Maybe unused
    pd.currentArea = 0;
    // Visited scenes
    pd.scenesVisited = new List<string>();
    // Mapped scenes
    pd.scenesMapped = new List<string>();
    // Sadly appears to be unused
    pd.playerStory = new List<string>();

NPC Dialogue
^^^^^^^^^^^^

I chose to not include any of the flags used for dialogue of NPCs, as those would unnecessarily bloat this document.
