General Instructions
================
--------------------------------

1. Clone this repo onto your local machine.
2. Open the "vs_AI" map (or the "vs_Human" map) via the StarCraft II Editor
3. Edit the scripts outside of the Editor itself using [Atom](https://atom.io/) or [Sublime](https://www.sublimetext.com/)
4. To add additional functionality, edit the existing scripts within the "vs_AI/Scripts" or "vs_Human/Scripts" folder respectively or create new ones and register any triggers you create as outlined in the Conventions section!

    > The Conventions listed below are highly recommended to implement your behaviors via custom scripts. These are adhered to for the purposes of the AnyBot Starter Kit.
    >
	> **Please do not alter the triggers emplaced in the maps themselves, as this is quite likely to break scripted functionality.**

Purpose of AnyBot Starter Kit
============
-----------------------------

The AnyBot Starter Kit is intended to be a starting point for those who wish to create an Artificial Intelligence to play StarCraft II using GalaxyScript rather than simply editing the rules of the default StarCraft II AI. This script was developed entirely from scratch by Patrick Bradshaw, Timothy So, Siddhi Gupta, Wayne Chi, and Chuqi Cha through the University of Southern California's Networked Game AI class.

The included map includes all gameplay logic created from the use of only triggers, native GalaxyScript functions, and our own designed logic, which can be improved upon and expanded via future projects! While the project began as an attempt to bring AI development into StarCraft II and build an AI that can react to the player and meet their skill level at any point, it has become dual purposed as a bridge into the world of scripting for StarCraft II.

If you choose to use our Starter Kit and have improvements you would like to add onto the kit or would like us to link to your project from this page for any future developers to see where this can be taken, please let us know by posting an issue on this page and we are happy to work with you to incorporate your changes into our source so that it continues to grow and evolve. We will also periodically be updating and improving this code base ourselves, although it will not be our primary task in the future - so any help is greatly appreciated! In addition, we plan to add an additional section to both our website and our readme to showcase the projects that build upon the foundations we have laid!

Use of Readme
============
------------------------------

* Use Table of Contents to navigate through the sections _(Bitbucket feature only. :/)_
* Review the edit log for recent changes
* Questions regarding the content of the Readme should be posted as issues on the [GitHub](https://github.com/pbradshawusc/AnyBot) page.
	* Question resolutions will be added to the Readme and noted with an Edit Log

Links
====
----------

* [Project Website](http://www-scf.usc.edu/~chuqicha/524website/524progress.html)
* [SC2Mapster Wiki](http://www.sc2mapster.com/wiki/galaxy/script/): A great source for scripting questions. The forum has a healthy amount of tutorials and questions, although they are more geared toward scripting alternative game modes and scripted stories than AI.
* [List of Native Galaxy Script Functions](https://github.com/grum/sc2-dev/blob/master/Mods/Core.SC2Mod/Base.SC2Data/TriggerLibs/natives.galaxy): This link can be a lifesaver - This guide does not contain a listing of the native types in GalaxyScript or the native functions. The linked file contains a listing of all data types and functions natively supported by GalaxyScript, which are your most powerful tools for interacting with the game. Many of these functions will be added to the **Natives** section of this Readme as we build more colloquial definitions.
* [GitHub](https://github.com/pbradshawusc/AnyBot)

Table of Contents
==============
----------------------------------
[TOC]

AnyBot Conventions
================
----------------------------------------

## Trigger Editor

> The trigger editor is used only to create win/loss conditions and trigger our main script.
>
> **Behaviors should be added via GalaxyScript files rather than through the editor.**

The trigger editor is used to import our galaxy scripts into the main script itself. Apart from this, the trigger editor is only used to create win/loss conditions for the game, as well as remove the fog of war and initialize our main script through the use of `mapInitiated()`. All other trigger handling is done through the scripts themselves so that they can maintain conventions outlined below and effectively interface with the global data stored in the Blackboard.

> The trigger editor, however, is the best resource for exploring native functions.
>
> In addition, use this link to find even more native functions: [List of Native Functions](https://github.com/grum/sc2-dev/blob/master/Mods/Core.SC2Mod/Base.SC2Data/TriggerLibs/natives.galaxy "List of Native Functions")
>
> **Steps to find native functions:**
>
> 1. Create a new trigger via the Gear icon.
> 2. Add a new item for whatever function you are looking for:
> a. If looking for a function to fire a trigger, add a new event.
> b. If looking for a function to cause an action, add a new action.
> c. If looking for a function to return a variable, add a new variable.
> d. If looking for a function to return a state, add a new condition.
> 3.  Ensure that the trigger editor is not showing Raw Data.
> a. _Icon that looks like a DNA strand is not highlighted._
> 4. Double click the item that you created to bring up the box of available functions.
> a. Browse this list until you find a function that fits your needs.
> b. Clicking on a function in the list will bring up more details in the text field at the bottom of the window.
> 5. Double click on the function that you have selected.
> 6. In the toolbar, open the "Data" menu and select "View Script".
> 7. In the script pop-up, scroll to find the implementation of the trigger that you created.
> a. If you are looking for an event, instead look for the init function rather than the imp function.
> 8. Here you will find the function that is being called to create your desired behavior. Copy and paste this into your user-defined trigger within your script.
> 9. Lastly, ensure that the parameters being passed into the function are correct for your event.
> a. _Often times, the function that this script creates will use EventUnit() or other standards. This may not be correct for your desired behavior._

## General Process

> **Modified Blackboard**
>
> - Shared data stored in Blackboard.galaxy via globals
> - Triggers first post observed data into the Blackboard
> - Triggers then assign orders to the end of the current queue
> - **Limited** Arbiter for purchasing decisions only


The overall architecture is a modified version of the Blackboard problem solving strategy. In this strategy, each trigger that is called records the relevant information that it has gathered into the global Blackboard, which is available for use by all other triggers. This allows each trigger to work independently, while sharing the same shared data bank. However, unlike the traditional Blackboard strategy, there is no arbiter that choses what solution to go with at each given time. Rather, each trigger responds autonomously, and so it is up to the developers to ensure that these behaviors do not conflict. This is outlined further in the Conventions section of this Readme document. This is the largest downfall of this simplified implementation of the Blackboard strategy (used to circumvent processing limitations of GalaxyScript) as relevance of each solution is not accounted for by default. The one exception to this rule is for purchasing decisions, which are made in the _PurchasingTick_ function that acts as an arbiter only over purchasing decisions.

## Blackboard

> **Script to house ALL shared data (i.e. globals).**
>
> - Structs
> - Globals
> - **NO** Functions _Aside from initial set up of specific arrays_

Included in the Macro map is a file called `Blackboard.galaxy`. This file is intended solely to house all shared data for the AnyBot AI. This houses all structs and global variables used in all scripts. The intention of this consolidation is to enforce the Blackboard strategy outlined above, and to ensure that all behaviors work off of the same shared data rather than working with multiple copies of the same data. In addition, this will ensure consistency between data types used throughout the various scripts.

### Blackboard Structs

> Anthology of all structs used on the Blackboard.
>
> Format:
>
> - Struct Name - _Purpose_
>     - Member Variable - _Purpose_

* EnemyUnit - _Used to track all actual units owned by the enemy._
    * unit mUnit - _The actual unit base type that we wish to track. Many attributes can be gathered from this variable using GalaxyScript's native library._
	* int mRecentBase - _The most recent base that this unit has been spotted at. This should be taken as unreliable data (as in not guaranteed to be always correct)._
* AllyUnit - _Used to track all units owned by AnyBot._
    * unit mUnit - _The actual unit base type that we wish to track. Many attributes can be gathered from this variable using GalaxyScript's native library._
    * int mRecentBase - _The most recent base that this unit has been spotted at. This should be taken as unreliable data (as in not guaranteed to be always correct)._
    * <del>int mQueueCount</del> - **Deprecated**
* DamageUnit - _Used to track enemy units being attacked for purposes of focusing fire._
    * unit mUnit - _The actual unit base type that we wish to track. Many attributes can be gathered from this variable using GalaxyScript's native library._
    * fixed mDamage - _The amount of damage that has already been assigned to this unit._
* AllyUnitGroup - _Used to track all unit groups owned by AnyBot. These are where large group orders can be issued without iterating over all units._
    * string groupName - _The name used to identify this group._
    * unitgroup group - _The actual unitgroup base type that we wish to track. Many attributes can be gathered from this variable using GalaxyScript's native library._
    * <del>int expectedSize - _The number of units that this group should expect to have._</del> - **Deprecated**
* BuildOrderItem - _Used to represent items to be purchased as a part of the build order._
	* string unitName - _The unit that we wish to purchase._
	* string supply - _The amount of supply that we should wait to attain before making this purchase (used supply, not available supply)._

### Blackboard Globals

> Anthology of all globals used on the Blackboard.
>
> Format:
>
> - Section/Data Purpose
>     - Global Variable - _Purpose/Usage_

* Debug Variables
    * bool gDebugLog_Enabled - _When true, debug messages will be displayed in the log. Note that other debug log variables will have to be set to true for many of the messages to appear. More is outlined in the Debug section._
    * bool gDebugLog_MineralsEnabled - _When true, messages regarding mineral counts for AnyBot will be displayed._
	* bool gDebugLog_PurchasingEnabled - _When true, messages regarding purchasing activities for AnyBot will be displayed._
	* bool gDebugLog_DPSEnabled - _When true, messages regarding dps counts and calculations for AnyBot will be displayed._
	* bool gDebugLog_AttackEnabled - _When true, messages regarding attack decisions for AnyBot will be displayed._
	* bool gDebugLog_TravelEnabled - _When true, messages regarding travel activities for AnyBot will be displayed._
	* bool gDebugLog_ReaperEnabled - _When true, messages regarding reaper micros for AnyBot will be displayed._
	* bool gDebugLog_SkillCheckEnabled - _When true, messages regarding skill check failures for AnyBot will be displayed._
	* bool gDebug_FogOfWarOn - _When true, the game will be played with fog of war. When false, fog of war will be disabled._
	* bool gDebug_MaxGameSpeed - _When true, the game will start at maximum game speed. When false, the default game speed will be used._
* Unit Types
	* string[24] gUnitTypes_MyUnits - _This list contains all unit types (internal string names) for all Terran non-structural units._
	* string[30] gUnitTypes_MyStructures - _This list contains all unit types (internal string names) for all Terran structural units._
	* string[23] gUnitTypes_EnemyStructures - _This list contains all unit types (internal string names) for all Zerg structural units._
* General
	* int gGeneral_AnyBotTeamNumber - _This is the player number assumed by AnyBot._
	* int gGeneral_MaxBases - _This is the maximum number of supported bases. Currently: 4_
	* unitfilter gGeneral_DefaultFilter - _This is a unit filter used in some of the micros. It filters out any missiles as well as dead or hidden units._
* Purchasing
	* BuildOrderItem[] gAnyBotPurchasing_BuildOrder - _This is the list of items to be purchased in the build order. These are guaranteed purchases to be made sequentially, occasionally waiting for a specific supply count. This does not include incidental purchases._
	* int gAnyBotPurchasing_BuildOrderCurrentIndex - _The current index we are at within our build order._
	* int gAnyBotPurchasing_BuildOrderLength - _The length of our build order. When we reach this index we know that we have completed our build order._
	* bool gAnyBotPurchasing_BuildingAddOn - _Stores whether we are currently building an add on or not. This is used to prevent addon re-purchasing before a structure has landed to begin the construction._
* AnyBot Base Information
	* int[] gAnyBotBases_MineralsLeftAtBase - _The remaining minerals at a base._
	* int[] gAnyBotBases_SCVMinersAtBase - _The number of miners currently working at a base._
	* int[] gAnyBotBases_SCVHarvestersAtBase - _The number of harvesters currently working at a base._
	* int[] gAnyBotBases_RefineriesAtBase - _The number of refineries at a base._
	* bool[] gAnyBotBases_ExpansionAtBase - _Whether an expansion exists at the given base or not._
	* bool[] gAnyBotBases_UnderAttack - _Whether the given base is under attack or not._
	* int[] gAnyBotBases_ImportanceOfBase - _How many units are currently stationed within the base based on value. Currently not used, but slated for defensive purposes._
* AnyBot Worker Information
	* AllyUnit[] gAnyBotWorkers_MULEList - _Tracking all MULEs created by AnyBot._
	* int gAnyBotWorkers_MULECount - _The number of MULEs in our list._
	* AllyUnit[] gAnyBotWorkers_SCVList - _Tracking all SCVs created by AnyBot._
	* int gAnyBotWorkers_SCVCount - _The number of SCVs in our list._
* AnyBot Structure Information
	* int gAnyBotStructures_BarracksTechLabs - _The number of tech labs we have attached to barracks._
	* int gAnyBotStructures_FactoryTechLabs - _The number of tech labs we have attached to factories._
	* int gAnyBotStructures_StarportTechLabs - _The number of tech labs we have attached to starports._
	* int gAnyBotStructures_BarracksReactors - _The number of reactors we have attached to barracks._
	* int gAnyBotStructures_FactoryReactors - _The number of reactors we have attached to factories._
	* int gAnyBotStructures_StarportReactors - _The number of reactors we have attached to starports._
	* AllyUnit[] gAnyBotStructures_\#_UNITNAME_\#List/Count - _The list and count (respectively) of every type of Terran structural unit created by AnyBot._
* AnyBot Army Information
	* fixed gAnyBotArmy_GlobalDPS - _The currently estimated damage per second of AnyBot's entire army._
	* int gAnyBotArmy_CurrentAttackGroup - _The attack group currently being created by AnyBot. Currently supporting 1 & 2, but extensible to support more with appropriate additional Blackboard globals and Army script modifications._
	* AllyUnit[] gAnyBotArmy_AttackGroup#List - _Tracking all units of the attack group with the associated number._
	* int gAnyBotArmy_AttackGroup#Count - _The number of units in the associated attack group list._
	* fixed gAnyBotArmy_AttackGroup#DPS - _The estimated damage per second of the associated attack group._
	* fixed gAnyBotArmy_AttackGroup#EstimatedHealth - _The estimated health of the associated attack group._
	* int gAnyBotArmy_AttackGroup#Target - _The base being targeted by the associated attack group._
	* AllyUnit[] gAnyBotArmy_\#_UNITNAME_\#List/Count - _The list and count (respectively) of every type of Terran non-structural unit (excluding SCV and MULE) created by AnyBot._
	* AllyUnitGroup[] gAnyBotArmy_ReaperGroupList - _The list of reaper groups sent to harrass the enemy._
	* unitgroup gCurrentReaperPair - _The current pair of reapers harrassing the enemy._
	* int gAnyBotArmy_ReaperGroupCount - _The count of reaper groups in the group list._
	* int gAnyBotArmy_ReaperCurrentlyBeingCreated - _The index currently being created in the reaper group list._
* AnyBot Research Information
	* bool gAnyBotResearch_Started\#_RESEARCH NAME_\# - _Whether we have already started researching._
	* bool gAnyBotResearch_Have\#_RESEARCH NAME_\# - _Whether we actually have this research._
* Enemy Base Information
	* bool[] gEnemyBases_BaseExists - _Whether the enemy has a base at the specified location._
	* int[] gEnemyBases_WorkersAtBase - _The number of workers at each base._
	* fixed[] gEnemyBases_DPSAtBase - _The estimated damage per second of all attacking units at the base._
	* fixed[] gEnemyBases_EstimatedHealthAtBase - _The estimated total health of all units at the base._
	* fixed gEnemyBases_DroneModifier - _The amount of health and damage to account for from drones._
* Enemy Structures
	* EnemyUnit[] gEnemyStructures_UnitList - _Tracking all structural Zerg units made by the opponent._
	* int gEnemyStructures_UnitCount - _The number of units in the structural unit list._
	* EnemyUnit[] gEnemyArmy_ _UNITNAME_ - _Tracking each type of Zerg structural unit made by the opponent separately. Use with associated count variable._
* Enemy Army
	* fixed gEnemyArmy_GlobalDPS - _The estimation of the enemy army's entire damage per second._
	* fixed gEnemyArmy_GlobalEstimatedHealth - _The estimation of the enemy army's entire health._
	* EnemyUnit[] gEnemyArmy_UnitList - _Tracking all non-structural Zerg units made by the enemy player. Use with associated count variable._
	* DamageUnit[] gEnemyArmy_DamageUnitList - _Tracking all non-structural Zerg units made by the enemy player and being attacked by our units. Used for focusing fire._
* Scaling
	* fixed gSkill_EnemySkillLevel - _The enemy player's observed skill level from 0-1._

## Utils

> **Script housing widely used utility functions.**
>
> Use these whenever possible as opposed to implementing a new function/process.
>

Included in the Macro map is a file called `Utils.galaxy`. This file is used to compile common calls used within triggers or common trigger responses needed by several behaviors to build the shared memory in the Blackboard. Methods and triggers should only be placed in this file when they are useful to be called in multiple different types of behaviors or set up memory that is important for use in several behaviors. For set up functions or helper functions used in only one behavior, please keep those methods within that behavior's script rather than placing them in the Utils file.

### Utils Function List

> Format:
>
> #### Section
> * `FunctionName(Parameters)`
>     * `Parameter Name` = Description
>     * "Description:" _Actual Description_
>     * "Returns:" Return Type
>         * Explanation of returned value
>     * Notes:
>         * Note

#### Determine Unit Ownership/Type

* `UtilsIsUnitMyUnit(unit u)`
    * `Unit u` = The unit you wish to check
    * Description: _This function will check if the requested unit is an actual unit (as opposed to a structure) on AnyBot's team._
    * Returns: bool
	    * true = Unit u is on AnyBot's team AND is an actual unit (not a structure)
	    * false = Unit u is EITHER not on AnyBot's team OR a structure unit
* `UtilsIsUnitMyStructure(unit u)`
	* `Unit u` = The unit you wish to check
	* Description: _This function will check if the requested unit is a structure on AnyBot's team._
	* Returns: bool
		* true = Unit u is on AnyBot's team AND is a structure unit
		* false = Unit u is EITHER not on AnyBot's team OR an actual unit]
* `UtilsIsUnitEnemyStructure(unit u)`
	- `Unit u` = The unit you wish to check
	- Description: _This function will check if the requested unit is a structure that is not on AnyBot's team._
	- Returns: bool
		* true = Unit u is not on AnyBot's team AND is a structure unit
		* false = Unit u is EITHER on AnyBot's team OR an actual unit
* `UtilsIsUnitEnemyUnit(unit u)`
	- `Unit u` = The unit you wish to check
	- Description: _This function will check if the requested unit is a unit that is not on AnyBot's team._
	- Returns: bool
		* true = Unit u is not on AnyBot's team AND is an actual unit
		* false = Unit u is EITHER on AnyBot's team OR a structural unit
* `UtilsIsUnitOwnedByAnyBot(unit u)`
	- `Unit u` = The unit you wish to check
	- Description: _This function will check if the requested unit is owned by AnyBot._
	- Returns: bool
		* true = Unit u is on AnyBot's team
		* false = Unit u is not on AnyBot's team

#### Unit Information
* `UtilsClosestEnemyBaseForUnit(unit u)`
	- `Unit u` = The unit you wish to check
	- Description: _This function will tell you what the closest enemy base number is for the requested unit._
	- Returns: int
		* -1 = There is no closest base, often an error case.
		* 1-4 = The closest base number is the one specified (not index).
* `UtilsClosestAnyBotBaseForUnit(unit u)`
	- `Unit u` = The unit you wish to check
	- Description: _This function will tell you what the closest AnyBot base number is for the requested unit._
	- Returns: int
		* -1 = There is no closest base, often an error case.
		* 1-4 = The closest base number is the one specified (not index).
* `UtilsGetDPSForUnit(Unit u)`
	- `Unit u` = The unit you wish to check
	- Description: _This function will tell you what the estimated DPS is for the requested unit._
	- Returns: fixed
		- \# = The estimated damage per second from the requested unit.

#### Add/Delete Purchase Requests

* `UtilsAddToBuildOrder(string unitToAdd, int supply)`
	* `String item` = The type of item you wish to purchase.
	* `Int mineralPrice` = The number of minerals required to purchase this item.
	* `Int vespenePrice` = The amount of vespene gas required to purchase this item.
	* `Int priority` = The urgency of this purchase **(1-100 ONLY)**.
	* `String structure` = The type of structure (or unit) that can make this purchase.
	* Description: _This function will add a purchase request to the list of AnyBot's requested purchases. This is used to create build orders._
	* Returns: N/A

## Conventions

Below is a listing of conventions used by all scripts in order to ensure that they interact properly.

* General Methods
    * Method names should always begin with the name of the script first. _For example, the utils Is My Unit has the method name_ `UtilsIsUnitMyUnit` _rather than simply_ `IsUnitMyUnit`.
    * Structs should never be returned from a method. _This is due to GalaxyScript having no copy constructor, so I don't believe this is even syntactically valid._
* Triggers
    * All triggers should be registered in a `NAMERegisterTriggers()` method that is called within `Main.galaxy` in the method `RegisterTriggers()`.
    * Trigger callbacks should all start with the name of the script first. _For example, the purchasing Tick trigger has the method name_ `PurchasingTick` _rather than simply_ `Tick`.
* Globals
    * All globals should be declared in `Blackboard.galaxy`.
    * Globals should begin with the lower-case letter **g**. _For example, the variable to turn on or off debug chat is_ `gDebugLog_Enabled` _rather than_ `debugLogEnabled`.
    * Globals should be added only if existing global variables are not sufficient. This will help prevent memory clutter.
    * If data needs to be tracked about individual units or entities, the data should be added to the struct and all shifting functions should be updated prior to pushing any code.
* Utils
    * Utils are for often repeated general tasks, and these tasks only. Do not put script-specific tasks in this file.
    * On the other hand, if a task that you use in your script will likely be repeated elsewhere _(i.e. identifying a unit's ownership and type)_, then add this method to the Utils script.
    * All methods in the Utils script should begin with the word *Utils* as outlined in the trigger callbacks and general methods section.
* Unit Orders
    * Many scripts order units based on many different triggers. Because of this, the potential for overwriting tasks assigned in other triggers can cause issues with AnyBot's overall behavior.
    * Whenever possible, use `c_orderQueueAddToEnd`. This will allow the unit to execute any other tasks that have been assigned to it before executing the new task. _Note that when issuing orders to SCVs that are currently harvesting minerals,_ `c_orderQueueAddToEnd` _will not be executed until the minerals have been exhausted. In this special case, use_ `c_orderQueueAddToFront` _by default instead, even though this may cause issues with other behaviors._
    * If the task must be completed right away, use `c_orderQueueAddToFront`. This will cause the unit to immediately execute your task, and then return to whatever it was doing before.
    * Only in extreme circumstances should `c_orderQueueReplace` be used. When this is used, comment this usage with the justification for using Replace over Add To Front.


Edit Log
=======
-----------------
## Update to v2.0
> Date: 4/20/16
>
> **Sections:**
>
> * General Instructions
> * NEW - Purpose of AnyBot Starter Kit
> * Use of Readme
> * Links
> * AnyBot Conventions
> 	* General Process
> 	* Blackboard
>		* Blackboard Structs
>		* Blackboard Globals
> 	* Utils
>		* REMOVED - Utils Trigger List
> 		* Utils Function List
>	* Conventions
>

-----------------
## Complete Creation
> Date: 2/2/16
>
> **Sections:**
>
> * General Instructions
> * Use of Readme
> * Links
> * AnyBot Conventions
>     * Trigger Editor
>     * General Process
>     * Blackboard
>         * Blackboard Structs
>         * Blackboard Globals
>     * Utils
>         * Utils Trigger List
>         * Utils Function List
>             * Section
>             * Determine Unit Ownership/Type
>             * Add/Remove Units from Tracking
>             * Add/Delete Purchase Requests
>     * Conventions
>
