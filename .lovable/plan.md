

## Fix Table Layout and Hide Sort Icons

### Changes to `src/components/DealExpandedPanel.tsx`

**1. Add `table-fixed w-full` to both tables**

- History table (line ~660): Change `<Table>` to `<Table className="table-fixed w-full">`
- Action Items table (line ~785): Change `<Table>` to `<Table className="table-fixed w-full">`

This forces the browser to strictly respect the percentage widths (70% for Task/Changes, etc.).

**2. Remove sort icons from both sections**

Remove the `{getHistorySortIcon('...')}` calls from all History table headers (lines 666, 671, 676, 681) and `{getActionItemSortIcon('...')}` calls from all Action Items table headers (lines 791, 796, 801, 806, 811).

The column header buttons remain clickable for sorting -- only the arrow icons are removed.

**Before (example):**
```tsx
<button className="flex items-center gap-1" onClick={() => handleHistorySort('changes')}>
  Changes {getHistorySortIcon('changes')}
</button>
```

**After:**
```tsx
<button className="flex items-center gap-1" onClick={() => handleHistorySort('changes')}>
  Changes
</button>
```

This applies to all sortable column headers in both the History and Action Items tables (9 headers total).

