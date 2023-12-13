# API Reference

## 5.1 SplinePoint

Representation of a control point. SplinePoint is used to define Splines. Passing a new
array of points to a spline changes the spline.

### 5.1.1 Public Enumerations

| Code                         | Description |
| :--------------------------- | :---------- |
| ```Type {Smooth, Broken, FreeSmooth}``` |  Smooth mirrors the tangents, Broken makes the tangents independent and FreeSmooth is a combination of the two|

### 5.1.2 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```SplinePoint.Type```| type The type of the point Vector3 position The position of the point |
| ```Color ```| color The color of the point |
| ```Vector3 normal```| The normal direction of the point |
| ```float size```| The size of the point |
| ```Vector3 tangent```| The first tangent position for Bezier splines |
| ```Vector3 tangent2```| The second tangent position for Bezier splines |

### 5.1.3 Public Methods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```void SetPosition(Vector3 pos)``` | Sets the position of the point and moves its tangents too |
| ```void SetTangentPosition(Vector3 pos)``` | Sets the tangent position of the point |
| ```void SetTangent2Position(Vector3 pos)``` | Sets the second tangent’s position of the point |

### 5.1.4 Static Methods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```SplinePoint Lerp(SplinePoint a, SplinePoint b, float t)``` | Interpolation between two spline points |

### 5.1.5 Constructors

| Code                         | Description |
| :--------------------------- | :---------- |
| ```SplinePoint(Vector3 p)``` | Creates a new smooth point |
| ```SplinePoint(Vector3 p, Vector3 t)``` | Creates a new smooth point |
| ```SplinePoint(Vector3 pos, Vector3 tan, Vector3 nor, float s, Color col)``` | Creates a new fully defined smooth point |
| ```SplinePoint(Vector3 pos, Vector3 tan, Vector3 tan2, Vector3 nor, float s, Color col)``` | Creates a new fully defined smooth point |

## 5.2 SplineResult

When a spline is evaluated, multiple values are returned. This is the result of spline
evaluation.

### 5.2.1 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```Vector3 position``` | The position of the evaluation result |
| ```Vector3 normal``` | The normal of the evaluation result |
| ```Vector3 direction``` | The direction of the evaluation result |
| ```Color color``` | The color of the evaluation result |
| ```float size``` | The size of the evaluation result |
| ```double percent``` | The time (0-1) the spline was evaluated at |

### 5.2.2 Read-Only Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```Quaternion rotation``` | Returns ```Quaternion.LookRotation(direction, normal)``` |
| ```Vector3 right``` | Returns a perpendicular vector to direction and normal |

### 5.2.3 Public Methods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```void Lerp(SplineResult b, double t)``` | Interpolates between the current values and b’s values |
| ```void Lerp(SplineResult b, float t)``` | Interpolates between the current values and b’s value|
| ```void CopyFrom(SplineResult input)``` | Copies the values of the input SplineResult object |

### 5.2.4 Static Methods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```SplineResult Lerp(SplineResult a,SplineResult b, double t)``` | Interpolates between two spline results |
| ```SplineResult Lerp(SplineResult a, SplineResult b, float t)``` | Interpolates between two spline results |
| ```void Lerp(SplineResult a, SplineResult b, double t, SplineResult target)``` | Interpolates between two spline results and stores the result in the target spline result |
| ```void Lerp(SplineResult a, SplineResult b, float t, SplineResult target)``` | Interpolates between two spline results and stores the result in the target spline result |

## 5.3. Spline

A spline in world space. This class stores a single spline and provides methods for
evaluation, length calculation, raycasting and more.

### 5.3.1 Example

