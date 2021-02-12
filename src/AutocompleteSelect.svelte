<script>
  export let onSubmit = () => {};
  export let onInput = () => {};
  export let name
  export let getValue = option => option
  export let predicate = (results, search = "") => results.filter(result => getValue(result).toLowerCase().includes(search.toLowerCase()))
  export let options = [];
  export let isSelectRequired = false;
  export let classes = {};
  export let value = "";
  export let listId = Date.now().toString() 
  export let inputId
  export let placeholder = ""
  export let isExpanded = false;

  let modifierLabelWidth;
  let inputRef;
  let highlightIndex = 0;
  let containerHeight

  const handleExpandResults = () => {
    highlightIndex = 0;
    isExpanded = true;
  };

  const hideResults = () => {
    isExpanded = false

    if(isSelectRequired && !options.find(option => getValue(option) === value)) {
      value = ""
      onInput(value)
    }
  };

  const handleInput = event => {
    onInput(event);
    handleExpandResults();
  };

  const handleKeyDown = ({ key }) => {
    switch (key) {
      case "Escape":
        hideResults();
        break;
      case "ArrowUp":
        if (isExpanded && highlightIndex === 0) {
          highlightIndex = matches.length - 1;
        } else {
          highlightIndex -= 1;
        }
        break;
      case "ArrowDown":
        if (!value && !isExpanded) {
          handleExpandResults();
          break;
        }

        if (isExpanded && highlightIndex === matches.length - 1) {
          highlightIndex = 0;
        } else {
          highlightIndex += 1;
        }
        break;
      case "Tab":
        hideResults();
        break;
      case "Enter":
        handleSubmit(matches[highlightIndex] || value);
        break;
      default:
        return;
    }
  };

  const handleSubmit = match => {
    if (!match) return;
    onSubmit(match);
    value = getValue(match)
    hideResults();
  };

  $: matches = predicate(options, value);
</script>

<style>
  :global(.svelte-autocomplete-select) {
    display: inline-block;
    position: relative;
    border: 1px solid #bebebe;
    border-radius: 0.25rem;
    padding: 0.25rem 0.5rem;
  }

  :global(.svelte-autocomplete-select-input) {
    height: 2.5rem;
    min-width: 12rem;
    font-size: 1rem;
    color: #212121;
    border: none;
    margin: 0;
    padding: 0;
  }

  :global(.svelte-autocomplete-results-container) {
    display: none;
  }

  :global(.svelte-autocomplete-results-container.isExpanded) {
    display: block;
  }

  :global(.svelte-autocomplete-select-overlay) {
    position: fixed;
    top: 0;
    left: 0;
    z-index: 1;
    width: 100%;
    height: 100%;
  }

  :global(.svelte-autocomplete-select-list) {
    width: calc(100% - 2px);
    position: absolute;
    left: 0;
    right: 0;
    top: 2rem;
    list-style-type: none;
    border: 1px solid transparent;
    background-color: transparent;
    color: #212121;
    border-radius: 0 0 .1rem .1rem;
    padding-left: 0;
    margin: 0;
    z-index: 10;
    text-align: left;
  }

  :global(.svelte-autocomplete-select-list.has-options) {
    border-color: #bebebe;
    background-color: #fff;
  }

  :global(.svelte-autocomplete-select-option) {
    padding: 0.5rem;
    user-select: none;
    cursor: pointer;
  }

  :global(.svelte-autocomplete-select-option span) {
    font-weight: bold;
    color: #212121;
  }

  :global(.svelte-autocomplete-select-option[aria-selected="true"]),
  :global(.svelte-autocomplete-select-option:hover) {
    background-color: rgba(0,0, 0, .1);
  }
</style>

<div class="svelte-autocomplete-select {classes.container ?? ''}" bind:clientHeight={containerHeight}>
  <input
    id={inputId}
    type="text"
    class="svelte-autocomplete-select-input {classes.input ?? ''}"
    bind:value={value}
    bind:this={inputRef}
    on:keydown={handleKeyDown}
    on:input={handleInput}
    on:click={handleExpandResults}
    aria-autocomplete="list"
    aria-controls={listId}
    autocapitalize="none"
    autocomplete="off"
    {placeholder}
    {name}
  />
    <slot name="icon" value={value} isExpanded={isExpanded}></slot>
    <div
      class:isExpanded
      class="svelte-autocomplete-results-container {classes.results ?? ''}"
      role="combobox"
      aria-expanded={isExpanded}
      aria-owns={listId}
    >
      <div class="svelte-autocomplete-select-overlay {classes.overlay ?? ''}" on:click={hideResults} />
      <ul id={listId} class="svelte-autocomplete-select-list {classes.list ?? ''} {matches?.length > 0 ? 'has-options' : ''}" style="top: {containerHeight}px;" role="listbox">
        {#each matches as match, index (match)}
          <slot name="option" matches={matches} match={match} index={index} handleSubmit={handleSubmit}>
            <li
              class="svelte-autocomplete-select-option {classes.option ?? ''}"
              on:click={() => handleSubmit(match)}
              aria-selected={index === highlightIndex}
              role="option"
            >
              {getValue(match)}
            </li>
          </slot>
        {/each}
      </ul>
    </div>
</div>
