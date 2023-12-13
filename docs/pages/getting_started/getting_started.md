# Getting Started
**Forever** is a complete solution for creating endless runner games or procedurally generated
linear games (non-infinite) such as racing (closed loop) and skiing (A-B) games in both 3D and 2D.

The system is based around the concepts of level segments – game objects which are designed
by hand and then instantiated and extruded during runtime. The generated segments in a level can
be a fixed amount or can be an indefinite amount with new segments being created as the player
proceeds and old ones – removed.

The entire system is designed with ease of use and performance in mind while keeping the
flexibility and the possibility for extensions high. By default, all extrusion operations are done in a
separate thread so that the game experience remains smooth.

Forever is based off another Dreamteck plugin called Dreamteck Splines and if both plugins are
installed, Forever offers the possibility to also bend splines if a level segment contains such – this is
handy for creating enemies, constrained to paths within a segment or even procedural meshes
within the generated procedural segments. In order to make use of the Dreamteck Splines support
in Forever, the **“DREAMTECK_SPLINES”** scripting define needs to be added inside the **Project
Settings / Player settings**.

![](./pages/getting_started/_images/007.png)

## 1.1 How Everything Works

During runtime, level segment prefabs are spawned and stitched together in order to
form the level. The order of spawning can be random or user-defined. Each segment is
instantiated (Object Pooling is not used because the segments are modified) and destroyed
when no longer needed. The level segments are created by hand in the Unity editor like any
other object.

A spline path is generated for each spawned segment and each segment is extruded
along its own path.

How the path is generated is defined by special scripts called Path Generators. The path
generators hold the logic for path generators. Forever comes with a couple of Path
Generator scripts but users can easily create their own path generation logic by inheriting
from the **LevelPathGenerator** class.

## 1.2 Package Contents

- Forever- Editor
    - Gameplay
    - Level Generator
    - Level Segments
    - Examples.unitypackage
- Splines
- Utilities
The Splines and Utilities folders are required by Forever and their contents
should not be removed.

Inside the Forever folder, there is a unitypackage file containing example
projects. They are a good starting point for beginners and if examples are not
needed, then this package can be safely deleted.

The Gameplay folder contains logic for running along the generated levels. The
classes inside are self-dependent so if the built-in runner logic is not needed for the
purpose of the project, this folder can also be removed safely. However, it is
recommended that at least the ProjectedPlayer.cs file is kept in the project because
it acts as a communicator component between the Level Generator and the player –
it lets the Level Generator know where the player is so that new segments can be
generated and old ones – removed. In conclusion – even if a custom player script is
used to traverse the levels, the logic of the ProjectedPlayer.cs is a must have in order
for the Level Generator to work properly.

## 1.3 The Level Segment

The Level Segments are the building blocks of each procedural level that
Forever generates. They are simply prefabs which have the Level Segment
component attached to them.
During runtime, the Level Generator spawns these prefabs and builds the entire level
out of them.

A Level Segment can either be Extruded or Custom. Extruded segments will be bent
along the generated random path while custom segments will not be altered. A level could
be built entirely from Custom or Extruded segments or a combination of the two.

Each Level Segment holds definitions for how each of its children is handled during the
extrusion process. Objects can be offset, rotated and scaled based on the generated path,
or they can be ignored completely.

### 1.3.1 Creating Level Segments

To create a Level Segment, create a Game Object in the scene and add the
Level Segment component to it. Inside the inspector two buttons will appear:
**SAVE AND SAVE AS BUTTONS IMAGE**

Level Segment prefabs should be created and applied using these two
buttons instead of clicking the “Apply” button in the prefab menu at the top. If a
Level Segment prefab is saved like a regular prefab, it will not be able to pack the
needed information for the generation and this may result in an error during
runtime.

### 1.3.2 Basic Segment Setup

When a segment is created,
it first needs to be set up properly
before it can be used in the level
generation process.

![](./pages/getting_started/_images/010.png)

The Type of the segment is
the first most important setting that
needs to be made. By default,
segments are set to “Extrude”
which means that the segment will
be bent along the generated level
path.

All segments in a level sequence need to be able to snap seamlessly with
each other and extruded level segments need to be designed in a straight line – the
bending and turning will come from the level generation during runtime.
After adding a Level Segment component to an object, it will also
automatically create a Rigidbody and set it to kinematic. This is done to improve
the performance of the level generation and the Floating Origin work.

#### 1.3.2.1 Extruded Segment Type
When “Extrude” is selected, an object list will appear inside the
inspector. This is the list of all game objects in the level segment. Clicking on
each one will bring up the Extrusion Settings window:

![](./pages/getting_started/_images/014.png)

