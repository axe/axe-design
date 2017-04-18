# Core

## Description

## Dependencies

- [Math](Math.md)

## Sub-Components

- Monitors, Windows, & Views
- Memory
- Game State
- Data Structures

## Terms

- **Predicate**: Determines whether a given value meets some criteria
- **Consumer**: Passes a value to a function to be consumed
- **Compare**: Compares two values and returns a value less than zero if a is less than b
- **Factory**: Creates a value of a specific type
- **MemoryFactory**: A factory which can also reset and copy a value
- **HasAlternative**: An object which has an alternative value
- **CanCopy**: An object which can be copied
- **CanExpire**: An object which can expire and listen for removal
- **HasData**: An object which has generic data attached to it
- **Flags**: A bitset with a maximum size of 64 bits
- **Match**: 
- **FlagsMatch**:
- **HasFlags**:
- **ExpiresCleaner**:
- **Anchor**:
- **Placement**:
- **Collection**:
- **ListArray**:
- **ListSorted**:
- **ListLinked**:
- **ListLinkedNode**:
- **Memory**:
- **MemoryHeap**:
- **MemoryPool**:
- **IntStack**:
- **Registry**:

## Design

#### function Predicate< T >
- ( test: T ): boolean

#### function Consumer< T >
- ( consume: T ): void

#### function Compare< T >
- ( a: T, b: T ): number

#### interface Factory< T >
- create(): T

#### interface MemoryFactory< T > : Factory< T >
- reset( value: T ): T
- copyTo( source: T, destination: T ): T

#### interface HasAlternative< T >
- alternative(): T

#### interface CanCopy< T >
- copy(): T

#### interface CanExpire
- isExpired(): boolean
- expire(): void
- onExpire(): void

#### function ExpiresCleaner : Predicate< CanExpire >

#### interface HasData
- getData(): any
- setData( data: any ): any

#### enum Match
- Exact
- All
- AnyOf
- None
- NotAll

#### class Flags
- bits: long
- add( bits: long|Flags ): void
- remove( bits: long|Flags ): void
- only( bits: long|Flags ): void
- matches( bits: long|Flags, match: Match ): boolean
- get( index: int|enum ): boolean
- set( index: int|enum, on: boolean ): void
- on( index: int|enum ): void
- off( index: int|enum ): void
- toggle( index: int|enum ): void

#### class FlagsMatch : Flags
- match: Match
- matches( bits: number|Flags )

#### interface HasFlags
- getFlags(): Flags

#### class Anchor
- base: float
- anchor: float
- set( base: float, anchor: float ): void
- set( value: float, relative: boolean ): void
- get( total: float ): float

#### class Placement
- left: Anchor
- right: Anchor
- top: Anchor
- bottom: Anchor
- maximize(): void
- center( width: float, height: float ): void
- attach( dx: float, dy: float, width: float, height: float ): void
- relative( leftAnchor: float, topAnchor: float, rightAnchor: float, bottomAnchor: float ): void
- getBounds( parentWidth: float, parentHeight: float, out: Bounds ): Bounds
- contains( x: float, y: float, parentWidth: float, parentHeight: float ): boolean
- getWidth( parentWidth: float ): float
- getWidth( parentWidth: float, minWidth: float ): float
- getHeight( parentHeight: float ): float
- getHeight( parentHeight: float, minHeight: float ): float
- getLeft( parentWidth: float ): float
- getTop( parentHeight: float ): float
- getRight( parentWidth: float ): float
- getBottom( parentHeight: float ): float
- maximized(): Placement
- centered( width: float, height: float ): Placement
- attached( dx: float, dy: float, width: float, height: float ): Placement

#### interface Collection< T, N >
- add( value: T ) : N
- add( values: T[] ): number
- add( values: Collection< T > ): number
- remove( value: T ): boolean
- pop(): T
- poll() : T
- first(): T
- last(): T
- isEmpty(): boolean
- clear(): void
- size(): int
- setCleaner( cleaner: Predicate< T > ): void
- getCleaner(): Predicate< T >
- clean(): void
- forEach( predicate: Predicate< T > ): int
- forAll( consumer: Consumer< T > ): void
- where( predicate: Predicate< T >, consumer: Consumer< T > ): void
- count( predicate: Predicate< T > ): int
- first( predicate: Predicate< T > ): T
- exists( predicate: Predicate< T > ): boolean

#### class ListArray< T > : Collection< T, number >
- values: T[]
- size: int
- increases: int
- cleaner: Predicate< T >

#### class ListSorted< T > : ListArray< T >
- comparator: Compare< T >

#### class ListLinked< T > : Collection< T, LinkedNode< T > >
- head: LinkedNode< T >
- cleaner: Predicate< T >

#### class ListLinkedNode< T >
- value: T
- next: ListLinkedNode< T >
- prev: ListLinkedNode< T >
- set( value: T ): void
- insertAfter( node: LinkedListNode< T > ): void
- unlink(): void
- removeFor( value: T ): boolean
- isFor( value: T ): boolean
- isLinked(): boolean
- front( very: boolean ): void
- back( very: boolean ): void

#### interface Memory< T >
- alloc(): T
- free( value: T ): void
- size(): int
- capacity(): int

#### class MemoryPool< T > : Memory< T >
- factory: MemoryFactory< T >
- pool: T[]
- size: int
- maximum: int
- increase: int

#### class MemoryHeap< T > : Memory< T >
- factory: Factory< T >

#### class IntStack
- stack: int[]
- size: int
- peek( returnOnEmpty: int ): int
- pop( returnOnEmpty: int ): int
- push( value: int ): void
- get( index: int ): int
- size(): int

#### Registry< T >
- identifier: int
- items: T[]
- create(): int
- register( identifier: int, item: T ): void
- get( identifier: int ): T
