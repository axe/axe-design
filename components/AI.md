# AI

## Description

## Dependencies

- [Core](Core.md)
- [Math](Math.md)
- [Spatial Database](SpatialDatabase.md)

## Sub-Components

- Finite State Machine
- Fuzzy State Machine
- Steering Behaviors
- Path Finding

## Terms

- **State**: A state to apply to an actor

### Finite State Machine
- **Binary Condition**: A function that returns true or false
- **Transition**: A set of conditions that need to be true to signal a state change. Can have a priority and a probability factor.
- **Transition Map**: Says "if actor is in state A and a transition is true, move to state B"
- **Finite**: Helper functions to create complex finite conditions
- **Finite Machine**: Handles updating the current state and determines the next state

### Fuzzy State Machine
- **Fuzzy Condition**: A function that returns a value between 0.0 and 1.0
- **Fuzzy**: Helper functions to create complex fuzzy conditions
- **Fuzzy Machine**: Handles updating the current states

### Steering Behaviors
- **Filter**:
- **Target**:
- **Constraint**:
- **Behavior**:
- **Group Behavior**:
- **Complex Behavior**:
- **Prioritized Behavior**:
- **State Behavior**:
- **Contextual Behavior**:

### Path Finding

## Design

#### State<S, E>
- `enum: E`
- `subject: S`
- `update( state: GameState, view: View<V> ): void`
- `begin( state: GameState, view: View<V> ): boolean`
- `end( state: GameState, view: View<V> ): boolean`

### Finite State Machine

#### BinaryCondition< S >
- `( subject: S ): boolean`

#### Transition< S >
- `condition: BinaryCondition<S>`
- `priority: number`
- `probability: number`

#### TransitionMap< S >
- `transitions: Transition<S>[][][]`
- `add<E>( fromState: E, transition: Transition<S>, toState: E ): void`
- `all<E>( transition: Transition<S>, toState: E ): void`
- `getTransitions( fromState: E )`

#### Finite
- `and<S>( ...conditions: BinaryCondition<S> ): BinaryCondition<S>`
- `or<S>( ...conditions: BinaryCondition<S> ): BinaryCondition<S>`
- `not<S>( ...conditions: BinaryCondition<S> ): BinaryCondition<S>`
- `nofuzz<S>( trueMin: number, trueMax: number, condition: FuzzyCondition<S> ): BinaryCondition<S> `

#### FiniteMachine
- ``

### Fuzzy State Machine

### Steering Behaviors

### Path Finding
