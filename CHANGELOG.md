# Changelog

All notable changes to `qalainau/filament-warp-tree` are documented here.

## [1.1.2] - 2026-11-27

### Fixed

- **Saving a window for an item someone else had just deleted did nothing**: with “Rename”, “Edit”, “Move to…”, “Merge into…”, “Delete” or “Add an item under” open (and your own row buttons from `getTreeNodeActions()`), if another person deleted that item — or it moved out of what you may see, or to another team — pressing the button left the window open and nothing happened (row buttons showed a 404). The window now closes with “That item no longer exists. The tree has been reloaded.”, and the branch is reloaded without the item.

## [1.1.1] - 2026-11-26

### Fixed

- **Nested set descendants with the same left value keep a fixed order**: `descendants()` on a nested set now orders by the left column, then the primary key, before its limit, so a table holding several trees (left values repeat) returns the same items every time.

## [1.1.0] - 2026-11-22

### Added

- **`$defaultOpenLevel` for the tree page**: `TreePage` (and `TreeWidget`, `TreeRelationManager`) can open the branches down to a level when it is shown, with the same name and meaning as `defaultOpenLevel()` of `SelectTree`, `TreeNavigation` and the tree grid. The default is `0` — only the roots, as before.

## [1.0.4] - 2026-11-19

### Fixed

- **Console error `warpTree… is not defined`**: on pages where Livewire re-renders a Tree field inside another Alpine component, the browser console could show `warpTreeSelectTree is not defined` (and the same for the other Tree components) before the component's script had arrived. Empty components are now registered up front, and the real ones replace them as soon as they load. No visible change.

## [1.0.3] - 2026-11-17

### Fixed

- **Tree grid**: with a row opened, the count below the table counted the rows opened under it as well (“Showing 1 to 5 of 3 results”, or “11 to 25 of 30” on the second page). It now counts the top level, as the total already did (“Showing 1 to 3 of 3 results”). Display only; on standard tables and on Warp Tables (`->warp()`).

## [1.0.2] - 2026-11-17

### Changed

- README: the tree grid on a Warp Table (`->warp()`) needs Warp Table 1.5.0 or later, which shows the first column with its indent and arrow. The note that PHPStan does not know `->warpTree()` was out of date since 1.0.1 (the plugin ships a PHPStan extension) and now links to “Static analysis”.

## [1.0.1] - 2026-11-16

### Fixed

- **Static analysis**: Larastan no longer reports “Call to an undefined method” for `->warpTree()`, `->warpTreeSum()`. The plugin ships `extension.neon`, a PHPStan extension that reads Filament's macros (loaded automatically with `phpstan/extension-installer`; otherwise add it to `includes`). See “Static analysis” in the README.
- **Static analysis**: the setting methods of the plugin class are declared as returning the plugin, so `$panel->plugin(…::make()->…())` no longer fails with “Panel::plugin() expects Filament\Contracts\Plugin”.

## [1.0.0] - 2026-11-15

### Added

