# lavesiime.github.io
***
# RSDK XML Guide

This is a small guide on how to use the XML modding API in the Retro Engine V3/V4 decompilations. Not much else to say, so let's get started!

## Basic Formatting

The basic formatting of an XML file starts with the XML version at the top. For this tutorial, all that's needed is `<?xml version="1.0"?>`. Then, the first "class" starts. For RSDK, the only class used is `game`. In this `game` class, all the other sub-classes are contained inside of it. These sub-classes are what actually do stuff, so let's talk about them, starting with...


## Palettes

Although it may be a bit tedious, the XML support also allows for changing the global palette. I say that because, instead of having you input an already packed color for each index, you have to provide the R, G, and B values separately. However, it isn't my place to complain about it, so let's get on to the useful info. The format allows all 256 colors across the 8 palettes to be replaced, the format it follows is

```xml
<palette>
    <color bank="[pal ID]" index="[index]" r="[red]" g="[green]" b="[blue]"> </color>
    [...]
</palette>
```

For example, the below would change their corresponding colors to the colors they should have
- index `64` would be `0x808080`, or `128`, `128`, `128`
- index `65` would be `0x8F8F8F`, or `143`, `143`, `143`
- index `66` would be `0x747474`, or `116`, `116`, `116`

```xml
<palette>
    <color bank="0" index="64" r="128" g="128" b="128"> </color>
    <color bank="0" index="65" r="143" g="143" b="143"> </color>
    <color bank="0" index="66" r="116" g="116" b="116"> </color>
</palette>
```


## Objects

The objects section of the XML file deals with adding global objects for the game to use. The format is as follows

```xml
<objects>
    <object name="[object name]" script="[script path]" forceLoad="[true/false]"> </object>
    [...]
</objects>
```

With this template, there are a few things you need to change
- `[object name]` should be replaced with the object's name
- `[script path]` should be replaced with the object's script path, based on `Scripts/`
- `forceLoad` is set to either `true` or `false` depending on what you want
  - `false` appends it to the existing global object list, offseting all further object counts by 1
    - If you're making an original game, this is what you'll want
  - `true` will place it at the end of the stage object list, leaving all existing object types intact
    - If you're making a mod for an existing game, this is what you'll want

For example...

```xml
<objects>
    <object name="Coin" script="Global/Coin.txt" forceLoad="false"> </object>
    <object name="Shoe" script="Global/Shoe.txt" forceLoad="true"> </object>
</objects>
```

This would add two objects to the object list.
1. `Coin` would get added to the _global_ object list, with it offsetting all stage objects by 1. Its script path would be `Scripts/Global/Coin.txt`
2. `Shoe` would get added to the end of the _stage_ object list, leaving all preceeding objects alone. Its script path would be `Scripts/Global/Shoe.txt`


## Global Variables

Global variables are variables that are, well, global and can be changed by any script. Unlike object values or static values, these are kept the same between scene reloads. They are inserted into the `<variables>` class. The format they follow is

```xml
<variables>
    <variable name="[variable name]" value="[default value]"> </variable>
    [...]
</variables>
```

Of course, `[variable name]` is replaced with your actual variable name, and `[default value]` is replaced with what you want the default value to be. More variable entries can be entered until a satisfactory amount is reached. An example would be...

```xml
<variables>
    <variable name="options.remixedMusic" value="1"> </variable>
    <variable name="options.customPalettes" value="1"> </variable>
</variables>
```

In this example, two global variables are created; `options.remixedMusic` and `options.customPalettes`, both with default values of `1`. Now, these variables can be accessed from scripts just like any other global variable.


## Sound effects

This section talks about adding global SFX. The format differs between the RSDKv3 (Sonic CD) decompilation and the RSDKv4 (Sonic 1/2) decompilation.

For RSDKv3 (Sonic CD), the format used follows the below, where `sfx path` is the path to your SFX file, relative to `Data/SoundFX/`.

```xml
<sounds>
    <soundfx path="[sfx path]"> </soundfx>
    [...]
</sounds>
```

