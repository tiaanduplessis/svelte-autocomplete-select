<script>
  import AutocompleteSelect from "./AutocompleteSelect.svelte";

  const basicOptions = ["Option 1", "Option 2", "Option 3", "Option 4"];
  const objectOptions = [
    { label: "Option 1", foo: true, bar: false },
    { label: "Option 2", foo: false, bar: true },
    { label: "Option 3", foo: true, bar: false }
  ];
  let asyncOptions = [];
  let isLoading = false;
  let timeout;

  const getAsyncOptions = value => {
    if (timeout) {
      clearTimeout(timeout);
    }
    isLoading = true;
    timeout = setTimeout(() => {
      isLoading = false;
      asyncOptions = basicOptions;
    }, 2000);
  };

  const handleSubmit = value => {
    alert(`Selected! ${JSON.stringify(value)}`);
  };
</script>

<style>
  :global(html) {
    font-family: "Inter", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
      Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif,
      "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
    color: #212121;
  }

  :global(body) {
    line-height: 1.5;
    margin: 0;
  }

  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 0 auto;
    max-width: 60rem;
    padding: 3rem 1rem;
    text-align: center;
  }

  h1 {
    font-size: 2rem;
  }

  h2 {
    margin: 3rem auto 1rem;
    font-size: 1.25rem;
  }

  footer {
    padding: 2rem;
  }
</style>

<main>
  <h1>svelte-autocomplete-select</h1>

  <p>Flexible, zero-dependency Autocomplete component for Svelte.</p>

  <h2>Basic</h2>

  <AutocompleteSelect
    options={basicOptions}
    onSubmit={handleSubmit}
    placeholder="Basic" />


  <h2>With Icon</h2>

  <AutocompleteSelect
    options={basicOptions}
    onSubmit={handleSubmit}
    placeholder="With Icon"
    let:isExpanded>
    <span slot="icon">{isExpanded ? '☝️' : '👇'}</span>
  </AutocompleteSelect>

  <h2>With forced select</h2>

  <AutocompleteSelect
    options={basicOptions}
    onSubmit={handleSubmit}
    placeholder="With forced select"
    isSelectRequired={true} />

  <h2>With array of objects as options</h2>

  <AutocompleteSelect
    options={objectOptions}
    getValue={obj => obj.label}
    onSubmit={handleSubmit}
    placeholder="With array of objects as options" />

  <h2>With custom options</h2>

  <AutocompleteSelect
    options={basicOptions}
    onSubmit={handleSubmit}
    placeholder="With custom options"
    let:match
    let:handleSubmit>
    <li style="padding: 1rem;" on:click={() => handleSubmit(match)}>
      😀
      <strong>{match}</strong>
    </li>
  </AutocompleteSelect>

  <h2>With async options</h2>

  <AutocompleteSelect
    options={asyncOptions}
    predicate={results => results}
    onInput={value => getAsyncOptions(value)}
    onSubmit={handleSubmit}
    placeholder="With async options">
    <span slot="icon">{isLoading ? '🤔' : '👇'}</span>
  </AutocompleteSelect>

  <footer>
    <a
      href="https://github.com/tiaanduplessis/svelte-autocomplete-select"
      target="_blank"
      rel="noopener noreferrer">
      View on GitHub
    </a>
  </footer>
</main>
