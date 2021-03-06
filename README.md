
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
- [Install](#install)
- [Credits](#credits)
- [Contribute](#contribute)
- [License](#License)

## Demo

[Check it out](https://svelte-autocomplete-select.netlify.app/)

## Usage

```svelte
<script>
     import AutocompleteSelect from "svelte-autocomplete-select";
</script>

<AutocompleteSelect
    options={["Option 1", "Option 2", "Option 3", "Option 4"]}
    onSubmit={console.log}
    placeholder="Basic"
/>
```

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
    
