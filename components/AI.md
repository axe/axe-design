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

- **State Machine**: Updates the state of a subject

### Finite State Machine
- **Binary Condition**: A function that returns true or false
- **Finite Transition**: A set of conditions that need to be true to signal a state change. Can have a priority and a probability factor.
- **Finite Transition Map**: Says "if actor is in state A and a transition is true, move to state B"
- **Finite**: Helper functions to create complex finite conditions
- **Finite State**: A state to apply to an actor
- **Finite State Map**: A map of state logic to state enumeration
- **Finite Machine**: Handles updating the current state and determines the next state

### Fuzzy State Machine
- **Fuzzy Condition**: A function that returns a value between 0.0 and 1.0
- **Fuzzy**: Helper functions to create complex fuzzy conditions
- **Fuzzy Condition Map**: A map of state enumeration to the fuzzy condition
- **Fuzzy State**: A state to apply to an actor to a certain degree determined by the fuzzy condition
- **Fuzzy State Map**: A map of state logic to state enumeration
- **Fuzzy Machine**: Handles updating the current states

### Steering Behaviors
- **SteerSubject**: An object which is controlled by steering behaviors
- **SteerController**: Handles applying the forces of a steering behavior to a subject
- **Filter**: Determines whether an entity can affect the subject (is near, visible, etc)
  - **Side**: Returns true if the target is in front/back of the subject
  - **View**: Returns true if the target is view of the subject (given FOV)
  - **Proximity**: Returns true if the target is within a circle around the subject
  - **And**: A compound filter that returns true if ALL of its filters return true
  - **Or**: A compound filter that returns true if ANY of its filters return true
  - **None**: Always returns true
- **Target**: Provides information on a target like position, velocity, direction, etc
  - **Aggregate**: Targets the result of an aggregate given nearby targets
  - **Chain**: Targets a given target, if none is found checks a second target
  - **Filtered**: Targets a given target if it passes a filter
  - **Future**: Targets the future position of another target
  - **InLine**: Targets the closest point in the segment between two targets
  - **Interpose**: Targets a specific point % of the way between two targets
  - **Relative**: Targets relative to another subject (ie. protect another subject)
- **Aggregate**: Takes a group of targets and outputs a single target
  - **Closest**: Targets the closest in the group
  - **Average**: Targets the average position of the group
  - **Slowest**: Targets the slowest target in the group
  - **Weakest**: Finds the target which the subject can intercept the soonest
  - **Strongest**: Finds the target which can intercept the subject the soonest
- **Constraint**: A restriction placed on a subject when it comes to its velocity, direction, etc
  - **Turning**: Restricts how fast a subject can turn
  - **ZeroVelocityThreshold**: Restricts the velocity to zero when it's below a given threshold
- **Behavior**: A function which produces a force to be applied to the subject
  - **To**: Steers towards a target at maximum velocity
  - **Away**: Steers away from a target at maximum velocity
  - **Arrive**: Steers to a target coming to a gradual stop
  - **Wander**: Steers a subject randomly around
  - **Constant**: Steers a subject with some constant force
  - **Drive**: Steers a subject based on driving inputs (forward, turn left, turn right, backup)
  - **Containment**: Steers a subject to stay inside a geometry
  - **Face**: Steers a subject to look at a target
  - **Follow**: Steers a subject to follow behind a potentially moving target
  - **FlowField**: Steers a subject based on a flow field
  - **Path**: Steers a subject along a path
  - **Modifier**: Modifies an existing behavior
- **Accumulator**: A function which accepts the output of multiple behaviors to produce a single output
  - **Average**: Takes the average of the output
  - **First**: Takes the first non-zero output
  - **Max**: Takes the largest output
  - **Context**: Prioritizes "bad" behaviors above "good"
- **Group Behavior**: A behavior which uses nearby entities to produce a force to be applied
  - **Alignment**: A force which aligns the subject with it's neighbors
  - **Cohesion**: A force which aligns the subject to the center of it's neighbors
  - **Separation**: A force which keeps a subject from running into it's neighbors
  - **Avoid**: A force which avoids collisions with it's neighbors while actively moving
  - **Dodge**: A force which avoids collisions with it's neighbors while not actively moving
  - **MatchVelocity**: A force which helps the subject match it's velocity to it's neighbors
- **Complex Behavior**: A behavior which is made up of multiple behaviors
- **Accumulated Behavior**: A complex behavior which uses an accumulator to calculate an output force
- **State Behavior**: A complex behavior where behaviors are called based on the state of the subject

### Path Finding
- **Node**:
- **Move**:
- **Map**:

## Design

#### StateMachine< S, E >
- `subject: S`
- `updateFrequency: number`
- `update( state: GameState, view: View<V> ): void`

---
### Finite State Machine

#### BinaryCondition< S >
- `( subject: S ): boolean`

#### FiniteTransition< S >
- `condition: BinaryCondition<S>`
- `priority: number`
- `probability: number`

#### FiniteTransitionMap< S, E >
- `transitions: FiniteTransition<S>[E][E][]`
- `add( fromState: E, transition: FiniteTransition<S>, toState: E ): void`
- `all( transition: FiniteTransition<S>, toState: E ): void`

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

#### FiniteStateMap< S, E >
- `states: FiniteState<S>[E]`
- `set( stateEnum: E, state: FiniteState<S> ): void`

#### FiniteMachine< S, E > : StateMachine< S, E >
- `current: E`
- `next: E`
- `states: FiniteStateMap<S, E>`
- `transitions: FiniteTransitionMap<S, E>`

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
- `update( subject: S, active: number, totalActive: number, state: GameState, view: View<V> ): void`

#### FuzzyStateMap< S, E >
- `states: FuzzyState<S>[E]`
- `set( stateEnum: E, state: FuzzyState<S> ): void`

#### FuzzyConditionMap< S, E >
- `conditions: FuzzyCondition<S>[E]`
- `set( stateEnum: E, condition: FuzzyCondition<S> ): void`

#### FuzzyMachine< S, E > : StateMachine< S, E >
- `states: FuzzyStateMap<S, E>`
- `conditions: FuzzyConditionMap<S, E>`

---
### Steering Behaviors

#### SteerSubject< V >
- `getPosition(): V`
- `getVelocity(): V`
- `getAcceleration(): V`
- `getDirection(): V`

#### SteerController< V >
- `subject: SteerSubject<V>`
- `steer: Steer<V>`
- `constraint: Constraint<V>`
- `immediate: boolean`
- `update( state: GameState ): void`

#### Filter< V >
- `isValid( subject: SteerSubject<V>, test: SpatialEntity<V> )`

#### FilterSide< V > : Filter< V >
- `front: boolean`

#### FilterView< V > : Filter< V >
- `fovTan: number`
- `fovCos: number`
- `fovType: FieldOfView`

#### FilterProximity< V > : Filter< V >
- `minimum: number`
- `maximum: number`

#### FilterAnd< V > : Filter< V >
- `filters: Filter<V>[]`

#### FilterOr< V > : Filter< V >
- `filters: Filter<V>[]`

#### FilterNone< V > : Filter< V >

#### Target< V >

#### TargetAggregate< V > : Target< V >

#### TargetChain< V > : Target< V >

#### TargetFiltered< V > : Target< V >

#### TargetFuture< V > : Target< V >

#### TargetInLine< V > : Target< V >

#### TargetInterpose< V > : Target< V >

#### TargetRelative< V > : Target< V >

---
### Path Finding
