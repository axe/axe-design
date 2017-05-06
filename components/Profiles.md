# Profiles

## Description

The Profiles component provides a way to save player data - the profiles they've
created, their saved game states, their preferences, and achievements.

## Dependencies

- [Files & Streams](FilesStreams.md)

## Sub-Components

- None

## Terms

- **GenericData**: A generic streamable object with any number of named values
- **Player**: A player of your game who may or may not need to sign-in.
- **Profile**: An instance of a game customized by a player with one or more
  saved game states.
- **Achievement**: A target number of events or measurement that the player
  profile strives to complete (kills, distance traveled, etc).
- **ProfileAchievement**: The current value achieved by the player profile.

## Design

#### class GenericData
- get< T >( name: string ): T
- set< T >( name: string, value: T ): T

#### class Player
- id: string
- displayName: string
- loginName: string
- email: string
- profiles: Profile[]
- preferences: Preferences
- data: GenericData

#### class Profile
- id: string
- name: string
- data: GenericData
- achievements: ProfileAchievement[]
- states: GenericData[]

#### class Achievement
- id: int
- name: string
- description: string
- icon: string
- target: double
- next: Achievement

#### class ProfileAchievement
- achievement: int
- achieved: double
