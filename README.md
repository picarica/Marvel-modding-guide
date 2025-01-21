# Ultimate Modding Guide Marvel rivals

## prepare rivals for mod installation (skippable if you already have some mods installed)

1. download [fmodel ](https://fmodel.app/) 
2. open fmodel and set path to Marvel Rivals ROOT Install directory (ex: C:\\Program Files (x86)\\Steam\\steamapps\\common\\MarvelRivals)
3. Change UE Version to "GAME_MarvelRivals"

### then we need to add AES key and mapping file

in fmodel :  
File > AES

add: 0x0C263D8C22DCB085894899C3A3796383E9BF9DE0CBFB08C9BF2DEF2E84F29D74

settings > Local Mapping file (check it)
and add from from fmodel-files from this repository  (5.3.2-1525091+++depot_marvel+S1_1_release-Marvel.usmap)

### now export all files from .paks

1. in fmodel open pakchunkCharacter-Windows.pak
2. (new windows will oopen) and then right-click on Marvel > Save Folder's Package models
3. navigate to where you opened FModel from, right next to it should be Output Folder, open it
4. navigate to Output > Exports > Marvel > Content > Marvel
5. copy Characters, and VFX folder to C:\Program Files (x86)\Steam\steamapps\common\MarvelRivals\MarvelGame\Marvel\Content\Marvel\

### Rename original .pak to .old
1. Navigate to C:\Program Files (x86)\Steam\steamapps\common\MarvelRivals\MarvelGame\Marvel\Content\Paks/
2. rename pakchunkCharacter-Windows.pak to pakchunkCharacter-Windows.pak.old
3. now new mods are loaded

## Load models into Blender

### download blender and required plugin
1. download and install blender
2. download this plugin https://extensions.blender.org/add-ons/io-scene-psk-psa/
3. open Blender naviagate to Edit > Preferences > Add-ons
4. on right top of newly opened window press the small arrow > Install from disk, and select the downloaded addon

Now we can import Marvel rivals models

before we import any new models delete everything in scene, i will write out next steps hoping you have some experience in blender (can be refactored)

1. File > Import > Unreal PSK
to find out which model you want to mod you can reference this spreadsheet https://docs.google.com/spreadsheets/d/1o10PJ0zFTKBSbnEvgiC25gMZpD1DnNptAP1IuyxBdfg/edit?pli=1&gid=0#gid=0
after first ID number ex: 1037 (Magneto) the nubmers after that are skins and variants
navigate to exported models from fmodel for me path is C:\Users\picarica\Downloads\FModel\Output\Exports\Marvel\Content\Marvel\Characters\1020\1020001\Meshes\SK_1020_1020001.pskx
note: Import the basic model, without any additional names just numbers and after that .pskx
2. Change Scale: from 1.00 to 0.01
3. Change Vertex color space from sRGBA to Linear
4. Change bone length from 1m to 5m
5. Press import
6. Rename imported model Orange to "Armature"

now the stuff is up to you what you will do, but our example will follow with no exaggerated edits maybe some texture paintings

### Adding textures to our model in blender right away
you can assign textures in blender so it will be easier in UE, and you can easily see if you assigned textures correctly

1. Select model and go to Shading tab (on top)
2. in the Right bottom panel select Material Tab (Red icon with Checkered ball)
3. then on the bottom you can add Image textures Ctrl+a > Image Texture , click the small image icon the left then find the correct texture
textures are differentiated in 4 groups
T_(model number and skin variant)_(Body part associated with texture)_Variant
We have 4 variants, the last letter of texture name assigns our variant lets explain variants
T_1043001_Body_N - Normal map Texture
T_1043001_Body_D -  Image texture
T_1043001_Body_ORM - Roughness texture
T_1043001_Body_S - Specular Texture

when we differentiated all types of texutres now you just add them all and plug them in correct plugs in Principled BSDF node
_D variant goes into Base color
_N goes firstly into Normal vector, you can add it via CTRL+A and then into Normal in Principled BSDF
_ORM goes into roughness
_S goes into Specular (first you have to expand it)


4.(Optinal) for safety i split my model by materials first, go back into Layout TAB on top
5. slect model and press TAB, then press CTRL+A selecting all vertices and press right-click > Seperate > By material

if you are doing advance model swapping you can use this guide https://www.youtube.com/watch?v=GUapvxT97XQ to  make sure model is lining up, and he shows you how to paint vortex weights, relevant info is from 2:38 to 17:00
exporting we will handle 

6. Exporting, press File > Export > FBX, on the bottom use the same filename as the original model for simplicity so for our example: SK_1020_1020001.fbx
on the right change options
in Geometry > Smoothing > Face
in Geometry > Vertex colors > Linear
Then Export it two times, once call it SK_1020_1020001.fbx then rename it to SK_1020_1020001_Lobby.fbx you should have two identical files called differently

....
!!! Missing Chapter!!!
....


## Importing .fbx models into Unreal Engine

### Installing unreal Engine
1. install epic game store
2. click unreal engine and install unreal engine 5.3.2

### create new project

after your editor opens, make sure you have set Raytracing and Started content OFF ! 
Target platfom: Desktop
and MAKE SURE Project Name is exactly : Marvel
project path is whatever, just make sure you can find it

after project is created make sure you see Content Browser on the bottom and resize it to max, we will be using only thi
if its not visible you can ssee it when you click on top Windows > Content Browser

### then in content Browser we need to recreate marvel folder structure
it should looke like 
All > Content > Marvel > Characters > 1020 > 10200001 
in the last folder 10200001 of your character create three folders
Texture > LobbyTexture
Meshes
Materials > Lobby

### now we can import mesh files
first start with Normal mesh file not lobby one
Click in the content browser > Import, and locate our exported .fbx file from blender
in the import window expand Advanced and make sure 
Import Morph Targets
Import Vertex attributes
are Checked
and change Normal Import method, to = Import normals and tangents

then you can click import

you should see three mesh files skeletons and all materials and textures from blender, if not we need to import them now
make sure you move Materials to Materials folder amd Textures to Texture folder

now make sure you save project and close project

### Install Json2DA

1. download https://github.com/peek6/Json2DA/tree/tekken8 as zip file
2. move contents from zip file to your project Content folder for me its C:\Users\picarica\Documents\Unreal Projects\Marvel\Content\Python (if Python folder doesnt exist, create it)
3. open C:\Users\picarica\Documents\Unreal Projects\Marvel\Config\DefaultEngine.ini
and add these two lines at the bottom

[/Script/PythonScriptPlugin.PythonScriptPluginSettings]
bDeveloperMode=True

[Core.System]
CanUseUnversionedPropertySerialization=False
4. save close and we done

### export correct .json files from fmodel

1. navigate back to the exported model file materials for me tis (C:\Users\picarica\Downloads\FModel\Output\Exports\Marvel\Content\Marvel\Characters\1020\1020001\Materials)
and delete all files even folder materials
2. open Fmodel and navigate to the same folder, and Right click to Material folder and click Save Folder's Package Properties (.json)
now we have correct jsons we will use in UE

### apply new jsons to our materials

1. open back up unreal engine
2. navigate to Materials folder
3. do these steps for all our materials
3a. Right click > Scripted Asset Actions >  Import MI JSON 2 MM
3b. locate the correct .json from C:\Users\picarica\Downloads\FModel\Output\Exports\Marvel\Content\Marvel\Characters\1020\1020001\Materials, there should be our newly exported jsons
select the correct mesh corresponding to the material, the name should match, in blender material, or the one that are imported in UE 
3c. click correct json (click over all the errors these are normal)
3d. Right click on the Current Material and > Create Material Instance
3e. (if you applied textures in Blender skip this step) now you need to apply all textures in the Material instance 
if textures didnt import with the mesh import, you need add them now from C:\Users\picarica\Downloads\FModel\Output\Exports\Marvel\Content\Marvel\Characters\1020\1020001\Textures, you can import all of them and move them to Textures folder, import _D textured to Base color _ORM TO ORM window etc.
4. now open our base Mesh in Meshes folder and apply our newly created _inst(material instances) materials
5. import Lobby Mesh, keep options the same but make sure you Deselect Skeleton, and you can remove all imported Textures and Materials and apply same instanced materials to Lobby model as well
6. Copy all Materials and copy them to Materials > Lobby folder
at the ned of process make sure you 

### Data Asset (DEPRECATED)

when cooking Data asset is not needed

1. navigate in our Content browser to folder All > Content
2. Right click empty sapce > Miscelanious > search Primary Asset Label
3. Open new Data Asset Label
4. Change Chunk ID to any number
5. Change cook rule to Always Cook
6. in Explicit Asset
add all asset we used so we add
Our base mesh and lobby mesh
SK_1020_1020001
SK_1020_1020001_Lobby

and add ALL materials(only instancted ones) and Textures in project

### Export Settings

1. Go to Edit > Project Settings > Packaging and Uncheck "Use IO Store"
2. in the same windows Search for "cook everything" and make sure that is checked
3. also Check Generate Chunks
4. in main window click Platforms > Windows > Binary configuration = Shipping
5. in the same window you can press Cook Content

and we are done now we just copy new mod to our game files

the files were cooked in our project files for me it was C:\Users\picarica\Documents\Unreal Projects\Marvel\Saved\Cooked\Windows\Marvel\Content\Marvel\Characters

so copy all files from C:\Users\picarica\Documents\Unreal Projects\Marvel\Saved\Cooked\Windows\Marvel\Content\Marvel\Characters to C:\Program Files (x86)\Steam\steamapps\common\MarvelRivals\MarvelGame\Marvel\Content\Marvel\Characters

make sure you have backup of the characters you moded.

DONE!
