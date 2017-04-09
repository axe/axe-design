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

#### StateMachine< S, E >
- `subject: S`
- `updateFrequency: number`
- `update( state: GameState, view: View<V> ): void`

---
### Finite State Machine

#### BinaryCondition< S >
- `( subject: S ): boolean`

#### Transition< S >
- `condition: BinaryCondition<S>`
- `priority: number`
- `probability: number`

#### TransitionMap< S, E >
- `transitions: Transition<S>[E][E][]`
- `add( fromState: E, transition: Transition<S>, toState: E ): void`
- `all( transition: Transition<S>, toState: E ): void`

#### Finite
- `and<S>( ...conditions: BinaryCondition<S> ): BinaryCondition<S>`
- `or<S>( ...conditions: BinaryCondition<S> ): BinaryCondition<S>`
- `not<S>( condition: BinaryCondition<S> ): BinaryCondition<S>`
- `consecutive<S>( times: number, condition: BinaryCondition<S> ): BinaryCondition<S>`
- `nofuzz<S>( trueMin: number, trueMax: number, condition: FuzzyCondition<S> ): BinaryCondition<S>`

#### FiniteState< S >
- `update( subject: S, state: GameState, view: View<V> ): void`
- `begin( subject: S, state: GameState, view: View<V> ): boolean`
- `end( subject: S, state: GameState, view: View<V> ): boolean`

### FiniteStateMap< S, E >
- `states: FiniteState<S>[E]`
- `set( stateEnum: E, state: FiniteState<S> ): void`

#### FiniteMachine< S, E > : StateMachine< S, E >
- `current: E`
- `next: E`
- `states: FiniteStateMap<S, E>`
- `transitions: TransitionMap<S, E>`

---
### Fuzzy State Machine

#### FuzzyCondition< S >
- `( subject: S ): number`

#### Fuzzy
- `min<S>( ...conditions: FuzzyCondition<S> ): FuzzyCondition<S>`
- `max<S>( ...conditions: FuzzyCondition<S> ): FuzzyCondition<S>`
- `avg<S>( ...conditions: FuzzyCondition<S> ): FuzzyCondition<S>`
- `not<S>( condition: FuzzyCondition<S> ): FuzzyCondition<S>`
- `runningAverage<S>( samples: number, condition: FuzzyCondition<S> ): FuzzyCondition<S>`
- `ranged<S>( min: number, max: number, condition: FuzzyCondition<S> ): FuzzyCondition<S>`

#### FuzzyState< S >
- `update( subject: S, active: number, state: GameState, view: View<V> ): void`

### FuzzyStateMap< S, E >
- `states: FuzzyState<S>[E]`
- `set( stateEnum: E, state: FuzzyState<S> ): void`

#### FuzzyMachine< S, E > : StateMachine< S, E >
- `states: FuzzyStateMap<S, E>`
- `conditions: FuzzyCondition[E]`

---
### Steering Behaviors

---
### Path Finding