From there, the object can be set up for the extrusion process.
Multiple objects can be selected by holding down L. Shift or
Control/Command.

- **Enabled** – Should the object be included in the extrusion
    process?
- **Include in Bounds** – What part of the object to include inside
    the bounds calculation.
- **Rotation** – Should rotation be applied?
- **Keep Upright** – If true, the object will be rotated but will be
    also leveled along a specific vector.
- **Scale** – Should scale be applied?
- **Bend Mesh** – Should the mesh of the object be bent along the
    spline?
- **Mesh Collider** – How should mesh colliders be handled?
- **Bend Sprite (Experimental)** – attempts to bend 3D sprites.
Extruded segments will have their bounds drawn inside the editor.
Editor. The bounds must cover the segment’s main geometry completely in
order for the segment to be generated correctly. When an object’s mesh is
set to bend and the object itself is included in the bounds, the bounds will
automatically expand to fit the mesh. However, if the level segment doesn’t
have a single big mesh that covers the whole segments, the bounds could be
expanded artificially using empty game objects and putting them at the far
ends of the segment.

Where the bounds end defines where the next segment will start so
having bounds which are extending outside of the visual area will create gaps:

![](./pages/getting_started/_images/016.png) ![](./pages/getting_started/_images/017.png)

In the same time, if an object’s mesh is set to extrude but the object
itself is configured to not be included inside the bounds, upon extrusion, the
object’s mesh will be flattened by the bounds:

![](./pages/getting_started/_images/018.png) ![](./pages/getting_started/_images/019.png)

This is why it is important to include the meshes inside the bounds for
objects that are meant to bend.

It is, however, useful to leave a mesh out of the bounds calculation
and still have it bent. Such is the case for detail meshes like grass and other
vegetation which might protrude out of the segment bounds a little. Having
the grass excluded from the bounds calculation will make sure it is tightly
packed with everything else and no gaps between segments appear.

Extruded segments also have the Axis setting which can be set to
either X, Y or Z. The axis defines along which axis the segment will be
extruded. For 3D games, the Z axis is usually the one used and for 2D games,
the X axis is used. The Y axis could work for an endless runner game where
the player is constantly falling down or climbing a tower.

#### 1.3.2.2 Custom Segment Type
The custom segments are
segments which are simply
spawned and not modified. In
Custom mode the object list is gone
and in its place, there are two
Transform reference fields:

![](./pages/getting_started/_images/038.png)

- **Entrance** – The point of entry
- **Exit** – The point of exit

Each custom segment must have those two assigned. When
generating the level, the Level Generator will align the custom segments
based on where their Entrance transform is positioned and how it is oriented.
The Exit transform is used to tell the Level Generator where and how to place
and orient the segment
that comes after that.
The proper placement
for these two objects
would be at the edges of
the segment where it
needs to connect with
the other segments.

![](./pages/getting_started/_images/023.png)

The Keep upright option is useful for when the level segment needs
to be leveled perfectly regardless of the orientation of the previous segment.
Otherwise, the custom segment will be oriented to match the previous
segment’s direction. Usually it is recommended to use **“Keep Upright”** for
custom segments.

Custom segments do not necessarily have to go in a straight line.
The Entrance and the Exit can
point in different direction. In
fact, custom segments are
very useful when creating 90-
degree turns. Having non-
straight segments, however,
can cause a couple of issues.

![](./pages/getting_started/_images/024.png)

If three 90-degree turns (turning in the same direction) spawn one
after each other, it is possible that the segments will intersect with existing
previous segments. In order to prevent that either the sequences have to
be set up in such a way that such segments spawn rarely or a custom
segment shuffle approach must be used (see **Segment Shuffle**).

### 1.3.3 Setting Up Extruded Segments In 2D

When working in 2D, there are a few things that need to be considered. First
of all, the path needs to be set up to generate along the X axis. This can be done
either by rotating the Level Generator so that its local Z axis aligns with the world X
axis (rotate it 90 degrees along the Y axis) or by setting the start orientation of the
path generator to 0, 90, 0:

![](./pages/getting_started/_images/026.png)

Next, each 2D segment that is using Extrusion needs to be configure to extrude along
the X axis:

![](./pages/getting_started/_images/027.png)

Sprite renderers can be set up to bend along the paths too. Select an object
from the objects list and toggle “Bend Sprite” in the Extrusion Settings window. This
will convert the Sprite Renderer into a configuration of a Mesh Filter and a Mesh
Renderer that uses the sprite material to display the sprite.

Currently only Sprite Renderers can be set to bend along the path. Tilemaps
and Sprite Shape Renderers are still unsupported. Polygonal collider bending support
will come in version 1.01.