- **Choosing:** `SelectTree` reads only the opened branches (fast on trees of 50,000 items), searches with the path (*inside bos* finds Sales › East › Boston › Inside sales), shows the chosen item as a path, and checks the value on the server. Lowest level only, a parent with everything under it, disabled and hidden items, extra items such as *No department*, child counts, levels opened at first, lists that stay open, adding an item from the field, nested sets, other key columns, soft-deleted items. Icons on items (`icon()`, `leafIcon()`, or an icon per record), each SVG sent once per request.
- **Switching:** `Compat\SelectTree` takes the methods and defaults of codewithdennis/filament-select-tree, so changing the import is enough.
- **Showing:** `TreePathColumn`, `TreePathEntry` and the `TreeNode` validation rule.
- **Filtering:** `TreeFilter` filters a table by a branch and everything under it, and shows the path again after the page is reloaded.
- **Tree grid:** `->warpTree()` on a table and `HasTreeGrid` on its page (list page, relation manager or table widget) show the rows of the table as a tree. The first column gets the indent and an arrow; the rows under a row are read when it is opened; pagination counts the top level and sorting sorts siblings. `->warpTreeSum()` shows on a parent the total of itself and everything under it. Search and filters show the matching rows with their parents (faded) and open the branches down to them. `TreeIndentAction` and `TreeOutdentAction` change a row's parent, checked on the server (the table's query, `canEdit()` or the policy's `update`, no loops, `maxDepth()`). Needs only a `parent_id` column.
- **Querying:** `HasTree`, `descendantKeys()`, `ancestorKeys()`, `withinSubtreeOf()` and `whereInSubtree()`.
- **Who sees what:** `visibleSubtree()` on the plugin, `ScopedToVisibleSubtree` on resources and `onlyVisibleSubtree()` on fields and filters.
- **Editing:** `TreePage` to drag (or use Alt + arrow keys), rename, add, move, merge and delete items, with the records filed under them (`TreeRecords`) counted and moved along, stale moves refused, a history of changes, and icons on items (`getTreeIcon()`).
- Works in reorderable repeaters, builders, tabs, wizards, modals and modals inside modals, relation managers, with `live()` and `$set`, and in SPA mode. Filament tenancy, ten languages.
- **A tree next to a table:** `HasTreeNavigation` puts the tree on the left of a resource's list page. Clicking a branch filters the table to that branch and everything under it (kept in the URL), each branch shows how many rows it holds, and rows are moved by dragging them onto a branch — several at once when they are ticked. `MoveToBranchBulkAction` does the same without dragging. Moves check `canEdit()` for every row and read rows and branches again through the resource, so tenancy and `visibleSubtree()` hold.
- **`TreeBuilder`:** a form field to edit a small tree inside the form of its record — add, edit and delete items in modals, drag or use Alt + arrow keys to reorder and nest. Saved as a nested array (a JSON column) or as rows of a `HasMany` relationship. The method names and the stored shape match saade/filament-adjacency-list.
- **Several items at once:** tick rows of the tree (Shift for a range) and drag one of them, or press *Move to…*, to move them together — all or nothing, with the same checks as a single move (`moveTreeNodes()`).
- **Open all / close all** buttons beside the search box. Opening stops after 500 branches on very large trees.
- **CSV:** `protected static bool $hasCsvActions` adds *Export CSV* and *Import CSV*. The import updates names, parents and order by id, or adds a tree from another system; every row is checked (visible items only, permissions, loops, depth limit) before anything is written.
- **`CheckboxTree`:** the tree inside the form with a checkbox on every item, for permissions and visibility. A parent has three states (checked, partly checked, empty); checking a parent checks everything under it, also in branches that were never opened. `cascade(false)` checks items one by one.
- **Rows of the tree:** your own buttons on each row (`getTreeNodeActions()`: Filament actions that get the row's item as `$record`), a badge (`getTreeBadge()`, `getTreeBadgeColor()`), a description under the name (`getTreeDescription()`), and the whole form of an item in a modal (`protected static bool $editsInModal`). Rows are read again after an action.
- **A depth limit:** `protected static ?int $maxDepth` on the tree page, widget and relation manager. Drops inside an item at the limit are not offered, and the server refuses every move, merge and new item that would go deeper — counting the levels under the moved item.
- **`TreeWidget`:** the tree page as a widget, with a resource (query, form, labels, permissions) or just a model (policy), editable or read-only.
- **`TreeRelationManager`:** edit the tree that belongs to a record (menu items, a bill of materials) on that record's edit page. Only the record's own items can be seen, moved or chosen as a target.
- `InteractsWithTree`: the trait behind the three, for a Livewire component of your own.
- **Restricting pages per role:** `canAccessUsing()` and `canWriteUsing()` on `FilamentWarpTreePlugin` decide, per page, who may open a `TreePage` and who may only look at it. Read-only people see no buttons that change the tree, can't drag rows, and get a 403 when calling `moveTreeNode()` or `moveTreeNodes()` directly; the hidden actions don't open. `TreePage::canWrite()` can be overridden to make a page read only.
