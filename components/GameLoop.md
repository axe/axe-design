# Game Loop

## Description

The Game Loop component determines when the game should update its logic and
when it should render.

## Dependencies

- [Core](Core.md)

## Sub-Components

- None

## Terms

- `Game Loop`: A class which helps determine when and how many updates and draws
  occur each second.

## Design

#### interface GameLoop
- onStart( elapsed: GameTime ): void
- onLoop( elapsed: GameTime ): void

#### class GameLoopVariable
- maximumElapsed: long

#### class GameLoopFixed
- updateRate: float
- drawRate: float
- maxUpdates: int
- sleep: boolean
- updateTime: float
- drawTime: float

#### class GameLoopInterpolated
- frameRate: long
- maxUpdates: int
- yield: boolean
- time: long
- draws: int