For Custom type segments, there are no additional steps that need to be
made – 2D segments of custom type will work out of the box.

### 1.3.4 Example Setup for Extruded Segments

- Create an empty Game Object and name it “Segment”.
- Create a couple of Plane objects. Snap them together and parent them under
    the empty Game Object:

![](./pages/getting_started/_images/029.png) ![](./pages/getting_started/_images/030.png)

- Add the Level Segment component to the Segment object.
- Inside the Level Segment component, make sure that the type is set to
    “Extruded” and the Axis is set to Z.
- Expand the Objects foldout, click on Plane1 and then, holding shift, click on
    Plane6 inside the Level Segment object list:

![](./pages/getting_started/_images/031.png)

- Inside the extrusion settings window,
    check “Bend Mesh” and make sure
    that “Include in Bounds” has the
    “Mesh” flag checked so that the
    meshes of the planes are included in
    the bounds. If the meshes are
    properly included, the entire segment
    will have a green outline:

![](./pages/getting_started/_images/032.png)

These are the calculated bounds of
the segment.

- Set the “Mesh Collider” field to “Copy”. This will make sure that the mesh
    colliders of the Plane objects will copy the extruded mesh and the collision will
    correspond to the visuals.
- Add other level design objects under the Segment game object

![](./pages/getting_started/_images/034.png) ![](./pages/getting_started/_images/035.png)

In this case, crates are added to the segment. The crates should not have their
meshes bent so “Bend Mesh” is not checked for the crates.

- Click the “Save” button inside the Level Segment component inspector and
    specify a place inside the project where the segment should be saved.
- A Level Segment prefab will be created which can now be used inside the Level
    Generator.

![](./pages/getting_started/_images/036.png)

### 1.3.5 Example Setup for Custom Segments

- Duplicate the existing level segment prefab from the previous example and
    place an instance of it inside the scene.
- Set the type of the Segment to “Custom”
    from the Level Segment component
    inspector.

![](./pages/getting_started/_images/038.png)

- Click both “Create” buttons next to the “Entrance” and
    “Exit” reference fields to create an entry an exit point for
    the custom segment. This will create two new children at
    the 0,0,0 local coordinates of the segment

![](./pages/getting_started/_images/037.png)

- Select the Entrance object from the hierarchy and snap it to the start of the
    segment by holding V and moving the position handle.
- Do the same for the Exit object.


- Upon selecting the segment game object again, the Entrance and Exit objects
    will be visualized with green and red.

![](./pages/getting_started/_images/040.png)

- Toggle “Keep Upright” if desired and Save the segment

### 1.3.6 Editing Existing Level Segments

To edit an existing Level Segment prefab, simply put it in the scene and click
“Save” after editing is done.

#### 1.3.6.1 Editing Level Segment Prefabs in Unity 2018.3+
If using Unity 2018.3 or above, the level segment prefabs can be
edited inside the prefab mode safely. Once the Level Segment prefab has
been created using the “Save As” button, it is safe to edit inside the prefab
mode and packing operations will be performed automatically upon exiting
prefab mode.

## 1.4 The Level Generator

The Level Generator is the main brain behind the generation of the levels. It is a
component that uses the Singleton design pattern (meaning that there should be only
one active Level Generator per scene) and it is recommended to be placed in a separate
Game Object, dedicated only to it. All generated level segments during runtime will
become children of that object.

To create a Level Generator, add the
Level Generator component to an
empty game object in the scene.

The Inspector of a newly created
Level Generator will show two errors:

- A Path Generator needs to
    be assigned
- No defined levels.

![](./pages/getting_started/_images/043.png)

The Level Generator needs to have a Path Generator assigned to it so by assigning
an existing Path Generator asset (see The Path Generator ), this error will be fixed.

The Level Generator is based on levels. Each level contains a collection of sequences
which contain a collection of level segments. In order for the Level Generator to work, it
needs to have at least one level defined. To create a new level asset, select
**Assets/Create/Forever/Level**. In the Level Generator component, expand the Levels
foldout and link the newly created level asset into the Level Collection list:

![](./pages/getting_started/_images/042.png)

### 1.4.1. Managing Levels

Levels in Forever are comprised of sequences. Each sequence is a collection
of level segments. After the given sequence has finished spawning segments, the
level will move onto the next existing sequence until all sequences are done.

A level needs to have at least one sequence in order to be able to spawn Level
Segments. To open the sequence editor for a level, select the level asset in the
project and click the **“Edit Sequence”** button in the inspector.

![](./pages/getting_started/_images/045.png)