For RSDKv4 (Sonic 1/2), the format adds the `name` parameter, to allow for `SfxName` uses in the scripts. Other than that, the format is the same as RSDKv3.

```xml
<sounds>
    <soundfx name="[sfx name]" path="[sfx path]"> </soundfx>
    [...]
</sounds>
```

An example of an RSDKv4 one would be like...

```xml
<sounds>
    <soundfx name="Shoot" path="Global/Shoot.wav"> </soundfx>
    <soundfx name="Charged Shot" path="Global/ChargedShot.wav"> </soundfx>
</sounds>
```

This would add SFX `Shoot` and `Charged Shot` to the global SFX list, with paths of `Data/SoundFX/Global/Shoot.wav` and `Data/SoundFX/Global/ChargedShot.wav`, respectively.

## Players

Adding players is the simplest part of the XML process, as it only involves

```xml
<players>
    <player name="[player name]"> </player>
    [...]
</players>
```

Every entry in this `players` class adds one entry to the selectable players on the dev menu. Of course, it is up to the scripts to proper implement this. Note that without extra script modifications, these extra character slots <!-- *shudders* --> will have no effect in-game. An example of it can be seen below

```xml
<players>
    <player name="NASH"> </player>
    <player name="RINGO"> </player>
</players>
```

This would add players `NASH` and `RINGO` to the character selection screen, normally taking up `stage.playerListPos` values `4` and `5`, by default.

## Stages

The format for stages is perhaps the most complex out of the bunch, but not because it's difficult. Rather, it just needs the most values assigned to it in order to be setup properly, so don't be intimidated. The format follows

```xml
<[stagelist]>
    <stage name="[stage title]" folder="[stage folder]" id="[stage ID]" highlight="[true/false]"> </stage>
</[stagelist]>
```

- `[stagelist]` is the list for the stage to appear on
  - `presentationStages`
  - `regularStages`
  - `bonusStages`
  - `specialStages`
- `[stage title]` is the stage's title to use on the dev menu
  - The name drawn by the title card is independent from this, it's stored in the scene file itself instead
- `[stage folder]` is the folder the stage is contained in
  - Based on `Data/Stages/`
- `[stage ID]` is the ID/Act of the stage
- The entry gets highlighted if `highlight` is set to `true`
  - Normally only set for entries with a name in their entry, if there's only the act number present on the row then this isn't needed

An example would be the following...

```xml
<bonusStages>
    <stage name="LEVEL EDITOR" folder="LevED" id="1" highlight="true"> </stage>
</bonusStages>
```

This would first add stage `LEVEL EDITOR` to the `bonusStages` list. It'd use act `1` of the stage in the `LevED` folder, which means that it would be in `Data/Stages/LevED/Act1.bin`. It'd also get highlighted when selected because `highlight` is set to true.


## Big example

Now, let's take everything we've learned and make one big game.xml file.

