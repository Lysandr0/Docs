# Road map

This page provides a short and likely not entirely accurate selection of topics that will be in focus sooner or later. All-time topics are not added here, unless they are a major topic of a planned release or milestone, namely reducing false positives and (minor) fixes in general. From "Planned" on it gets shifty.

For a more detailed explanation or discussion of future design issues see the Design (TODO: link) page.

# Current focus
* 3.16.1-SNAPSHOT
    * An alpha/beta release. (Main purpose: get feedback on how internal changes work out.)
    * Finish current iteration.
        * Cleanup / fixes.
    * Should have.
        * hacc needs more special cases or invalidates special cases too quickly.
            * Fly-no-fly, air-water/ice, x-y ...
         * Simple alteration of data expiration stages.
            * Remove all non-essential cached data and configuration (keep player data). Configurable timing.
            * Fully remove data. Configurable timing.
            * (Configurable: Clear violation history - later only means to reset counters, in case PlayerData is still present. I.e. rename configuration option.)
            * (Configurable: Clear execution history. Typically this should be more or less automatic.)
            * (Change configuration accordingly. Time to clear reversible/irrelevant non-essential, time to clear irreversible but non-essential, time to clear essential - options for offline data.)
    * Nice to have.
        * Continue penalty implementation: configurable penalties.
            * Configuration. _penalties.penaltyid1: 30%cancel,70%penaltyid123_,150%action:cmd:kropotnikalive:0:0
            * Factories (similar implementation to actions).
            * Implement (immediate execution) support for all fight checks for starters.
            * (No stored penalties yet. Later stored penalties allow adding penalties to other checks, and possibly have a duration of validity, such as times, ticks, time.)
        * Down-to-ground set-back policy. (Evaluate and implement if simple enough to do - think of using this, in case the survivalfly set-back is gone, or even as a permanent option, provided it works at all.)
        * Listener registration overhaul: adapt to current implementation.
            * Consider to add a self-register method to the MiniListener interface (or another one), to reduce type issues.
            * Set priorities and tags everywhere in a consistent way.
            * Prefer to use MiniListener implementations everywhere.

