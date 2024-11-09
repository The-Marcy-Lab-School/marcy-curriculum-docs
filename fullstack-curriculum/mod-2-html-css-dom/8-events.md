# 2-2-1-dom-events

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-2-1-lecture-dom-events)!
{% endhint %}

**Table of Contents**
- [Event Driven Architecture](#event-driven-architecture)
- [First, an example:](#first-an-example)
- [addEventListener](#addeventlistener)
  - [Event Type](#event-type)
  - [Event Handlers](#event-handlers)
  - [The `event` object](#the-event-object)
  - [Good to know, not to use: inline handlers](#good-to-know-not-to-use-inline-handlers)
- [Event Propagation](#event-propagation)
  - [Event Delegation](#event-delegation)
- [Removing Event Listeners](#removing-event-listeners)

## Event Driven Architecture

So far, the code that we have written will be executed, one line after the other.

```js
console.log('a')
console.log('b')
console.log('c')
```

In **event-driven architecture**, some of the code that we write will only be executed in response to a **user event** such as:
* clicking a button
* moving your mouse
* using your keyboard

![](./img/events.png)

## First, an example:

The snippet below does the following:

1. Select the element that will be the "target" of the event
2. Define an **event handler** callback. This will be invoked with an `event` object.
3. Add an **event listener** to the button that will
   1. "listen" for `"click"` events
   2. respond by invoking the event handler
  
```js
const button = document.querySelector('button');
const handleClick = (event) => {
  console.log(`an event of type ${event.type} occurred!`);
}
button.addEventListener('click', handleClick);
```

* An **event handler** is the function that is invoked when an event "fires".
* An element can be programmed to listen for multiple types of events.

```js
button.addEventListener('click', handleEvent);
button.addEventListener('mouseover', handleEvent);
```

## addEventListener

### Event Type

When you use the `Element.addEventListener(eventType, handler)` method, the first argument defines the event type to listen for. **It should always be a string.**

There are many event types but the most commonly used are:
- `"click"` - an element was clicked
- `"mousemove"` - the mouse moved over an element
- `"keydown"` - a key was pressed down
- `"keyup"` - a key was released
- `"submit"` - a form was submitted
- `"input"` - the `value` of an `input`, `select`, or `textarea` has changed

### Event Handlers

The second argument is an **Event Handler**, a callback function that is invoked when the event fires.

```js
// a generic event handler
const eventHandler = () => console.log('an event occurred!');

// register an event listener on a button
document.querySelector('button').addEventListener('click', eventHandler);

// register these event listeners on the entire document
document.addEventListener('mousemove', eventHandler);
document.addEventListener('keydown', eventHandler);
```

### The `event` object

When any event handler is invoked, it is given an `event` object as an argument. The `event` object has about the event, with different properties for different event types.

```js
// this event handler ignores the event argument
const eventHandler = () => console.log('an event occurred!');

// this event handler actually uses it!
const printEvent = (event) => console.log(event);

// we can assign multiple event handlers to an element
document.querySelector('button').addEventListener('click', eventHandler);
document.querySelector('button').addEventListener('click', printEvent);
```

Here are some essential `event` properties:

- `event.target` — the Element that triggered the event. Available for all events
- `event.key` — the key pressed. Available for keyboard events.
- `event.x` / `event.y` — the location of the mouse in the window. Available for mouse events. (alias for `.clientX`/`.clientY`)

> See this in action in `turtle-walker/` website.

### Good to know, not to use: inline handlers

You can also define event handlers inline directly in HTML:

```html
<button onclick="console.log('hello world');">I have an inline handler!</button>
```

This is good to be aware of for when we get to React but you should NOT use this.

## Event Propagation

> **Propagation**: the act or process of spreading something

When an event takes place on an element (e.g. a button is clicked), that element and all of its parent elements can "hear" that event and can listen/handle those events. We call this **event propagation** or **bubbling**.

So, imagine we had the following structure:

```html
<div id="outer">
  <div id="middle">
    <button id="inner">Click me!</button>
  </div>
</div>
```

If we click on the `button#inner` element, the `div#middle` and `div#outer` elements can also "hear" that element. That means that we can add an event listener to each of those elements, and they would all be triggered!

```js
const testPropagation = (event) => {
  console.log(`Event triggered by: #${event.target.id} (event.target)`);
  console.log(`Handled by: #${event.currentTarget.id} (event.currentTarget)`);
}

document.querySelector('#outer').addEventListener('click', testPropagation);
document.querySelector('#middle').addEventListener('click', testPropagation);
document.querySelector('#inner').addEventListener('click', testPropagation);
```

When an event fires, the callback's `event` object will have two key properties that can tell us which element triggered the event and which element is handling the event:
- `event.target` (the Element that fired the event)
- `event.currentTarget` (the Element handling the event)

To prevent events from bubbling up, use `event.stopPropagation()`

```js
const testPropagation = (event) => {
  console.log(`Event detected on #${event.target.id}`);
  console.log(`Event handled by: #${event.currentTarget.id}`);
  event.stopPropagation()
}
```

**Q: What would happen if we removed the event handlers for `#inner` and `#middle`?**

### Event Delegation

> **Delegation**: the act of empowering to act for another.

Event propagation/bubbling allows a really powerful design pattern called **event delegation**. Suppose you had the following list:

```html
<ul id="picture-list">
  <li><img src="cat1.webp" alt="a funny cat"></li>
  <li><img src="cat2.jpg" alt="a funny cat"></li>
  <li><img src="cat3.jpg" alt="a funny cat"></li>
</ul>
```

Each list item has a picture and a solid black border. As long as we have our mouse hovering over on an image, we want the border of that image (and only that image) to turn red! We can do that with an event listener like this:

```js
const toggleBorder = (event) => {
  console.log(event.type + ' event detected on: ', event.target);
  console.log('event handled by: ', event.currentTarget);

  // toggle the highlight class (which will make the border red) on the closest li to the image
  event.target.closest('li').classList.toggle('highlight');

  event.stopPropagation()
}
```

Now, to get that to work for all of our images, one solution would be to add `mouseover` and `mouseout` event handlers to every single image...

```js
document.querySelector("#picture-list img:nth-child(1)").addEventListener('mouseover', toggleBorder);
document.querySelector("#picture-list img:nth-child(1)").addEventListener('mouseout', toggleBorder);
document.querySelector("#picture-list img:nth-child(2)").addEventListener('mouseover', toggleBorder);
document.querySelector("#picture-list img:nth-child(2)").addEventListener('mouseout', toggleBorder);
document.querySelector("#picture-list img:nth-child(3)").addEventListener('mouseover', toggleBorder);
document.querySelector("#picture-list img:nth-child(3)").addEventListener('mouseout', toggleBorder);
```

...but that looks kind of awful. If we had 100 images, then we'd need 200 event listeners... 🤮 

Instead, we can just add the event listener to the container, the `ul#picture-list`. This requires one important tweak: we have to make sure that only events triggered by the `img` elements themselves are handled with a guard clause

```js
const toggleBorder = (event) => {
  console.log(event.type + ' event detected on: ', event.target);
  console.log('event handled by: ', event.currentTarget);

  // Element.matches returns true if the given element would be selected by the given CSS selector
  // If the target of the event wasn't an image, we don't care about it
  if (!event.target.matches('img')) return;

  event.target.closest('li').classList.toggle('highlight');
}
const ul = document.querySelector('#picture-list');
ul.addEventListener('mouseover', toggleBorder);
ul.addEventListener('mouseout', toggleBorder);
```

Pretty neat, right?!

## Removing Event Listeners
One of the reasons why passing a named callback function to your listeners is better is because you can then remove them if you need to. 

```js
const handleCountClick = (e) => {
  e.target.dataset.count++;
  e.target.innerText = e.target.dataset.count;
};
const counterButton = document.querySelector("#counter");
counterButton.addEventListener('click', handleCountClick);


const removeListenerButton = document.querySelector("#remove-listener");
removeListenerButton.addEventListener('click', (e) => {
  // To remove an event listener, provide the event type and the handler
  counterButton.removeEventListener('click', handleCountClick);
})
```

We remove event listeners to limit user interactions and also be 100% sure that we aren't committing memory leaks when we remove elements. (However, modern browsers are pretty good at cleaning up after us). 

**Q: Why can we write the `removeListenerButton` event listener as an inline arrow function but we can't for the `counterButton` event listener?**