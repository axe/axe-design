# Spatial Database

## Description

## Dependencies

- [Core](Core.md)
- [Math](Math.md)

## Sub-Components

## Terms

## Design

### Core Classes

#### interface SpatialEntityProducer< V, E >
- getPosition( entity: E ): V                 // what is the position of the entity?
- getShape( entity: E ): V                    // what shape is the entity?
- getFlags( entity: E ): long                 // what is the entity?
- getCollisionFlags( entity: E ): long        // which entities do I collide with?
- isStatic( entity: E ): boolean              // update index for entity?
- isInert( entity: E ): boolean               // remove entity from database?

#### interface SpatialDatabase< V, E >
- producer: SpatialEntityProducer< V, E >
- liveQueries: SpatialQueryLive< V, E >[]
- add( entity: E ): void
- clear(): void
- refresh( removeInert: boolean = true, updateIndices: boolean = true, updateLiveQueries: boolean = true ): int
- collisions( flags: long, match: Match, callback: CollisionCallback< V, E > ): int
- intersects( query: SpatialQuery< V >, callback: SearchCallback< V, E > ): int
- contains( query: SpatialQuery< V >, callback: SearchCallback< V, E > ): int
- raytrace( query: SpatialQuery< V >, callback: SearchCallback< V, E > ): int
- knn( query: SpatialQuery< V >, nearest: E[], distances: float[] ): int
- addLiveQuery( live: SpatialQueryLive< V, E > )
- forEach( consumer: Consumer< E > ): int
- forEachFlagged( flags: long, match: Match, consumer: Consumer< E > ): int

#### class SpatialQuery< V >
- position: V
- end: V
- shape: Shape< V >
- maximum: int
- flags: long
- match: Match

#### enum SpatialQueryType
- Intersects = 1
- Contains = 2
- Nearest = 3
- Raytrace = 4

#### class SpatialQueryLive< V, E > : SpatialQuery< V >, SearchCallback< V, E >
- entities: E[]
- distances: float[]
- count: int
- type: SpatialQueryType
- fixed: boolean
- active: boolean
- callback: SpatialQueryLiveCallback< V, E >
- remove(): void

#### interface SpatialQueryLiveCallback< V, E >
- onLiveAdd( entity: E ): void
- onLiveRemove( entity: E ): void

#### interface SearchCallback< V, E >
- onSearchStart(): void
- onSearchResult( entity: E, overlap: float, index: int, query: SpatialQuery< V > ): boolean
- onSearchEnd(): void

#### CollisionCallback< V, E >
- onCollisionStart(): void
- onCollisionResult( subject: S, otherSubject: S, overlap: float, index: int, second: boolean ): void
- onCollisionEnd(): void

#### ListLinkedBounded< V, E > : ListLinked< E >
- min: V
- max: V
- center: V
- isOverCenter( position: V, shape: Shape< V > ): boolean
- isContained( position: V, shape: Shape< V > ): boolean
- isIntersecting( position: V, shape: Shape< V > ): boolean

## Implementations

### Array (brute-force)

#### SpatialArray< V, E > : SpatialDatabase< V, E >
- entities: E[]
- count: int

### Binary Tree

#### SpatialBinaryNode< V, E > : ListLinkedBounded< V, E >
- size: int
- overflowResizes: int
- underflowResizes: int
- axis: int
- minNode: SpatialBinaryNode< V, E >
- maxNode: SpatialBinaryNode< V, E >
- parent: SpatialBinaryNode< V, E >

#### SpatialBinaryTree< V, E > : SpatialDatabase< V, E >
- desiredLeafSize: int
- refreshThreshold: int
- root: SpatialBinaryNode< V, E >

### Power Tree (quadtree, octree)

#### SpatialPowerNode< V, E > : ListLinkedBounded< V, E >
- size: int
- overflowResizes: int
- underflowResizes: int
- children: SpatialPowerNode< V, E >[]
- parent: SpatialPowerNode< V, E >

#### SpatialPowerTree< V, E > : SpatialDatabase< V, E >
- desiredLeafSize: int
- refreshThreshold: int
- root: SpatialPowerNode< V, E >

### Grid

#### SpatialGridNode< V, E >
- cellNode: ListLinkedNode< SpatialGridNode< V, E > >
- databaseNode: ListLinkedNode< SpatialGridNode< V, E > >
- entity: E
- cell: SpatialGridCell< V, E >

#### SpatialGridCell< V, E > : ListLinkedBounded< V, E >
- index: int
- offset: int
- lookback: V

#### SpatialGridDatabase< V, E > : SpatialDatabase< V, E >
- cells: SpatialGridCell< V, E >[]
- dimensions: V
- offset: V
- size: V
- sizeInverted: V
- dimensionsScale: V
- outside: SpatialGridCell< V, E >
- entities: ListLinked< SpatialGridNode< V, E > >
- min: V
- max: V

### Sweep and Prune

#### SpatialSweepBounds< V, E >
- min: SpatialEdge< V, E >[]
- max: SpatialEdge< V, E >[]
- entity: E
- updateEdges(): void

#### SpatialSweepEdge< V, E >
- value: float
- min: boolean
- bounds: SpatialSweepBounds< V, E >

#### SpatialSweepEdgeList< V, E >
- size: int
- sorted: int
- edges: SpatialSweepEdge< V, E >[]
- sort(): void

#### SpatialSweepDatabase< V, E > : SpatialDatabase< V, E >
- lists: SpatialSweepEdgeList< V, E >[]
