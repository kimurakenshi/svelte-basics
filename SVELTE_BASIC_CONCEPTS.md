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
  * [Spread props](#spread-props)
- [Logic](#logic)
  * [If blocks](#if-blocks)
  * [Else blocks](#else-blocks)
  * [Else If blocks](#else-if-blocks)
  * [Each blocks](#each-blocks)
  * [Keyed Each blocks](#keyed-each-blocks)
  * [Await blocks](#await-blocks)
- [Events](#events)
  * [Event Handlers Inline:](#event-handlers-inline-)
  * [Event Modifiers](#event-modifiers)
  * [Component Events](#component-events)
  * [Event forwarding](#event-forwarding)
- [Bindings](#bindings)
  * [Text Inputs](#text-inputs)
  * [Checkbox Inputs](#checkbox-inputs)
  * [Group Inputs](#group-inputs)
  * [Textarea Inputs](#textarea-inputs)
  * [Select](#select)
  * [Select Multiple](#select-multiple)
  * [Content editable bindings](#content-editable-bindings)
  * [Each block bindings](#each-block-bindings)
  * [Media elements](#media-elements)
  * [Dimensions](#dimensions)
  * [This](#this)
  * [Component bindings](#component-bindings)
- [Lifecycle](#lifecycle)
  * [onMount](#onmount)
  * [onDestroy](#ondestroy)
  * [beforeUpdate and afterUpdate](#beforeupdate-and-afterupdate)
  * [tick](#tick)
- [Stores](#stores)
  * [Writeable Store](#writeable-store)
  * [Auto-subscriptions](#auto-subscriptions)
  * [Readable stores](#readable-stores)
  * [Derived stores](#derived-stores)
  * [Custom stores](#custom-stores)
  * [Store bindings](#store-bindings)
- [Motion](#motion)
  * [Tweened](#tweened)
  * [Spring](#spring)
- [Transitions](#transitions)
  * [Basic](#basic)
  * [Adding parameters](#adding-parameters)
  * [In and Out](#in-and-out)
  * [Transition events](#transition-events)
  * [Local transitions](#local-transitions)
  * [Deferred transitions](#deferred-transitions)
- [Animations](#animations)
- [Actions](#actions)
  * [Declaration](#declaration)
  * [Adding Parameters](#adding-parameters)
- [Classes](#classes)
  * [Shorthand class directive](#shorthand-class-directive)
- [Component composition](#component-composition)
  * [Slot fallback](#slot-fallback)
  * [Named slots](#named-slots)
  * [Checking for slot content](#checking-for-slot-content)
  * [Slot props](#slot-props)

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
### Event Handlers Inline:

```sveltehtml
<div on:mousemove="{e => m = { x: e.clientX, y: e.clientY }}">
	The mouse position is {m.x} x {m.y}
</div>
```

### Event Modifiers

```sveltehtml
<button on:click|once={handleClick}>
	Click me
</button>
```

The full list of modifiers:

- `preventDefault` — calls event.preventDefault() before running the handler. Useful for client-side form handling, for example.
- `stopPropagation` — calls event.stopPropagation(), preventing the event reaching the next element
- `passive` — improves scrolling performance on touch/wheel events (Svelte will add it automatically where it's safe to do so)
- `nonpassive` — explicitly set passive: false
- `capture` — fires the handler during the capture phase instead of the bubbling phase 
- `once` — remove the handler after the first time it runs
- `self` — only trigger handler if event.target is the element itself

You can chain modifiers together, e.g. `on:click|once|capture={...}`.

### Component Events

Components can also dispatch events. To do so, they must create an event dispatcher.

> `createEventDispatcher` must be called when the component is first instantiated — you can't do it later inside e.g. a setTimeout 
callback. This links dispatch to the component instance.

```sveltehtml
<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function sayHello() {
		dispatch('message', {
			text: 'Hello!'
		});
	}
</script>
```

and to listen to this event We would do:

```sveltehtml
<SomeComponent on:message={handleMessage}/>
```

The directive to listen to events is an attribute prefixed with `on:` followed by the `event name` that we are dispatching.

### Event forwarding

Unlike DOM events, component events don't bubble. If you want to listen to an event on some deeply nested component, 
the intermediate components must forward the event.

An `on:message` event directive without a value means 'forward all message events'.

In the following component hierarchy:

```sveltehtml
<App>
  <Outer>
    <Inner/>
  </Outer>
</App>
```

Outer component can forward events emitted by Inner component like so:

```sveltehtml
<script>
	import Inner from './Inner.svelte';
</script>

<Inner on:message/>
```

> NOTE: Event forwarding works for DOM events too. (ex: `<button on:click>`)
 
## Bindings

As a general rule, data flow in Svelte is top down — a parent component can set props on a child component, 
and a component can set attributes on an element, but not the other way around.

Sometimes it's useful to break that rule and that the reason `bind` exists.

### Text Inputs

```sveltehtml
<input bind:value={name}>
```

This means that not only will changes to the value of name update the input value, 
but changes to the input value will update name.

### Checkbox Inputs

```sveltehtml
<script>
  let yes = false;
</script>

<input type=checkbox bind:checked={yes}>
```

### Group Inputs

If you have multiple inputs relating to the same value, you can use `bind:group` along with the value attribute. 
Radio inputs in the same group are mutually exclusive; checkbox inputs in the same group form an array of selected values.

```sveltehtml
<script>
	let scoops = 1;
	let flavours = ['Mint choc chip'];

	let menu = [
		'Cookies and cream',
		'Mint choc chip',
		'Raspberry ripple'
	];
</script>

<h2>Size</h2>

<label>
	<input type=radio bind:group={scoops} value={1}>
	One scoop
</label>

<label>
	<input type=radio bind:group={scoops} value={2}>
	Two scoops
</label>

<label>
	<input type=radio bind:group={scoops} value={3}>
	Three scoops
</label>

<h2>Flavours</h2>

{#each menu as flavour}
	<label>
		<input type=checkbox bind:group={flavours} value={flavour}>
		{flavour}
	</label>
{/each}
```

### Textarea Inputs

```sveltehtml
<script>
  let value = `Some words are *italic*, some are **bold**`;
</script>

<textarea bind:value></textarea>
```

### Select

```sveltehtml
<script>
	let questions = [
		{ id: 1, text: `Where did you go to school?` },
		{ id: 2, text: `What is your mother's name?` },
		{ id: 3, text: `What is another personal fact that an attacker could easily find with Google?` }
	];

	let selected;

	let answer = '';

	function handleSubmit() {
		alert(`answered question ${selected.id} (${selected.text}) with "${answer}"`);
	}
</script>

<h2>Insecurity questions</h2>

<form on:submit|preventDefault={handleSubmit}>
	<select bind:value={selected} on:change="{() => answer = ''}">
		{#each questions as question}
			<option value={question}>
				{question.text}
			</option>
		{/each}
	</select>

	<input bind:value={answer}>

	<button disabled={!answer} type=submit>
		Submit
	</button>
</form>
```

>  `<option>` values are objects rather than strings. 

### Select Multiple

A select can have a multiple attribute, in which case it will populate an array rather than selecting a single value:

```sveltehtml
<script>
  let menu = [
    'Cookies and cream',
    'Mint choc chip',
    'Raspberry ripple'
  ];
  
  let flavours = ['Mint choc chip'];
</script>
<select multiple bind:value={flavours}>
	{#each menu as flavour}
		<option value={flavour}>
			{flavour}
		</option>
	{/each}
</select>
```

### Content editable bindings

Elements with a `contenteditable="true"` attribute support `textContent` and `innerHTML` bindings:

```sveltehtml
<div
	contenteditable="true"
	bind:innerHTML={html}
></div>
```

### Each block bindings

You can even bind to properties inside an each block.

```sveltehtml
{#each todos as todo}
	<div class:done={todo.done}>
		<input
			type=checkbox
			bind:checked={todo.done}
		>

		<input
			placeholder="What needs to be done?"
			bind:value={todo.text}
		>
	</div>
{/each}
```

> Note that interacting with these `<input>` elements will mutate the array. If you prefer to work with immutable data, 
you should avoid these bindings and use event handlers instead.


### Media elements

The `<audio>` and `<video>` elements have several properties that you can bind to. This example demonstrates a few of them- ````

The complete set of bindings for `<audio>` and `<video>` is as follows — six readonly bindings...

- `duration` (readonly) — the total duration of the video, in seconds
- `buffered` (readonly) — an array of {start, end} objects
- `seekable` (readonly) — ditto
- `played` (readonly) — ditto
- `seeking` (readonly) — boolean
- `ended` (readonly) — boolean

...and five two-way bindings:

- `currentTime` — the current point in the video, in seconds
- `playbackRate` — how fast to play the video, where 1 is 'normal'
- `paused` — this one should be self-explanatory
- `volume` — a value between 0 and 1
- `muted` — a boolean value where true is muted

Videos additionally have readonly `videoWidth` and `videoHeight` bindings.

### Dimensions

Every `block-level` element has clientWidth, clientHeight, offsetWidth and offsetHeight read-only bindings:

```sveltehtml
<div bind:clientWidth={w} bind:clientHeight={h}>
	<span style="font-size: {size}px">{text}</span>
</div>
```

### This

The readonly this binding applies to every element (and component) and allows you to obtain a reference to rendered elements. 
For example, we can get a reference to a `<canvas>` element:

```sveltehtml
<canvas
	bind:this={canvas}
	width={32}
	height={32}
></canvas>
```

> Note that the value of canvas will be undefined until the component has mounted, so we put the logic inside the onMount life cycle function.

### Component bindings

Just as you can bind to properties of DOM elements, you can bind to component props. For example, we can bind to the value prop of this `<Keypad>` component as though it were a 
form element:

```sveltehtml
<script>
  import Keypad from './Keypad.svelte';

  let pin;
  $: view = pin ? pin.replace(/\d(?!$)/g, '•') : 'enter your pin';

  function handleSubmit() {
    alert(`submitted ${pin}`);
  }
</script>

<h1 style="color: {pin ? '#333' : '#ccc'}">{view}</h1>

<Keypad bind:value={pin} on:submit={handleSubmit}/>
```

> Use component bindings sparingly. It can be difficult to track the flow of data around your application if you have 
> too many of them, especially if there is no 'single source of truth'.

## Lifecycle

### onMount

Runs after the component is first rendered to the DOM. 

```sveltehtml
<script>
	import { onMount } from 'svelte';

	let photos = [];

	onMount(async () => {
		const res = await fetch(`https://jsonplaceholder.typicode.com/photos?_limit=20`);
		photos = await res.json();
	});
</script>
```

> It's recommended to put the fetch in onMount rather than at the top level of the `<script>` because of server-side 
> rendering (SSR). With the exception of onDestroy, lifecycle functions don't run during SSR, which means we can avoid 
> fetching data that should be loaded lazily once the component has been mounted in the DOM.

> If the `onMount` callback returns a function, that function will be called when the component is destroyed.

### onDestroy

To run code when your component is destroyed.

```sveltehtml
<script>
	import { onDestroy } from 'svelte';

	let seconds = 0;
	const interval = setInterval(() => seconds += 1, 1000);

	onDestroy(() => clearInterval(interval));
</script>
```

### beforeUpdate and afterUpdate

The `beforeUpdate` function schedules work to happen immediately before the DOM has been updated. 
`afterUpdate` is its counterpart, used for running code once the DOM is in sync with your data.

```sveltehtml
<script>
	import { beforeUpdate, afterUpdate } from 'svelte';

	let div;
	let autoscroll;

	beforeUpdate(() => {
		autoscroll = div && (div.offsetHeight + div.scrollTop) > (div.scrollHeight - 20);
	});

	afterUpdate(() => {
		if (autoscroll) div.scrollTo(0, div.scrollHeight);
	});
</script>
```

> Note that `beforeUpdate` will first run before the component has mounted, so we need to check for the existence of div 
> before reading its properties.

### tick

The tick function is unlike other lifecycle functions in that you can call it any time, not just when the component 
first initialises. It returns a promise that resolves as soon as any pending state changes have been applied to the 
DOM (or immediately, if there are no pending state changes).

When you update component state in Svelte, it doesn't update the DOM immediately. Instead, it waits until the next microtask 
to see if there are any other changes that need to be applied, including in other components. Doing so avoids unnecessary 
work and allows the browser to batch things more effectively.

You can see that behaviour in this example. Select a range of text and hit the tab key. Because the `<textarea>` value changes, 
the current selection is cleared and the cursor jumps, annoyingly, to the end. We can fix this by importing tick ...and running 
it immediately before we set this.selectionStart and this.selectionEnd at the end of handleKeydown:

```sveltehtml
<script>
	import { tick } from 'svelte';

	let text = `Select some text and hit the tab key to toggle uppercase`;

	async function handleKeydown(event) {
		if (event.key !== 'Tab') return;

		event.preventDefault();

		const { selectionStart, selectionEnd, value } = this;
		const selection = value.slice(selectionStart, selectionEnd);

		const replacement = /[a-z]/.test(selection)
			? selection.toUpperCase()
			: selection.toLowerCase();

		text = (
			value.slice(0, selectionStart) +
			replacement +
			value.slice(selectionEnd)
		);

		await tick();
		this.selectionStart = selectionStart;
		this.selectionEnd = selectionEnd;
	}
</script>

<style>
	textarea {
		width: 100%;
		height: 200px;
	}
</style>

<textarea value={text} on:keydown={handleKeydown}></textarea>
```

## Stores

### Writeable Store

Not all application state belongs inside your application's component hierarchy. Sometimes, 
you'll have values that need to be accessed by multiple unrelated components, or by a regular JavaScript module.
In Svelte, we do this with stores. A store is simply an object with a `subscribe` method that allows interested parties to
be notified whenever the store value changes. A writable store, has `set` and `update` methods in addition to subscribe.


### Auto-subscriptions

To avoid boilerplate code like this:

```sveltehtml
<script>
	import { onDestroy } from 'svelte';
	import { count } from './stores.js';
	import Incrementer from './Incrementer.svelte';
	import Decrementer from './Decrementer.svelte';
	import Resetter from './Resetter.svelte';

	let count_value;

	const unsubscribe = count.subscribe(value => {
		count_value = value;
	});

	onDestroy(unsubscribe);
</script>
```

We can reference a store value by prefixing the store name with `$`:

```sveltehtml
<script>
	import { count } from './stores.js';
	import Incrementer from './Incrementer.svelte';
	import Decrementer from './Decrementer.svelte';
	import Resetter from './Resetter.svelte';
</script>

<h1>The count is {$count}</h1>
```

>Auto-subscription only works with store variables that are declared (or imported) at the top-level scope of a component. 
> You're not limited to using $count inside the markup, either — you can use it anywhere in the `<script>` as well, such 
> as in event handlers or reactive declarations.

### Readable stores

For those cased where it does not make sense to allow components to set the store values from the outside.

```sveltehtml
import { readable } from 'svelte/store';

export const time = readable(new Date(), function start(set) {
	const interval = setInterval(() => {
		set(new Date());
	}, 1000);

	return function stop() {
		clearInterval(interval);
	};
});
```

The first argument to readable is an `initial value`, which can be null or undefined if you don't have one yet. 
The second argument is a `start function` that takes a `set callback` and returns a `stop function`. The start function is 
called when the store gets its first subscriber; stop is called when the last subscriber unsubscribes.

### Derived stores

You can create a store whose value is based on the value of one or more other stores with derived.

```js
export const elapsed = derived(
	time,
	$time => Math.round(($time - start) / 1000)
);
```

> It's possible to derive a store from multiple inputs, and to explicitly set a value instead of returning it 
(which is useful for deriving values asynchronously). [More info](https://svelte.dev/docs#derived) 

### Custom stores

As long as an object correctly implements the subscribe method, it's a store. Beyond that, anything goes. 
It's very easy, therefore, to create custom stores with domain-specific logic.

For example, the count store from our earlier example could include increment, decrement and reset methods 
and avoid exposing set and update:

```js
import { writable } from 'svelte/store';

function createCount() {
        const { subscribe, set, update } = writable(0);

        return {
        subscribe,
        increment: () => update(n => n + 1),
        decrement: () => update(n => n - 1),
        reset: () => set(0)
};
}

export const count = createCount();
```

### Store bindings

If a store is writable you can bind to its value, just as you can bind to local component state.

For example having this two stores:

```js
import { writable, derived } from 'svelte/store';

export const name = writable('world');

export const greeting = derived(
	name,
	$name => `Hello ${$name}!`
);
```

and the following component:

```sveltehtml
<script>
	import { name, greeting } from './stores.js';
</script>

<h1>{$greeting}</h1>
<input bind:value={$name}>

<button on:click="{() => $name += '!'}">
	Add exclamation mark!
</button>
```

- Changing the input value will now update name and all its dependents.
- We can also assign directly to store values inside a component. The `$name += '!'` assignment is equivalent to `name.set($name + '!')`.

## Motion

Svelte includes tools to help you build slick user interfaces that use animation to communicate changes.

### Tweened

```sveltehtml
<script>
	import { tweened } from 'svelte/motion';
	import { cubicOut } from 'svelte/easing';

	const progress = tweened(0, {
		duration: 400,
		easing: cubicOut
	});
</script>

<style>
	progress {
		display: block;
		width: 100%;
	}
</style>

<progress value={$progress}></progress>

<button on:click="{() => progress.set(0)}">
	0%
</button>

<button on:click="{() => progress.set(0.5)}">
	50%
</button>

<button on:click="{() => progress.set(1)}">
	100%
</button>
```

### Spring

The spring function is an alternative to tweened that often works better for values that are frequently changing.

```sveltehtml
<script>
  import { spring } from 'svelte/motion';

  let coords = spring({ x: 50, y: 50 }, {
    stiffness: 0.1,
    damping: 0.25
  });

  let size = spring(10);
</script>

<style>
  svg { width: 100%; height: 100% }
  circle { fill: #ff3e00 }
</style>

<div style="position: absolute; right: 1em;">
  <label>
    <h3>stiffness ({coords.stiffness})</h3>
    <input bind:value={coords.stiffness} type="range" min="0" max="1" step="0.01">
  </label>

  <label>
    <h3>damping ({coords.damping})</h3>
    <input bind:value={coords.damping} type="range" min="0" max="1" step="0.01">
  </label>
</div>

<svg
        on:mousemove="{e => coords.set({ x: e.clientX, y: e.clientY })}"
        on:mousedown="{() => size.set(30)}"
        on:mouseup="{() => size.set(10)}"
>
  <circle cx={$coords.x} cy={$coords.y} r={$size}/>
</svg>
```

## Transitions

We can make more appealing user interfaces by gracefully transitioning elements into and out of the DOM. 
Svelte makes this very easy with the `transition` directive.

### Basic

```sveltehtml
<script>
	import { fade } from 'svelte/transition';
	let visible = true;
</script>

<label>
	<input type="checkbox" bind:checked={visible}>
	visible
</label>

{#if visible}
	<p transition:fade>
		Fades in and out
	</p>
{/if}
```

### Adding parameters

```sveltehtml
<script>
  import { fly } from 'svelte/transition';
  let visible = true;
</script>

<label>
  <input type="checkbox" bind:checked={visible}>
  visible
</label>

{#if visible}
  <p transition:fly="{{ y: 200, duration: 2000 }}">
    Flies in and out
  </p>
{/if}
```

### In and Out 

Instead of the transition directive, an element can have an in or an out directive, or both together. 

```sveltehtml
<script>
  import { fade, fly } from 'svelte/transition';
  let visible = true;
</script>

<label>
  <input type="checkbox" bind:checked={visible}>
  visible
</label>

{#if visible}
  <p in:fly="{{ y: 200, duration: 2000 }}" out:fade>
    Flies in, fades out
  </p>
{/if}
```

### Transition events

Svelte dispatches events that you can listen to like any other DOM event:

```sveltehtml
<p
	transition:fly="{{ y: 200, duration: 2000 }}"
	on:introstart="{() => status = 'intro started'}"
	on:outrostart="{() => status = 'outro started'}"
	on:introend="{() => status = 'intro ended'}"
	on:outroend="{() => status = 'outro ended'}"
>
	Flies in and out
</p>
```

### Local transitions

Ordinarily, transitions will play on elements when any container block is added or destroyed. In the example here, t
oggling the visibility of the entire list also applies transitions to individual list elements.

Instead, we'd like transitions to play only when individual items are added and removed — in other words, when the user 
drags the slider.

We can achieve this with a local transition, which only plays when the immediate parent block is added or removed:

```sveltehtml
<script>
	import { slide } from 'svelte/transition';

	let showItems = true;
	let i = 5;
	let items = ['one', 'two', 'three', 'four', 'five', 'six', 'seven', 'eight', 'nine', 'ten'];
</script>

<style>
	div {
		padding: 0.5em 0;
		border-top: 1px solid #eee;
	}
</style>

<label>
	<input type="checkbox" bind:checked={showItems}>
	show list
</label>

<label>
	<input type="range" bind:value={i} max=10>

</label>

{#if showItems}
	{#each items.slice(0, i) as item}
		<div transition:slide|local>
			{item}
		</div>
	{/each}
{/if}
```

### Deferred transitions

A particularly powerful feature of Svelte's transition engine is the ability to defer transitions, so that they can be coordinated 
between multiple elements.

We can achieve this effect using the `crossfade` function, which creates a pair of transitions called `send` and `receive`. 
When an element is 'sent', it looks for a corresponding element being 'received', and generates a transition that transforms
the element to its counterpart's position and fades it out. When an element is 'received', the reverse happens. If there is no counterpart, the fallback transition is used.

[Full example](https://svelte.dev/tutorial/deferred-transitions)

## Animations

In the

We used deferred transitions to create the illusion of motion as elements move from one todo list to the other.

To complete the illusion, we also need to apply motion to the elements that aren't transitioning. 
For this, we use the `animate` directive.

```sveltehtml
<script>
	import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';
	import { flip } from 'svelte/animate';

	const [send, receive] = crossfade({
		duration: d => Math.sqrt(d * 200),

		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: t => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
		}
	});

	let uid = 1;

	let todos = [
		{ id: uid++, done: false, description: 'write some docs' },
		{ id: uid++, done: false, description: 'start writing blog post' },
		{ id: uid++, done: true,  description: 'buy some milk' },
		{ id: uid++, done: false, description: 'mow the lawn' },
		{ id: uid++, done: false, description: 'feed the turtle' },
		{ id: uid++, done: false, description: 'fix some bugs' },
	];

	function add(input) {
		const todo = {
			id: uid++,
			done: false,
			description: input.value
		};

		todos = [todo, ...todos];
		input.value = '';
	}

	function remove(todo) {
		todos = todos.filter(t => t !== todo);
	}

	function mark(todo, done) {
		todo.done = done;
		remove(todo);
		todos = todos.concat(todo);
	}
</script>

<div class='board'>
	<input
		placeholder="what needs to be done?"
		on:keydown={e => e.key === 'Enter' && add(e.target)}
	>

	<div class='left'>
		<h2>todo</h2>
		{#each todos.filter(t => !t.done) as todo (todo.id)}
			<label
				in:receive="{{key: todo.id}}"
				out:send="{{key: todo.id}}"
				animate:flip
			>
				<input type=checkbox on:change={() => mark(todo, true)}>
				{todo.description}
				<button on:click="{() => remove(todo)}">remove</button>
			</label>
		{/each}
	</div>

	<div class='right'>
		<h2>done</h2>
		{#each todos.filter(t => t.done) as todo (todo.id)}
			<label
				class="done"
				in:receive="{{key: todo.id}}"
				out:send="{{key: todo.id}}"
				animate:flip
			>
				<input type=checkbox checked on:change={() => mark(todo, false)}>
				{todo.description}
				<button on:click="{() => remove(todo)}">remove</button>
			</label>
		{/each}
	</div>
</div>

<style>
	.board {
		display: grid;
		grid-template-columns: 1fr 1fr;
		grid-gap: 1em;
		max-width: 36em;
		margin: 0 auto;
	}

	.board > input {
		font-size: 1.4em;
		grid-column: 1/3;
	}

	h2 {
		font-size: 2em;
		font-weight: 200;
		user-select: none;
		margin: 0 0 0.5em 0;
	}

	label {
		position: relative;
		line-height: 1.2;
		padding: 0.5em 2.5em 0.5em 2em;
		margin: 0 0 0.5em 0;
		border-radius: 2px;
		user-select: none;
		border: 1px solid hsl(240, 8%, 70%);
		background-color:hsl(240, 8%, 93%);
		color: #333;
	}

	input[type="checkbox"] {
		position: absolute;
		left: 0.5em;
		top: 0.6em;
		margin: 0;
	}

	.done {
		border: 1px solid hsl(240, 8%, 90%);
		background-color:hsl(240, 8%, 98%);
	}

	button {
		position: absolute;
		top: 0;
		right: 0.2em;
		width: 2em;
		height: 100%;
		background: no-repeat 50% 50% url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%23676778' d='M12,2C17.53,2 22,6.47 22,12C22,17.53 17.53,22 12,22C6.47,22 2,17.53 2,12C2,6.47 6.47,2 12,2M17,7H14.5L13.5,6H10.5L9.5,7H7V9H17V7M9,18H15A1,1 0 0,0 16,17V10H8V17A1,1 0 0,0 9,18Z'%3E%3C/path%3E%3C/svg%3E");
		background-size: 1.4em 1.4em;
		border: none;
		opacity: 0;
		transition: opacity 0.2s;
		text-indent: -9999px;
		cursor: pointer;
	}

	label:hover button {
		opacity: 1;
	}
</style>
```

## Actions

Actions are essentially element-level lifecycle functions. They're useful for things like:

* interfacing with third-party libraries
* lazy-loaded images
* tooltips
* adding custom event handlers

### Declaration

An action function receives a node and some optional parameters, and returns an action object. That object can have a 
`destroy` function, which is called when the element is unmounted.

```js
export function pannable(node) {
	// setup work goes here...

	return {
		destroy() {
			// ...cleanup goes here
		}
	};
}
```

[A practical example](https://svelte.dev/tutorial/actions)


### Adding Parameters

Like transitions and animations, an action can take an argument, which the action function will be called with 
alongside the element it belongs to.

Example:

```sveltehtml
<button use:longpress={duration} />
```

If you need to pass multiple arguments to an action, combine them into a single object, as in `use:longpress={{duration, spiciness}}`

[Full example](https://svelte.dev/tutorial/adding-parameters-to-actions)

## Classes

Like any other attribute, you can specify classes with a JavaScript attribute, seen here:

```sveltehtml
<button
	class="{current === 'foo' ? 'selected' : ''}"
	on:click="{() => current = 'foo'}"
>foo</button>
```

Or even simpler than that:

```sveltehtml
<button
	class:selected="{current === 'foo'}"
	on:click="{() => current = 'foo'}"
>foo</button>
```

### Shorthand class directive

Often, the name of the class will be the same as the name of the value it depends on:

```sveltehtml
<div class:big={big}>
	<!-- ... -->
</div>
```

could be simplified to:

```sveltehtml
<div class:big>
	<!-- ... -->
</div>
```

## Component composition

Before a component can accept children, it needs to know where to put them. We do this with the `<slot>` element. 

App component:

```sveltehtml
<script>
	import Box from './Box.svelte';
</script>

<Box>
	<h2>Hello!</h2>
	<p>This is a box. It can contain anything.</p>
</Box>
```

Box component:

```sveltehtml
<div class="box">
	<slot></slot>
</div>
```

### Slot fallback

A component can specify fallbacks for any slots that are left empty, by putting content inside the `<slot>` element:

```sveltehtml
<div class="box">
  <div class="box">
    <slot>
      <em>no content was provided</em>
    </slot>
  </div>
</div>
```

So we can do:

```sveltehtml
<Box>
  <h2>Hello!</h2>
  <p>This is a box. It can contain anything.</p>
</Box>

<Box/>
```

### Named slots

To have control over placement when we need multiple slots we use `slot=slotName`.

```sveltehtml
<script>
  import ContactCard from './ContactCard.svelte';
</script>

<ContactCard>
	<span slot="name">
		P. Sherman
	</span>

  <span slot="address">
		42 Wallaby Way<br>
		Sydney
	</span>
</ContactCard>
```

And in the `ContactCard` component:

```sveltehtml
<article class="contact-card">
	<h2>
		<slot name="name">
			<span class="missing">Unknown name</span>
		</slot>
	</h2>

	<div class="address">
		<slot name="address">
			<span class="missing">Unknown address</span>
		</slot>
	</div>

	<div class="email">
		<slot name="email">
			<span class="missing">Unknown email</span>
		</slot>
	</div>
</article>
```

### Checking for slot content

In some cases, you may want to control parts of your component based on whether the parent passes in content for a certain slot. 
Perhaps you have a wrapper around that slot, and you don't want to render it if the slot is empty.
You can do this by checking the properties of the special `$$slots` variable.

`$$slots` is an object whose keys are the names of the slots passed in by the parent component. 
If the parent leaves a slot empty, then $$slots will not have an entry for that slot.

```sveltehtml
{#if $$slots.comments}
	<div class="discussion">
		<h3>Comments</h3>
		<slot name="comments"></slot>
	</div>
{/if}
```

### Slot props

[TBD]
