# three-pathfinding

Navigation mesh toolkit for ThreeJS, based on [PatrolJS](https://github.com/nickjanssen/PatrolJS). Computes paths between points on a 3D nav mesh, supports multiple zones, and clamps movement vectors for FPS controls. To learn how to create a navigation mesh using Blender, see [Creating a Nav Mesh](https://www.donmccurdy.com/2017/08/20/creating-a-nav-mesh-for-a-webvr-scene/).

Thanks to [Nick Janssen](https://github.com/nickjanssen) for creating [PatrolJS](https://github.com/nickjanssen/PatrolJS), which was the basis for this library.

![screenshot](https://user-images.githubusercontent.com/1848368/34424850-d79e5a24-ebf4-11e7-87c4-afc75cdc41bd.png)

## Introduction

Traditionally games and 3D apps used waypoints to help their AI agents navigate. This is bad and has a lot of problems, but is generally easier to implement than navigation meshes. Navmeshes are far more accurate, faster, and take into account the size of the AI agent (e.g. tanks require move space to maneuver than soldiers).

For a thorough introduction to Navigation mesh pathfinding, see AI Blog's article, [Fixing Pathfinding Once and For All](http://www.ai-blog.net/archives/000152.html).

## Quickstart

### Installation

```
npm install --save three-pathfinding
```

### Creating a Navigation Mesh

This library does not build navigation meshes for you — instead, create a navigation mesh using [Blender](https://youtu.be/v4d_6ZCGlAg?t=6m8s), [Recast](https://github.com/recastnavigation/recastnavigation) ([CLI](https://github.com/but0n/recastCLI.js)), or another tool.

The library accepts a [THREE.Geometry](https://threejs.org/docs/#api/core/Geometry) instance, which can be loaded with any three.js loader and converted from BufferGeometry if necessary.

### Example

Loading a mesh from a `.gltf` file:

```js
let mesh;

const loader = new THREE.GLTFLoader();
loader.load( 'navmesh.gltf', ({scene}) => {
    scene.traverse((node) => {
        if (node.isMesh) mesh = node;
    });
}, undefined, (e) => {
    console.error(e);
});
```

Initializing the library, creating a level, and finding a path:

```js
const Pathfinder = require('three-pathfinding');
const pathfinder = new Pathfinder();

// Create level.
const ZONE = 'level1';
pathfinder.setZoneData(ZONE, Pathfinder.createZone(mesh.geometry));

// Find path from A to B.
const groupID = pathfinder.getGroup(ZONE, a);
const path = pathfinder.findPath(a, b, ZONE, groupID);
```

### Running the demo locally

```
git clone https://github.com/donmccurdy/three-pathfinding.git
cd three-pathfinding

npm install
npm run dev
```

The demo will start at http://localhost:9966/demo/demo.html.

## API

<!--- API BEGIN --->

<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### Table of Contents

-   [Path](#path)
    -   [setZoneData](#setzonedata)
    -   [getGroup](#getgroup)
    -   [getRandomNode](#getrandomnode)
    -   [getClosestNode](#getclosestnode)
    -   [findPath](#findpath)
    -   [clampStep](#clampstep)
    -   [createZone](#createzone)
-   [Zone](#zone)
-   [Group](#group)
-   [Node](#node)

## Path

Defines an instance of the pathfinding module, with one or more zones.

### setZoneData

Sets data for the given zone.

**Parameters**

-   `zoneID` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** 
-   `zone` **[Zone](#zone)** 

### getGroup

Returns closest node group ID for given position.

**Parameters**

-   `zoneID` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** 
-   `position` **THREE.Vector3** 

Returns **[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)** 

### getRandomNode

Returns a random node within a given range of a given position.

**Parameters**

-   `zoneID` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** 
-   `groupID` **[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)** 
-   `nearPosition` **THREE.Vector3** 
-   `nearRange` **[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)** 

Returns **[Node](#node)** 

### getClosestNode

Returns the closest node to the target position.

**Parameters**

-   `position` **THREE.Vector3** 
-   `zoneID` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** 
-   `groupID` **[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)** 
-   `checkPolygon` **[boolean](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)**  (optional, default `false`)

Returns **[Node](#node)** 

### findPath

Returns a path between given start and end points. If a complete path
cannot be found, will return the nearest endpoint available.

**Parameters**

-   `startPosition` **THREE.Vector3** Start position.
-   `targetPosition` **THREE.Vector3** Destination.
-   `zoneID` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** ID of current zone.
-   `groupID` **[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)** Current group ID.

Returns **[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;THREE.Vector3>** Array of points defining the path.

### clampStep

Clamps a step along the navmesh, given start and desired endpoint. May be
used to constrain first-person / WASD controls.

**Parameters**

-   `start` **THREE.Vector3** 
-   `end` **THREE.Vector3** Desired endpoint.
-   `node` **[Node](#node)** 
-   `zoneID` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** 
-   `groupID` **[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)** 
-   `endTarget` **THREE.Vector3** Updated endpoint.

Returns **[Node](#node)** Updated node.

### createZone

(Static) Builds a zone/node set from navigation mesh geometry.

**Parameters**

-   `geometry` **THREE.Geometry** 

Returns **[Zone](#zone)** 

## Zone

Defines a zone of interconnected groups on a navigation mesh.

**Properties**

-   `groups` **[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[Group](#group)>** 

## Group

Defines a group within a navigation mesh.

## Node

Defines a node (or polygon) within a group.

**Properties**

-   `id` **[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)** 
-   `neighbours` **[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)>** IDs of neighboring nodes.
-   `centroid` **THREE.Vector3** 
-   `portals` **[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)>>** Array of portals, each defined by two vertex IDs.
-   `closed` **[boolean](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** 
-   `cost` **[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)** 
<!--- API END --->

## Thanks to

* [PatrolJS](https://github.com/nickjanssen/PatrolJS)
* [bgrin's astar library](https://github.com/bgrins/javascript-astar)
* [Digesting Duck's Simple Stupid Funnel Algorithm](http://digestingduck.blogspot.jp/2010/03/simple-stupid-funnel-algorithm.html)
* [Recastnavigation's level mesh](https://github.com/memononen/recastnavigation)
* [Constrained Movement Along Navmesh pt. 3](http://digestingduck.blogspot.com/2010/07/constrained-movement-along-navmesh-pt-3.html?m=1)
