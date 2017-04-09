# Axe Game Engine Design

This project contains details on the architecture & design of Axe.

Each component that makes up Axe is broken up into it's own design with the
following information:
- Description
- Dependencies
- Sub-Components (if applicable)
- High-Level Design
  - Class/Interface/Enums and their purpose
- Low-Level Design
  - Class/Interface/Enums and their properties and functions

Axe components are designed to be easily incorporated into any code base, you do
not need to use Axe as the main game engine and you don't even need to use its
primitives (vectors, matrices, etc).

### Components

- [AI](components/AI.md)
- [Animation](components/Animation.md)
- [Asset Management](components/AssetManagement.md)
- [Audio](components/Audio.md)
- [Core](components/Core.md)
- [Debugging Utilities](components/DebuggingUtilities.md)
- [Easing](components/Easing.md)
- [Entity](components/Entity.md)
- [Entity Management](components/EntityManagement.md)
- [Event System](components/EventSystem.md)
- [Game Loop](components/GameLoop.md)
- [Game Patches & DLC](components/GamePatchesDLC.md)
- [Input](components/Input.md)
- [Input/Output](components/InputOutput.md)
- [Localization](components/Localization.md)
- [Math](components/Math.md)
- [Multithreading](components/Multithreading.md)
- [Networking](components/Networking.md)
- [Particle Effects](components/ParticleEffects.md)
- [Paths](components/Paths.md)
- [Physics](components/Physics.md)
- [Rendering](components/Rendering.md)
- [Scripting System](components/ScriptingSystem.md)
- [Spatial Database](components/SpatialDatabase.md)
- [System Integration](components/SystemIntegration.md)
- [Tools](components/Tools.md)
- [User Interface](components/UserInterface.md)

### Component Organization Diagram

![Architecture](images/AxeArchitecture.png)
