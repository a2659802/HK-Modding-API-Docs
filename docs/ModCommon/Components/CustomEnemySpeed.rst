.. role:: raw-html-m2r(raw)
   :format: html

CustomEnemySpeed
================

Class in ModCommon / Inherits from MonoBehavior / Implemented in Components/CustomEnemySpeed.cs

Description
^^^^^^^^^^^

A component which, when attached to a Hollow Knight enemy object, can make this enemy infinite, and increase in difficulty over time.

Properties
""""""""""

.. list-table::
   :header-rows: 1

   * - Property
     - Default state
     - Description
   * - active
     - false
     - Determines if the component is running. Setting this true with SetActive will cause this component to track damage dealt on the enemy. It will not modify the enemy's speed.
   * - speedModActive
     - false
     - Determines if the component is applying its speed modifications to the enemy it is attached to. Setting this to true through StartSpeedMod() will apply all speed modifications needed. It will also automatically set active to true with will cause the component to start tracking damage dealt.
   * - cachedAnimator
     - gameObject GetComponent
     - Determines the animator which contains the animations which will be affected by this component. If the animator used to animate the enemy isn't on the gameobject this component is attached to (for example, if it's attached to a child game object instead), you can set this property before adding any animations to apply it to that tk2dSpriteAnimator instead.
   * - cachedHealthManager
     - gameObject GetComponent
     - Determines the HealthManager which contains the health for this enemy. If this HealthManager used to determine this enemy's health isn't on the gameobject this component is attached to (for example, if it's attached to a parent game object instead), you can set this property before setting this component active to monitor that HealthManager instead.
   * - damageDone
     - 0
     - Can be read to get the amount of damage the character has done to this enemy. Is automatically set to the enemy's maximum hp minus their current HP.
   * - danceSpeed
     - 2.0
     - Determines, using a weighted averaging system, the amount by which the enemy's speed should be modified. The default value of 2.0 will use the numbers provided in AnimationData and WaitData verbatim. 1.0 will use the original vanilla values. Details on this behavior are discussed below.


AnimationData
"""""""""""""

WaitData is a simple structure which is used to specify information about an enemy's animation state. It's in the following format:

.. list-table::
   :header-rows: 1

   * - Property
     - Description
   * - AnimationName
     - A string containing the name of the tk2dSpriteAnimationClip that you are interested in modifying. CustomEnemySpeed will attempt to load this clip from the cachedAnimator (which is usually the tk2dSpriteAnimator on the gameobject, unless you've explicitly set it otherwise).
   * - AnimationSpeedFactor
     - This is the speed factor of the Animation. This means that the animation's framerate will be **multiplied** by this number. Thus a value of 3.0 will make this enemy's animation run three times as quickly.
   * - DefaultAnimationSpeed
     - This value represents the normal framerate of the animation. It is used internally by CustomEnemySpeed, and can be read once the wait is loaded. **Do not set this value directly when creating an AnimationData to be added to CustomEnemySpeed, it will be ignored and you are wasting your time.**


Example:

.. code-block:: csharp

               AnimationData a = new CustomEnemySpeed.AnimationData()
               {
                   // You can find the animation name by dumping the gameobject with
                   // PrintSceneHierarchyTree(gameObject, "file.txt");
                   // and examining the FSM it dumped for Tk2dPlayAnimation or similar.
                   // These will contain a string called "clipName" which details the animation that
                   // will be played.
                   AnimationName = "Cast Antic",
                   // This will make the particular animation run at 0.7 times normal speed.
                   // Be careful with modifying animations. It's possible that changing their
                   // speed will have weird consequences. For example- slowing down grimm's
                   // upward slash animation causes him to travel farther along the floor before
                   // slashing upwards. To fix this his velocity on this state must also be modified.
                   // This is currently beyond the scope of CustomEnemySpeed V1.
                   AnimationSpeedFactor = 0.7f
               };

WaitData
""""""""

WaitData is a simple structure which is used to specify information about an enemy's wait state. It's in the following format:

.. list-table::
   :header-rows: 1

   * - Property
     - Description
   * - FSMName
     - A string containing the name of the PlayMakerFSM containing the FSMState with the wait you are interested in changing. Often "Control" for newer and better designed enemies, although some have multiple FSMs each with their own sets of waits.
   * - FSMStateName
     - A string containing the name of the FSMState with the wait you are interested in changing. It's highly recommended one looks at an FSM dump (either the scene dump, the modcommon gameobject dump, or the one generated from the FSM mod) to figure out the names of the states they are interested in modifying.
   * - WaitTimeInverseFactor
     - This is the speed factor of the Wait state. That means that the Wait's time will be **divided**\  by this number. Thus a value of 3.0 will make the enemy's wait proceed thrice as quickly, making this enemy's state three times as fast. This is done for two reasons: Chiefly this behavior is easier to imagine than the opposite (a value of 0.3333 making the enemy 3 times normal speed). Secondly, to match the way AnimationSpeedFactor functions in AnimationData.
   * - DefaultWaitTime
     - This value is used internally by CustomEnemySpeed, and can be read once the wait is loaded. **Do not set this value directly when creating a WaitData to be added to CustomEnemySpeed, it will be ignored and you are wasting your time.**


Example:

.. code-block:: csharp

               WaitData w = new CustomEnemySpeed.WaitData()
               {
                   // Like most modern enemies, the FSM is called control
                   FSMName = "Control",
                   // You can find the state name by dumping the gameobject with
                   // PrintSceneHierarchyTree(gameObject, "file.txt");
                   // and examining the FSM it dumped for Waits.
                   // Or by using the FSM mod.
                   FSMStateName = "Slash Antic",
                   // This makes this particular wait twice as fast by default.
                   WaitTimeInverseFactor = 2.0f
               };

Public Methods
""""""""""""""

.. list-table::
   :header-rows: 1

   * - Method
     - Description
   * - void SetHealthManager (HealthManager hm)
     - Sets the tracked HealthManager to a custom HM. Call this only if the HealthManager you are interested in tracking is not on the gameobject that this component is attached to. This set is done indirectly to update maximum health to this new healthmanager.
   * - void SetEnemyMaxHealth (int health)
     - Sets the maximum health of the enemy. If you call this while the player has done damage to the enemy it will set their new health to maximum health - damageDone. If this value is less than or equal to zero it will kill the enemy.
   * - void OverrideDamageDone (int dmg)
     - Sets the damageDone value to a different integer, then modifies the enemy health manager to reflect this amount of damage having been dealt. If damageDone exceeds the maximum health of the enemy, they will be killed.
   * - void AddAnimationData / void AddWaitData
     - Adds a AnimationData or WaitData struct to the list of animations/waits with speeds that should be modified. If speedModActive is true, it will also modify the speed of this animation/wait immediately. Otherwise it will simply store it until this is enabled.
   * - bool RemoveAnimationData/ bool RemoveWaitData
     - Removes an AnimationData or WaitData from the list of animations/waits with speeds that should be modified. If speedModActive is true, it will also restore the speed of this animation/wait to the original value. These functions return true if they successfully found and removed the data, and false otherwise.
   * - void StartSpeedMod
     - This will modify the speeds of all animations and waits which have been added using AddAnimationData or AddWaitData. It also modifies any future added animations or waits automatically. Finally, it sets the CustomEnemySpeed active causing it to start tracking damage dealt.
   * - void RestoreOriginalSpeed
     - This will restore all the default animation and wait speeds. It also stops any future added animations or waits from modifying any speeds. It does not set CustomEnemySpeed to inactive, so it will continue tracking damage dealt.
   * - void updateDanceSpeed[sic] (double newDanceSpeed)
     - This will modify the danceSpeed to a new value, which, in turn, will affect the speed at which every animation and wait should be run at. See below.


danceSpeed and actual animation/wait speeds
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Consider this: You're making a nightmare version of the mega moss charger fight. So you take some of the charger's animations and waits and speed them up to make this fight more enjoyable and difficult. To you, these speeds are perfect, and the people who playtest your mod agree that all of the animations and waits are at the same difficulty level, that is not one moss charger attack feels too fast or slow in comparison to the others. But, they do complain about the difficulty as a whole, some find it too hard and others too easy. What if there was an easy way you could provide difficulty levels to your new god-fight?  Or maybe you want to have it get harder as the player does more damage to it. In either case, danceSpeed is your friend, and one of the core benefits of using CustomEnemySpeed.

danceSpeed is designed as a corse way for balancing enemy movement speeds. It affects all animations and waits which are added to the CustomEnemySpeed class. It's designed to maintain the difficulty between attacks (if no one attack is harder or easier to dodge than others at dance speed 1.5, no one attack will be harder or easier to dodge at 2.5 or any other number). What's more, it allows for dynamic readjustment of enemy speeds. It can be explained as such:

danceSpeed is the number of times your speed modifiers are applied to the enemy, plus one. A danceSpeed of 1.0 applies your modifiers 0 times, leaving you with the original vanilla speeds. 2.0, exactly once, 3.0 twice, so making an animation 1.75 times as fast will, with a danceSpeed of 3.0, make it (1.75 * 2 or ) 2.5 times as fast.

`this graph <https://www.desmos.com/calculator/mznwkgvyoa>`_ shows the danceSpeed variable on the x axis, and the speed of a given wait or animation (relative to vanilla hollow knight) on the y axis. a is the speed factor of this animation. Drag a around and see how the animation speed changes at different dance speeds.
