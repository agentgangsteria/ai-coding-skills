# Naming a Split Component

Run this only once the split is earned — `SKILL.md` gates it on a named file that renders the pure half. Naming an unearned pair is how a pass-through acquires a real-looking name and survives review.

When a component splits into a stateful wrapper and a pure one, the pure component takes the most specific accurate name available and the wrapper takes what is left.

Never put a role suffix on the pure component — `*View`, `*Presentational`, `*UI`, `*Markup`. The pair already shows which half holds the state, so the suffix spends a word repeating the file layout, and it collides the moment one wrapper renders two pure components.

## Name the pure component

Work down these rungs and stop at the first that yields a name.

1. **The noun already in the code.** The component usually named itself before you arrived: whoever wrote the markup had to name the thing to write an `id`, an `aria-label`, or a class. Read the component and grep the feature folder for that vocabulary — element `id`s, `aria-label`s, semantic class names, story `title`s, and the words the comments reach for — plus whatever the design canvas or the Storybook sidebar already calls it. Usual finds: `Bar`, `Card`, `Panel`, `Strip`, `Banner`, `Drawer`, `Dialog`, `List`.
2. **The part, not the feature.** When the pure component renders one distinguishable part of a larger feature, name it for that part — `NavigationBar` is the bar inside `Navigation`.
3. **The feature name.** Only when the pure component genuinely *is* the whole feature and no noun narrows it.

Done when: rung 1 was answered by reading, not by recall. Quote where the noun came from — or, to fall past it, quote the `id`s, classes and comments you read that lack one. "No noun narrows it" is a claim about the code and carries the same burden as any other; unsupported, it means rung 1 went unattempted.

Check the result as a catalogue entry: `Components/NavigationBar` names a thing, `Components/NavigationView` names a leftover.

## Name the wrapper

The feature name, when rungs 1–2 left it free — `Navigation` wraps `NavigationBar`.

`<Feature>Container` only when rung 3 took the feature name — `ContactSectionContainer` wraps `ContactSection`.

`*Container` is the last rung of the whole procedure, not a shortcut past the first two. It is mechanical where a real name takes judgement, so it is what gets reached for under pressure. Two tripwires:

- **Twice in one change is once too many.** Two pairs landing on `*Container` together is not the rare case arriving twice — it is rung 1 skipped twice. Go back and read the markup.
- **Expect it to be rare.** A repo where more than a handful of pairs end in `*Container` has stopped naming and started labelling.

`*Wrapper` is the same marker under another word. Pick one per repo and never mix; these conventions use `*Container`.

## Where this goes wrong

The contact form was ruled a rung-3 case — "no UI noun narrows the contact form" — and nearly shipped as `ContactForm`/`ContactFormContainer`. The noun was in the file the whole time: `id="contact-form-panel"`, `className="… bg-panel …"`, a story comment about rendering "the panel standalone", and a prop comment about measuring "the form panel". Rung 1 had been answered from recall. The pair is `ContactFormPanel`/`ContactForm`.

## Precedence

This procedure outranks matching the neighbours. When a sibling still carries a role suffix, name the new component by the rungs above and flag the sibling for rename — never propagate the old suffix for consistency's sake.
