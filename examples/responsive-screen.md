# Responsive Screen Example

This example uses containers for a responsive Canvas App screen with a header, content area, gallery/form split, and footer actions.

## Screen Structure

```text
scrRequests
└── conRoot
    ├── conHeader
    ├── conContent
    │   ├── conListPane
    │   │   └── galRequests
    │   └── conFormPane
    │       └── frmRequest
    └── conFooter
```

## Screen Settings

Use the app's responsive settings. Avoid fixed layout assumptions where possible.

## Root Container

`conRoot`:

```powerfx
X = 0
Y = 0
Width = Parent.Width
Height = Parent.Height
LayoutDirection = LayoutDirection.Vertical
```

Suggested properties:

```text
PaddingLeft = 16
PaddingRight = 16
PaddingTop = 12
PaddingBottom = 12
Gap = 12
```

## Header

`conHeader`:

```powerfx
Width = Parent.Width
Height = 64
```

Place title, search, and primary actions here. On narrow screens, move search into the content area or stack it below the title.

## Content

`conContent`:

```powerfx
Width = Parent.Width
Height = Parent.Height - conHeader.Height - conFooter.Height - 24
LayoutDirection = If(Parent.Width < 900, LayoutDirection.Vertical, LayoutDirection.Horizontal)
```

Use a gap between panes:

```text
Gap = 12
```

## Gallery Area

`conListPane`:

```powerfx
Width = If(Parent.Width < 900, Parent.Width, Max(320, Parent.Width * 0.38))
Height = If(Parent.Width < 900, Parent.Height * 0.45, Parent.Height)
```

`galRequests`:

```powerfx
Width = Parent.Width
Height = Parent.Height
Items = SortByColumns(Filter(Requests, Status = "Open"), "Created", SortOrder.Descending)
```

Delegation note: confirm the `Filter()` and `SortByColumns()` formula against the actual connector.

## Form Area

`conFormPane`:

```powerfx
Width = If(Parent.Width < 900, Parent.Width, Parent.Width - conListPane.Width - 12)
Height = If(Parent.Width < 900, Parent.Height * 0.55, Parent.Height)
```

`frmRequest`:

```powerfx
Width = Parent.Width
Height = Parent.Height
Item = galRequests.Selected
```

For mobile, set the form to fewer columns. For desktop, use two columns only when labels and values remain readable.

## Footer and Actions

`conFooter`:

```powerfx
Width = Parent.Width
Height = 56
LayoutDirection = LayoutDirection.Horizontal
```

For narrow screens:

```powerfx
LayoutDirection = If(Parent.Width < 600, LayoutDirection.Vertical, LayoutDirection.Horizontal)
Height = If(Parent.Width < 600, 112, 56)
```

Buttons:

```powerfx
Width = If(Parent.Width < 600, Parent.Width, 160)
```

## Accessibility Notes

- Keep focus order from header to content to footer.
- Use clear accessible labels for icon buttons.
- Do not hide validation messages behind hover-only behavior.
- Keep button sizes comfortable for touch.
- Ensure text does not clip at mobile width.
