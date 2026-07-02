# Components

Power Apps has several kinds of reusable UI and extension patterns. Be precise about which one the user means.

## Component Types

Common meanings of "component":

- Canvas components: reusable building blocks made inside Canvas Apps.
- Component libraries: shared libraries of Canvas components for reuse across apps.
- Code components: Power Apps component framework controls, often called PCF controls.

Ask which type the user means when it matters.

## Canvas Components

Canvas components are best for repeated UI and behavior inside Canvas Apps.

Use them for:

- Headers and app bars.
- Navigation.
- Confirmation dialogs.
- Loading overlays.
- Reusable input blocks.
- Status badges.
- Repeated layout sections.

Good component design:

- Uses input properties for text, configuration, and state.
- Uses output properties for values the host app needs.
- Uses behavior/event-style properties only when the target environment supports them; otherwise let the host screen handle actions based on component output state.
- Avoids direct references to screen controls.
- Avoids hard-coded data source names unless intentionally data-specific.
- Has accessible labels for interactive controls.
- Adapts to the width and height provided by the host screen.

## Component Libraries

Use component libraries when multiple apps should share the same component. They are useful for consistent design systems, navigation, headers, dialogs, and branded UI.

Practical guidance:

- Keep library components stable and generic.
- Version changes deliberately.
- Test updates in consuming apps before broad rollout.
- Avoid putting highly app-specific business logic in shared components.
- Document input and output properties.

## Custom Properties

Use custom properties to make a component configurable.

Common input properties:

- `TitleText`
- `Visible`
- `Items`
- `SelectedKey`
- `IsBusy`
- `MessageText`
- `Theme`

Common output or event properties:

- `OnSelect`
- `OnConfirm`
- `OnCancel`
- `SelectedItem`
- `IsValid`

Prefer clear property names. Avoid vague names such as `Value1` or `Flag`.

## Component State

Keep state ownership clear:

- The host screen should usually own workflow state.
- The component should expose events and selected values.
- Use internal component state only for visual behavior that belongs inside the component.

Example host state:

```powerfx
UpdateContext({ locShowConfirm: true })
```

Example component configuration:

```powerfx
cmpConfirm.Visible = locShowConfirm
cmpConfirm.TitleText = "Delete request"
cmpConfirm.MessageText = "This action cannot be undone."
```

## Code Components / PCF

Power Apps component framework code components are custom controls built by professional developers. They can be used to improve the user experience in model-driven apps and canvas apps, subject to current platform support and limitations.

Use PCF when:

- A standard Canvas control cannot meet the requirement.
- Rich custom rendering is needed.
- A specialized input, visualization, or interaction is required.
- Professional development, source control, packaging, and testing are available.

Avoid PCF when:

- A standard control or Canvas component is enough.
- The team cannot maintain TypeScript and packaging.
- The requirement is only styling that can be handled with normal controls.
- The control would duplicate built-in platform behavior.

## Canvas Components vs PCF

Prefer Canvas components for maker-owned reusable UI inside Canvas Apps. Prefer PCF for custom controls that need code, advanced rendering, or behavior not available with built-in Canvas controls.

Decision guide:

| Requirement | Prefer |
| --- | --- |
| Reusable header or nav | Canvas component |
| Confirmation dialog | Canvas component |
| Reusable validation UI | Canvas component |
| Custom calendar or grid behavior beyond built-in controls | PCF |
| Specialized visualization | PCF |
| Organization-wide design pattern across apps | Component library |

## Limitations and Caution

Do not assume:

- A PCF API is available in both model-driven and canvas apps.
- A code component can bypass platform security.
- A component library update is risk-free for consuming apps.
- A component can access tenant or data source metadata unless explicitly designed and permitted.

When answering PCF questions offline, ask for:

- App type: canvas or model-driven.
- Control purpose.
- Target host.
- Data binding needs.
- Current error or limitation.
- Whether the team can maintain a professional code component.

## Accessibility

Components must preserve accessibility:

- Use accessible names.
- Keep focus order logical.
- Support keyboard interaction.
- Do not rely on color alone.
- Make dialogs clear and dismissible.
- Test narrow layouts.

## ALM

For production apps:

- Put component libraries and PCF controls in solutions where applicable.
- Version shared components.
- Test consuming apps after component updates.
- Use managed solutions for test and production.
- Document dependencies.

## Answering Component Questions

When the user asks about components:

1. Clarify whether they mean Canvas components, component libraries, or PCF/code components.
2. Recommend the simplest component type that meets the requirement.
3. Provide property names and usage patterns.
4. Mention accessibility, ALM, and maintenance implications.
5. Warn when current platform support must be verified in Microsoft Learn or the target environment.
