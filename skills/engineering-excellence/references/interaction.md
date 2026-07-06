# User Interaction

Interactive elements should clearly communicate that they are interactive.

## Cursor

Always use `cursor: pointer` for interactive elements, including:

- buttons
- links styled as buttons
- clickable cards
- menu items
- tabs
- icon buttons
- dropdown triggers
- switches
- checkboxes with custom UI
- radio buttons with custom UI
- any element with a click handler

Never use `cursor: pointer` on non-interactive elements.

## Hover

Interactive elements should provide visual hover feedback.

## Focus

Keyboard users must always receive a visible focus indicator.

Never remove focus outlines unless replaced with an accessible alternative.

## Touch Targets

Interactive controls should provide sufficiently large touch targets (minimum 44×44px when appropriate).

## Accessibility

Interactive elements should be keyboard accessible.

Avoid clickable `<div>` or `<span>` unless absolutely necessary.

Prefer semantic HTML elements such as:

- button
- a
- input