```csharp
using UnityEngine;
using System.Collections;
using Dreamteck.Splines; //Include the Splines namespace
public class SimpleSplineController : MonoBehaviour {
    void Start () {
        //Create a new B-spline with precision 0.9
        Spline spline = new Spline(Spline.Type.BSpline, 0.9);
        //Create 3 control points for the spline
        spline.points = new SplinePoint[3];
        spline.points[0] = new SplinePoint(Vector3.left);
        spline.points[1] = new SplinePoint(Vector3.up);
        spline.points[2] = new SplinePoint(Vector3.right);
        //Evaluate the spline and get an array of values
        SplineResult[] results = new SplineResult[spline.iterations];
        spline.Evaluate(results);
        //Display the values in the editor
        for (int i = 0; i < results.Length; i++)
        {
            Debug.DrawRay(results[i].position, results[i].normal,
            results[i].color);
        }
    }
}
```

### 5.3.2 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```SplinePoint[] points```| The control points of the spline |
| ```Spline.Type type``` | The type of the spline which defines what interpolation should be used. |
| ```double precision``` | The approximation rate (0-0.9999) of the spline |
| ```AnimationCurve customValueInterpolation``` | Custom curve for size and color interpolation between points |
| ```AnimationCurve customNormalInterpolation``` | Custom curve for normal interpolation between points |

### 5.3.3 Read-Only Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```bool isClosed``` | Whether or not the spline is closed |
| ```double moveStep``` | The step size of the percent incrementation when evaluating a spline (based on percision) |
| ```int iterations``` | The total count of samples for the spline (based on the precision) |

### 5.3.4 Public Methods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```SplineResult Evaluate(double percent)``` | Whether or not the spline is closed |
| ```void Evaluate(ref SplineResult[] samples, double from = 0.0, double to = 1.0)``` | The step size of the percent incrementation when evaluating a spline (based on percision) |
| ```Vector3 EvaluatePosition(double percent)``` | The total count of samples for the spline (based on the precision) |
| ```void EvaluatePositions(Vector3[] positions, double from = 0.0, double to = 1.0)``` | Evaluates the spline using its precision and writes the result positions to the array. |
| ```float CalculateLength(double from = 0.0, double to = 1.0, double resolution = 1.0)``` | Calculates the length of the spline |
| ```double Project(Vector3 point, int subdivide = 4, double from = 0.0, double to = 1.0)``` | Projects a point on the spline. Returns evaluation percent. |
| ``bool Raycast(out RaycastHit hit, out double hitPercent, LayerMask layerMask, double resolution = 1.0, double from = 0.0, double to = 1.0, QueryTriggerInteraction hitTriggers = QueryTriggerInteraction.UseGlobal)`` | Casts rays along the spline against all colliders in the scene |
| ```bool RaycastAll(out RaycastHit[] hits, out double[] hitPercents, LayerMask layerMask, double resolution = 1.0, double from = 0.0, double to = 1.0, QueryTriggerInteraction hitTriggers = QueryTriggerInteraction.UseGlobal)``` | Casts rays along the spline against all colliders in the scene and returns all hits. Order is not guaranteed. |
| ```double Travel(double start, float distance, Direction direction)``` | Returns the percent from the spline at a given distance from the start point |
| ```void Close()``` | Closes the spline (requires the spline to have at least 4 points) |
| ```void Break()``` | Breaks the closed spline |
| ```void Break(int at)``` | Breaks the closed spline at a given point |
| ```void ConvertToBezier()``` | Converts the spline from Hermite to Bezier without losing its initial shape. |

### 5.3.5 Constructors

| Code                         | Description |
| :--------------------------- | :---------- |
| ```Spline(Type t)``` | Creates a spline of a given type |

## 5.4 LevelSegment

A MonoBehaviour component for defining level segments. It holds information for

### 5.4.1 Public Enumerations

| Code                         | Description |
| :--------------------------- | :---------- |
| ```Type { Extruded, Custom }``` | Extruded type means the segment will be extruded along a path and a Custom type will not modify the segment |
| ```Axis { X, Y, Z }``` | The Axis along which the extrusion takes place |
| ```EvaluateMode { Cached, Accurate }``` | Evaluation mode for the path operations. Cached uses the cached samples and Accurate calculates new values on the fly |
| ```GenerationState { Idle, Free, Extruding, Preparing, Destroying }``` | Generation state description for the level generation process. |

