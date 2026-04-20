# Compose UI Skill

A specialized skill for AI coding assistants (Claude, Copilot, Antigravity) to help implement high-performance, maintainable, and bug-free Jetpack Compose and Compose Multiplatform (KMP) code.

## Current Features & Topics

The skill provides comprehensive guidance on the following areas:

### 1. Composable Structure & Organization
- **Function Separation**: Guidelines on keeping composables small and managing recomposition scopes.
- **Argument Best Practices**: Proper use of default values and exposed `Modifier` parameters.
- **Component Reuse**: Strategies for extracting and organizing shared UI elements (App-wide, Feature-wide, Screen-level).
- **Design System Integration**: Usage of centralized dimensions and naming conventions for UI constants.

### 2. State Management & Performance
- **State Hoisting**: Rules for choosing the correct level for state ownership.
- **Property Drilling Prevention**: Passing only necessary data to child composables.
- **derivedStateOf**: Proper usage for caching and performance optimization.

### 3. ViewModel & Data Flow Architecture
- **State Styles**: Guidance on MVI (default) vs MVVM architectural patterns.
- **State Transfer**: Efficiently moving data from ViewModel to UI using `StateFlow` and `stateIn`.
- **Event Handling**: Robust patterns for one-off events (e.g., navigation, snackbars) using `LaunchedEffect` and state clearing.
- **ViewModel Parameters**: Correct patterns for injecting startup data into ViewModels.

### 4. Stability & Recomposition Optimization
- **Collections Stability**: Detailed strategies for handling unstable collections (Stability config, Immutable collections, Stable wrappers).
- **Skippability**: Ensuring composables can skip unnecessary recompositions.

### 5. Common UI Patterns (Reference Library)
- **Text Inputs**: Handling modern `TextFieldState` and legacy value-based inputs.
- **Dialogs**: Patterns for UI-driven and VM-driven confirmation and interaction dialogs.
- **Lists**: Best practices for `LazyColumn`, `LazyRow`, keys, and content types.

---

## Contributing & Feedback

Suggestions for new features, pattern updates, or bug reports are welcome! Please **open an issue** in the repository to propose changes or discuss improvements.

---

## Roadmap

Upcoming additions to further enhance the skill's capabilities:

- [ ] **Animations**: Comprehensive guide on `AnimatedVisibility`, `AnimatedContent`, `animate*AsState`, and transition management.
- [ ] **Side Effects Deep-Dive**: Exhaustive patterns for `LaunchedEffect`, `DisposableEffect`, `SideEffect`, and coroutine scope management.
- [ ] **Modifier Ordering**: Detailed analysis of the "Chain of Responsibility" pattern and how order impacts layout, visuals, and interaction.
- [ ] **Accessibility (a11y)**: Semantic properties and testing for inclusive UI.
- [ ] **Testing Patterns**: Guidance on writing UI tests for Compose.

