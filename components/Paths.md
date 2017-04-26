# Paths

## Description

The Paths component contains point, linear, curved,  disjoint, combination, and
subset paths used in animation, particle effects, and networking. Any data type
can be a point - as long as it has a Calculator< T > implementation.

## Dependencies

- [Easing](Easing.md)
- [Math](Math.md)

## Sub-Components

- None

## Terms

- `Path`: The base class which uses a set of points to generate another point
  given a delta value between 0.0 and 1.0
- `PathGenerator`: Generates an empty path or a path given a set of points.
- `Point Paths` (listed in order from least complex to most)
  - `Point`: The generated point can only be one value.
  - `Jump`: The generated point can be one of many values - all evenly separated. This is useful for sprint animations.
- `Linear Paths` (listed in order from least complex to most)
  - `Tween`: A path between two points
  - `Integral`: A path between N evenly interpolated points (moves a value
    at the same speed between each point no matter how "far" they are from each
    other)
  - `Delta`: A path between N points spaced based on N delta values
    (controls how much time it takes to get from each point in the form of
    deltas)
  - `Linear`: A path between N points interpolated based on their distance
    apart (useful for moving a value at a consistent speed between points)
  - `Timed`: A path between N points spaced based on N times (controls
    how much time it takes to get from each point - used in keyframe animations)
  - `Eased`: A path between N points spaced based on N times with an easing
    (controls how much time it takes to get from each point and an easing
    between each point - used in keyframe animations)
- `Curved Paths` (listed in order by least to most close to direct path)
  - `Quadratic`: A path with 3 points, two being start & end points and the
    other point being a control point.
    https://en.wikipedia.org/wiki/B%C3%A9zier_curve#Quadratic_B.C3.A9zier_curves
  - `Cubic`:  A path with 4 points, two being start & end points and the
    other two being control points.
    https://en.wikipedia.org/wiki/B%C3%A9zier_curve#Cubic_B.C3.A9zier_curves
  - `Bezier`: A generic (N point) version of Cubic & Quadratic Paths.
    https://pomax.github.io/bezierinfo/
  - `Hermite`: A path with 4 points, two being start & end points and the
    other two being tangents off of the start and end points.
  - `ParametricCubic`: A generic parametric cubic path which accepts a
    matrix and weight.
    https://www.cs.helsinki.fi/group/goa/mallinnus/curves/curves.html
  - `BasisSpline`: A specific ParametricCubic path.
    http://web.mit.edu/hyperbook/Patrikalakis-Maekawa-Cho/node17.html
  - `CatmullRom`: A path that goes through all N points smoothly.
    https://www.mvps.org/directx/articles/catmull/
  - `Chaikin`: A path that more closely follows the direct paths between
    points by following the lines and then cutting the corners to the next line.
    http://www.idav.ucdavis.edu/education/CAGDNotes/Chaikins-Algorithm/Chaikins-Algorithm.html
  - `Kramer`: A path that closely follows the direct paths between points
    that also goes through all points.
    http://www.reddit.com/r/gamedev/comments/1ei88i/very_fast_2d_interpolation/
  - `QuadraticCorner`: A path where each turn between lines is a quadratic
    curve - you can control how large the curve from line to line is.
- `Composite Paths`
  - `Additive`: Adds the result of two paths together.
  - `Scaled`: Scales the result of a path by a given value.
  - `Sub`: Generates points based on a subset of another path.
  - `Combo`: A combination of multiple paths.
  - `Compiled`: An integral path generated from N point calculations of
    another path.
  - `Uniform`: A linear path generated from N point calculations of another
    path.

## Design

#### Path< T >
- set( out: T, delta: float ): T
- get( delta: float ): T
- getPointCount(): int
- getPoint( index: int ): T
- setPoints( points: T[] ): int
- getCalculator(): Calculator< T >
- setCalculator( calculator: Calculator< T > ): void

#### PathGenerator< T > : Factory< Path< T > >
- create(): Path< T >
- create( calculator: Calculator< T > ): Path< T >
- create( calculator: Calculator< T >, points: T[] ): Path< T >

#### PathPoint< T > : Path< T >
- point: T

#### PathJump< T > : Path< T >
- points: T[]

#### Tween< T > : Path< T >
- start: T
- end: T

#### PathIntegral< T > : Path< T >
- points: T[]

#### PathDelta< T > : Path< T >
- points: T[]
- deltas: float[]

#### PathLinear< T > : PathDelta< T >

#### PathTimed< T > : Path< T >
- points: T[]
- times: float[]

#### PathEased< T > : PathTimed< T >
- easings: Easing[]

#### PathQuadratic< T > : Path< T >
- p0: T
- p1: T
- p2: T

#### PathCubic< T > : Path< T >
- p0: T
- p1: T
- p2: T
- p3: T

#### PathBezier< T > : Path< T >
- points: T[]
- weights: int[]
- inverses: float[]

#### PathHermite< T > : Path< T >
- start: T
- startTangent: T
- end: T
- endTangent: T

#### PathParametricCubic< T > : Path< T >
- points: T[]
- matrix: float[] []
- weight: float
- invert: boolean
- loop: boolean

#### PathBasisSpline< T > : PathParametricCubic< T >

#### PathCatmullRom< T > : PathParametricCubic< T >

#### PathChaikin< T > : Path< T >
- points: T[]
- depth: int
- loops: boolean
- midpoint: float

#### PathKramer< T > : Path< T >
- points: T[]
- depth: int
- looseness: float
- roughness: float
- loops: boolean

#### PathQuadraticCorner< T > : Path< T >
- points: T[]
- midpoint: float
- loops: boolean

#### PathAdditive< T > : Path< T >
- first: Path< T >
- second: Path< T >

#### PathScaled< T > : Path< T >
- path: Path< T >
- scale: T

#### PathSub< T > : Path< T >
- path: Path< T >
- start: float
- end: float

#### PathCombo< T > : Path< T >
- paths: Path< T >[]
- times: float[]

#### PathCompiled< T > : PathJump< T >

#### PathUniform< T > : PathDelta< T >
