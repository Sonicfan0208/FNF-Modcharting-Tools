# FNF Modcharting Tools
Just a thing I made to make modcharting easier, should be easy to add to most engines.
Still very WIP and not everything is supported yet! Only works on psych 0.7.x.

## Features
### Modifier system for easing in and out effects
![](https://github.com/TheZoroForce240/FNF-Modcharting-Tools/blob/main/readme/modifiers.gif)
### Custom Sustain Renderer (using FlxStrip for stretchy sustains)
### Multiple playfields that can have their own positions and modifiers
![](https://github.com/TheZoroForce240/FNF-Modcharting-Tools/blob/main/readme/sustains.gif)
### Custom Modifier Support via Hscript
![](https://github.com/TheZoroForce240/FNF-Modcharting-Tools/blob/main/readme/custommods.gif)
### THIS MODCHART SYSTEM ONLY HAS SUPPORT FOR PSYCH ENGINE
- [Psych Engine](https://github.com/ShadowMario/FNF-PsychEngine) (0.7.x, includes lua functions)
### Support for multiple modcharts
Most versions of this modchart system doesn't have support for middlescroll, now it does! in the modchart editor, make a new folder in the songs data folder called "modchartData", inside that folder save the modchart as listed
- If you're using only upscroll, save the modchart as "modchart-upscroll"
- If you're using middlescroll AND upscroll, save the modchart as "modchart-middleUp"
- If you're using only downscroll, save the modchart as "modchart-downscroll"
- If you're using middlescroll AND downscroll, save the modchart as "modchart-middleDown"
  
IT IS CASE SENSITIVE, SAVE IT EXACTLY AS IT'S LISTED

## Credits 
- [Original FNF Team](https://github.com/ninjamuffin99/Funkin) - They made the game
- [NotITG](https://www.noti.tg/) - Inspiration (made me love modcharts lol)
- [OpenITG](https://github.com/openitg/openitg) - Math used for some modifiers
- [TheZoroForce240](https://github.com/TheZoroForce240/FNF-Modcharting-Tools) - Creator of modcharting tools base
- [Vortex2Oblivion](https://github.com/Vortex2Oblivion) - Helper from modcharting tools
- [Manu614](https://github.com/Manu614) - Helper from modcharting tools
- [UncertainProd](https://github.com/UncertainProd) - Helper from modcharting tools
- [Joalor64GH](https://github.com/Joalor64GH) - Helper from modcharting tools
- [Edwhak_KB](https://github.com/EdwhakKB) - Added some modifiers and fixed stuff + skewNotes
- [Glowsoony](https://github.com/glowsoony) - Help with some 0.7.X stuff! + skewNotes too
- [Slushi_Github](https://github.com/Slushi-Github) - Help with reorganisation with haxelib edition
- [2JENO](https://github.com/2JENO) - Help with the Import.hx, GPU thing (fixes some things so thanks!)

## Installation 
1. You need the most recent version of HaxeFlixel for it to work. (5.5.0 as of writing)
2. If your running any (engine or psych) that uses SScript (For Custom Modifiers) then I really recommened using versions 6.1.80 and up. NO LOWER VERSIONS! (If you use older versions, sorry but SScript in older version apperantly has many memory leaks when it comes to any scripts (even without modchartingTools). -glowsoony
### With Source:
1. Install the haxelib by typing `haxelib git fnf-modcharting-tools https://github.com/Sonicfan0208/FNF-Modcharting-Tools` in the console
2. Now you only need to make a few small additions to get everything working,
- In MusicBeatState.hx:
```haxe
class MusicBeatState extends modchart.modcharting.ModchartMusicBeatState
{
  
```
- In PlayState.hx:
```haxe
import modchart.modcharting.ModchartFuncs;
import modchart.modcharting.NoteMovement;
import modchart.modcharting.PlayfieldRenderer;
  
```
```haxe
override public function create()
{
	//Add this before function create() (For Psych 0.7.1+)
	var backupGpu:Bool;
	//Add this before generateSong(); (For Psych 0.7.1+)
	backupGpu = ClientPrefs.data.cacheOnGPU;
	ClientPrefs.data.cacheOnGPU = false;
	//Add this before camfollow stuff and after strumLineNotes and notes have been made
	playfieldRenderer = new PlayfieldRenderer(strumLineNotes, notes, this);
	playfieldRenderer.cameras = [camHUD];
	add(playfieldRenderer);
	add(grpNoteSplashes); /*place splashes in front (add this if the engine has splashes).
	If you have added this: remove(or something) the add(grpNoteSplashes); which is by default below the add(strumLineNotes);*/
	//if you use PSYCH 0.6.3 use this code
	ModchartFuncs.loadLuaFunctions(); //add this if you want lua functions in scripts
	//being used in psych engine as an example
callOnLuas('onCreatePost', []);
      
  //Find this line and then add it
public function startCountdown():Void
{
  generateStaticArrows(0);
  generateStaticArrows(1);
  
  //add after generating strums
  NoteMovement.getDefaultStrumPos(this);
//Find this line and then add it (For Psych 0.7.1+)
override function destroy() {
	ClientPrefs.data.cacheOnGPU = backupGpu;
```

- In StrumNote.hx:
```haxe
//Import FlxSkewedSprite at the top
import flixel.addons.effects.FlxSkewedSprite;
//change "FlxSprite" to "FlxSkewedSprite"
class StrumNote extends FlxSkewedSprite
```

- In Note.hx:
```haxe
//Import FlxSkewedSprite at the top
import flixel.addons.effects.FlxSkewedSprite;
//change "FlxSprite" to "FlxSkewedSprite"
class Note extends FlxSkewedSprite
{
  //add these 2 variables for the renderer
  public var mesh:modchart.modcharting.SustainStrip = null;
  public var z:Float = 0;
```

- In FunkinLua.hx (Found in psychlua folder) (0.7.X exclusive!):
```haxe
//at the start of the HX
    import modchart.modcharting.ModchartFuncs; //to fix any crash lmao
class FunkinLua
{
    //add this variable bellow "public var closed:Bool = false;"
  	public static var instance:FunkinLua = null;
    #if desktop DiscordClient.addLuaCallbacks(this); #end
    ModchartFuncs.loadLuaFunctions(this); //add this if you want lua functions in scripts
    being used in psych engine as an example
```
- In HScript (Found in psychlua folder) (0.7.X exclusive!)
``` haxe
//under the function (PRESET!)
//copy and paste this code if you use under SScript 6.1.80
override function preset()
{
	set('Math', Math);
	set('ModchartEditorState', modchart.modcharting.ModchartEditorState);
	set('ModchartEvent', modchart.modcharting.ModchartEvent);
	set('ModchartEventManager', modchart.modcharting.ModchartEventManager);
	set('ModchartFile', modchart.modcharting.ModchartFile);
	set('ModchartFuncs', modchart.modcharting.ModchartFuncs);
	set('ModchartMusicBeatState', modchart.modcharting.ModchartMusicBeatState);
	set('ModchartUtil', modchart.modcharting.ModchartUtil);
	for (i in ['mod', 'Modifier'])
		set(i, modchart.modcharting.Modifier); //the game crashes without this???????? what??????????? -- fue glow
	set('ModifierSubValue', modchart.modcharting.Modifier.ModifierSubValue);
	set('ModTable', modchart.modcharting.ModTable);
	set('NoteMovement', modchart.modcharting.NoteMovement);
	set('NotePositionData', modchart.modcharting.NotePositionData);
	set('Playfield', modchart.modcharting.Playfield);
	set('PlayfieldRenderer', modchart.modcharting.PlayfieldRenderer);
	set('SimpleQuaternion', modchart.modcharting.SimpleQuaternion);
	set('SustainStrip', modchart.modcharting.SustainStrip);
	
	modchart.modcharting.ModchartFuncs.loadHScriptFunctions(this);
//--(else if you use SScript above or equal to version 6.1.80)--
override function preset()
{
	set('Math', Math);
	setClass(modchart.modcharting.ModchartEditorState);
	setClass(modchart.modcharting.ModchartEvent);
	setClass(modchart.modcharting.ModchartEventManager);
	setClass(modchart.modcharting.ModchartFile);
	setClass(modchart.modcharting.ModchartFuncs);
	setClass(modchart.modcharting.ModchartMusicBeatState);
	setClass(modchart.modcharting.ModchartUtil);
	setClass(modchart.modcharting.Modifier); //the game crashes without this???????? what??????????? -- fue glow
	setClass(modchart.modcharting.Modifier.ModifierSubValue);
	setClass(modchart.modcharting.ModTable);
	setClass(modchart.modcharting.NoteMovement);
	setClass(modchart.modcharting.NotePositionData);
	setClass(modchart.modcharting.Playfield);
	setClass(modchart.modcharting.PlayfieldRenderer);
	setClass(modchart.modcharting.SimpleQuaternion);
	setClass(modchart.modcharting.SustainStrip);
	modchart.modcharting.ModchartFuncs.loadHScriptFunctions(this);
//Function initMod -- Init's the mods functions for Hscript (found in psychlua)
//Place this function anywhere in the HScript class!
public function initMod(mod:modchart.modcharting.Modifier)
{
	call("initMod", [mod]);
}
```
- In Import.hx, you should copy what mine adds and paste it there

- In Project.xml:
```xml
<!--Set this to the engine you're using!-->
<define name="PSYCH" />

<haxelib name="fnf-modcharting-tools" />

```
You need to define which engine you're using to fix compiling issues, or it would default to base game settings (downscroll won't work etc).
Available ones: PSYCH, KADE(notTested), LEATHER, FOREVER_LEGACY(notTested), YOSHI(notTested), FPSPLUS(notTested)

Note: If you use psych engine you should add this (have in mind "ver" is the version you want to use, do not add the text, use the brain)
(just in case minimal ver is 0.6.0 to 0.7.3)
and no if psych 0.7.4 or more releases i won't port this due some changes Psych has (they break MT to it max so srry :D)

```xml

<define name="PSYCHVERSION" value="ver"/>

```

to get 0.7.X and up add a higher version than 0.7 (example 0.7.3),
leave it as another value to use 0.6.3 edition


3. Now if your game compiles successfully then you should be all good to go.
