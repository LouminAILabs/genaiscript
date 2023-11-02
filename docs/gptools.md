# Authoring GPTool scripts

GPTools has a text template engine that is used to expand and assemble prompts before being sent to OpenAI. These templates can be forked and modified.

All prompts are JS files named as `*.gptool.js`. You can use the `GPTools - Fork a gptool...` to fork any known prompt.

All `system.*.gptool.js` are considered system prompt templates
and are unlisted by default. There is no variable expansion in those.

## Example

```js
gptool({
    title: "Shorten", // displayed in UI
    // also displayed, but grayed out:
    description:
        "A prompt that shrinks the size of text without losing meaning",
    categories: ["shorten"], // see Inline prompts later
})

// this appends text to the prompt
$`Shorten the following FILE. Limit changes to minimum.`

// you can debug the generation using goo'old logs
console.log({ fragment: env.file })

// this is similar to $`SUMMARY: ${env.fragment}`
// but the variable is appropriately delimited
def("FILE", env.file)

// more text appended to prompt
$`Respond with the new FILE.`
```

## Metadata

Prompts use `gptool({ ... })` function call
to configure the title and other user interface elements.

```js
gptool({
    title: "Shorten", // displayed in UI
    // also displayed, but grayed out:
    description:
        "A prompt that shrinks the size of text without losing meaning",
    categories: ["shorten"], // see Inline prompts later
})
```

### title: string

`title` is used as the prompt name, displayed in the light-bulb UI

```js
gptool({ title: "Shorten" })
```

#### description: string

`description` provides more details and context about the prompt.

```js
gptool({
    title: "Shorten",
    description:
        "A prompt that shrinks the size of text without losing meaning.",
})
```

### system: prompt_template_id[]

Override the system prompts with a custom prompt.

```js
gptool({
    title: "Generate code",
    system: ["system.code"],
})
```

### outputFolder

You can specify an output folder using `outputFolder` in the script.

```js
gptool({
    ...,
    outputFolder: "src",
})
```

You can specify the output folder using `outputFolder` variable in the gpspec file.

```markdown
<!-- @outputFolder

mysrc

-->
```

### LLM parameters

These are taken from prompt, or from system prompt, or set to default.

-   `temperature: 1`, makes the model more "creative", defaults to 0.2
-   `model: "gpt-4"`, changes default model
-   `maxTokens: 800`, sets the maximum response size

### Other parameters

-   `unlisted: true`, don't show it to the user in lists. Template `system.*` are automatically unlisted.

See `gptools.d.ts` in the sources for details.

## Logging

Use `console.log` and friends to debug your prompts.

## Variable Expansion

Variables are referenced and injected using `env.variableName` syntax.

When you apply a prompt to a given fragment, a number of variables are set including

-   `env.fence` set to a suitable fencing delimiter that will not interfere with the user content delimiters.
-   `env.links` set of linked files and content

> For a full list with values, run any prompt, click on the "GPTools" in the status bar and look at prompt expansion trace.

### Fenced variables

As you expand user markdown into your prompt, it is important to properly fence the user code, to prevent (accidental) prompt injection and confusion.

The `env.fence` variable is set to a suitable fencing delimiter that will not interfere with the user content delimiters.

```js
$`
${env.fence}
${env.fragment}
${env.fence}
`
```

The `def("SUMMARY", env.fragment)` is a shorthand to generate a fence variable output.
The "meta-variable" (`SUMMARY` in this example) name should be all uppercase (but can include
additional description, eg. `"This is text before SUMMARY"`).

```js
def("SUMMARY", env.fragment)

// approximately equivalent to:

$`SUMMARY:`
fence(env.fragment)

// approximately equivalent to:

$`SUMMARY:
${env.fence}
${env.fragment}
${env.fence}
`
```

### Linked files

When the markdown references to a local file, the link name and content will be available through `env.links`

```js
Use documentation from DOCS.

def("DOCS", env.links.filter(f => f.filename.endsWith(".md")))
```

In the coarch files, those link you be part of a bulletted list.

### Current file

The current file is also available as a linked file through, `env.file`

### fetchText(ur: string | LinkedFile): Promise<{ ok: boolean; status: number; statusText: string; text?: string; file: LinkedFile }>

Use `fetchText` to to issue GET requests and download text from the internet.

```ts
const { text, file } = await fetchText("https://....")
if (text) $`And also ${text}`

def("FILE", file)
```

## Conditional expansion

You can use regular JavaScript `if` statements.

```js
if (env.output) def("CODE", env.output)
```

## Inline variable

You can inject custom variables in the process by authoring them as markdown comments in your `.gpspec.md` files. The variable are accessible through the `env.vars` field.

```markdown
Lorem ipsum...

<!-- @myvar

myvalue
-->
```

And somewhere in the prompt

```js
const myvalue = env.vars["myvar"]
```

## Inline prompts

You can inject prompt in the process by authoring them as markdown comments in your `.gpspec.md` files. Essentially, you are defining variables that will be expanded in the prompt templates.

This example defines a prompt instruction that will be injected in all prompts (that refer to that variable).

```markdown
Lorem ipsum...

<!-- @prompt

Avoid acronyms.
-->
```

Another prompt just for the summaries.

```markdown
Lorem ipsum...

<!-- @prompt.summarize
Keep it short.
-->
```

The prompts have to reference the variable, that is the `summarize.gptool.js` has to include `"summarize"`
as one of it's `categories` (otherwise, only `@prompt` is inserted).
The expansion of these variables is scoped. If you include `"bar.baz"` category,
it will insert variables `{{@prompt}}`, `{{@prompt.bar}}`, and `{{@prompt.bar.baz}}` (in this order, skipping any missing variables).

The inline prompts have to occur at the end of the body of a fragment.

```markdown
# Image resize {#OI62}

A command line that takes a file name, a size, and an output file name, resizes the image using the best algorithm, and saves the resized image. Use node.js LTS.

<!-- @prompt

Use the writing style of software technical writer.

-->
```

## Settings

The Visual Studio Code extension has various configuration settings:

### `max cached temperature`

This setting controls the threshold to disable caching for prompts with high temperature; since the temperature increases the randomness
of the response. Default is `0.1`.