#### 1.4.1.1 Creating and Managing Sequences
To create a new sequence inside a given level, click the “Add
Sequence” button in the Level Window. A new empty sequence will be
created. To add level segments to the sequence, drag and drop the prefabs
inside the sequence. Note that only prefabs with the Level Segment
component can be added to sequences.

When a sequence has Level Segments assigned to it, these level
segments can be re-
arranged by dragging and
dropping them. This is useful
for when the sequence type
is set to “Ordered”.

![](./pages/getting_started/_images/046.png)

Each sequence can
be configured via the
**Settings** button at the top
right corner.

There are three types of sequences:
- Ordered
- Random by Chance
- Shuffled
- Custom
**Ordered** will spawn all segments inside the sequence in the order
they are added and then the level will move onto the next sequence.

**Random by Chance** will spawn N random segments (N - defined by
the Count property).

**Shuffled** will create a randomly shuffled list of the segments and will
spawn all segments without repeating

**Custom** allows for the developer to write their own algorithm for
picking segments (see Segment Shuffle).

Right-clicking the green header of a sequence will show a context
menu with options for management like renaming, reordering and removing.

**Note – a sequence can be set to loop forever if set to “Random” and
the count of the segments is set to 0. In this case, the sequence will never
finish unless interrupted with a script.**

### 1.4.2 Level Generator Settings

- **Build on Awake**
As the name suggests, if this is toggled, the level generator will start building
the level as soon as it becomes active in the scene. If not toggled, then the level
generation can be started by calling ```LevelGenerator.instance.StartGeneration();```
- **Load Timeout**
How long should the Level Generator wait for a level to load. This is used for
edge cases where there is a problem loading the level scene.
- **Multithreaded**
Should the generation process be carried out on another thread? Usually this
should be set to True except for WebGL applications where multithreading is not
supported. Having the generation logic run on a separate thread will greatly improve
the performance.
- **Use Unload Unused Assets**
If set to true, Resources.UnloadUnusedAssets() will be called after each level
is done generating. This can cause performance hiccups however. Forever has a
built-in resource management system that is faster and could be used instead.
- **Loop Segments**
Use this option when creating looped tracks with the Custom Path Generator.
It will tell the runners and projected player that the level should start over once the
last segment is reached.
- **Max. Segments**
Defines the maximum number of level segments that can exist in the level.
Once started, the Level Generator will keep adding new segments ahead until the
Max. Segments number is reached. At this point, the Level Generator will start
removing old segments to keep the count of the segments in check.
- **Generate Segments Ahead**
Defines how many level segments should be generated ahead of the current
segment. This is needs to be coherent with the view distance in the game. If the
segments in the project are big (long), then generation could add just a couple
segments ahead, otherwise, more segments will be needed.
- **Activate Segments Ahead**
Defines how many of the generated segments head should also be activated.
When a segment is generated, its colliders get deactivated in order to save
performance. When a segment is near the player, it can be activated so that collision
starts to work. This value needs to be between 1 and **“Generate Segments Ahead”**.

- **Level Iteration**
Defines how the Level Generator will iterate through the linked levels inside
the Level Collection.
- **Ordered Finite** – goes through all levels in order and stops generating
after the last level
- **Ordered Clamp** – goes through all levels in order and repeats the last level
indefinitely
- **Ordered Loop** – goes through all levels in order and loops back when the
list ends
- **Random** – picks random levels to generate indefinitely
- **Single Repeat** – will generate the level defined by “Start Level”
indefinitely
- **Single Finite** – will generate the level defined by “Start Level” a single time
and will stop

## 1.5 The Path Generator

The Path Generators are scriptable objects which are created as assets
inside the Project and hold logic for path generation. In order to generate
any level, there needs to be a Path Generator assigned to the Level
Generator. The Path Generators generate paths for each segment separately
in the form of Spline control points. The generated paths in Forever are interpolated
using either the Bezier spline function or are just linear.

To create a Path Generator, right click inside the Project panel -> **Create/Forever/Path Generator**. This will bring up an editor window with the available Path Generators.

![](./pages/getting_started/_images/049.png)

More path generators can be created by deriving from the
LevelPathGenerator class (see Extending the Functionality for more
information). As soon as a new Path Generator class is created, it
will be automatically indexed inside the New Path Generator
window.

Once a Path Generator is created, it can be configured by selecting it in the Project
panel and tweaking its values in the inspector.

Path Generators can be switched at any time during the level generation and the
level path will proceed to generate according to the current Path Generator that is in
use.

### 1.5.1 Setting up a Path Generator

All path generators come with a core setting for the generated path. This
defines what the generated path per each segment will be – not the entire level path.

![](./pages/getting_started/_images/053.png)

