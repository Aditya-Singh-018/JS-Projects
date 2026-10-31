# Mini Project Learnings: Updating CSS Variables with JavaScript

## Project Overview

This mini project uses HTML controls to dynamically change CSS variables. By moving the sliders or selecting a color, you can update an image's spacing, blur, and base color without manually editing the stylesheet.

## 1. CSS Custom Properties (Variables)

CSS variables let you store reusable values and update them in one place.

```css
:root {
  --base: #ffc600;
  --spacing: 10px;
  --blur: 10px;
}
```

- Custom properties start with `--`.
- `:root` makes these variables available throughout the document.
- Use `var()` to read a variable:

```css
img {
  padding: var(--spacing);
  background: var(--base);
  filter: blur(var(--blur));
}
```

**Key lesson:** Define a value once, then reuse it wherever needed.

## 2. Connecting HTML Inputs to JavaScript

The controls have attributes such as `name`, `value`, and `data-sizing`.

```html
<input
  type="range"
  name="spacing"
  min="10"
  max="200"
  value="10"
  data-sizing="px"
>
```

- `type="range"` creates a slider.
- `min` and `max` set its limits.
- `value` sets its initial value.
- `name="spacing"` identifies which CSS variable to update.
- `data-sizing="px"` stores the unit to append to the value.

The color input works similarly, but its value is a color such as `#ffc600`, and it doesn't need a `px` suffix.

## 3. Selecting Multiple Elements

```js
const inputs = document.querySelectorAll('.controls input');
```

`querySelectorAll()` returns all inputs inside an element with the class `controls`. It returns a collection that you can loop over to attach event listeners.

## 4. Event Listeners

```js
inputs.forEach(input => {
  input.addEventListener('input', handleUpdate);
});
```

- `forEach()` processes each input.
- `addEventListener()` runs a function when an event happens.
- The `input` event fires as the user adjusts a slider or changes a color, providing immediate feedback.
- The `change` event is also useful when you only need to react after a change is committed.

## 5. Understanding `this` in an Event Handler

```js
function handleUpdate() {
  console.log(this.value);
  console.log(this.name);
}
```

When this regular function is called as an event listener, `this` refers to the input element that triggered the event.

For example, when the spacing slider is moved:
- `this.name` is `"spacing"`.
- `this.value` is the slider's current value, such as `"50"`.
- `this.dataset.sizing` is `"px"`.

Note: `this` behaves differently in arrow functions. Arrow functions don't create their own `this`; they inherit it from the surrounding scope.

## 6. Reading Custom `data-*` Attributes

```js
const suffix = this.dataset.sizing || '';
```

HTML `data-*` attributes let you attach custom information to an element. JavaScript reads them through `dataset`.

- `data-sizing="px"` becomes `this.dataset.sizing`, which is `"px"`.
- If the attribute is absent, `|| ''` uses an empty string as a fallback.

This lets the same handler support both numeric controls with units and color inputs without units.

## 7. Dynamically Updating CSS from JavaScript

```js
document.documentElement.style.setProperty(
  `--${this.name}`,
  this.value + suffix
);
```

This is the central idea of the project.

- `document.documentElement` refers to the page's root `<html>` element.
- `style.setProperty(propertyName, value)` sets a CSS property.
- A template literal builds the property name. For example, if `this.name` is `"spacing"`, then `` `--${this.name}` `` becomes `"--spacing"`.
- `this.value + suffix` builds a valid CSS value, such as `"50px"` or `"#ff0000"`.

The inline custom property overrides the corresponding value declared in `:root`, so the page updates immediately.

## 8. Why Units Matter

CSS properties such as `padding` and `blur()` require lengths with units.

```js
this.value + suffix
```

For a slider value of `50` with a `px` suffix, JavaScript sets `50px`. Without the suffix, `padding: 50` is invalid CSS.

Color values such as `#ffc600` don't need a length unit.

## 9. Debugging Lessons

This project highlights a few common mistakes:

### Missing semicolon

```css
--base: #ffc600;
```

Declarations inside a CSS rule should be separated with semicolons.

### Incorrect variable syntax

Incorrect:

```css
background: (--base);
```

Correct:

```css
background: var(--base);
```

### Calculating a value but not using it

If you write:

```js
const suffix = this.dataset.sizing || '';
```

but then pass only `this.value` to `setProperty()`, the suffix has no effect. Use `this.value + suffix`.

### Check the browser console

Use the browser's Developer Tools to inspect console errors and the Elements/Styles panel to see whether the CSS variables are changing.

## 10. Complete Flow of the Project

1. The page loads with default CSS variables.
2. JavaScript selects the inputs.
3. Event listeners are attached to each input.
4. The user changes a slider or color.
5. `handleUpdate()` reads the triggering input's name, value, and optional unit.
6. `setProperty()` updates the matching CSS custom property on `<html>`.
7. CSS rules that use that variable update the page's appearance.

## Key Takeaways

- CSS custom properties store reusable, dynamically changeable values.
- `:root` is a convenient place to define global CSS variables.
- `querySelectorAll()` selects multiple matching elements.
- Event listeners make the page respond to user interaction.
- `dataset` reads HTML `data-*` attributes.
- `this` in a regular event-handler function refers to the element that triggered the event.
- `style.setProperty()` lets JavaScript update CSS properties.
- Values must be valid for the CSS property, including units where required.
- Small syntax mistakes can prevent CSS declarations from working, so use browser DevTools to debug.

## Ideas to Extend the Project

- Add a reset button that restores the default values.
- Display the current spacing and blur values beside the sliders.
- Save the user's settings in `localStorage`.
- Add more CSS variables, such as image width or border radius.
