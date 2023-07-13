# Stand LUA API -- Full Tutorial

## This is a work-in-progress.

#### Authored by vs.galactic/kryptcat | Co-Authored by stand.gg/Sapphire
#### Good luck, and godspeed.

---
<br></br>

# Section 1: Resources
This section contains useful resources to aid you in your scripting journey. At any point, feee free to come back to this section.

Also, please be sure to read through this list, as most of the answers to your questions will be found here.

[Native Documentation](https://nativedb.dotindustries.dev/natives) -- Contains the native functions from GTA V. These are the functions that govern the game, and we can use them via the menu. Almost every single basic feature can be achieved with a combination of these.

[Stand LUA Documentation](https://stand.gg/help/lua-api-documentation) -- Contains the functions necessary to add features to Stand. Everything from buttons to sliders to submenus, and even simpler versions of native functions can be found here.

[Pluto Documentation](https://pluto-lang.org/docs/Introduction) -- Stand uses a custom LUA fork made by a well-known member and staff member *well in that case*, and this contains that documentation. You do not need to add `.pluto` to your extension, as both `LUA` and `PLUTO` scripts get interpreted by Stand's engine as text, then run as `pluto` scripts. This means you can either write your script in LUA, PLUTO, or a combination of both.

[Data Dumps](https://github.com/DurtyFree/gta-v-data-dumps) -- This is a repository of well-known data dumper durtyfree on GitHub, and is extremely helpful for developing scripts.

[Decompiled Scripts](https://github.com/Primexz/GTAV-Decompiled-Scripts) -- These are scripts that govern GTA V, which have been decompiled for your convenience. Global variables and the way missions work are detailed in here, if you can read them. Keep in mind that this includes not just GTA Online scripts, but GTA V ones as well, meaning if you don't look where you need to, you'll be led on a wild goose chase.

[Hash Database](https://github.com/calamity-inc/gta-v-joaat-hash-db) -- An alternative (and possibly more comprehensible) version of the Data Dumps, which contains JOAAT (Jenkins-One-At-A-Time) hashes for GTA V, including pickups, weapons, and more.

[UnknownCheats GTA V Section](https://www.unknowncheats.me/forum/grand-theft-auto-v/) -- This houses more experienced developers that develop cheats and try to look for exploits in GTA V.

[Yimura's Offsets](https://github.com/Yimura/GTAV-Classes) -- In making his open-source GTA V menu YimMenu, Yimura has provided us with the offsets, which can be useful if you are screwing around with memory and offsets in your scripts.

Other Stand Scripts -- No link for this one, but just browse the repository in the Stand menu, or browse #lua-scripts in the discord server. You learn scripting by looking at others! (If you copy-and-paste, you will not learn anything. Learn by examining and changing or improving.)

#programming -- Discord channel for developers; like you! Please don't be afraid to ask questions here, but be sure to explore the entire documentation before asking, or you will look lazy to others. Try to be respectful, and people will most likely be respectful back!

----
<br/>

# Section 2: Natives --What?

Okay, you've read the section above, and you understand native functions. But... there are 4 versions in Stand!

By using `util.require_natives` at the top of your script, you are telling Stand to actually look for native functions in your script; if you don't use this, none of your script will work! (Well, except for the menu stuff, but no features will work)

### Natives come in different "flavors;" `regular`, `g`, or `uno`.

| Flavor | Meaning |
| ---- | ---- |
| Regular (just include the native) | Regular natives. You'll need to follow the native documentation precisely; no deviations. |
| g | Omits namespaces. So, instead of doing (for example) `ENTITY.GET_ENTITY_MODEL`, you would instead just do `GET_ENTITY_MODEL`. The thing before the period is the *namespace*. |
| uno | No type safety, and `Vector3` objects will be interpreted as 3 floats, so you do not need to expand them for natives. Keep in mind this will not work for tables, only `Vector3` objects that you have acquired via natives or your own doing, using `v3` from Stand's library. |
| g-uno | A combination of both *g* and *uno* natives. |

### Reading the native documentation

Reading the native documentation can be difficult for newcomers. Don't worry; you'll get the hang of it in no time.

For example, take this function:
```
void SET_​PLAYER_​WANTED_​LEVEL(
Player player,
int wantedLevel,
BOOL disableNoMission
)
```
The native documentation in [resources](#section-1-resources) does a very good job of spelling everything out for us. Let's go through the function one-by-one.

- The thing before the main function, the `void`, is the ***return type***. There are only two things to remember;
    - If the return type is a `void`, setting a variable to it will end poorly; it returns nothing. Don't set anything equal to functions that have `void` return types.
    - Every variable with a return type can be used anywhere that return type is accepted.
- The main body of the function is `SET_PLAYER_WANTED_LEVEL`. This is just used to call the function.
- `Player player` looks confusing, but the `Player` at the beginning (in caps) is the ***type***, and the second `player` (in lowercase) is the parameter.
- Same thing applies for `int wantedlevel`; the `int` is the type (integer), and the `wwantedlevel` is the paremeter.
- ...And same thing for the `BOOL disableNoMission`.

To find the *namespace* of any function, all you'll have to do is scroll up until you see in BIG TEXT something like **PLAYER** or **CAM**; that's your namespace.

Keep in mind that this works for almost all native functions. I say *almost*, because there are some natives that have paremeters with a star (`*`) next to them. If you know C/C++, you'll know where this is going, but if you don't, don't worry! We'll be getting into:

### Advanced Natives

Some natives, as we've discussed, have stars next to their paremeters. Take this one, for example:

```
BOOL GET_​SCREEN_​COORD_​FROM_​WORLD_​COORD(
float worldX,
float worldY,
float worldZ,
float* screenX,
float* screenY
)
```
This is the `GRAPHICS.GET_SCREEN_COORD_FROM_WORLD_COORD` native. This native does what it says it does; gets screen coords from world coords. But wait, there are no numbers in the return value, only BOOL!

This is where you'll have to get into memory. These stars are pointers, and you'll have to put in variables with allocated memory into them.

| Type | Bytes to allocate (size) |
| ---- | ---- |
| char | 1 |
| short int (short) | 2 |
| int | 4 |
| float | 4 |
| double | 8 |
| long int (long) | 8 |
| Vector3 | 24 |

Currently, we're working with two floats, so we'll allocate two variables with size `4`.

```lua
--require natives blah blah
local xmem = memory.alloc(4)
local ymem = memory.alloc(4)

GRAPHICS.GET_SCREEN_COORD_FROM_WORLD_COORD(myvec.x, myvec.y, myvec.z, xmem, ymem)

local x, y = memory.read_float(xmem), memory.read_float(ymem)
```

There we go! That's how we extract values from functions that have these star symbols. You won't come by these very often, but it's good to know about them when you get the chance.

----

<br/>

# Section 3: Developing Your Script

Okay, you're ready to develop your script. Now what? Well, time to get started!
Go into your Lua Scripts folder in Stand. This can be done via the menu, in the "Lua Scripts" section and doing "Open Folder," or navigating through `Appdata > Roaming > Stand > Lua Scripts`.

Make a new file here. You can do this via making a new text file, or going into your favorite text/script editor (I personally recommend [Sublime Text](https://www.sublimetext.com/)) and saving your script there, with the extension of either `.lua` or `.pluto`.

Make sure to include natives at the top of your script! Without this, you'll only be able to access Stand's functions and not the game's Native functions, which will significantly reduce your potential. If you only want to use Stand functions, either for a test (like making a "Hello World!" toast/print) or for memory, then you don't have to include natives. 99% of the time, you will have to, though, so do it.

Start developing! ***The hardest thing to do is to start***; to get ideas. Personally, I would think of any issues or flaws you want quelled with GTA V, and think about how to solve them, then break them down into little steps that make up one big thing. Can't see players? Make a simple ESP! Can't aim properly? Make an aimbot! Can't find objects? Find them via teleporting to them! The world is your oyster. Later on, we'll be dissecting some of my scripts in order to walk you through examples of how certain features work, if you're up for that.

----

<br/>

# Section 4: Globals / Game Scripts
`Global variables` are variables that do not change from script to script; therefore they have the name "global variables."

These global variables govern almost everything in GTA Online, from **cooldowns** to **product/supplies** to even the speed of **Terrorbyte drones**. If you want to create scripts that utilize these variables, you're going to have to dig through decompiled scripts, which have been provided above in the [resources](#section-1-resources) section.

Digging through these scripts is an arduous process, requiring more time and effort than casually creating features for your script, but they will pay off in the long run. It's best to know where to start looking for the things you want, or else you will face difficulties and burnout. ***Please look closely at the script names and determine which are right for your use case***.

For example, if I wanted to remove the cooldown of a specific thing in the Interaction Menu, I would look for a background script that has some mention of the Interaction Menu. This would be `am_pi_menu.c`.

| Shortened Version | Meaning |
| ---- | ---- |
| am | ambient |
| fm | freemode |
| gb | goonboss (CEO/MC/VIP) |
| mp | multiplayer |

Keep in mind that not *every* script that isn't marked with `mp` isn't multiplayer, it's just because of a naming convention. Since this repository contains every single game script and not just the Online ones, you'll have to do some minor sifting.

![They're never consistent...](/neverconsistent.png)

Setting and getting the values of these global variables is easily done with `memory.read` and `memory.write` functions, along with `memory.script_global`, since you need it to give you the memory address for said global variable to write into.

Global variables are formatted with the offset present (thank you decompilers) that allows us access to them immediately. Take, for example, global `Global_262145.f_13150` (`"GB_SIGHTSEER_TIME_LIMIT"`) from the `tunables_processing.c` game script. From the name, we can immediately tell that this is the ***Sightseer VIP*** Work time limit variable, and if we change it, we'll probably change the time limit for said VIP Work. To actually change it, we'll need to add the offset to the chunk of the main global variable, making our final value be: `262145 + 13150 = 275295.`

We now plug this value into our function of reading or writing the global variable;

```lua
function ReadIntFromAddr(addr)
    return memory.read_int(memory.script_global(addr))
end
```
```lua
function WriteIntToAddr(addr, write)
    memory.write_int(memory.script_global(addr), write)
end
```

. . . where addr is the value we have now got.

Now, of course, keep in mind that not every single number is an integer. Most of them will be, but some are `floats` (decimals) or `longs` (64-bit integer numbers). Be sure to use the memory functions according to these use cases.

----
<br/>

# Section 5: Examples

This section will go over some examples of features of a script, as well as certain GTA quirks.

```lua
--Spawn Vehicle Example
function spawn_vehicle(vehName, v3Position)
    local vehicleHash = util.joaat(vehName) --obtain the hash

    util.request_model(vehicleHash) --request the model (game loads it)
    --without loading the model we will not be able to spawn said model

    if STREAMING.HAS_MODEL_LOADED(vehicleHash) then --check if the model was loaded

        --you could do entities.create_vehicle (stand's) here, but we'll use the native:
        VEHICLE.CREATE_VEHICLE(vehicleHash, v3Position.x, v3Position.y, v3Position.z,
        0, true, true, true)

        STREAMING.SET_MODEL_AS_NO_LONGER_NEEDED(vehicleHash) --set the model as no longer needed (cleanup)

    end
end
```

In this example, we make a function spawn a vehicle. Unfortunately, that's not as easy as just calling `VEHICLE.CREATE_VEHICLE`, as you can see; we need to request the model. Things like this are what tripped me up when I was starting out, and they will trip you up, too, if you aren't careful.

Now, keep in mind, even though you are inputting the vehicle name into this function, *it isn't actually the full name of the vehicle*. For example, the `Oppressor MK II` doesn't have that name in the game's code, it has the concisely named `"oppressor2"` name instead; make sure you are aware of this when writing your code. Remember the `hashes` and `data dumps` in the [resources](#section-1-resources) section from earlier? This is where they come in very handy.


```lua
--Simple ESP Example
util.require_natives("2944a") --require natives
menu.toggle_loop(menu.my_root(), "ESP All Players", {}, "", function()
    local myCoords = ENTITY.GET_ENTITY_COORDS(players.user_ped())
    local allPlayers = players.list(false, true, true) --don't include yourself!

    for _, playerID in pairs(allPlayers) do
        local ped = PLAYER.GET_PLAYER_PED_SCRIPT_INDEX(playerID)
        local coords = ENTITY.GET_ENTITY_COORDS(ped)
        GRAPHICS.DRAW_LINE(myCoords.x, myCoords.y, myCoords.z,
            coords.x, coords.y, coords.z,
            255, 255, 255, 255)
    end
end)
```

In this example, we make a simple ESP using the native DRAW_LINE functions. Keep in mind that this will not draw lines through some walls, since the native is written that way. If you do want to do that, you'll have to use stand's `directx` functions to draw lines via the screen, so you'll also have to do `world coords to screen coords`.

Getting all players in a table via the `players.list` Stand function, we can then iterate through each playerID in order to get their positino, then draw a line towards them. Everything else should be self-explanatory with [resources](#section-1-resources).
