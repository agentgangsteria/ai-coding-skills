# Naming a Split Component

When a component splits into a stateful wrapper and a pure one, the pure component takes the most specific accurate name available and the wrapper takes what is left.

Never put a role suffix on the pure component — `*View`, `*Presentational`, `*UI`, `*Markup`. The pair already shows which half holds the state, so the suffix spends a word repeating the file layout, and it collides the moment one wrapper renders two pure components.

## Name the pure component

Work down these rungs and stop at the first that yields a name.

1. **The UI noun.** What would a designer call this element? `Bar`, `Card`, `Panel`, `Strip`, `Banner`, `Drawer`, `Dialog`, `List`.
2. **The design source.** Take the word the design canvas, the mockup, or the existing Storybook sidebar already uses for it.
3. **The part, not the feature.** When the pure component renders one distinguishable part of a larger feature, name it for that part — `NavigationBar` is the bar inside `Navigation`.
4. **The feature name.** Only when the pure component genuinely *is* the whole feature and no noun narrows it.

Check the result as a catalogue entry: `Components/NavigationBar` names a thing, `Components/NavigationView` names a leftover.

## Name the wrapper

The feature name, when rungs 1–3 left it free — `Navigation` wraps `NavigationBar`.

`<Feature>Container` only when rung 4 took the feature name — `ContactSectionContainer` wraps `ContactSection`.

`*Container` is the last rung of the whole procedure, not a shortcut past the first three. It is mechanical where a real name takes judgement, so it is what gets reached for under pressure. Two guards:

- **Show your work.** Before writing either filename, name at least two specific candidates tried and rejected from rungs 1–3, and why each was wrong. "Nothing came to mind" is not a rejected candidate — it means those rungs went unattempted.
- **Expect it to be rare.** A repo where more than a handful of pairs end in `*Container` has stopped naming and started labelling.

`*Wrapper` is the same marker under another word. Pick one per repo and never mix; these conventions use `*Container`.

## Precedence

This procedure outranks matching the neighbours. When a sibling still carries a role suffix, name the new component by the rungs above and flag the sibling for rename — never propagate the old suffix for consistency's sake.
