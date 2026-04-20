# Dialog Patterns

Important:
- Remember state hoisting rules.
- Usually 2 types: VM-driven (by external state) and UI-driven (by a button that updates local state).
- Readability >> performance for show/hide dialogs, since they are triggered very rarely.

## 1. UI-Driven Dialog

```kotlin
@Composable
fun PostCardHistory(post: Post, navigateToPost: (String) -> Unit) {
    var openDialog by remember { mutableStateOf(false) }

    Row(
        Modifier.clickable(onClick = { navigateToPost(post.id) }),
    ) {
        PostImage(
            post = post,
            modifier = Modifier.padding(16.dp),
        )
        Column(
            Modifier
                .weight(1f)
                .padding(vertical = 12.dp),
        ) {
            Text(
                text = stringResource(id = R.string.home_post_based_on_history),
                style = MaterialTheme.typography.labelMedium,
            )
            PostTitle(post = post)
            AuthorAndReadTime(
                post = post,
                modifier = Modifier.padding(top = 4.dp),
            )
        }
        IconButton(onClick = { openDialog = true }) {
            Icon(
                painter = painterResource(R.drawable.ic_more_vert),
                contentDescription = stringResource(R.string.cd_more_actions),
            )
        }
    }
    if (openDialog) {
        AlertDialog(
            modifier = Modifier.padding(20.dp),
            onDismissRequest = { openDialog = false },
            title = {
                Text(
                    text = stringResource(id = R.string.fewer_stories),
                    style = MaterialTheme.typography.titleLarge,
                )
            },
            text = {
                Text(
                    text = stringResource(id = R.string.fewer_stories_content),
                    style = MaterialTheme.typography.bodyLarge,
                )
            },
            confirmButton = {
                Text(
                    text = stringResource(id = R.string.agree),
                    style = MaterialTheme.typography.labelLarge,
                    color = MaterialTheme.colorScheme.primary,
                    modifier = Modifier
                        .padding(15.dp)
                        .clickable { openDialog = false },
                )
            },
        )
    }
}
```

Notes:
- Dialog state and state read (`if (openDialog)`) is roughly in the same scope as the button that opens dialog — following state hoisting rules.
- If you need to simplify/shorten this function, prefer to extract the dialog first and then wrap the UI.

## 2. VM-Driven Dialog

```kotlin
@Composable
fun TransactionsScreen(viewModel: MyViewModel) {
    val state by viewModel.state.collectAsStateWithLifecycle()

    TransactionsScreen(
        state,
        callback1,
        callback2,
        // other params
    )
}

@Composable
fun TransactionsScreen(
    state: UiState,
    // other params
) {
    // Normal composables here
    Surface {
        // ...
    }

    // Dialog handling
    if (state.shouldShowDialog) {
        Dialog(/*...*/)
    }
}
```

Notes:
- It is safe to place VM-driven dialog handling in any function that accepts the full state, rather than a part of it. The pattern:

```kotlin
@Composable
fun Foo(state: UiState) {
    // Non-inline composable that takes a subset of state
    PartDependingComposable(state.normalData)

    // Dialogs handling
    if (state.showDialog) {
        Dialog(/*...*/)
    }
}
```

In this case, on dialog show, all functions that accept complete state will recompose, but `PartDependingComposable` will skip because `normalData` is most probably unchanged.
