# Math

## Description

## Dependencies

- [Math](Math.md)

## Sub-Components

- Primitives
- Shapes

## Terms

## Design

#### class Vec< D, T >

#### class Matrix< D, T >

#### class Bound< D, T >


#### interface Shape< V >
- finite(): boolean
- radius(): float
- sign( position: V, point: V ): int
- distance( position: V, point: V ): float
- distanceSq( position: V, point: V ): float
- normal( position: V, point: V, out: V ): boolean
- raytrace( position: V, point: V, direction: V ): boolean
- bounds( position: V, bounds: Bounds< V > ): boolean

#### class Bounds< V > : Shape< V >
- min: V
- max: V
- thickness: float

#### class Range< V > : Shape< V >
- start: V
- end: V
- thickness: float

#### class Line< V > : Shape< V >
- start: V
- end: V
- thickness: float

#### class Sphere< V > : Shape< V >
- center: V
- radius: float

#### class Plane< V > : Shape< V >
- origin: V
- normal: V
- thickness: float

#### interface HasShape< V >
- getShape(): Shape< V >
- getShapePosition(): V
- sign( point: V ): int
- distance( point: V ): float
- distanceSq( point: V ): float
- normal( point: V, out: V ): boolean
- raytrace( point: V, direction: V ): boolean
- bounds( bounds: Bounds< V > ): boolean

#### interface ShapeCalculator< V, S< V >, H< V > >
- distance( s: S, h: H, offset: V ): float
- distanceSq( s: S, h: H, offset: V ): float
- contains( s: S, h: H, offset: V ): boolean
- intersects( s: S, h: H, offset: V ): boolean
