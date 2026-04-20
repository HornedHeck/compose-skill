# Text Input Patterns

There are 2 approaches to handle text input: Modern and Legacy.
Prefer Modern for new projects; use the appropriate one for projects with existing input handling.

## 1. Modern Approach

Use state-based text fields via the `TextFieldState` class.

### Case 1: State hoisting in ViewModel

Suitable when you need to perform evaluation as the user types.

```kotlin
// Single-state approach
data class UiState(
    // It is ok to have mutable TextFieldState here despite data class immutability.
    // TextFieldState is Stable so it will not cause problems.
    val login: TextFieldState = TextFieldState(),
    // rest of the fields
)

class MyViewModel : ViewModel() {
    // Classic MVVM multi-state approach
    val loginState = TextFieldState()

    // Ongoing validation
    init {
        viewModelScope.launch {
            snapshotFlow {
                // For single-state approach
                _state.login.text.toString()

                // For multi-state approach
                loginState.text.toString()
            }.collectLatest {
                // Perform validation and error state update here
            }
        }
    }
}

@Composable
fun FormField(
    login: TextFieldState,
    error: String?,
    // No onTextChange needed
) {
    TextField(
        state = login,
        supportingText = {
            error?.let {
                Text(it)
            }
        },
        // Filtering can be done via input/output transformation:
        inputTransformation = InputTransformation.maxLength(16),
        outputTransformation = OutputTransformation {
            if (length > 4) insert(4, "-")
            if (length > 9) insert(9, "-")
            if (length > 14) insert(14, "-")
        },
    )
}
```

Notes:
- You do not need to extract TextField to a separate function for narrow recomposition scope if it depends on TextFieldState only (no other lambda providers or `State<T>`), since this overload has an internal recomposition scope. However you can still extract for readability.
- The example shows 2 ways to declare state: as separate VM fields or as part of a single UiState data class. Both are correct; the single UiState is default (SSoT principle). Separate fields are for MVVM projects with multiple states.

### Case 2: Local State

It is fine to leave state locally if there is no business logic on it. For example, a form verified only when the user presses Submit:

```kotlin
@Composable
fun Form(submit: (String, String) -> Unit) {
    Column {
        val login = rememberTextFieldState() // rememberSaveable under the hood
        LoginField(login)

        // Other fields in the same fashion

        Button(
            onClick = {
                submit(login.text.toString(), /*...*/)
            }
        ) {
            // Button content
        }
    }
}

@Composable
fun LoginField(login: TextFieldState) {
    TextField(state = login)
}
```

State hoisting rules still apply — hoist state as low as possible while accessible to all consumers.

## 2. Legacy Approach

Legacy approach uses value-based text fields.

Guidelines:
- Immediately and synchronously update state in VM function:
```kotlin
fun updateLogin(login: String) {
    val clearedLogin = login.trim()
    // Update value of text field ASAP
    _state.value.update {
        it.copy(login = clearedLogin)
    }
    // ANY async work is after the update, including server-side verification
    viewModelScope.launch {
        // ...
        // It is ok to produce new state here
    }
}
```
- State type is `String`.