### 5.4.2 Public Delegates

| Code                         | Description |
| :--------------------------- | :---------- |
| ```void SegmentEnterHandler(LevelSegment segment)``` | A handler for when a segment is entered. |
| ```delegate void EmptyHandler()``` | An empty handler |

### 5.4.3 Events

| Code                         | Description |
| :--------------------------- | :---------- |
| ```SegmentEnterHandler onSegmentEntered``` | Static event, called when a segment is entered. |
| ```EmptyHandler onActivate``` | Called when the segment is activated |
| ```EmptyHandler onEnter;``` | Called when the segment is entered |

### 5.4.4 Read-Only Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```bool activated``` | Is the segment activated |
| ```bool isReady``` | Is the segment extruded and ready to be played? |

### 5.4.5 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```Type type = Type.Extruded``` | The type of the segment. |
| ```Transform customEntrance``` | A Transform entrance for the entry point of a custom level segment |
| ```Transform customExit``` | A Transform entrance for the exit point of a custom level segment |
| ```bool customKeepUpright``` | Should this segment be kept upright if custom? |
| ```LevelSegmentCustomPath[] customPaths``` | The array of custom paths, defined for this level segments |
| ```ObjectProperty[] objectProperties``` | The array, containing all registered children and their extrusion properties if the type is set to Extrude |
| ```int customMainPath``` | Index, pointing to the custom path that is considered to be the main path for the segment. (For example, the middle one – 1) |
| ```Axis axis``` | Axis of extrusion |
| ```SplineResult[] samples``` | The array of path samples used by the Evaluate method and etc. |
| ```Spline spline``` | The spline, constructed for this level segment (this is the extrusion path). |
| ```int sampleRate``` | Number of spline samples between two control points |
| ```LevelSegment next``` | Reference to the next Level Segment |
| ```LevelSegment previous``` | Reference to the previous Level Segment |
| ```Level level``` | Reference to the level this segment belongs  |
| ```bool stitch``` | Should the segment be stitched seamlessly to the previous one when the type is set to Extrude? |

### 5.4.6 Public Methods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```TS_Bounds GetBounds()``` | Whether or not the spline is closed |
| ```void Destroy()``` | The step size of the percent incrementation when evaluating a spline (based on percision) |
| ```void DestroyImmediate()``` | The total count of samples for the spline (based on the precision) |
| ```void AddCustomPath(string name)``` | Add a new custom path with a name |
| ```void RemoveCustomPath(int index)``` | Remove a custom path at index |
| ```void Evaluate(double percent, SplineResult result, EvaluateMode mode = EvaluateMode.Cached)``` | Evaluate the level segment’s path and return a result. |
| ```Vector3 EvaluatePosition(double percent, EvaluateMode mode = EvaluateMode.Cached)``` | Evaluate the level segment’s path and return only the position. |
| ```double Travel(double start, float distance, Spline.Direction direction, out float traveled, EvaluateMode mode = EvaluateMode.Cached)``` | Returns the percent from the level segment’s path at a given distance from the start point |
| ```void Project(Vector3 point, SplineResult result, double from = 0.0, double to = 1.0, EvaluateMode mode = EvaluateMode.Cached)``` | Finds the closest result to a world point along the level segment’s path |
| ```void Enter()``` | Enter the segment and let the Level Generator know. |

## 5.5 LevelSegment.LevelSegmentCustomPath

A class that holds the definition and cache for a custom path inside the Level Segment.
During runtime, operations like Evaluate, Travel and Project can be performed on it.

### 5.5.1 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```string name``` | The name of the path |
| ```Color color``` | The path color |
| ```SplinePoint[] localPoints``` | The path’s points, local to the Level Segment’s root |

