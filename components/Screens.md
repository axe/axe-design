# Screens

## Description

The Screens component handles passing input, updating, and drawing the current
game screen and sub-screens. Screens can have transitions for entering and
exiting, can have required assets, can preload assets, and will trigger
applicable events. A Screen has one or more View which is a section of the
screen with a scene to render and entities to update.

## Dependencies

- [AI](AI.md)
- [Asset Management](AssetManagement.md)
- [Core](Core.md)
- [Event System](EventSystem.md)
- [Math](Math.md)

## Sub-Components

- None

## Terms

- **CanDraw**:
- **CanUpdate**:
- **HandlesInput**:
- **Screen**:
- **ScreenEventListener**:
- **ScreenEvents**:
- **ScreenManager**:
- **ScreenManagerState**:
- **ScreenManagerEventListener**:
- **ScreenManagerEvents**:
- **ScreenTransition**:
- **ScreenTransitionResult**:
- **ScreenTransitionEntry**:
- **ProjectionOutside**:
- **Camera**:
- **View**:

## Design

#### interface CanDraw
- draw( time: GameTime, view: View ): void

#### interface CanUpdate
- update( elapsed: GameTime, view: View ): void

#### interface HandlesInput
- input( input: InputEngine ): void

#### interface Screen< S : enum > : CanDraw, CanUpdate, HandlesInput, HasEvents, HasData
- views: View[]                             // The views to render for this screen
- parent: Screen< S >                       // The screen which opened this sub-screen
- manager: ScreenManager< S >               // The manager of this screen
- assets: AssetContext                      // The assets required by this screen
- preload: S[]                              // The assets of the screens mapped by these states can be loaded when this screen is entered
- onExit(): void                            // Called when this screen has completed exiting
- onExiting(): void                         // Called when this screen has started exiting
- exiting( time: GameTime, view: View )     // Called during screen exit
- onEnter(): void                           // Called when this screen has completed entering
- onEntering(): void                        // Called when this screen has started entering
- entering( time: GameTime, view: View )    // Called during screen enter
- onCancel(): void                          // Called when a screen transition has been interrupted by another to be transitioned to

#### interface ScreenEventListener
- onScreenEvent( screen: Screen ): void

#### class ScreenEvents
- Exiting: Event< ScreenEventListener >
- Exit: Event< ScreenEventListener >
- Entering: Event< ScreenEventListener >
- Enter: Event< ScreenEventListener >

#### interface ScreenManager< S : enum > : CanDraw, CanUpdate, HandlesInput, HasEvents, HasData
- screens: Screen[]
- states: S[]
- current: S
- next: S
- state: ScreenManagerState
- transitions: ScreenTransitionEntry[]
- add( state: S, screen: Screen ): Screen
- addEntrance( entering: S, transition: ScreenTransition ): ScreenTransitionEntry< S >
- addExit( exiting: S, transition: ScreenTransition ): ScreenTransitionEntry< S >
- addTransition( entering: S, exiting: S, transition: ScreenTransition ): ScreenTransitionEntry< S >
- get( state: S ): Screen
- set( state: S ): Screen
- stack( state: S ): boolean
- unstack(): boolean
- proceed(): void

#### enum ScreenManagerState
- Inactive
- Entering
- Exiting
- Active

#### interface ScreenManagerEventListener
- onScreenManagerEvent( previous: Screen, next: Screen ): void

#### class ScreenManagerEvents
- Stacked: Event< ScreenManagerEventListener >
- UnStacked: Event< ScreenManagerEventListener >
- Entering: Event< ScreenManagerEventListener >
- Entered: Event< ScreenManagerEventListener >
- Exiting: Event< ScreenManagerEventListener >
- Exited: Event< ScreenManagerEventListener >

#### interface ScreenTransition
- onStart( time: GameTime, entering: boolean ): ScreenTransitionResult
- onTransition( time: GameTime, entering: boolean ): ScreenTransitionResult
- onEnd( time: GameTime, entering: boolean ): void

#### enum ScreenTransitionResult
- Finish                            // stop transition, don't call end
- End                               // stop transition, call end
- Continue                          // continue calling transition

#### class ScreenTransitionEntry< S >
- entering: S
- exiting: S
- transition: ScreenTransition

#### enum ProjectionOutside
- Ignore
- Clamp
- Relative

#### interface Camera< V > : HasData
- planes: Plane< V >[]
- update(): void
- intersects( elapsed: GameTime, shape: Shape< V >, position: V ): boolean

#### interface View< V > : HasData
- scene: Scene< V >
- camera: Camera< V >
- placement: Placement
- screen: Screen
- projectionMatrix: Matrix< V >
- viewMatrix: Matrix< V >
- combinedMatrix: Matrix< V >
- update(): void
- draw( elapsed: GameTime ): void
- project( x: float, y: float, outside: ProjectionOutside, out: V ): V
- project( out: V ): V
- unproject( x: float, y: float, outside: ProjectionOutside, out: V ): V
- unproject( x: float, y: float, out: V ): V
