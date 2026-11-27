# Standards brief

Review the diff against the repo's documented standards, the user's recorded preferences, and the smell baseline below.

Report, per file and hunk:

- **Violations**: each place the diff breaks a documented standard or a recorded preference. Cite the rule (file and rule, or the note it came from). These can be hard violations.
- **Smells**: each baseline smell you see, named and with the hunk quoted. A smell is always a judgement call ("possible Feature Envy"). When a documented standard endorses what the baseline would flag, the standard wins and the smell is dropped.

Leave out anything tooling already enforces. Give `file:line` for every finding. Under 400 words. The report is done when every changed file has been checked against every rule and smell.

## Smell baseline

Fowler's code smells (_Refactoring_, ch. 3), each as _what it is_ → _how to fix_:

- **Mysterious Name**: a name that doesn't reveal what it does or holds. → rename it; if no honest name comes, the design is murky.
- **Duplicated Code**: the same logic shape in more than one hunk or file. → extract the shared shape and call it from both.
- **Feature Envy**: a method that reaches into another object's data more than its own. → move it onto the data it envies.
- **Data Clumps**: the same few fields or params travelling together. → bundle them into one type.
- **Primitive Obsession**: a primitive or string standing in for a domain concept. → give the concept its own small type.
- **Repeated Switches**: the same `switch` or `if`-cascade on the same type in several places. → polymorphism, or one map both sites share.
- **Shotgun Surgery**: one logical change forcing scattered edits across files. → gather what changes together into one module.
- **Divergent Change**: one module edited for several unrelated reasons. → split it so each changes for one reason.
- **Speculative Generality**: abstraction, parameters, or hooks for needs the spec doesn't have. → delete it and inline until a real need shows.
- **Message Chains**: long `a.b().c().d()` navigation the caller depends on. → hide the walk behind one method on the first object.
- **Middle Man**: a class or function that mostly delegates onward. → call the real target directly.
- **Refused Bequest**: an implementer that ignores most of what it inherits. → replace inheritance with composition.