### 5.5.2 Read-Only Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```Spline spline``` | A reference to the path’s spline |
| ```SplineResult[] samples``` | The cached spline samples of the path |

### 5.5.3 Public Methods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```void Evaluate(double percent, SplineResult result)``` | Evaluate the path at a given percent and write to the SplineResult object |
| ```Vector3 EvaluatePosition(double percent)``` | Evaluate the path at a given percent and return only the position |
| ```double Travel(double start, float distance, Spline.Direction direction, out float traveled)``` | Find the percent along the path that is *distance* away from the starting percent |
| ```void Project(Vector3 point, SplineResult result, double from = 0.0, double to = 1.0)``` | Find the closest result to a world point along the path. |
| ```void Transform()``` | Transform all local points and write the results into the paths’ spline |
| ```void InverseTransform()``` | Inverse transform the path’s spline and write the results into the local points array |
| ```void CalculateSamples()``` | Calculate the samples and cache them |
| ```LevelSegmentCustomPath Copy()``` | Duplicate the path |

## 5.6 LevelPathGenerator

The base class for generating paths. It is not used directly by the Level Generator and
needs to be inherited. It provides the framework for generating the paths.

### 5.6.1 Enumerations

| Code                         | Description |
| :--------------------------- | :---------- |
| ```PathType { Bezier = Spline.Type.Bezier, Linear = Spline.Type.Linear}``` | The type of the path – Bezier or Linear |

### 5.6.2 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```PathType pathType``` | The type of the path |
| ```int sampleRate``` | How many samples per point |
| ```int controlPointsPerSegment``` | How many control points per segment will be generated? |
| ```bool customNormalInterpolation``` | Should custom normal interpolation be used? |
| ```AnimationCurve normalInterpolation``` | The custom normal interpolation curve |
| ```bool customValueInterpolation``` | Should custom size and color interpolation be used? |
| ```AnimationCurve valueInterpolation``` | The custom value interpolation curve |

### 5.6.3 Public Methods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```void GeneratePath(LevelSegment inputSegment)``` | Generate the path for the given segment |

## 5.7 HighLevelPathGenerator

An abstraction of the LevelPathGenerator class allowing for simpler path generation
code. It is not used directly and provides an abstraction of the LevelPathGenerator
framework.

### 5.7.1 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```bool useCustomNormalDirection``` | Should the normals of all generated points be overridden? |
| ```Vector3 customNormalDirection``` | The custom normal direction to override with |
| ```Vector3 startOrientation``` | Start orientation of the path generation |

## 5.8 RandomPathGenerator

A Path Generator class used for random path generation. It provides a lot of settings to
help cover a wide range of use cases.

### 5.8.1 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```bool usePitch``` | Should pitch be randomized? |
| ```bool useYaw``` | Should yaw be randomized? |
| ```bool useRoll``` | Should roll be randomized? |
| ```bool useColors``` | Should colors be randomized? |
| ```bool useSizes``` | Should sizes be randomized? |
| ```bool restrictPitch``` | Should pitch angles be restricted within a certain range? |
| ```bool restrictYaw``` | Should yaw angles be restricted within a certain range? |
| ```bool restrictRoll``` | Should roll angles be restricted within a certain range? |
| ```Vector3 minOrientation``` | The minimum allowed angles when restriction is on |
| ```Vector3 maxOrientation``` | The maximum allowed angles when restriction is on |
| ```Vector3 minRandomStep``` | Minimum target angle change |
| ```Vector3 maxRandomStep``` | Maximum target angle change |
| ```Vector3 minTurnRate``` | Minimum angle move rate |
| ```Vector3 maxTurnRate``` | Maximum angle move rate |
| ```Vector3 startTargetOrientation``` | The initial target angle when generation starts |
| ```bool useStartPitchTarget``` | Should pitch use the startingOrientation? |
| ```bool useStartYawTarget``` | Should yaw use the startingOrientation? |
| ```bool useStartRollTarget``` | Should roll use the startingOrientation? |
| ```Gradient minColor``` | Minimum gradient for color randomization |
| ```Gradient maxColor``` | Maximum gradient for color randomization |
| ```AnimationCurve minSize``` | Minimum curve for size randomization |
| ```AnimationCurve maxSize``` | Maximum curve for size randomization |
| ```Vector3 minSegmentOffset``` | Minimum offset between segments |
| ```Vector3 maxSegmentOffset``` | Maximum offset between segments |
| ```Space segmentOffsetSpace``` | The transformation space of the offset coords |
| ```Vector3 newLevelMinOffset``` | Minimum offset between segments of different levels |
| ```Vector3 newLevelMaxOffset``` | Maximum offset between segments of different levels |
| ```Space levelOffsetSpace``` | The transformation space of the offset coords |