Since the generated paths in Forever
are actually splines, two algorithms are
supported – Bezier and Linear. All Path
Generators in Forever are written to generate
Bezier tangents for the paths but if the Type of
the path is set to “Linear” the Bezier
interpolation will not be used. For example,
this is how the Spiral path generator works
when the generated path is set to Bezier:

![](./pages/getting_started/_images/056.png)

And if one of the generated segments is selected, a debug visualization will
display how the point’s tangents are generated:

![](./pages/getting_started/_images/054.png)

If the path is set to Linear, the result of the generation will bypass the tangents:

![](./pages/getting_started/_images/055.png)

It does not look good in the case of the spiral, but there are cases where this
effect could be sought after.

The Points Per Segment field defines how many path points will be generated
per segment. More points are better for a more detailed generation but the average
project should not require more than 3 to 5 points per segment.

The Sample Rate defines the rate at which the generated spline will be
sampled between each two points. 10 means 10 samples between points and with a
segment with three points, that would mean a total of 20 samples per segment.
Increasing the samples would increase the smoothness of extrusion but usually 10
samples should be enough.

The Normal and Value interpolations define how path Normals, Colors and
Values will be interpolated from point to point. By default, a linear interpolation is
used but in order to increase smoothness, curves can be used to define easing.

### 1.5.2 Random Path Generator

This is the most versatile path generator that comes by default
with the Forever package. It can be used for the generation of a wide
range of paths and along with directions, it can also randomize path
sizes, colors and normals.

The first settings panel for the Random Path Generator is the Orientation
panel. This is where the general path
behavior is defined – where the path can go
and how it behaves. This is defined by the
pitch, yaw and roll properties. By default, all
three are disabled which will produce a path
going in a straight line. When one is
enabled, additional fields for setup are
shown. All values inside the orientation panel are in signed degrees.

![](./pages/getting_started/_images/058.png)

When the orientation of the Random Path Generator reaches the target
angle, the algorithm finds a new target angle to move towards. This new target angle
is defined by the restrictions (if enabled) and the target step angles which define the
minimum and maximum random angle to add to the current angle to produce the
new target.

- Restrict – should the orientation of the path be restricted?
- Restrict Min. – minimum allowed angle (-45 for example)
- Restrict Max. – maximum allowed angle (60 for example)
- Min. Target Step – minimum angle to change the target with (should
    be positive)
- Max. Target Step – maximum angle to change the target with (should
    be positive)
- Min. Turn Rate – minimal angle to turn the path towards the target
    angle with (should be positive)
- Max. Turn Rate – maximum angle to turn the path towards the target
    angle with (should be positive)
- Level Start Target – target angle for when the level loads

Here is an example setup for the path generator along with the produced result:

![](./pages/getting_started/_images/062.png)

![](./pages/getting_started/_images/063.png)

The Sizes panel provides settings for randomizing the sizes of the generated path
points. Path point sizes affect how the geometry is generated and if scaling is enabled
for objects, the objects’ transforms will be scaled too.

The Sizes panel has two curve fields for the minimum and maxium size. The
curves define how the points will be scaled generating from the start towards the end
of the segment. This is an example setup:

![](./pages/getting_started/_images/064.png)

![](./pages/getting_started/_images/066.png)

It is a good idea to enable value interpolation in the Path panel settings when
using sizes and colors and use an ease-in, ease-out curve.

The Colors panel works the same way as the sizes panel but alters the point
colors. It uses two gradients as opposed to two curves. In order to see applied colors,
the extruded objects should also be set up to receive color from the Extrusion Settings
window and should use a shader which supports vertex colors.

The Offset panel ads offsets to the
generated segments. There are two types of
offsets – segment offset which applies an
offset to each segment and new level offset
which applies an offset to the first segment
from each new level. The space property defines how the offset is calculated. World will
add a world offset while Self will add an offset that is local to the path.

![](./pages/getting_started/_images/068.png)

### 1.5.3 Custom Path Generator

The Custom Path Generator is the most unique path generator
that comes out of the package. First of all, it is meant for finite levels.
So in order to use it, the Level Generator needs to be set to “Finite”.

![](./pages/getting_started/_images/067.png)

This generator uses a user-defined path which is then broken into pieces and
fed to all segments. The user-defined path is a spline which can either be Hermite,
Bezier, BSPline or Linear. The path can either be created in the editor, defined by
code during runtime or a combination of the two – user-defined with runtime
randomization.

To create a path for the Custom Path Generator, select the generator in the
project tab and focus the origin of the scene in the scene view. Click the “Create
Point” button twice to create two spline points and move the second one away in
the scene view.