* 3.16.2-SNAPSHOT
    * An alpha/beta release.
    * Fixes / check
        * Consistency: login/logout/disable/shutdown... dead? / set back queued? / ...
            * Set back, other queued actions.
        * Test totem of un-dings.
        * NoFall: Fix crop trampling (more complex / interesting than it might look like).
            * Consider a PacketMapper thing, relating Packets to Bukkit events.
                * At least Pos/Look vs. PlayerMoveEvent.
                * Use information to track cheats better (e.g. checking what the client sends for on-ground, or how many 'micro moves' have been used, possibly analyze such further to detect cheats, fix NoFall).
                * (Performance-wise: Consumed and other contextually irrelevant packets needn't be examined over and over, as might happen with the current FlyingQueue implementation.)
            * Spin-off: Relate interact/other packets to Position/Look updates directly, in order to have a fast lookup for packet inversion issues (such as interact being processed before move).
    * Put violation and execution histories into PlayerData.
    * Invalidation of stored data (registered).
        * Invalidate offline, world change, after so and so seconds unused, never.
        * Default invalidation stage (essential, non-essential, directly remove) -> auto . I.e. delete at which stage and/or transfer to offline data or not. 
        * Actually add IOfflinePlayerData -> contain essentials, get rid of PlayerMap.
        * Call invalidation after creating offline data.
    * Registry log file.
        * In-depth registry information (sorting order of listeners and other).
        * Remove non-essential info from 'ncp version' and from the ordinary log file.
        * Aiming at splitting things ~ server.log, player/violations log (current log file) and registry.log.
        * For the REGISTRY stream only warnings and worse should make it to the INIT log, perhaps only with extended status logging, registry information could make it into the ordinary log file.
        * Might need changes/additions for organizing log files, similar to nocheatplus.log, possibly extended to always archive by data +- switch all files with one of them exceeding some size.


# Scheduled
* 3.16.X-SNAPSHOT
    * An alpha/beta release.
    * New implementation of exemption: advanced features and API.
        * Invalidation conditions (tick based, offline, world, certain event + priority level).
        * Generic event wrapping and standard hooks (e.g. invalidation on certain event monitor).
        * Advanced handlers (clearNoFallData)?
    * Integrate cncp into NCP (first the generic functionality, possibly all by reflection or by direct inclusion of the cncp jar).
    * Small scale fixes / additions:
        * Autofish - concerning the bot-aspect not much with lasting effect can be done, other than would rather fit into an anti-farming plugin with heat-map based spawn/loot reduction. Anti speed fishing doesn't appear to be possible due to things not being accessible (except for adding more or less static penalties for fast-ish recasting).
* 3.17.0-RC
    * RC release for 3.16.x-SNAPSHOT: Major impact on internals (event registry) and API breakage.
* 3.17.1
    * Block breaking overrides completion: Attempt to complete configuration and covered area - complement with better stats and commands to use stats of a player (for certain blocks) as a configured workaround directly.
    * Completion of other API, such as adding API and commands to enable/disable checks per world per check type, per check debug flags. (Might be in any other release).
    * (More room for fixes, possibly small additions/alterations with fight checks.)
Topics that will be tackled soon, no guarantee on order.
* 3.17.X
    * Block shape overhaul. Vastly breaking change, concerning many (internal) block access methods.
    * Should move most block stuff to non-static. Some functionality might stay static (e.g. the on ground check).
    * (This may be postponed, if MC 1.13 is too easy to adapt to.)
* Vehicle envelope: Account for boat speed and acceleration: water/ground/ice (1.12).
* Fight: Detect mobs crammed into narrow space (possibly similar) adapt or skip certain checks.
* Force chunk load: Change to prevent moving within unloaded chunks, adapt data lazily where possible. Use a scheduler-like thing to still load chunks to ensure more smooth operation in average.
* NDT-skip-fly (bow/velocity-optimized).
* Boat placement (1.12 on ground, before on water): deny if ... at least if colliding with other boats.
* Attempt to fix false positives:
    * SurvivalFly: sprint+jump on flat ground (low speed up, bunny condition loss makes use of buffer).
    * SurvivalFly: below micro move threshold issues (0.0625):
        * < 0.025 above ground after respawn (possibly other cases, like stepping to roughly this amount onto layered snow).
    * SurvivalFly: Move onto block level but not on ground (likely an h-collision with step up ~ layered snow, speed + 0.5 shoes).
    * Stuck in hoppers?
    * Vehicles: piston + slime block push up.
    * Horizontal pushing of pistons.
* Decision for next topic between:
    * Fight checks (multiple iterations pending).
    * Data storage overhaul (multiple iterations pending).
    * set back policy (a) suggest set back (b) new policy: force fall.
* Improve fastbreak to catch bulk breaking of fast-breaking blocks faster.
* Evaluate: hacc with another slightly longer term tracking with extra limit - might allow a lower limit.
* Evaluate: Get rid of horizontal buffer (would yield a lot of reports).
* CreativeFly models, levitation: (Deny) descend speed concept + demand ascend. Make behavior fully dependent on the model (no elytra checking within the check, use some kind of special flag to model ~ glideStyleNoOne idk). Possibly allow glide-style ascend even if deny ascend is set (or ascend speed 0).
* Fight checks, penalty actions: implement more penalty actions, allow use in fight checks.
* Check data access: Implement (more) fine grained data removal for the remaining check types.
* Fight checks, loop checks design.
    * Implement generic loop methods, not depending on certain checks.
    * Have multiple rounds with differing checks (1. fastest checks + determine latency window 2.-X. Further checks to refine window Last: checks that just use the latency window + best guess).
    * Possibly  redo LocationTrace + pvp vs. pve config for loop checks.
* Moving checks: set back policy refinements.
    * suggestSetBack instead of setting it within LOWEST event priority. Really set on MONITOR, only if not cancelled.
    * Unified handling of specialties with set back locations for all moving checks (not within survivalfly).
    * One spot to decide how to alter scheduled set back locations (voidtovoid, forcefall, downtoground).
    * Actually implement at least one new set back policy (forcefall or downtoground).
    * (Let checks and config select which alterations are allowed.)
* Fight checks / latency:
    * Refine / implement-at-all: Make more use of a latency window - cross check with other checks and data sources (!).
    * Have a sequence of past latency estimates+windows and compare that of the attacker to the LocationTrace (latency _at that time_).
    * Update the LocationTrace on MONITOR (ensure), include the latest possible time stamp.
* Fight checks: Refine and implement new.
    * Refine loop checks (if not already done), to make full use of capabilities of the loop.
    * Implement new (planned) checks, like targeting/difficulty.
    * Test + sketch out further directions (no joke :p).
* SurvivalFly: sprint+jump and special case refinement. Possibly recode with new style workaround class use.
* Further examine role of UNKNOWN (vanilla) server teleport. Consider to always cancel them if setting into blocks, and cancel the teleport and _schedule_ a set-back for on-tick execution (+ config). Could touch questions with passable/phase, if those questions exist with the default configuration at all.
* Evaluate: command driven state machines (feed/trigger via actions, machines and further data queries via config).
* Evaluate: model configs (reference other configurations to override selected areas of the configuration, without altering the configurations, just apply model with id 'abc' from 'modelconfigs/xyz.yml').

## Planned
Topics that likely will follow up, no guarantee on order, might slip further away.
* Dynamic check types.
* Consider first steps into CheckPipelineRegistry.
    * I.e. have (non-Bukkit) custom pipeline events where the checks run inside. 
    * This also allows for introducing abstraction, so the pipeline object provides access to Bukkit internals.
    * The pipeline objects may stay active during full event handling until monitor (inclusive).
    * Easy add-in checks, possibly easy API/hooking points for exemption (wrapping lowest-monitor is safest, but there might be cases where pinpointing around NCP listeners with no effect on other plugins is demanded).
    * The pipeline registry also directs towards auto registering (and unregistering), provided all related listeners are inside somehow.
* Vehicle checks refinements: passable? Which ones to check + configure? Actual speed limiting. Pistons / block changes, at least simplistic.
* Interact ray-tracing: Use passable ray-tracing, possibly make it ray-tracing per block.
* Packet inversion problem: interact before look.
* Refine onground checks (spider vs. stuck with bounding box).
* Change isOnGround to a more flexible method (allow for distanceToGround), or provide an extra method.
* Block compatibility config and defaults: add named sets of blocks to set the same flags for many, with activation flag.
* Better horizontal velocity handling.
* More packet level checks (tab-complete, nasty data, strip content from books, items).
* Checks against unwanted content exposure (prevent sending content of books, item names, other).
* Detect "more inventory".
* Detect sending too few packets.
* More long term event frequency tracking.
* Single conditional action support (might also add generic access to violation levels via those).

## Planned (at some distance)
Pretty hard targets, but too far off the schedule to give any ETA.
* Add check/notification for players not using any velocity (mostly sf), at least start to log it for testing. [A simple first thing is in debug logs, not a check.]
* _"Full" UUID compatibility_. Better handling of data with UUIDs, API. Cache policy for name<->uuid mappings.
* Refine logging options in general (rolling files, manual entries, backups).
* Better mod support for arbitrary blocks and tools. Includes cleanup of block-properties-handling.
* Ingame configuration management (convenience, comparison, suggestions, hot-fixes).
* Velocity handling: Switch to 3d-entries with "exact" per-axis/direction counting.
* Per player fake blocks (via per player block cache or via BlockChangeTracker).
* Checks for creative mode exploits (enchantments, clickable text). [Specialized plugin(s) exist, postponed.]
* Moving/morepackets: Further invalidate past intervals to be more precise. Maybe include latency estimates.
* A log viewer with specialized features. E.g. confine view to one player + name/uuid mentioned. Interpret moving debug logs and find violations and patterns or custom defined sequences. Process large logs in a more or less smart way.
* Refine debug logging: Allow per-player or group files. Consider triggers to auto-start debug logging. Consider hooking into the debug stream in-game (!), possibly with reduced data. Might add a binary format, replay? Post-violation debug log - keep in memory and log on trigger/demand.
* Event/pattern engine: Allow pattern detection and/or automata-based configurable meta checks.
* Full core-recode. API-independent core + on-the-fly adding and removal of checks and other.