```xml
<?xml version="1.0"?>
<game>
	<palette>
		<color bank="0" index="0" r="255" g="0" b="255"> </color>
		<color bank="0" index="1" r="0" g="0" b="0"> </color>
		<color bank="0" index="2" r="32" g="32" b="128"> </color>
		<color bank="0" index="3" r="64" g="64" b="160"> </color>
		<color bank="0" index="4" r="96" g="96" b="192"> </color>
		<color bank="0" index="5" r="128" g="128" b="224"> </color>
		<color bank="0" index="6" r="224" g="224" b="224"> </color>
		<color bank="0" index="7" r="160" g="160" b="160"> </color>
		<color bank="0" index="8" r="128" g="128" b="128"> </color>
		<color bank="0" index="9" r="64" g="64" b="64"> </color>
		<color bank="0" index="10" r="224" g="160" b="128"> </color>
		<color bank="0" index="11" r="160" g="96" b="64"> </color>
		<color bank="0" index="12" r="224" g="0" b="0"> </color>
		<color bank="0" index="13" r="128" g="0" b="0"> </color>
		<color bank="0" index="14" r="64" g="0" b="0"> </color>
		<color bank="0" index="15" r="224" g="224" b="0"> </color>
	</palette>

	<objects>
		<object name="Player Object" script="Players/PlayerObject.txt" forceLoad="false"> </object>
		<object name="Partner Object" script="Players/PartnerObject.txt" forceLoad="false"> </object>
		<object name="HUD" script="Global/HUD.txt" forceLoad="false"> </object>
		<object name="Chippit" script="Global/Chippit.txt" forceLoad="false"> </object>
		<object name="Dust Puff" script="Global/DustPuff.txt" forceLoad="false"> </object>
	</objects>

	<variables>
		<variable name="options.gameMode" value="0"> </variable>
		<variable name="options.hardMode" value="0"> </variable>
		<variable name="oscillation" value="0"> </variable>
		<variable name="player.score" value="100"> </variable>
		<variable name="player.lives" value="3"> </variable>
	</variables>

	<sounds>
		<soundfx name="Jump" path="Global/Jump.wav"> </soundfx>
		<soundfx name="Slide" path="Global/Slide.wav"> </soundfx>
		<soundfx name="Collect" path="Global/Collect.wav"> </soundfx>
	</sounds>

	<players>
		<player name="SCOUT"> </player>
	</players>

	<presentationStages>
		<stage name="TITLE SCREEN" folder="Title" id="1" highlight="true"> </stage>
		<stage name="CREDITS" folder="Credits" id="1" highlight="true"> </stage>
	</presentationStages>

	<regularStages>
		<stage name="GREEN PLAINS 1" folder="Level-01" id="1" highlight="true"> </stage>
		<stage name="2" folder="Level-01" id="2" highlight="false"> </stage>
		<stage name="3" folder="Level-01" id="3" highlight="false"> </stage>
	</regularStages>

	<specialStages>
		<stage name="SPECIAL STAGE" folder="SpecialS" id="1" highlight="true"> </stage>
		<stage name="2" folder="SpecialS" id="2" highlight="false"> </stage>
	</specialStages>

	<bonusStages>
		<stage name="BONUS GAME 1" folder="BonusS" id="1" highlight="true"> </stage>
		<stage name="2" folder="BonusS" id="2" highlight="false"> </stage>
	</bonusStages>
</game>
```

This (rather large) example will do a bunch of things.

- First, it'll set the global palette to have the colours seen below

<img src="paletteExample2.png" style="height: 8;" />

- Then, it'll add objects to the global item list, `Player Object`, `Partner Object`, `HUD`, `Chippit`, and `Dust Puff`. The scripts they all have will, in order, be `Players/PlayerObject.txt`, `Players/PartnerObject.txt`, `Global/HUD.txt`, `Global/Chippit.txt`, and `Global/DustPuff.txt`.

- After that, a few global variables will be created, namely `options.gameMode`, `options.hardMode`, `oscillation`, `player.score`, and `player.lives`. `player.score` will have a default of `100`, and `player.lives` will have a default of `3`.

- Next, some sounds will be added to the global sounds list. `Jump` with a path of `Global/Jump.wav`, `Slide` with a path of `Global/Slide.wav`, and `Collect` with a path of `Global/Collect.wav`.

- From there, a player entry of `SCOUT` will be added to the player list. Not much else to be said here, so let's move on!

- Now, all the stages are added.
  - Presentation Stages get `TITLE SCREEN` and `CREDITS`
  - Regular Stages get `GREEN PLAINS 1-3`
  - Special Stages get `SPECIAL STAGE`
  - Bonus Stages get `BONUS GAME 1-2`

## Conclusion

Well, that should just be about everything. This new system allows easy-to-read XML files to be used instead of byte-based `GameConfig.bin`'s, and I'd say that's pretty good, especially for modding. As I've stated throughout this tutorial, all this _adds_ onto the things existing in the `GameConfig`, so a complete replacement can only happen if you dummy out the Game Config file. Other than that, though, have fun!