Created points can be selected by clicking on them in the scene view or using
the dropdown menu inside the custom path panel.
If the path has 4 or more created points, then it can be set to loop. Looped
paths are useful for the generation of racing tracks.

Deleting points is done using the X button next to the selected point in the inspector

![](./pages/getting_started/_images/072.png)

![](./pages/getting_started/_images/073.png)

Segments, generated using the Custom Path Generator will get stretched
along the generated spline path so it is important to set the segment count in the
Level Generator properly.

### 1.5.4 Spiral Path Generator

The Spiral Path Generator does what its name suggests – it
generates a spiral path. It is simple to set up as it only requires a few
properties:

![](./pages/getting_started/_images/076.png)

- Axis – The axis, around which the spiral will revolve
- Turn Rate – turn rate of the axis per generated spline point. More points per segment
    will make a sharper turn
- Steepness – how steep the spiral is in degrees
- Normal Rotation – rotates the normal of the points around the spiral. For example,
    in order to produce a wall-ride type of game, set the normal rotation to either -90 or
    90 and the entire surface will rotate

### 1.5.5 Wavy Path Generator

The Wavy Path Generator generates a zig-zag type of path
going in one general direction.

![](./pages/getting_started/_images/078.png)

The Angle property defines how much the path will be deviating from the
initial direction and the Turn Rate defines at what rate this will be happening. Higher
turn rate means sharper turns.

The Turn Axis property defines the axis around the turns will be made. A turn
axis of 0,1,0 means the path will make turns around the Y axis and will be flat.

![](./pages/getting_started/_images/079.png) ![](./pages/getting_started/_images/080.png)

## 1.6. Gameplay

Forever provides a package of behaviors for following the generated level segments out
of the box. These are components which take advantage of the system’s API and make it
easy to set up a working prototype within minutes with only some simple coding needed to
control the offset of the player while following.

### 1.6.1. The Runner Component

There are three Runner components in Forever – Runner,
LaneRunner and CustomLaneRunner. Both LaneRunner and
CustomLanerRunner derive from Runner and add lane switching
functionality.

To create a Runner,
add the Runner component to
the Player object or another
existing object in the scene.
The Runner component is
found in the component
menu as “Basic Runner”.

![](./pages/getting_started/_images/084.png)

All Runners have a
Follow Speed property as well as a “Follow” toggle which defines if the Follower
should run the logic for following.

Only the Player object should have the Is Player property toggled as it defines
whether or not the Runner should notify the Level Generator of its presence. The
Runner which has the Is Player property set to true, will cause the Level Generator
to create new segments as it follows.

Physics Mode defines the target which will receive the follow result –
Transform, Rigidbody or Rigidbody2D

Start Mode defines where the runner will start along the entire available
generated level. Available modes are Percent, Distance and Project.

- Percent defines a percent [0-1] along the level where the Runner will
    start.
- Distance calculates the distance in world units from the start of the
    generated level to the point where the Runner should start
- Project will attempt to find the closest available point along the
    generated level and start the Runner from there

The Motion foldout controls how the running is applied to the target. If Apply
Motion is not toggled, the follow result will not be applied to the target at all.
However, the following logic will still run and the Runner’s public result property
which holds the follow information will update. This is useful if applying the motion
needs to happen in another component.

![](./pages/getting_started/_images/088.png)

There is a position offset and a rotation offset property. Both work locally to
the current follow result meaning that X is always perpendicular to the current
segment path and Y always aligns with the path normal.

To implement the player’s sideways movement, these two offsets need to be
modified with a script.

### 1.6.2 Laner Runner

The Lane Runner is an abstraction of the Runner and adds lane
switching functionality without the need of additional programming.

The Lane Runner adds additional parameters for defining the
lanes. It uses the **Width** property to define the total span of the lanes and the **Lane
Count** property to define how
many lanes there are. For
example, if the Width is set to 3
and the Lane Count is set to 3,
this means that there will be
three lanes, each one with the
width of 1 unit. Start Lane
defines which of the lanes will be
the player’s starting lane and
Lane Switch speed along with
the Lane Switch Speed Curve
define the speed at which the
Runner will switch between the
lanes.

![](./pages/getting_started/_images/090.png)

The Lane Runner has a property called the **Lane Vector**. It is a 2D Vector which
defines the direction of the lanes in relation to the generated path. A lane vector of
(1, 0) defines horizontal lanes and a vector of (0, 1) defines vertical lanes.

If **Use Custom Paths** is toggled, the Lane Runner will look for custom paths,
defined in the current Level Segment and will use them instead of the artificial lanes.
The percent along the custom paths will be mapped to the percent along the current
segment [0-1] so if the custom paths have a more irregular shape, the movement
along them using the Lane Runner might look unnatural. If the Level Segment does
not have custom paths defined, then it will fall back to using artificial lanes.

