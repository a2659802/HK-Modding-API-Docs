Logging
=======

It's likely you'll want to debug your mod to ensure hooks are being triggered when they should, and that they are triggering on the correct data.
Conveniently enough the API has logging built in.

How to Log
^^^^^^^^^^

Inside your main class, the one that extends :code:`Mod` you can simply use one of the following

.. code-block:: c#

    public void Initialize(){
        Log("Something to be logged as long as logging is enabled");
        LogDebug("Something to be logged as long as debug logs are enabled");
        LogError("Something to be logged as long as error logs are enabled");
        LogFine("Something to be logged as long as dev logs are enabled");
        LogWarn("Something to be logged as long as warning logs are enabled");
    }

Outside of your main class you'll have to use :code:`Modding.Logger.Log` or any other logging level functions.

Where do my logs go?
^^^^^^^^^^^^^^^^^^^^

Your logs will go into a file called :code:`ModLog.txt` in your save folder.
Your save folder will be located at these locations based on operating system:

Windows :code:`%appdata%/../locallow/team cherry/hollow knight/`

Mac     :code:`~/Library/Application Support/unity.Team Cherry.Hollow Knight/`

Logger Settings
^^^^^^^^^^^^^^^

Previously in the How to Log section we mentioned logging being enabled, so below will be instructions on how to change settings related to logging.

In our save folder there will be a file called :code:`ModdingApi.GlobalSettings.json` open this file in any editor and look for this entry (It may require manual addition), larger values are finer logging?

.. code-block:: js

    "IntValues": {
        "keys": [
            "LoggingLevel"
        ],
        "values": [
            2
        ]
    },

Additionally we may want our logs to show up in game, we can enable the in game console by setting this value:

.. code-block:: js

    "BoolValues": {
        "keys": [
            "ShowDebugLogInGame"
        ],
        "values": [
            true
        ]
    },

This console can be toggled once this setting is set to true by pressing f10.