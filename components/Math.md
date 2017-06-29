# Math

## Description

## Dependencies

## Sub-Components

## Terms

## Design

#### interface Calculator< V >

#### class Plot< D, T >
- next(): boolean
- reset(): void
- end(): void
- get(): T

#### class Randoms
- seed: long
- randomInt(): int
- randomInt( n: int ): int
- randomInt( min: int, max: int ): int
- randomFloat(): float
- randomFloat( n: float ): float
- randomFloat( min: float, max: float ): float
- randomBoolean(): boolean
- random< V >( values: V[] ): V

#### class Probability< V >
- set( value: V, probability: int ): Probability< V >
- add( value: V, probability: int ): Probability< V >
- get( value: V ): int
- remove( value: V ): Probability< V >
- random( rnd: Randoms ): V
- invert( reduce: boolean ): V[]

#### interface Noise< T >
- noise( x: T ): T
- noise( x: T, y: T ): T
- noise( x: T, y: T, z: T ): T
- noise( x: T, y: Y, z: T, octaves: int, frequency: T, amplitude: T, normalized: boolean ): T

#### class NoiseSimplex< T > : Noise< T >

#### class NoisePerlin< T > : Noise< T >
