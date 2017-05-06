# Game Loop

## Description

The Game Loop component determines when the game should update its logic and
when it should render.

## Dependencies

- [Core](Core.md)

## Sub-Components

- None

## Terms

- **Game Loop**: A class which helps determine when and how many updates and
  draws occur each second.
- **Game Loop Variable**: A loop which runs at full speed and has an equal
  number of update and draws.
- **Game Loop Interpolated**: A loop which has a fixed update rate and an
  unrestricted render rate which interpolates between previous and current
  update states.
- **Game Loop Fixed**: A loop which has separate target update and draw rates.

## Design

#### interface GameLoop
- onStart( elapsed: GameTime ): void
- onLoop( elapsed: GameTime ): void

#### class GameLoopVariable
- maximumElapsed: long

#### class GameLoopInterpolated
- frameRate: long
- maxUpdates: int
- yield: boolean
- time: long
- draws: int

#### class GameLoopFixed
- updateRate: float
- drawRate: float
- maxUpdates: int
- sleep: boolean
- updateTime: float
- drawTime: float
