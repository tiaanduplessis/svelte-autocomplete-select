
# svelte-autocomplete-select
[![package version](https://img.shields.io/npm/v/svelte-autocomplete-select.svg?style=flat-square)](https://npmjs.org/package/svelte-autocomplete-select)
[![package downloads](https://img.shields.io/npm/dm/svelte-autocomplete-select.svg?style=flat-square)](https://npmjs.org/package/svelte-autocomplete-select)
[![standard-readme compliant](https://img.shields.io/badge/readme%20style-standard-brightgreen.svg?style=flat-square)](https://github.com/RichardLitt/standard-readme)
[![package license](https://img.shields.io/npm/l/svelte-autocomplete-select.svg?style=flat-square)](https://npmjs.org/package/svelte-autocomplete-select)
[![make a pull request](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

> Flexible, zero-dependency Autocomplete component for Svelte

## Table of Contents

- [Demo](#demo)
- [Usage](#usage)
- [Props](#props)
- [Bindings and callbacks](#bindings-and-callbacks)
- [Styling](#styling)
- [Slots](#slots)
- [Install](#install)
- [Credits](#credits)
- [Contribute](#contribute)
- [License](#License)

## Demo

[Check it out](https://svelte-autocomplete-select.netlify.app/)

## Usage

This component targets Svelte 3. Start with an array of strings:

```svelte
<script>
     import AutocompleteSelect from "svelte-autocomplete-select";
</script>

<AutocompleteSelect
    options={["Option 1", "Option 2", "Option 3", "Option 4"]}
    onSubmit={console.log}
    name="basic-option"
    inputId="basic-option-input"
    listId="basic-option-results"
    placeholder="Basic"
/>
```

## Props

The types below describe the JavaScript API. `Option` means an item in your
`options` array; strings work with the defaults. For objects, supply a `getValue`
function that returns a string.

| Prop | Type | Default | Description |
| --- | --- | --- | --- |
| `options` | `Option[]` | `[]` | Options to filter and display. Use nonempty strings or objects. Items are used as Svelte keyed-each keys, so avoid duplicate strings or repeated references to the same object. |
| `getValue` | `(option: Option \| string) => string` | `option => option` | Text shown for an option and assigned to `value` when submitted. It can also receive free text submitted with Enter. |
| `predicate` | `(options: Option[], search: string) => Option[]` | Case-insensitive substring filter | Synchronously returns the visible options. The default compares `getValue(option).toLowerCase()` with `search.toLowerCase()` using `includes`. Use `results => results` if options are already filtered. |
| `value` | `string` | `""` | Current input text, used as the search query. Supports `bind:value`. |
| `onSubmit` | `(option: Option \| string) => void` | `() => {}` | Called with the chosen option, or free text when Enter has no highlighted match. See [callback behavior](#bindings-and-callbacks). |
| `onInput` | `(input: Event \| "") => void` | `() => {}` | Receives the native input event while typing, or `""` when required-selection validation clears invalid text. |
| `isSelectRequired` | `boolean` | `false` | Clears text that does not exactly match any `getValue(option)` when the component hides results. This is not the HTML `required` attribute. |
| `isExpanded` | `boolean` | `false` | Whether results are visible. Supports `bind:isExpanded`. |
| `classes` | `object` of CSS class strings | `{}` | Extra classes for individual elements; see [Styling](#styling). |
| `name` | `string \| undefined` | `undefined` | The input's HTML `name`, useful in forms. |
| `inputId` | `string \| undefined` | `undefined` | The input's HTML `id`, useful for a label's `for` attribute. |
| `listId` | `string` | `Date.now().toString()` | The results list's HTML `id`, also used by `aria-controls` and `aria-owns`. Pass a distinct ID for each instance; timestamp defaults can collide. |
| `placeholder` | `string` | `""` | The input's placeholder text. |

`name` and `inputId` have no explicit initializer in the component. Omitting them
leaves the attributes unset, but Svelte's development build warns that these
props were not supplied.

## Bindings and callbacks

Use `bind:value` to read the current text and `onSubmit` to receive the original
option object. `value` contains the string returned by `getValue`, not that object.
Callbacks are props (`onSubmit={...}` and `onInput={...}`), not Svelte component
events (`on:submit` or `on:input`); native input events are not forwarded.

```svelte
<script>
  import AutocompleteSelect from "svelte-autocomplete-select";

  const options = [
    { id: 1, label: "Apple" },
    { id: 2, label: "Banana" }
  ];
  let value = "";
  let isExpanded = false;
  let submitted = null;
  let lastInput = "";

  // Enter can also submit free text, so handle strings as well as objects.
  const getValue = option =>
    typeof option === "string" ? option : option.label;

  function onInput(eventOrValue) {
    lastInput = typeof eventOrValue === "string"
      ? eventOrValue
      : eventOrValue.target.value;
  }
</script>

<label for="fruit-input">Fruit</label>
<AutocompleteSelect
  {options}
  {getValue}
  {onInput}
  onSubmit={option => { submitted = option; }}
  inputId="fruit-input"
  listId="fruit-results"
  name="fruit"
  placeholder="Choose or type a fruit"
  bind:value
  bind:isExpanded
/>
<p>Text: {value}; results open: {isExpanded}</p>
<p>Last input: {lastInput}; submitted: {JSON.stringify(submitted)}</p>
```

- Typing calls `onInput` with the native event, then opens the results. Clicking
  the input also opens results. Arrow keys move the highlighted option; Enter
  submits the highlighted match, or the current text if there is no such match.
- Submitting calls `onSubmit` before updating `value` and hiding results. Empty
  submissions are ignored. Selecting an option does not call `onInput` unless
  required-selection validation then clears the text.
- With `isSelectRequired={true}`, Escape, Tab, an overlay click, or a submission
  hides results and clears text that does not exactly match an option, calling
  `onInput("")`. Enter can still call `onSubmit` with free text before this check;
  validate the callback argument if you only accept listed options.
- Assigning `value` or `isExpanded` from the parent does not call either callback.
  Setting `isExpanded` to `false` directly also skips required-selection validation.
- Empty-string options cannot be submitted and can still trigger `onInput("")`
  when required-selection validation runs. Prefer nonempty option strings.

## Styling

The component includes global default CSS. Pass extra class names through
`classes`; these are appended to the built-in classes, not replacements.

| `classes` key | Element | Built-in class |
| --- | --- | --- |
| `container` | Outer wrapper | `svelte-autocomplete-select` |
| `input` | Text input | `svelte-autocomplete-select-input` |
| `results` | Results wrapper | `svelte-autocomplete-results-container` |
| `overlay` | Outside-click overlay | `svelte-autocomplete-select-overlay` |
| `list` | Results `<ul>` | `svelte-autocomplete-select-list` |
| `option` | Default option `<li>` | `svelte-autocomplete-select-option` |

The results wrapper also has `isExpanded` while open; the list has `has-options`
when matches exist. The highlighted default option has `aria-selected="true"`.
The list's `top` position is set inline from the wrapper height.

Use global CSS, or `:global(...)` inside a parent Svelte component's `<style>`,
to reach the child elements. Combining the built-in and custom classes gives
these overrides higher specificity than the defaults:

```svelte
<script>
  import AutocompleteSelect from "svelte-autocomplete-select";
</script>

<AutocompleteSelect
  options={["Apple", "Banana", "Pear"]}
  name="styled-fruit"
  inputId="styled-fruit-input"
  listId="styled-fruit-results"
  classes={{
    container: "fruit-picker",
    input: "fruit-input",
    list: "fruit-list",
    option: "fruit-option"
  }}
/>

<style>
  :global(.svelte-autocomplete-select.fruit-picker) {
    border-color: #6d28d9;
    border-radius: 0.5rem;
  }

  :global(.svelte-autocomplete-select-input.fruit-input) {
    min-width: 16rem;
    font-size: 1.125rem;
  }

  :global(.svelte-autocomplete-select-list.fruit-list.has-options) {
    background-color: #faf5ff;
    border-color: #6d28d9;
  }

  :global(.svelte-autocomplete-select-option.fruit-option[aria-selected="true"]),
  :global(.svelte-autocomplete-select-option.fruit-option:hover) {
    background-color: #ede9fe;
  }
</style>
```

There are no CSS custom properties or forwarded `class`/`style` props. Use the
documented `classes` keys or the built-in selectors. A custom `option` slot
replaces the default `<li>`, so `classes.option` does not style that replacement.

## Slots

| Named slot | Slot props | Purpose |
| --- | --- | --- |
| `icon` | `value`, `isExpanded` | Content next to the input. |
| `option` | `matches`, `match`, `index`, `handleSubmit` | Custom markup for each match. Call `handleSubmit(match)` to select it. |

Declare `let:` directives on the element carrying the named slot:

```svelte
<script>
  import AutocompleteSelect from "svelte-autocomplete-select";
</script>

<AutocompleteSelect
  options={["Apple", "Banana", "Pear"]}
  name="icon-fruit"
  inputId="icon-fruit-input"
  listId="icon-fruit-results"
>
  <span slot="icon" let:isExpanded aria-hidden="true">
    {isExpanded ? "▴" : "▾"}
  </span>
</AutocompleteSelect>
```

A custom `option` slot must supply its own list-item markup, selection handler,
styling and accessibility attributes. `index` is the position in `matches`, not
the highlighted index; the highlighted state is not exposed as a slot prop.

## Install

This project uses [node](https://nodejs.org) and [npm](https://www.npmjs.com). 

```sh
$ npm install svelte-autocomplete-select
$ # OR
$ yarn add svelte-autocomplete-select
```

## Credits

This package is based on [simply-svelte-autocomplete](https://github.com/themarquisdesheric/simply-svelte-autocomplete)

## Contribute

1. Fork it and create your feature branch: `git checkout -b my-new-feature`
2. Commit your changes: `git commit -am "Add some feature"`
3. Push to the branch: `git push origin my-new-feature`
4. Submit a pull request

## License

MIT 
    
