# react-githubish-mentions
A wrapper for a textarea to offers autocomplete suggestions when triggered by @ or # or whatever

## LOOKING FOR MAINTAINERS

I no longer use this! To make sure PRs land in a timely fashion, request to become a maintainer.

## Installation
`yarn add react-githubish-mentions`

## What's it do
It strives to do exactly what GitHub does when you type `#` or `@`.

## How's it different from react-mentions
react-mentions is great!
but I needed:
- something a little more flexible (like letting me pass in my own component with
  avatars for individual Menu items)
- a menu that uses a portal instead of `position: 'absolute'`
- something with pixel-perfect caret positioning
- something that let's me use controlled components, like redux-form

## Usage

### Stick it around your component

Example:

```js

// MenuItem.js
const MenuItem = (props) => {
  // react-githubish-mentions provides `active` you provide everything else
  const {active, value} = props;
  const customStyle = {
    background: active ? 'blue' : 'white'
  };
  return (
    <div style={customStyle}>
      {value}
    </div>
  )
};
export default MenuItem;

// in form.js
import {MentionWrapper, MentionMenu} from 'react-githubish-mentions';
import MenuItem from './MenuItem';

const Form = (props) => {
  const atQuery = async (query) => {
    const {teamMembers} = this.props;
    // You must provide a `value` field. Everything else is optional. All this data will be passed to your custom MenuItem
    const tm = teamMembers.map((teamMember) => ({...teamMember, value: teamMember.name}));
    return tm.filter((member) => member.value.startsWith(query))
  };

  return (
    <MentionWrapper placeholder="Type your outcome here">
      <MentionMenu className="mentionMenuStyle" trigger="@" item={MenuItem} resolve={atQuery}/>
      <MentionMenu className="mentionMenuStyle" trigger="#" item={MenuItem} resolve={hashQuery}/>
    </MentionWrapper>
    )
};
```

## API

```
MentionWrapper
```

Think of this as `<textarea>`. Everything except the 2 options below get passed directly to the HTML component.

Options:
- `getRef`: a custom `ref`, because react yells if you call something ref.
- `component`: defaults to `"textarea"` you can pass in `"input"` or even a custom component if you want.
- `onChange(event, newValue)`: your standard `onChange` event handler, but with a newValue argument added
to support clicking an option with the mouse.

```
MentionMenu
```
Think of this as a trigger, written in JSX. Any `style` or `className` is passed to the menu that will pop up.

Options:
- `trigger`: The keystroke to watch for. Usually a single character like `@` or `#`
- `replace(trigger, userObject)`: A function dictating how to autocomplete the sentence.
Default is ``(userObj, trigger) => `${trigger}${userObj.value} ` ``.
If you wanted to write a trigger for emojis, you probably don't want the `:` trigger to remain,
and you want the character, not the text value, so you might use ``(userObj) => `${userObj.emoji} ` ``
- `resolve(query)`: An `AsyncFunction` that receives a text query (the part after the trigger, e.g. `query('@foo') === 'foo'`)
and returns an array of objects, where each object has a `value` key which is the string that will be injected into the textarea.
- `item`: A custom menu item that you provide. See example above.


## FAQ

Q: I'm debugging the menu & I don't see it in the DOM tree. What voodoo is this?

A: It's there, but it created a portal & lives in its own react root.
This is the only surefire way to guarantee the menu doesn't get clipped or scrolled away.
To learn more, see [react-portal-hoc](https://github.com/mattkrick/react-portal-hoc)

Q: How is the menu position calculated?

A: Very carefully, using [textarea-caret](https://github.com/component/textarea-caret-position).
Until `Document.caretPositionFromPoint()` is a thing, it's the best there is.

Q: How do I filter the results in my `resolve` function?

A: There are 100s of ways to filter,
you could go naive with a `startsWith` or `indexOf` or go extreme with a
fuzzy or levenshtein distance. Chances are, each trigger will warrant a different filter.
This package does one thing, and does it well. It leaves the filtering up to
your `resolve` function.

## License

MIT
