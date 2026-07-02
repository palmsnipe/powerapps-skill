# Responsive Canvas App Layout

Responsive Canvas Apps should adapt to desktop, tablet, and mobile without duplicating screens for every device. Prefer containers and formulas over fixed coordinates.

## Use Containers

Use a root vertical container for the screen:

- Header.
- Main content.
- Footer or action bar.

Inside the main content, use horizontal or vertical containers depending on available width.

## Horizontal and Vertical Containers

Use vertical containers for stacked layouts:

- Mobile screens.
- Forms.
- Section groups.

Use horizontal containers for side-by-side layouts:

- Gallery plus detail form.
- Filters plus results.
- Command bar layouts.

Use wrap or conditional layout when narrow screens cannot support side-by-side content.

## Flexible Width and Height

Prefer flexible sizing:

```powerfx
Parent.Width
```

```powerfx
Parent.Height
```

```powerfx
Parent.Width * 0.35
```

Use min and max logic where needed:

```powerfx
Max(320, Parent.Width * 0.35)
```

## Avoid Hard-Coded `X` and `Y`

Hard-coded coordinates are fragile. Use container alignment, padding, gap, and fill portions where possible.

Acceptable hard-coded values:

- Consistent padding.
- Header height.
- Minimum control sizes.

Risky hard-coded values:

- Absolute screen positions.
- Fixed widths for content that must work on mobile.
- Overlapping controls that depend on one screen size.

## Useful Formula Patterns

Width of a form in a two-column layout:

```powerfx
If(
    Parent.Width < 900,
    Parent.Width,
    Parent.Width * 0.6
)
```

Gallery width:

```powerfx
If(
    Parent.Width < 900,
    Parent.Width,
    Parent.Width * 0.4
)
```

Button width:

```powerfx
Min(220, Parent.Width)
```

Dynamic label height:

```powerfx
Self.Size * 2.8
```

## Desktop, Tablet, and Mobile

Desktop:

- Use split views where useful.
- Keep actions near the content they affect.
- Avoid excessive empty space.

Tablet:

- Use larger tap targets.
- Keep important commands visible.
- Consider stacked layouts for dense forms.

Mobile:

- Stack content vertically.
- Use full-width inputs and buttons where appropriate.
- Avoid multi-column forms unless fields are very short.

## Accessibility and Spacing

Responsive design is also accessibility work:

- Keep touch targets large enough.
- Preserve readable text size.
- Maintain color contrast.
- Avoid truncating important labels.
- Keep focus order logical.
- Do not use layout changes that confuse screen readers.

## Review Checklist

- Screen uses containers for primary layout.
- Content adapts at narrow widths.
- Important text does not overlap or clip.
- Buttons remain reachable.
- Forms remain readable.
- Keyboard and screen reader flow still makes sense.
