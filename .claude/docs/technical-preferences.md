# Technical Preferences

<!-- Populated by /setup-engine. Updated as the user makes decisions throughout development. -->
<!-- All agents reference this file for project-specific standards and conventions. -->

## Engine & Language

- **Engine**: Godot 4.6
- **Language**: GDScript (gameplay/UI scripting), C# (performance-critical systems), C++ via GDExtension (native only)
- **Rendering**: Mobile renderer (otimizado para performance em iOS/Android)
- **Physics**: Godot 2D Physics (built-in) + simulação customizada tipo Verlet para a mecânica de tensão de fio

## Input & Platform

<!-- Written by /setup-engine. Read by /ux-design, /ux-review, /test-setup, /team-ui, and /dev-story -->
<!-- to scope interaction specs, test helpers, and implementation to the correct input methods. -->

- **Target Platforms**: Mobile (iOS, Android)
- **Input Methods**: Touch
- **Primary Input**: Touch
- **Gamepad Support**: None
- **Touch Support**: Full
- **Platform Notes**: Gestos de um toque só (arrastar/puxar); tudo deve funcionar bem em telas pequenas; sem estados de hover

## Naming Conventions

Use convenções GDScript para arquivos `.gd` e convenções C# para arquivos `.cs`. Não existem
arquivos multi-linguagem — a fronteira é por arquivo. Na dúvida sobre qual linguagem um novo
sistema deve usar, perguntar ao usuário e registrar a decisão aqui.

**GDScript (.gd)**
- **Classes**: PascalCase (ex: `PlayerController`)
- **Variables/Functions**: snake_case (ex: `move_speed`, `take_damage()`)
- **Signals**: snake_case no passado (ex: `health_changed`)
- **Files**: snake_case igual à classe (ex: `player_controller.gd`)
- **Scenes**: PascalCase igual ao nó raiz (ex: `PlayerController.tscn`)
- **Constants**: UPPER_SNAKE_CASE (ex: `MAX_HEALTH`)

**C# (.cs)**
- **Classes**: PascalCase, `partial` (ex: `PlayerController`)
- **Public fields/properties**: PascalCase (ex: `MoveSpeed`)
- **Private fields**: _camelCase (ex: `_currentHealth`)
- **Methods**: PascalCase (ex: `TakeDamage()`)
- **Signal delegates**: PascalCase + sufixo `EventHandler`
- **Files**: PascalCase igual à classe (ex: `PlayerController.cs`)
- **Constants**: PascalCase (ex: `MaxHealth`)

## Performance Budgets

- **Target Framerate**: 60fps (fallback 30fps em aparelhos fracos)
- **Frame Budget**: 16.6ms
- **Draw Calls**: ~100 (baixo, apropriado para puzzle 2D mobile)
- **Memory Ceiling**: [TO BE CONFIGURED — definir após primeiro profile em aparelho real]

## Testing

- **Framework**: GUT (Godot Unit Test)
- **Minimum Coverage**: [TO BE CONFIGURED]
- **Required Tests**: Balance formulas, gameplay systems, networking (if applicable)

## Forbidden Patterns

<!-- Add patterns that should never appear in this project's codebase -->
- [None configured yet — add as architectural decisions are made]

## Allowed Libraries / Addons

<!-- Add approved third-party dependencies here -->
- [None configured yet — add as dependencies are approved]

## Architecture Decisions Log

<!-- Quick reference linking to full ADRs in docs/architecture/ -->
- [No ADRs yet — use /architecture-decision to create one]

## Engine Specialists

<!-- Written by /setup-engine when engine is configured. -->
<!-- Read by /code-review, /architecture-decision, /architecture-review, and team skills -->
<!-- to know which specialist to spawn for engine-specific validation. -->

- **Primary**: godot-specialist
- **GDScript Specialist**: godot-gdscript-specialist (.gd files — gameplay/UI scripts)
- **C# Specialist**: godot-csharp-specialist (.cs files — performance-critical systems)
- **Shader Specialist**: godot-shader-specialist (.gdshader files, VisualShader resources)
- **UI Specialist**: godot-specialist (no dedicated UI specialist — primary covers all UI)
- **Additional Specialists**: godot-gdextension-specialist (GDExtension / native C++ bindings only)
- **Routing Notes**: Invoke primary for cross-language architecture decisions and which systems belong in which language. Invoke GDScript specialist for .gd files. Invoke C# specialist for .cs files and .csproj management. Prefer signals over direct cross-language method calls at the boundary.

### File Extension Routing

<!-- Skills use this table to select the right specialist per file type. -->
<!-- If a row says [TO BE CONFIGURED], fall back to Primary for that file type. -->

| File Extension / Type | Specialist to Spawn |
|-----------------------|---------------------|
| Game code (.gd files) | godot-gdscript-specialist |
| Game code (.cs files) | godot-csharp-specialist |
| Cross-language boundary decisions | godot-specialist |
| Shader / material files (.gdshader, VisualShader) | godot-shader-specialist |
| UI / screen files (Control nodes, CanvasLayer) | godot-specialist |
| Scene / prefab / level files (.tscn, .tres) | godot-specialist |
| Project config (.csproj, NuGet) | godot-csharp-specialist |
| Native extension / plugin files (.gdextension, C++) | godot-gdextension-specialist |
| General architecture review | godot-specialist |
