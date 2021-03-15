# Svelte 

- [Introduction](#introduction)
    * [Basic Component Structure](#basic-component-structure)
    * [Shorthand attributes](#shorthand-attributes)
    * [Render HTML directly into a component](#render-html-directly-into-a-component)
- [Making an APP](#making-an-app)
- [Reactivity](#reactivity)
    * [Click handler example](#click-handler-example)
    * [Reactive declarations](#reactive-declarations)
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

### Click handler example

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

Log the value of a state variable whenever it changes

```sveltehtml
$: console.log(`the count is ${count}`);
```

Group statements together with a block

```sveltehtml
$: {
        console.log(`the count is ${count}`);
        alert(`I SAID THE COUNT IS ${count}`);
}
```

You can even put the $: in front of things like if blocks:

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

Props are declared with the `export` keyword:

```sveltehtml
<script>
	export let answer;
</script>
```

### Spread props

If you have an object of properties, you can 'spread' them onto a component instead of specifying each one:

```sveltehtml
<script>
  import Info from './Info.svelte';

  const pkg = {
    name: 'svelte',
    version: 3,
    speed: 'blazing',
    website: 'https://svelte.dev'
  };
</script>

<Info {...pkg}/>
```

And the above object is matching the properties declared in the `Info` component:

```sveltehtml
<script>
	export let name;
	export let version;
	export let speed;
	export let website;
</script>
```

## Logic

### If blocks

```sveltehtml
{#if user.loggedIn}
	<button on:click={toggle}>
		Log out
	</button>
{/if}
```

### Else blocks

```sveltehtml
{#if user.loggedIn}
	<button on:click={toggle}>
		Log out
	</button>
{:else}
	<button on:click={toggle}>
		Log in
	</button>
{/if}
```

### Else If blocks

```sveltehtml
{#if x > 10}
	<p>{x} is greater than 10</p>
{:else if 5 > x}
	<p>{x} is less than 5</p>
{:else}
	<p>{x} is between 5 and 10</p>
{/if}
```

### Each blocks

```sveltehtml
<ul>
	{#each cats as cat} 
		<li><a target="_blank" href="https://www.youtube.com/watch?v={cat.id}">
			{cat.name}
		</a></li>
	{/each}
</ul>
```    
> NOTE 1: `{#each cats as cat, i}` to get the index.

> NOTE 2: `{#each cats as { id, name }, i}` to destructure.

### Keyed Each blocks

By default, when you modify the value of an each block, it will add and remove items at the end of the block, 
and update any values that have changed. That might not be what you want. If Instead, we'd like to remove only what has changed
and leave the rest unaffected, we could use an unique identifier for the each block:

```sveltehtml
{#each things as thing (thing.id)}
	<Thing current={thing.color}/>
{/each}
```
The (thing.id) tells Svelte how to figure out what changed.

### Await blocks

```sveltehtml
{#await promise}
	<p>...waiting</p>
{:then number}
	<p>The number is {number}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}
```

If you know that your promise can't reject, you can omit the catch block. 
You can also omit the first block if you don't want to show anything until the promise resolves:

```sveltehtml
{#await promise then value}
	<p>the value is {value}</p>
{/await}
```

## Events

As we've briefly seen already, you can listen to any event on an element with the on: directive:

```sveltehtml
<div on:mousemove={handleMousemove}>
	The mouse position is {m.x} x {m.y}
</div>
```