## 5.9 CustomPathGenerator

A Path Generator class that uses a pre-defined path as a base and generates the level
path along that path.

### 5.9.1 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```bool loop``` | Should the path be looped (requires at least 4 control points)? |
| ```SplinePoint[] points``` | The control points of the path |
| ```Spline.Type customPathType``` | The type of the path |

## 5.10 SpiralPathGenerator

A simple Path Generator class that generates a path in a spiral.

### 5.10.1 Enumerations

| Code                         | Description |
| :--------------------------- | :---------- |
| ```enum Axis { X, Y, Z, NegativeX, NegativeY, NegativeZ }``` | The axis for the spiral generation. |

### 5.10.2 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```Axis axis``` | The axis for the spiral generation. |
| ```float turnRate``` | Spin rate per generated path point |
| ```float steepness``` | Steepness angle of the spiral |
| ```float normalRotation``` | Surface direction rotation around the spiral path |

## 5.11 WavyPathGenerator

A simple Path Generator class that produces a wavy path.

### 5.11.1 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```float angle``` | The maximum deviation angle for the wave |
| ```float turnRate``` | The turn rate towards the deviation |
| ```Vector3 turnAxis``` | The turn axis around the path will turn |

## 5.12 LevelGenerator

The Level Generator component responsible for the entire level generation process. It
holds a reference to all defined levels and a list of all currently present segments in the
scene.

### 5.12.1 Enumerations

| Code                         | Description |
| :--------------------------- | :---------- |
| ```Type { Infinite, Finite }``` | The type of the level generator – infinite or finite |
| ```LevelIteration { OrderedClamp, OrderedLoop, Random, None }``` | Iteration mode for the generator’s levels |

### 5.12.2 Events

| Code                         | Description |
| :--------------------------- | :---------- |
| ```LevelEnterHandler onLevelEntered``` | Called when a new level is entered by the player |
| ```LevelEnterHandler onLevelLoaded``` | Called when a new level is loaded by the generator |
| ```LevelEnterHandler onWillLoadLevel``` | Called when a new level is about to be loaded by the generator |

### 5.12.3 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```static LevelGenerator instance``` | The current active instance of the Level Generator (LevelGenerator.instance) |
| ```int generateSegmentsAhead``` | How many segments to generate ahead of the player when in Infinite mode? |
| ```int activateSegmentsAhead``` | How many segments to activate ahead of the player when in Infinite mode? |
| ```int maxSegments``` | Maximum segments that can exist in the scene in Infinite mode |
| ```bool buildOnAwake``` | Should the Level Generator start building as soon as it is awake? |
| ```Type type``` | The type of the Level Generator |
| ```int finiteSegmentsCount``` | The segment count when in Finite mode |
| ```bool finiteLoop``` | Is the level path looped in Finite mode (e.g. racing track) |
| ```Level[] levels``` | The array of defined levels |
| ```LevelIteration levelIteration``` | How levels will be iterated through? |
| ```int startLevel``` | The starting level of the Level Generator |

