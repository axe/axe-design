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

- `Input`: The state of any given input (key, mouse, joystick axis, etc).
- `InputAll`: A compound input which is triggered when ALL sub inputs are down.
- `InputAny`: A compound input which is triggered when ANY sub inputs are down.
- `InputFiltered`: A filtered view of an input.
- `InputRange`: A view of an input which depends on the raw input being between a minimum and maximum value.
- `InputGesture`: A combination of inputs that must occur without interruption in a certain order in a certain time frame.
- `InputAxis`: An input with x and y axis inputs.
- `Key`: A key on a keyboard.
- `KeyInput`: An input for a key (and has a character).
- `KeyEngine`: Initializes and updates key inputs and provides other settings.
- `MouseInput`: An input for mouse buttons that keeps track of where the mouse button was pressed.
- `MouseState`: Contains information on where the mouse is.
- `MouseEngine`: Initializes and updates mouse input and provides other settings.
- `Controller`: A game pad that may or may not be connected.
- `ControllerEngine`: Initializes and updates controller input and provides other settings.
- `InputListener`: A function which is notified when an event occurs on some input.
- `InputEventType`: A way which input can change.
- `InputEvent`: A grouping between an Input, an InputEventType, and an InputListener.
- `InputContext`: A context to which inputs are mapped to actions. InputContexts change based on screens and the UI, and multiple may be active at a time.
- `Inputs`: Manages input contexts and provides functions for combining & decorating inputs.

## Design

#### class Input
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
- name: string                // the name of the input, assigned by the system or specified by the user
- isDown(): boolean
- isUp(): boolean
- isPress(): boolean
- isChange(): boolean
- setDown( down: boolean, inputTime: long ): void
- update( currentTime: long, pressDelay: float, pressInterval: float ): void

#### class InputAll : Input
- inputs: Input[]        // the inputs to look at

#### class InputAny : Input
- inputs: Input[]        // the inputs to look at

#### class InputFiltered : Input
- input: Input           // the input to filter
- filterArray: float[]        // the last N raw input values to use to calculate the filtered value
- filterIndex: int            // the index to the last raw input into filterArray

#### class InputRange : Input
- input: Input           // the input the modify
- min: float                  // the minimum value the raw input must be to trigger a down state
- max: float                  // the upper bound used to normalize the input value

#### class InputGesture : Input
- inputs: Input[]
- manager: Inputs
- currentInput: int
- timeFrame: float
- startTime: long

#### class InputAxis : Input
- x: Input
- y: Input
- up: Input
- down: Input
- left: Input
- right: Input
- normalX: float
- normalY: float
- magnitude: float

#### enum Key
- A, B, C, etc

#### class KeyInput : Input
- key: Key
- character: char

#### interface KeyEngine
- update( currentTime: long ): void
- getKey( key: Key ): KeyInput
- getShift(): KeyInput
- getControl(): KeyInput
- getMeta(): KeyInput
- getCommand(): KeyInput
- getAlt(): KeyInput
- getQueue() Queue< KeyInput >
- setPressInterval( pressInterval: float ): void
- getPressInterval(): float
- setPressDelay( pressDelay: float ): void
- getPressDelay(): float

#### class MouseInput : Input
- x: int
- y: int

#### class Mouse
- x: int
- y: int
- dx: int
- dy: int
- delta: InputAxis
- sdx: int
- sdy: int
- wheel: int
- swheel: int
- inside: Input

#### interface MouseEngine
- BUTTON_COUNT: int = 16
- update( currentTime: long ): void
- getButton( index: int ): MouseInput
- getMouse(): Mouse
- getQueue(): Queue< MouseInput >
- setGrabbed( grabbed: boolean ): void
- isGrabbed(): boolean
- setHidden( hidden: boolean ): void
- isHidden(): boolean
- setPressInterval( pressInterval: float ): void
- getPressInterval(): float
- setPressDelay( pressDelay: float ): void
- getPressDelay(): float

#### interface Controller
- AXIS_COUNT: int = 8
- BUTTON_COUNT: int = 32
- getConnectOrder(): int
- isConnected(): boolean
- getButton( index: int ): Input
- getButtonCount(): int
- getAxis( index: int ): InputAxis
- getAxisCount(): int

#### interface ControllerEngine
- CONTROLLER_COUNT: int = 16
- update( currentTime: long ): void
- forEachConnected( consumer: Consumer< Controller > ): int
- getController( index: int ): Controller
- getControllerCount(): int
- setPressInterval( pressInterval: float ): void
- getPressInterval(): float
- setPressDelay( pressDelay: float ): void
- getPressDelay(): float

#### function InputListener< S : Input >
- ( input: S, type: InputEventType ): void

#### enum InputEventType
- Up
- Down
- Change
- Press
- Held
- isType( input: Input ): boolean

#### class InputEvent
- type: InputEventType
- input: Input
- listener: InputListener
- isTriggered(): boolean

#### class InputContext< A : enum >
- id: int
- active: boolean
- events: InputEvent[]
- defaultListener: InputListener
- setAction( action: A, input: Input, type: InputEventType, listener: InputListener ): void
- getActionEvent( action: A ): InputEvent
- update( currentTime: long ): void

#### class Inputs
- contexts: InputContext[]
- changeHistory: ListCircular< Input >
- update( currentTime: long ): void
- filtered( input: Input, samples: int ): InputFiltered
- range( input: Input, min: float, max: float ): InputRange
- axis( x: Input, y: Input ): InputAxis
- gesture( timeFrame: float, inputs: Input[] ): InputGesture
- any( inputs: Input[] ): InputAll
- all( inputs: Input[] ): InputAny
- addChange( input: Input ): void
- forEachDown( consumer: Consumer< Input > ): int
- newContext(): InputContext
- setContext( id: int ): boolean
- addContext( id: int ): boolean
- clearContexts(): void