To switch between lanes during runtime, the “ **lane** ” property of the Lane
Runner needs to be set [1 – Lane Count]. The Lane Runner will automatically switch
to the target lane.

### 1.6.3 Custom Lane Runner

The Custom Lane Runner is entirely meant to follow custom
paths (see Custom Level Segment Paths). It requires the Level Segments
to have at least one defined custom path and will follow with a uniform
speed.

This Runner is most suitable where complex level design is required and each
segment has unique passages which need to be traced accurately.

### 1.6.4 The Player Projector Component

The Player Projector is a simple component which does not affect
the object’s behavior in any way. Its purpose is to continuously project
the player along the generated level and notify the Level Generator if new
segments need to be created.

If a free player movement (for example physics or character controller) is
what the project is going for, this component should be attached to the Player
object.

Note that this component should not be attached to player objects which
have the Runner components – the Runner components notify the Level Generator
automatically.

### 1.6.5 Floating Origin

In games, if an object moves too far away from the origin of the
scene (in other words, its coordinate values are big numbers), things can
start to jitter. This happens because of the floating point limitation – the
bigger the floating point number is, the less digits it can have after its
decimal point.

To counter this issue, games use a trick called “Floating Origin”. If the player
along with the camera moves outside of a given coordinate range, all objects in the
game are offset so that the camera’s coordinates become 0,0,0. The player does not
notice anything because the whole game world moves with them but the
coordinates of the player and its camera are reset.

Forever offers a simple solution to that problem – the Floating Origin
component. The Floating Origin should be used in infinite runner games to prevent
the aforementioned issue. It automates the entire process and is integrated with the
Forever, sending offset events to all segments to notify them to update their values.

To use the Floating Origin, add it to the Level Generator object and add a
reference to the player camera. If the camera is not referenced, the Floating Origin
will attempt to find it automatically. Everything which needs to be offset, needs to
be put inside the Level Generator (usually this is the Player and the Camera).

## 1.7. Basic API Use

In order to use the Forever API, the Forever namespace needs to be included:

```csharp
using UnityEngine;
using Dreamteck.Forever;
```

This will give access to all Forever components like the Level Generator, Level
Segment, the Runners and the Builders.

### 1.7.1 Using the Level Generator in Code

The Level Generator is a singleton and can be reached from anywhere using
LevelGenerator.instance.
There are two main basic commands for the Level Generator:
LevelGenerator.instance.StartGeneration();
and LevelGenerator.instance.Clear();

```StartGeneration``` will start the generation sequence if Build on Awake is not
set. Clear will stop the generation process and remove all generated segments.

```LevelGenerator.instance.ready``` can be used to check if the initial generation
phase of the Level Generator is done – in other words if the game is ready to start.

```LevelGenerator.instance.segments``` is a list of all currently existing level
segments in the order of generation – the first being the oldest one and the last being
the newest one.

```LevelGenerator.instance.levels``` is the list of all defined levels.

```LevelGenerator.instance.currentLevel``` is a reference to the current level
used by the Level Generator. An example use of this would be to skip sequences. If
the current level has a single sequence which is set to be endless, the sequence can
be stopped like this:

```csharp
LevelGenerator.instance.currentLevel.sequenceCollection.sequences[0].Stop();
```

Then the next sequence / level can start.

#### 1.7.1.1 Evaluating the Generated Level

The generated level path can be evaluated at any given point using
the Evaluate method. In order to do so, the Dreamteck.Splines namespace
needs to be included. In order to get the Evaluation result, a SplineResult
object needs to be defined first. Then this object needs to be passed to the
evaluate method.

The Evaluate method uses a percent [0-1] which defines the point
along the generated path that will be evaluated, 0 being the beginning of the
path and 1 being the ending (the last current segment).

```csharp
SplineResult evalResult = new SplineResult();
protected override void Start()
{
    LevelGenerator.instance.Evaluate(0.5, evalResult);
    Debug.Log("Evaluated the middle of the segment - > " + evalResult.position);
}
```

The ```SplineResult``` object contains the position, direction, normal, size,
color and percent along the level generated path.

#### 1.7.1.2 Finding the Closest Point Along the Level

It is possible to find the nearest point along the level to an existing
point in world space. The Project method of the LevelGenerator does exactly
that and the result of the projection is a SplineResult object, like with the
Evaluate method.

```csharp
LevelGenerator.instance.Project(worldPosition, evalResult);
Debug.Log("Closest point along the path is at percent " + evalResult.percent + " and at position " + evalResult.position);
```

