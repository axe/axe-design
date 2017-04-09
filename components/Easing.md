# Easing

## Description

This component provides functions for controlling the velocity of an animation known as it's "easing". An easing is a function which takes a delta value between 0 and 1 and returns a value between 0 and 1 (typically). You can use easing functions for smooth transitions from one state to another, versus an often harsh linear interpolation.

## Dependencies

- None

## Sub-Components

- None

## Terms

- **Easing**: Single argument function which takes a delta (x) and returns a new delta (y).
- **Easing Type**: Single argument factory which takes an easing function and generates a new easing function.
- **Easing Scale**: Double argument factory which takes a scalar value and easing function and generates a new easing function

## Design

#### Easing
- `( x: number ): number`

#### EasingType
- `( easing: Easing ): Easing`

#### Ease
- `defaultEasing: Easing`
- `defaultEasingType: EasingType`
- `parse( easing: string ): Easing`
  - Accepts the following formats:
    - easing
    - easingType
    - easing-easingType
    - easing*scale
    - easingType*scale
    - easing-easingType*scale

#### Easings
- `scale( scale: number, easing: Easing ): Easing`
```javascript
return ((easing( x ) - x) * scale) + x
```
- `linear: Easing`
```javascript
return x
```
- `quad: Easing`
```javascript
return x * x
```
- `ease: Easing`
```javascript
i = (1.0 - x)
i2 = i * i
x2 = x * x
eq1 = (0.3 * i2 * x) + (3.0 * i * x2) + (x2 * x)
eq2 = 1.0 - i2 * i2
return eq1 * i + eq2 * x;
```
- `cubic: Easing`
```javascript
return x * x * x
```
- `quartic: Easing`
```javascript
x2 = x * x
return x2 * x2
```
- `quintic: Easing`
```javascript
x2 = x * x
return x2 * x2 * x
```
- `back: Easing`
```javascript
x2 = x * x
x3 = x2 * x
return x3 + x2 - x
```
- `sine: Easing`
```javascript
return sin(x * 1.57079632679)
```
- `overshot: Easing`
```javascript
return (1.0 - x * (7.0 / 10)) * x * (10.0 / 3.0)
```
- `elastic: Easing`
```javascript
x2 = x * x
x3 = x2 * x
scale = x2 * ((2.0 * x3) + x2 - (4.0 * x) + 2.0)
wave = -sin(x * 10.9955742876)
return scale * wave
```
- `revisit: Easing`
```javascript
return abs(x - sin(x * 3.14159265359))
```
- `lasso: Easing`
```javascript
return (1.0 - cos(x * x * x * 36.0) * (1.0 - x))
```
- `slowbounce: Easing`
```javascript
x2 = x * x
return (1.0 - abs((1.0 - x2) * cos(x2 * x * 14.8044066016)))
```
- `bounce: Easing`
```javascript
return (1.0 - abs((1.0 - x) * cos(x * x * 14.8044066016)))
```
- `smallbounce: Easing`
```javascript
inv = 1.0 - x
return (1.0 - abs(inv * inv * cos(x * x * 14.8044066016)))
```
- `tinybounce: Easing`
```javascript
inv = 1.0 - x
return (1.0 - abs(inv * inv * cos(x * x * 7.0)))
```
- `hesitant: Easing`
```javascript
return (cos(x * x * 12.0) * x * (1.0 - x) + x)
```
- `sqrt: Easing`
```javascript
return sqrt(x);
```
- `sqrtf: Easing`
```javascript
i = (1.0 - x)
i2 = i * i
return ((1.0 - i2 * i2) + x) * 0.5
```
- `log10: Easing`
```javascript
return (log10(x + 0.01) + 2.0) * 0.5 / 1.0021606868913213
```
- `slingshot: Easing`
```javascript
if (x < 0.7) {
  return (x * -0.357)
} else {
  d = x - 0.7
  return ((d * d * 27.5 - 0.5) * 0.5)
}
```
- `circular: Easing`
```javascript
return 1.0 - sqrt( 1 - x * x )
```
- `gentle: Easing`
```javascript
return (3.0 * (1.0 - x) * x * x) + (x * x * x)
```
- `bezier( mX1: number, mY1: number, mX2: number, mY2: number ): Easing`
  - https://gist.githubusercontent.com/gre/1926947/raw/KeySpline.js

#### EasingTypes
- `in: EasingType`
```javascript
return easing( x )
```
- `out: EasingType`
```javascript
return 1.0 - easing( 1.0 - x )
```
- `inout: EasingType`
```javascript
return (x < 0.5) ? (easing( 2.0 * x ) * 0.5) : (1.0 - (easing( 2.0 - 2.0 * x ) * 0.5))
```
- `yoyo: EasingType`
```javascript
return (x < 0.5) ? (easing( 2.0 * x )) : (easing( 2.0 - 2.0 * x ))
```
- `mirror: EasingType`
```javascript
return (x < 0.5) ? (easing( 2.0 * x )) : (1.0 - easing( 2.0 - 2.0 * x ))
```
- `reverse: EasingType`
```javascript
return easing( 1.0 - x )
```
- `flip: EasingType`
```javascript
return 1.0 - easing( x )
```
