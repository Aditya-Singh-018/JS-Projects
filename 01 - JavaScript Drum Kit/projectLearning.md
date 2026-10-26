# JavaScript Drum Kit — Project Learnings

*A brief revision note for VS Code*

## 1. HTML `data-*` attributes

The `data-*` attributes store custom information on an HTML element. In this project, `data-key` connects a keyboard key identifier to an audio element.

```html
<audio data-key="65" src="sounds/clap.wav"></audio>
```

JavaScript can read this value through `element.dataset.key`. The value is a string.

```javascript
const audio = document.querySelector('audio[data-key="65"]');
console.log(audio);
```

## 2. Selecting elements with the DOM

- `document.querySelector()` returns the **first** element matching a CSS selector.
- `document.querySelectorAll()` returns **all** matching elements.

For an attribute-value selector, put the value inside the square brackets:

```css
audio[data-key="65"]
```

Do not put the comparison outside the brackets, as in `audio[data-key] = 65`; that is not a valid selector.

## 3. Keyboard events

A `keydown` event fires when a key is pressed. The event object contains information about the key.

```javascript
document.addEventListener("keydown", (event) => {
  console.log(event.code, event.key);
});
```

- `event.code` identifies the physical key, such as `"KeyA"`.
- `event.key` gives the key value, such as `"a"` or `"A"`.
- `event.keyCode` is deprecated; prefer `event.code` or `event.key`.

If using `event.code`, make the HTML attribute match it:

```html
<audio data-key="KeyA" src="sounds/clap.wav"></audio>
```

## 4. CSS classes and transitions

`classList.add("playing")` applies the CSS rules for that class. `classList.remove("playing")` removes those rules.

```css
.key {
  transition: transform 0.07s ease;
}

.playing {
  transform: scale(1.1);
}
```

The transition does not happen merely because a class is added. It happens when a CSS property with a transition defined changes from one value to another.

- Adding `.playing` changes the key's scale from `1` to `1.1`.
- Removing `.playing` can animate the key back to its original scale.
- The `transition` property remains active as long as its CSS rule applies.

## 5. The `transitionend` event

`transitionend` fires when a CSS transition completes.

- `event.target` identifies the element that emitted the event.
- `event.propertyName` identifies the CSS property whose transition finished.
- If several properties transition, the event may fire once for each property.

Example:

```javascript
function removeTransition(event) {
  if (event.propertyName !== "transform") return;

  event.target.classList.remove("playing");
}

keys.forEach((key) => {
  key.addEventListener("transitionend", removeTransition);
});
```

This removes `.playing` when the key's `transform` transition finishes. The event may not fire if no transition occurs or if it is cancelled.

## 6. Playing and restarting audio

```javascript
audio.currentTime = 0;
audio.play();
```

Setting `currentTime` to `0` restarts the sound from the beginning before playing it again. Check that the selector found an audio element before calling methods on it.

## Key takeaways

- `data-*` stores custom values; it does not detect key presses by itself.
- `querySelector()` uses CSS selector syntax.
- Keyboard events let JavaScript respond to user input.
- CSS defines visual states; transitions animate changes between those states.
- `transitionend` lets JavaScript respond after a transition completes.
- `event.target` and `event.propertyName` help identify which element and property finished transitioning.

## Practice tasks

1. Log `event.propertyName` inside your transition handler.
2. Compare `keydown` with `keyup` by logging both events.
3. Try replaying the sound quickly by setting `audio.currentTime = 0` before `audio.play()`.
4. Test what happens if you remove the `.playing` class before the transition finishes.