Furthermore, this result can be used to find the level segment that
contains this projected point using the GlobalToLocalPercent method. This is
a method which a global percent [0-1] along the entire generated level into
a segment index and a percent [0-1] local to the segment with that index:

```csharp
SplineResult evalResult = new SplineResult();
LevelGenerator.instance.Project(worldPosition, evalResult);
int segmentIndex;
LevelGenerator.instance.GlobalToLocalPercent(evalResult.percent, out segmentIndex);
Debug.Log(LevelGenerator.instance.segments[segmentIndex].name);
```

#### 1.7.1.3 Find Point Along \*Distance\*
Since the entire level path is a collection of separate spline paths, this
means that it is not possible to easily calculate the evaluation percent for a
point that is located N units away from the point at a given percent along the
path. The length of the splines does not correspond to the percent values.
Both a 2-meter path and a 10 - meter will evaluate between [0-1] so if there
are shorter and longer segments, it gets very hard to find the proper distance
to move along the spline with.

Fortunately, Forever offers the **Travel** method. It takes a starting
point along the entire path (percent [0-1]) and a distance in world units to
travel along the paths. The returned result is the percent of the new point:

```csharp
SplineResult evalResult = new SplineResult();
double percent = LevelGenerator.instance.Travel(0.0, 10f, Spline.Direction.Forward);
LevelGenerator.instance.Evaluate(percent, evalResult);
```

### 1.7.2 Using the Level Segment in Code

The Level Segment component comes with a lot of the methods that
the LevelGenerator has in terms of path operations. The Evaluate, Project
and Travel methods are both present and work in the same way the ones in
LevelGenerator do. The LevelGenerator ones, however, operate only on the
path of the given segment so the [0-1] range encapsulates only the path of
the given segment.

```csharp
SplineResult evalResult = new SplineResult();
LevelSegment segment = LevelGenerator.instance.segments[0];
segment.Evaluate(0.5, evalResult);
```

For Level Segments which have custom paths defined, it is also
possible to perform the same operations on any given custom path. Custom
paths can be accessed through the customPaths array:

```csharp
LevelSegment segment = LevelGenerator.instance.segments[0];
segment.customPaths[0].Project(worldPoint, evalResult);
```

If neither the Runner components nor the ProjectedPlayer
component is used for the player object, the Enter method of a segment
needs to be called when a player enters the segment. This is very important
because this lets the Level Generator know where the player is and if new
segments need to be created / old - destroyed.

```csharp
SplineResult evalResult = new SplineResult();
LevelSegment lastSegment;
private void Update()
{
    if (LevelGenerator.instance == null) return;
    if (!LevelGenerator.instance.ready) return;
    LevelGenerator.instance.Project(transform.position,evalResult);
    int segmentIndex = 0;
    LevelGenerator.instance.GlobalToLocalPercent(evalResult.percent, out segmentIndex);
    LevelSegment current = LevelGenerator.instance.segments[segmentIndex];
    if(current != lastSegment)
    {
        current.Enter();
        lastSegment = current;
    }
}
```

### 1.7.3 Using the Runners in Code

Referencing a Runner is done the same way any other component is
referenced in Unity - ```Runner runner = GetComponent<Runner>();``` Once referenced, all
the properties that are available in the inspector are also available in code under the
same names. For example, ```runner.followSpeed = 5f;```

For Runners which are not set to automatically, start, the ```StartFollow()```
method needs to be called. This method has two overrides:

- ```StartFollow()``` – will automatically start following by using the defined
    Start Mode
- ```StartFollow(LevelSegment segment, double percent)``` – will start
    following the given segment at the given local percent
To bind player input to the basic runner (the Runner component), assign a
Vector2 value to the runner’s motion.offset and rotationOffset properties:

```csharp
runner.motion.offset = new Vector2(strafe, 0f);
runner.motion.rotationOffset = new Vector3(0f, 0f, roll);
```

#### 1.7.3.1 LaneRunner
The Lane Runner introduces the lane property. Changing the lane
property will change the lane of the runner.

This is an example of controlling the lane property using the arrow
keys:

```csharp
LaneRunner runner = GetComponent<LaneRunner>();
if (Input.GetKeyDown(KeyCode.LeftArrow)) runner.lane--;
if (Input.GetKeyDown(KeyCode.RightArrow)) runner.lane++;
```

#### 1.7.3.2 CustomLaneRunner
The Custom Lane Runner component also has the lane property
which points to the current custom path that the runner should follow. The
lane property of the Custom Lane Runner, however, might change
automatically if the runner enters a segment with less lanes than the previous
and the lane property is set to a higher value than the amount of lanes.
