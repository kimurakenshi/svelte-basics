# Svelte 

- [Introduction](#introduction)
    * [Basic Component Structure](#basic-component-structure)
    * [Shorthand attributes](#shorthand-attributes)
    * [Render HTML directly into a component](#render-html-directly-into-a-component)
- [Making an APP](#making-an-app)
- [Reactivity](#reactivity)
    * [`on:click`](#-on-click-)
    * [Reactive declarations](#reactive-declarations)
        + [Log the value of a state variable whenever it changes](#log-the-value-of-a-state-variable-whenever-it-changes)
        + [Group statements together with a block](#group-statements-together-with-a-block)
        + [You can even put the $: in front of things like if blocks:](#you-can-even-put-the----in-front-of-things-like-if-blocks-)
- [Props](#props)

## Introduction

### Basic Component Structure

A component is Svelte has the following structure:

```sveltehtml
<script>
	let name = 'world';
</script>

<h1>Hello {name.toUpperCase()}!</h1>

<style>
    h1 {
        color: purple;
    }
</style>
```
> NOTE: style rules are scoped to the component.

### Shorthand attributes

```sveltehtml
<script>
	let src = 'tutorial/image.gif';
	let name = 'Rick Astley';
</script>

<img {src} alt="{name} dances.">
```

### Render HTML directly into a component

```sveltehtml
<script>
    let htmlString = '<p>Rick Astley</p>';
</script>

<p>{@html htmlString}</p>
```

> NOTE: Svelte doesn't perform any sanitization of the expression inside. it's critical that you 
> manually escape HTML that comes from sources you don't trust, otherwise you risk exposing your 
> users to XSS attacks. 

## Making an APP

```js
import App from './App.svelte';

const app = new App({
    target: document.body,
    props: {
        // we'll learn about props later
        answer: 42
    }
});
```

## Reactivity

### `on:click`

```sveltehtml
<script>
let count = 0;

function handleClick() {
    count += 1;
}
</script>

<button on:click={handleClick}>
    Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
```

Svelte automatically updates the DOM when your component's state changes. 

### Reactive declarations 

Often, some parts of a component's state need to be computed from other parts (such as a fullname derived 
from a firstname and a lastname), and recomputed whenever they change:

```sveltehtml
<script>
    let count = 0;
    // re-run this code whenever any of the referenced values change
    $: doubled = count * 2;

    function handleClick() {
        count += 1;
    }
</script>

<button on:click={handleClick}>
    Clicked {count} {count === 1 ? 'time' : 'times'}
</button>

<p>{count} doubled is {doubled}</p>
```

#### Log the value of a state variable whenever it changes

```sveltehtml
$: console.log(`the count is ${count}`);
```
#### Group statements together with a block

```sveltehtml
$: {
        console.log(`the count is ${count}`);
        alert(`I SAID THE COUNT IS ${count}`);
}
```
#### You can even put the $: in front of things like if blocks:

```sveltehtml
$: if (count >= 10) {
        alert(`count is dangerously high!`);
        count = 9;
}
```

Because Svelte's reactivity is triggered by assignments, using array methods 
like push and splice won't automatically cause updates. But there's a more idiomatic solution:

```js
function addNumber() {
	numbers = [...numbers, numbers.length + 1];
}
```

A simple rule of thumb: the name of the updated variable must appear on the left hand side of the assignment.

For example:

```js
const foo = obj.foo;
foo.bar = 'baz';
```
...won't update references to obj.foo.bar, unless you follow it up with obj = obj.

## Props
