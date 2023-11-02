# Authoring GPSpecs

To start using GPTools, create a new `.gpspec.md` file and start adding content as markdown. You can use the CodeAction QuickFix light bulb to launch the gptools on this file.

```markdown A sample CoArch document.
# email address recognizer

Write a function that takes a string argument and returns true if the whole string is a valid email address, false otherwise.

...
```

When an AI transformation is computed, a refactoring code preview will be shown to confirm the changes. Click on each line of the change tree to see individual diff views. This is the same user experience as a refactoring.

You can accept or cancel the changes using the buttons at the bottom of the view.

## GPSpec Refinement

If you need to "influence" the answer of the LLM, you can click on the **Refine GPSpec** button in the status dialog (click on the statub bar icon) to refine the gpspec file
by adding a line. This flow provides an iterative, chat like experience to evolve your gpspec file.

## Running next GPTool

Once you have used a GPTool on a GPSpec file, this file becomes the "active GPSpec"
and you can use the GPTools status bar to launch the next GPTool without having
to open the gpspec file.