### 5.12.4 Public Methods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```void StartGeneration()``` | Enable the generator and set it to start working |
| ```void Clear()``` | Disable the generator and clear all created segments |
| ```void Restart()``` | Restart the generator |
| ```LevelSegment GetSegmentAtPercent(double percent)``` | Get the level segment at the current percent [0-1] |
| ```LevelSegment FindSegmentForPoint(Vector3 point)``` | Find the segment the given world point is closest to |
| ```void Evaluate(double percent, SplineResult result)``` | Evaluate the entire level and write the result into the SplineResult object |
| ```Vector3 EvaluatePosition(double percent)``` | Evaluate the entire level and return only the position along the path |
| ```void Project(Vector3 point, SplineResult result)``` | Project a world point onto the level path |
| ```double Travel(double start, float distance, Spline.Direction direction)``` | Find the percent along the entire level that is *distance* away from the starting percent |
| ```void CreateNextSegment()``` | Force the level generator to generate another segment |
| ```void DestroySegment(int index)``` | Destroy an existing level segment by index |

## 5.13 Level

A definition of a level in the game. It holds a Sequence Collection which in terms holds
one or more sequences inside and each sequence holds a collection of level segments.

### 5.13.1 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```bool enabled``` | Is this level enabled? |
| ```string title``` | The title of the level |
| ```SegmentSequenceCollection sequenceCollection``` | The sequence collection |
| ```bool remoteSequence``` | Is this level using a remote sequence? |
| ```string remoteSceneName``` | The scene name to load for the remote sequence |
| ```ThreadPriority loadingPriority``` | Loading priority for the remote sequence |

### 5.13.2 Read-Only Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```bool isReady``` | Is the level loaded and ready? |
| ```SegmentSequence[] sequences``` | A shorthand for the sequences of the level (equal to sequenceCollection.sequences) |

### 5.13.3 Public Methods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```void Initialize()``` | Initialize the level for generation |
| ```bool IsDone()``` | Are all sequences inside the level done? |
| ```void SkipSequence()``` | Skip the current sequence |
| ```void GoToSequence(int index)``` | Skip the current sequence and go to another sequence directly |
| ```LevelSegment InstantiateSegment(Vector3 position, Quaternion rotation)``` | Instantiate the next segment from the current sequence |
| ```Level Duplicate()``` | Return a deep copy of the level |

## 5.14 Level.Sequence

A collection of level segments.

### 5.14.1 Enumerations

| Code                         | Description |
| :--------------------------- | :---------- |
| ```Type { Ordered, Random, Custom }``` | How the sequence shuffles the segments |

### 5.14.2 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```bool enabled``` | Is the sequence enabled? |
| ```string name``` | The name of the sequence |
| ```SegmentDefinition[] segments``` | The segments of the sequence |
| ```int spawnCount``` | How many segments should the sequence spawn in random mode? |
| ```Type type``` | The type of the sequence |
| ```SegmentShuffle customShuffle``` | Custom segment Shuffle object for Custom sequences |
| ```bool preventRepeat``` | Should the sequence prevent repeating segments in random mode? |

### 5.14.3 Public Mehtods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```void Initialize()``` | Initialize the sequence for generation |
| ```void Stop()``` | Stop the sequence |
| ```SegmentDefinition NextDefinition()``` | Pull the next segment out of the sequence |
| ```bool IsDone()``` | Is the sequence done? |
| ```SegmentSequence Duplicate()``` | Create a deep copy of the sequence |

## 5.15 Level.SegmentSequenceCollection

A collection of sequences.

### 5.15.1 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```SegmentSequence[] sequences``` | The sequence array of this collection |

## 5.16 Level.SegmentDefinition

A definition for a level segment used inside the Level.Sequence. It holds a reference to
a level segment prefab or another sequence (nested sequence).

