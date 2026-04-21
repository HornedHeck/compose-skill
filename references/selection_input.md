# Selection Input Pattern

## Core Principles

- **Unidirectional Data Flow & State Hoisting:** State flows down, and events flow up. Always hoist selection state to a parent composable or a `ViewModel`. This makes individual selectable items (like rows or buttons) stateless, reusable, and easier to test.
- **Single Source of Truth:** Use the `ViewModel` (or a dedicated state holder class) as the source of truth for the selection state. This ensures consistency and preserves selection across configuration changes.
- **Keep Compose focused on UI:** Calculate logic (like how a selected item affects other state values) exclusively in the `ViewModel`. This maintains a clear separation of concerns and keeps the UI layer purely for rendering and capturing user intent.
- **Model selection state securely:** Use the type system to prevent invalid states:
  - Use non-null fields for strictly single selection.
  - Use nullable fields (`T?`) for optional selection.
  - Use `Set<T>` for multiple selections to ensure item uniqueness and fast lookup.

## Example - Quick Date Ranges for filter:
```kotlin
enum class QuickRange(@StringRes val visibleName : Int){
    THIS_WEEK(R.string.this_week),
    LAST_WEEK(R.string.last_week),
    THIS_MONTH(R.string.this_month),
    LAST_MONTH(R.string.last_month)
}

data class FilterUiState(
    val selectedQuickRange: QuickRange? = null,
    val startDate : LocalDate? = null,
    val endDate : LocalDate? = null,
)

class FilterViewModel: ViewModel(){
    
    private val _uiState = MutableStateFlow(FilterUiState())
    val uiState = _uiState.asStateFlow()

    fun selectQuickRange(range: QuickRange?) {
        if (uiState.value.selectedQuickRange != range) {
            _uiState.update { it.copy(selectedQuickRange = range) }
        }
    }

    fun selectStartDate(startDate : LocalDate) {
        // Implementation
    }

    fun selectEndDate(endDate : LocalDate) {
        // Implementation
    }
}

@Composable
fun DateSection(
    startDate: LocalDate?,
    endDate: LocalDate?,
    onStartDateChange: (LocalDate) -> Unit,
    onEndDateChange: (LocalDate) -> Unit,
    selectedRange: QuickRange?,
    onQuickRangeSelected: (QuickRange?) -> Unit
) {

    Column{
        Row(){
            DateInput(
                labelResId = R.string.start_date,
                selectedDate = startDate,
                onDateSelected = onStartDateChange
            )
            DateInput(
                labelResId = R.string.end_date,
                selectedDate = endDate,
                onDateSelected = onEndDateChange
            )
        }
        QuickDateRanges(
            selectedRange = selectedRange,
            onQuickRangeSelected = onQuickRangeSelected
        )
    }
}

@Composable
fun QuickDateRanges(
    selectedRange : QuickRange?,   
    onQuickRangeSelected: (QuickRange?) -> Unit,
) {
    FlowRow(
        modifier = Modifier.fillMaxWidth()
    ) {
        QuickRange.entries.forEach { range ->
            FilterButton(
                labelResId = range.visibleName,
                selected = selectedRange == range,
                onClick = { onQuickRangeSelected(range) },
            )
        }
    }
}
```
Implementation Details
- **Centralize mapping logic:** Notice how the conversion of a `QuickRange` to actual `startDate` and `endDate` values is handled entirely in the `ViewModel`. This keeps the Composable stateless regarding business logic.
- **Keep equality checks simple:** Use straightforward equality checks (`selected = selectedRange == range`) for the `selected` parameter. Avoid complex conditions in the `@Composable` to maintain fast and predictable recomposition.
- **Attach UI data to Enums:** By attaching display resources directly to the enum (`val visibleName: Int`), we avoid writing large `when` statements in the UI. This keeps Composables lean and delegates mapping to the state definition.
