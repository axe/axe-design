# Paths

## Description

The Paths component contains point, linear, curved,  disjoint, combination, and
subset paths used in animation, particle effects, and networking.

## Dependencies

- None

## Sub-Components

- None

## Terms

- `Path`: The base class which uses a set of points to generate another point
  given a delta value between 0.0 and 1.0
- `PathGenerator`:
- `AdditivePath`:
- `BasisSplinePath`:
- `BezierPath`:
- `CatmullRomPath`:
- `ChaikinPath`:
- `ComboPath`:
- `CompiledPath`:
- `CubicPath`:  
- `DurationPath`:
- `EasedPath`:
- `HermitePath`:   
- `IntegralPath`:
- `JumpPath`:
- `KramerPath`:
- `LinearPath`:
- `ParametricCubicPath`:
- `PointPath`:
- `QuadraticCornerPath`:
- `QuadraticPath`:
- `ScaledPath`:
- `SpringPath`:
- `SubPath`:
- `TimedPath`:
- `Tween`:
- `UniformPath`:


## Design

#### Path< T >
- set( out: T, delta: float, calculator: Calculator< T > ): T
- get( delta: float, calculator: Calculator< T > ): T
- getPointCount(): int
- getPoint( index: int ): T
- setPoints( points: T[] ): int

#### PathGenerator< T > : Factory< Path< T > >
- create(): Path< T >
- create( points: T[] ): Path< T >