### 5.16.1 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```GameObject prefab``` | The sequence array of this collection |
| ```bool nested``` | Is this definition a nested sequence? |
| ```SegmentSequence nestedSequence``` | The nested sequence for the definition if nested |

### 5.16.2 Public Methods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```LevelSegment Instantiate(Vector3 pos, Quaternion rot)``` | Instantiate the segment prefab |
| ```SegmentDefinition Duplicate()``` | Return a deep copy of the definition |

### 5.16.3 Constructors

| Code                         | Description |
| :--------------------------- | :---------- |
| ```SegmentDefinition()``` | Create as new empty definition |
| ```SegmentDefinition(string nestedName)``` | Create a definition with a nested sequence |
| ```SegmentDefinition(GameObject input)``` | Create a definition with a level segment prefab |

### 5.17 Runner

The basic Runner component used to traverse the generated levels.

### 5.17.1 Enumerations

| Code                         | Description |
| :--------------------------- | :---------- |
| ```UpdateMode { Update, FixedUpdate, LateUpdate }``` | In which method should the update logic run? |
| ```PhysicsMode { Transform, Rigidbody, Rigidbody2D }``` | What to apply the running result to? |
| ```StartMode { Percent, Distance, Project }``` | How does the Runner find its starting point? |

### 5.17.2 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```double startPercent``` | The starting percent along the level path if StartMode is set to Percent |
| ```UpdateMode updateMode``` | |
| ```float startDistance``` | The starting distance along the level path if StartMode is set to Distance |
| ```StartMode startMode``` | The start mode |
| ```float followSpeed``` | The follow speed of the runner |
| ```bool follow``` | Should the following logic be executed? |
| ```bool isPlayer``` | Is this the player object? |
| ```PhysicsMode physicsMode``` | The Physics mode of the runner |

### 5.17.3 Read-Only Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```MotionModule motion``` | The motion module of the runner which defines how transformations are applied |
| ```SplineResult result``` | The current result of the runner along the level path |

### 5.17.4 Public Methods

| Code                         | Description |
| :--------------------------- | :---------- |
| ```void StartFollow()``` | Start following using the defined start mode |
| ```void StartFollow(LevelSegment segment, double percent)``` | Start following using the given segment at the given percent |

## 5.18 LaneRunner : Runner

An abstraction of the Runner component, which introduces a simple lane switching
behavior which is easy to use in code.

### 5.18.1 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```float width``` | The width of all lanes |
| ```int laneCount``` | Lane count |
| ```int startLane``` | The start lane [1-laneCount] |
| ```float laneSwitchSpeed``` | How fast does the runner go from lane to lane |
| ```bool useCustomPaths``` | Should the runner use custom segment paths when available? |
| ```AnimationCurve laneSwitchSpeedCurve``` | Easing for the lane switching speed |
| ````Vector2 laneVector```` | Lane vector which defines the direction of the road. 1, 0 is a horizontal road. |
| ```int lane``` | The current lane [1-laneCount] |

## 5.19 CustomLaneRunner : Runner

An abstraction of the Runner component which allows for following custom segment
paths with uniform speed.

| Code                         | Description |
| :--------------------------- | :---------- |
| ```int lane``` | The current lane |
| ```float laneSwitchSpeed``` | How fast does the runner traverse from lane to lane? |

## 5.20 ProjectedPlayer

A passive component which continuously projects the player’s position onto the
generated level and lets the Level Generator know where the player is at any given time.

### 5.20.1 Enumerations

| Code                         | Description |
| :--------------------------- | :---------- |
| ```UpdateMode { Update, FixedUpdate, LateUpdate }``` | In which method should the update logic run? |

### 5.20.2 Public Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```UpdateMode updateMode``` | Which method to run the update logic in? |
| ```float updateTime``` | Update interval in seconds (0 runs every frame) |

### 5.20.3 Read-Only Properties

| Code                         | Description |
| :--------------------------- | :---------- |
| ```SplineResult result``` | The current result |
