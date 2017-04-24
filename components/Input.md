# Input

## Description

The Input component converts raw input data from devices into consistent data
presented in a platform independent API. The state of inputs can be checked for
their current value or a user can listen for input events.

## Dependencies

- None

## Sub-Components

- None

## Terms

- `InputState`: The state of any given input (key, mouse, joystick axis, etc).
- `InputStateAll`: A compound state which is triggered when ALL sub states are down.
- `InputStateAny`: A compound state which is triggered when ANY sub states are down.
- `InputStateFiltered`: A filtered view of an input state.
- `InputStateRange`: A view of an input state which depends on the raw input being between a minimum and maximum value.
- `InputStateGesture`: A combination of inputs that must occur without interruption in a certain order in a certain time frame.
- `InputStateAxis`: An input state with x and y axis states.
- `Key`: A key on a keyboard.
- `KeyState`: An input state for a key (and has a character).
- `KeyEngine`: Initializes and updates key states and provides other settings.
- `MouseButtonState`: An input state for mouse buttons that keeps track of where the mouse button was pressed.
- `MouseState`: Contains information on where the mouse is.
- `MouseEngine`: Initializes and updates mouse state and provides other settings.
- `Controller`: A game pad that may or may not be connected.
- `ControllerEngine`: Initializes and updates controller state and provides other settings.
- `InputListener`: A function which is notified when an event occurs on some input.
- `InputEventType`: A way which input can change.
- `InputEvent`: A grouping between an InputState, an InputEventType, and an InputListener.
- `InputContext`: A context to which inputs are mapped to actions. InputContexts change based on screens and the UI, and multiple may be active at a time.
- `Inputs`: Manages input contexts, has a list of the last raw input states to change, and provides functions for combining & decorating input states.

## Design

#### class InputState
- input: int                  
- window: int                 // an identifier for which window the input was last changed
- down: boolean               // is the input in a non-default state?
- value: float                // the current value of the input (calculated from trigger min/max, raw, & filter)
- raw: float                  // the raw input value
- frames: int                 // the number of frames the input has been in this state
- time: long                  // the amount of time the input has been in this state
- seconds: float              // the amount of time in seconds the input has been in this state
- presses: int                // the number of press events generated for this input since it has been down
- press: boolean              // whether a press event should be triggered this frame
- changeTime: long            // the exact time this input changed state
- lastUpdate: long            // the last time this input state updated
- isDown(): boolean
- isUp(): boolean
- isPress(): boolean
- isChange(): boolean
- setDown( down: boolean, inputTime: long ): void
- update( currentTime: long, pressDelay: float, pressInterval: float ): void

#### class InputStateAll : InputState
- states: InputState[]        // the inputs to look at

#### class InputStateAny : InputState
- states: InputState[]        // the inputs to look at

#### class InputStateFiltered : InputState
- state: InputState           // the input to filter
- filterArray: float[]        // the last N raw input values to use to calculate the filtered value
- filterIndex: int            // the index to the last raw input into filterArray

#### class InputStateRange : InputState
- state: InputState           // the input the modify
- min: float                  // the minimum value the raw input must be to trigger a down state
- max: float                  // the upper bound used to normalize the input state value

#### class InputStateGesture : InputState
- states: InputState[]
- currentState: int
- timeFrame: float

#### class InputStateAxis : InputState
- x: InputState
- y: InputState
- up: InputState
- down: InputState
- left: InputState
- right: InputState
- normalX: float
- normalY: float

#### enum Key
- A, B, C, etc

#### class KeyState : InputState
- key: Key
- character: char

#### interface KeyEngine
- update( currentTime: long ): void
- getKey( key: Key ): KeyState
- getShift(): KeyState
- getControl(): KeyState
- getMeta(): KeyState
- getCommand(): KeyState
- getAlt(): KeyState
- getQueue() Queue< KeyState >
- setPressInterval( pressInterval: float ): void
- getPressInterval(): float
- setPressDelay( pressDelay: float ): void
- getPressDelay(): float

#### class MouseButtonState : InputState
- x: int
- y: int

#### class MouseState
- x: int
- y: int
- dx: int
- dy: int
- sdx: int
- sdy: int
- wheel: int
- swheel: int
- inside: InputState

#### interface MouseEngine
- update( currentTime: long ): void
- getButton( index: int ): MouseButtonState
- getMouse(): MouseState
- getQueue(): Queue< MouseButtonState >
- setGrabbed( grabbed: boolean ): void
- isGrabbed(): boolean
- setHidden( hidden: boolean ): void
- isHidden(): boolean
- setPressInterval( pressInterval: float ): void
- getPressInterval(): float
- setPressDelay( pressDelay: float ): void
- getPressDelay(): float

#### interface Controller
- getConnectOrder(): int
- isConnected(): boolean
- getButtonCount(): int
- getButton( index: int ): InputState
- getAxisCount(): int
- getAxis( index: int ): InputStateAxis

#### interface ControllerEngine
- update( currentTime: long ): void
- getControllerCount(): int
- getController( index: int ): Controller
- setPressInterval( pressInterval: float ): void
- getPressInterval(): float
- setPressDelay( pressDelay: float ): void
- getPressDelay(): float

#### function InputListener< S : InputState >
- ( input: S, type: InputEventType ): void

#### enum InputEventType
- Up
- Down
- Change
- Press
- Held
- isType( state: InputState ): boolean

#### class InputEvent
- type: InputEventType
- state: InputState
- listener: InputListener
- isTriggered(): boolean

#### class InputContext< A : enum >
- id: int
- active: boolean
- events: InputEvent[]
- setAction( action: A, state: InputState, type: InputEventType, listener: InputListener ): void
- getActionEvent( action: A ): InputEvent
- update( currentTime: long ): void

#### class Inputs
- contexts: InputContext[]
- changeHistory: ListCircular< InputState >
- update( currentTime: long ): void
- filtered( state: InputState, samples: int ): InputStateFiltered
- range( state: InputState, min: float, max: float ): InputStateRange
- axis( x: InputState, y: InputState ): InputStateAxis
- gesture( timeFrame: float, states: InputState[] ): InputStateGesture
- any( states: InputState[] ): InputStateAll
- all( states: InputState[] ): InputStateAny
- newContext(): InputContext
- setContext( id: int ): boolean
- addContext( id: int ): boolean
- clearContexts(): void
