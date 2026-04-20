# List Patterns

Important:
- Stabilize composable functions (use one of the approaches from Section IV of the main skill to make collections stable).
- Use lazy collections: `LazyColumn`, `LazyRow`, `LazyVerticalGrid`, etc.
- Use `key` function to allow scroll preservation.
- Use `contentType` function in case of several view types.

## Example — Vertical List with 2 View Types

```kotlin
data class SampleData(
    val id: Int,
    val str: String,
    val isUrl: Boolean,
)

@Composable
fun LazyListSample(data: List<SampleData>) {
    LazyColumn {
        items(
            data,
            key = { it.id },         // Unique, primitive, enum or parcelable
            contentType = { it.isUrl } // Non-unique; what you use in if/when inside item composable
        ) { item ->
            if (item.isUrl) {
                UrlItem(item.str)
            } else {
                TextItem(item.str)
            }
        }
    }
}
```
