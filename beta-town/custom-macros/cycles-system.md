## The Cycles System

[Back to the main readme](./README.md).

A new fangled version of the cycles system for your time-keeping needs. The old cycles system was a mess, this one seeks to clean up some of its biggest problems, while also giving authors the ability to fine tune them even more. The core idea of the system remains largely similar, however.

> [!DANGER]
> This new version of the cycles system has undergone some major changes compared to the original implementation. It's a better macro set and system overall, but it is unfortunately **not compatible with code written for version 1.x of the system**.

**THE CODE:** [Minified](https://github.com/ChapelR/custom-macros-for-sugarcube-2/blob/master/scripts/minified/cycles.min.js). [Pretty](https://github.com/ChapelR/custom-macros-for-sugarcube-2/blob/master/scripts/cycles.js).  
**DEMO:** [Available](http://macros.twinelab.net/demo?macro=cycles).  
**GUIDE:** Not available.

### Macro: `<<newcycle>>`

**Syntax**: 
```
<<newcycle name [period] [incrementer] [suspend]>>
    <<phase phaseName [phaseName] ...>>
    [<<phase ...>>]
<</newcycle>>
```

The `<<newcycle>>` macro creates a new cycle instance. A cycle is a time-keeping mechanism that is based on passage transitions: each passage transition is one *turn*, and each turn adds one *stack* to the cycle by default. The `period` of the `<<newcycle>>` macro is how many **stacks** it takes to for the the cycle to change phases. The `incrementer` argument can be used to increase the number of stacks the cycle gains each **turn**. The `incrementer` is usually best left as `1`. The `suspend` keyword can be passed as a third argument to make the cycle start off *suspended*, meaning it won't automatically collect stacks right from the start. You can use the `<<phase>>` child tags to pass *phases* to the macro, which must be strings; things like `"morning"`, `"noon"`, and `"night"` or `"player turn"` and `"enemy turn"`. Each cycle must have at least two phases, and they should be provided in the order you want them to cycle. You can use any number of phases in a single `<<phase>>` tag, and any number of `<<phase>>` tags in the macro, so set it up however you want.

> [!NOTE]
> **About stacks, periods and incrementers**: First, try not to get to hung up on the term "stack." It helped me visualize what I was doing, and the term worked its way into the code, there's no special meaning there. For most cycles, one turn (that is, one passage transition) should equal one stack. A period is simply the number of turns/stacks it should take for the cycle's phase to change. **The period of a cycle is set in stone** once you create your cycle, so an additional system to alter the period was needed, hence incrementers. Incrementers can allow you to shorten your periods: for example, you can make each turn worth two stacks to shorten your periods by half. I recommend trying to stick to whole numbers wherever possible: if you feel like you'll want to decrease the period by one quarter, you'd want to have a higher incrementer and period to compensate. For example, if a period typically takes 4 turns and you want it to later take 3, your period should be 12 and your incrementer 3, then you can increase the incrementer to 4 later to shorten the turns it takes to change a phase from 4 to 3. If this seems overly confusing, that's because it is; you're not alone.

> [!TIP]
> You need to define your cycles before using them. Your `StoryInit` special passage is the best place to do so.

> [!WARNING]
> Any text, code, or content in the `<<newcycle>>` macro, outside its tags, will be ignored and discarded.

**Arguments**:

- `name`: the name to give your cycle. Each cycle should have a unique name.
- `period`: (optional) a whole number greater than 0. Represents the number of stacks it takes to change to the next phase. Defaults to `1`.
- `incrementer`: (optional) determines how many stacks a single turn is worth. Most authors will want this to be `1`, and defaults to that.
- `suspend`: (optional) the keyword `suspend`. If included, the cycle will start suspended (that is, paused and unable to automatically collect stacks on each turn).
- `phaseName`: a name for each phase you want to be part of the cycle. Every time a cycle gains enough stacks to reach its period, the next phase starts. Once the last phase ends, the first starts again. Phases should be strings. You need to give each cycle *at least* two phases.

**Usage**:
```
/* create a cycle for time that changes phases every three turns */
<<newcycle 'time' 4>>
    <<phase 'morning' 'midday' 'evening' 'night'>>
<</newcycle>>

/* create a cycle for a turn-based game */
<<newcycle 'turns' 1 1 suspend>>
    <<phase 'player'>>
    <<phase 'enemy'>>
<</newcycle>>

/* another example, with period and incrementer set to give an effective period of 4 */
<<newcycle 'meals' 12 3>>
    <<phase 'breakfast' 'lunch' 'dinner'>>
<</newcycle>>

/* you can use multiple child tags and multiple phaseNames per child tag, the order will still be preserved */
<<newcycle 'dayss' 10>>
    <<phase 'Sunday' 'Monday'>>
    <<phase 'Tuesday' 'Wednesday'>>
    <<phase 'Thursday' 'Friday' 'Saturday'>>
<</newcycle>>
```

### Macro: `<<editcycle>>`

**Syntax**: `<<editcycle name actions>>`

This macro can be used to edit a cycle in a variety of ways, such as increasing or decreasing its stacks, altering its incrementer, resetting it, or changing it's suspended state.

**Arguments**:

- `name`: The name of a previously defined cycle, by the `<<newcycle>>` macro or the `Cycle` API (see below).
- `actions`: A list of actions to perform. Available actions are:
    - `suspend`: suspends (pauses) a cycle. A suspended cycle cannot collect stacks autmatically on each turn. If the cycle is already suspended, has no effect.
    - `resume`: resumes a cycle that is suspended. If the cycle isn't suspended, has no effect.
    - `toggle`: toggles the suspended state of a cycle--suspends it if it is running, resumes it if it is suspended.
    - `incrementer` *`number`*: sets the incrementer to the indicated value. Should be a whole number greater than 0.
    - `change` *`number`*: changes the cycle's stack count *by* (not *to*) the indicated number. Positive numbers are added to the stack count, negative numbers are subtracted from it (technically still added, but you get the idea).
    - `reset`: reset the cycle, setting the stack count to zero.

> [!WARNING]
> Passing opposing actions, like `suspend` and `resume` to the same `<<editcycle>>` macro will not throw any errors, but is still a bad idea and unlikely to produce the results you want.

**Usage**:
```
/* reset and suspend a cycle */
<<editcycle 'turns' reset suspend>>

/* change the incrementer of a cycle, then resume it to make sure it's running */
<<editcycle 'time' incrementer 2 resume>> /* essentially halves the cycle's period */

/* toggle a cycle */
<<editcycle 'days' toggle>>

/* reduce the stack of a cycle */
<<editcycle 'meals' change -4>>
```

### Macro : `<<showcycle>>`

**Syntax**: `<<showcycle name [formatOptions]>>`

Prints the current phase of the indicated cycle to the page. You can optionally format the casing of the cycle with the keywords `uppercase`, `lowercase`, and `upperfirst`.

**Arguments**:

- `name`: The name of a previously defined cycle, by the `<<newcycle>>` macro or the `Cycle` API (see below).
- `formatOptions`: (optional) any one of the keywords `uppercase`, `lowercase`, or `upperfirst`.

**Usage**:
```
/* show the day of the week */
<<showcycle 'days'>>

/* show the time of day, Capitalized */
<<showcycle 'time' upperfirst>>

/* show the meal, UPPERCASED */
<<showcycle 'meals'>>

/* show the day of the week, lowercased */
<<showcycle 'days' lowercase>>
```

### Passage Tag: `cycles.pause`

Any passage with the tag `cycles.pause` (configurable in the pretty script) will suspend all cycles on that passage; so tagged passages will not automatically add stacks to any cycle. Useful for things like menus, startup passages, etc.

## JavaScript API

The JavaScript API exists on the `setup.Cycle` and (if possible) the `window.Cycle` object.

### Static Methods

These are the static methods of the `Cycles` API.

#### Method: `Cycle.add()`

**Returns**: a new `Cycle` instance.

**Syntax**: `Cycle.add(name, definition)`

Creates a new `Cycle` instance with the indicated definition, and saves it to your story's state with all the necessary plumbing.

**Arguments**:

- `name`: (*string*) the name to give your cycle. Each cycle should have a unique name.
- `definition`: (*object*) the definition object for the cycle. Definition objects have the following properties. See the `<<newcycle>>` macro for more information.
    - `phases`: (*string array*) an array of phases.
    - `period`: (*number*) (optional) a whole number greater than 0. Defaults to 1. 
    - `incrementer`: (*number*) (optional) a whole number greater than 0. Defaults to 1.
    - `active`: (*boolean*) (optional) if false, the cycle will start suspended. Defaults to true.

**Usage**:
```javascript
Cycle.add('time', {
    phases : ['morning', 'midday', 'evening', 'night'],
    period : 4
});

Cycle.add('days', {
    phases : ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'],
    period : 10
});

Cycle.add('turns', {
    phases : ['player', 'enemy'],
    active : false
});
```

#### Method: `Cycle.is()`

**Returns**: boolean.

**Syntax**: `Cycle.is(thing)`

Returns whether the passed *thing* is a `Cycle` instance.

**Arguments**:

- `thing`: (*any*) anything

**Usage**:
```javascript
var a = [];
var m = new Map();
var c = Cycle.add('blah', { phases : ['hey', 'hello'] });

Cycle.is(a); // false
Cycle.is(m); // false
Cycle.is(c); // true
```

#### Method: `Cycle.has()`

**Returns**: boolean.

**Syntax**: `Cycle.has(name)`

Returns whether a cycle with the given name exists.

**Arguments**:

- `name`: The name of a previously defined cycle, by the `<<newcycle>>` macro or the `Cycle` API (see below).

**Usage**:
```
<<if Cycles.has('blah')>>
    <<newcycle 'blah'>>
        <<phase 'hey' 'hello'>>
    <</newcycle>>
<</if>>
```

#### Method: `Cycle.get()`

**Returns**: a `Cycle` instance or `null`.

**Syntax**: `Cycle.get(name)`

Returns the `Cycle` instance with the given name, or `null` if it doesn't exist.

**Arguments**:

- `name`: The name of a previously defined cycle, by the `<<newcycle>>` macro or the `Cycle` API (see below).

**Usage**:
```javascript
var cycle = Cycle.get('blah');
```

#### Method: `Cycle.del()`

**Returns**: boolean.

**Syntax**: `Cycle.del(name)`

Deletes a cycle with the given name. Returns `true` if the cycle exists and was successfully deleted.

**Arguments**:

- `name`: The name of a previously defined cycle, by the `<<newcycle>>` macro or the `Cycle` API (see below).

**Usage**:
```javascript
Cycle.add('blah', { phases : ['hey', 'hello'] });
Cycle.del('blah');
Cycle.get('blah'); // null
```

#### Method: `Cycle.clear()`

**Returns**: nothing.

**Syntax**: `Cycle.clear()`

Deletes all registered cycles.

**Usage**:
```javascript
Cycle.add('blah', { phases : ['hey', 'hello'] });
Cycle.clear();
Cycle.get('blah'); // null
```

### Instance Methods

These are the methods of the `Cycle` instance.

#### Method: `cycle#current()`

#### Method: `cycle#update()`

#### Method: `cycle#reset()`

#### Method: `cycle#suspend()`

#### Method: `cycle#resume()`

#### Method: `cycle#toggle()`

#### Method: `cycle#isSuspended()`

#### Method: `cycle#editIncrementer()`

#### Method: `cycle#clone()`

#### Method: `cycle#toJSON()`

### Instance Properties

### Cycle Events

## Other Usage Notes

**Configuration options**:

**Story variable warning**:

**Task object warning**:

**Passage tag warning**: