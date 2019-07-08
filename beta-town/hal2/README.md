## Getting Started

[Try the Demo](./demo/ ':ignore')

### Introduction

This is an audio library designed for the [Twine 2](https://twinery.org/) story format [Harlowe (v2.1.0 or later)](https://twine2.neocities.org/). This is version 2 of HAL, documentation and downloads for previous version of HAL remain available for those who need them.

**Why a Second Major Version?**

HAL v2 fixes some of the issues of HAL v1, but the biggest change is the addition of macros and special passages to create a more user-friendly coding experience. JavaScript APIs are largely still available and similar to their v1 counterparts, though many have changed, so you'll want to review the documentation even if you're familiar with HAL v1 and planning on sticking to JavaScript.

**What Else Should I Know?**

This library uses [promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises). That means Internet Explorer versions lower than 11 are out. Other than that, all major modern browsers should work fine, though the volume control may look odd in certain browsers--this can be fixed with user-supplied CSS code if necessary.

This library is free and dedicated to the public domain. That means you don't need to provide credit or attribution if you don't want to, and you can do anything you like with the code. [Review the license](https://github.com/ChapelR/harlowe-audio/blob/master/LICENSE) for any questions or concerns about that.

**I'm Having Trouble**

The best place to ask for help if the issue is on your end is [the Twine Q&A](https://twinery.org/questions/), [the Subreddit](https://www.reddit.com/r/twinegames/), or [the Discord server](https://discordapp.com/invite/n5dJvPp). If you suspect that the problem is in the code or otherwise on my end, [open an issue](https://github.com/ChapelR/harlowe-audio/issues/new).

**Donate**

> [!NOTE]
> I suggest donating to [Twine development](https://www.patreon.com/klembot) if you really want to help out, but I'd welcome a few dollars if you feel like it.

[![ko-fi](https://www.ko-fi.com/img/donate_sm.png)](https://ko-fi.com/F1F8IC35)

### Installation

All you need to install this library is the code. Download the most recent version of HAL here, on the [releases page](https://github.com/ChapelR/harlowe-audio/releases). If decide to edit the code or need to pen it to copy and paste it into your IDE, be sure to open it and mess with it in a text editor, *not* a word processor. How you add this code to your project will depend on the compiler and IDE you use.

**In Twine 2 (online or standalone)**, copy and paste the code in `harlowe-audio.min.js` into your [Story JavaScript area](https://twinery.org/wiki/twine2:adding_custom_javascript_and_css), and the code in `harlowe-audio.min.css` into your Story Stylesheet area.

**In Tweego or Entwine/Grunt-Entwine**, include the files in your command-line options or in your source code directory as appropriate. Remember to watch your file order, and refer to your compiler's docs if you need help.

**In Twee2**, create a special passage with the `script` tag and place the JavaScript code in that passage, and do the same thing with the CSS and a `stylesheet` tag. Refer to its [docs](https://dan-q.github.io/twee2/documentation.html#twee2-syntax-special-passages) for more.

**In Extwee**, you need to create special `script`- and `stylsheet`-tagged passages in your Twee code in much the same way as in Twee2.

**In other compilers or IDEs**, you'll need to refer to your program's documentation for how to add JavaScript and CSS code.

## Configuration

HAL has a variety of configuration options users can alter to fine tune the library to meet their needs. These options can be set using a *special passage*, which is a normal passage with a specific name that has meaning to the library. The special passage for configuring HAL must be named `hal.config`.

<image src='./assets/config.jpg' title='An example of a configuration special passage.' alt='An example of a configuration special passage.'>

Each option is a property/value pair on its own line, with the property name appearing on the left, a colon, and then the property's value. For example, to set HAL's sidebar to start opened, you'd type:

```
sidebarStartClosed: false
```

To set the sidebar to start open and prevent the volume readout from appearing, you could type:

```
sidebarStartClosed: false
volumeDisplay: false
```

Options that you don't set remain at their default values.

> [!WARNING]
> The option names are **case-sensitive**, so `sidebarStartClosed` is not the same as `sidebarstartclosed`, and the latter *will not* work.

Below are a list of the options you can set in your configuration passage and their default values.

|Option|Default|Valid Options|Description|
|---|---|---|---|
|`preload`|`true`|`true` or `false`|Controls whether audio should be preloaded when the game is started, or if audio should only be loaded as needed. In most cases, you'll want to preload it. You can optionally include a loading screen by placing the A.preload() method beneath your track definitions.|
|`loadDelay`|`0`|A number of milliseconds.|Causes the loading screen to hang for a configurable number of milliseconds after loading completes. Use this to fine tune for edge cases, or work around [FOUC issues](https://en.wikipedia.org/wiki/Flash_of_unstyled_content).|
|`muteOnBlur`|`true`|`true` or `false`|Setting this option to true causes the sound in your game to mute when the user is in another tab or window.|
|`startingVol`|`0.5`|A number between 0 and 1.|Sets the default starting master volume.|
|`persistPrefs`|`true`|`true` or `false`|The master volume and mute are considered user preferences, in that they are intended for users to adjust. You can use this option to cause changes to these settings to be saved and restored from local storage. For example, if the user sets the master volume to `0.3`, then closes the game, the master volume will be `0.3` rather than the `startingVol` next time they play the game.|
|`globalA`|`true`|`true` or `false`|By default, and throughout this guide, the JavaScript API is sent to the global scope as `A`, matching HAL v1 and Howler for Harlowe's way of doing things. If you don't want that, or if the `window.A` name is already taken, you can find everything at `Chapel.Audio` instead.|
|`showControls`|`true`|`true` or `false`|This library includes a control panel (as a sidebar) for users to control the master volume and mute state. You can determine whether or not you want to use it or roll your own (or not include options, you Philistine) with this option. If set to false the sidebar, controls, and associated APIs will be unavailable in your game.|
|`sidebarStartClosed`|`true`|`true` or `false`|The siderbar slides in and out from the side of the screen. By default it starts closed (taking up very little space) and needs to be opened by the user to be used. If you'd rather it start open, schange this setting to `false`.|
|`volumeDisplay`|`true`|`true` or `false`|Determines whether a text readout of the current volume is displayed next to the volume control.|
|`trackLoadLimit`|`500`|A number of milliseconds.|Use this option to set a tolerance (in MS) for how long the preloading function will wait attempting to load a single track before moving on. In combination with the below option, this setting should be used to make sure the game doesn't just hang forever on slower connections.|
|`totalLoadLimit`|`8000`|A number of milliseconds.|Use this option to set a tolerance (in MS) for how long the preloading function will wait attempting to load all of the tracks before dismissing the load screen. In combination with the above option, this setting should be used to make sure the game doesn't just hang forever on slower connections.|

## Defining Tracks

### Parts of a Track Definition

Tracks are the meat-and-potatoes of an audio library, as you may expect. Tracks need to be given *names* so that you can call on them later, and one or more *source URLs* which point to audio files (e.g., the file name ends in an audio extension, like `.mp3` or `.ogg`). When you provide multiple sources to a track, HAL will automatically pick the first one in the list the browser can play and use that. Not all browsers support all audio file types. *Almost* every browser supports `mp3` files, but a few, like Chromium don't. Again, *almost* every browser also supports `ogg` files, but a few, like Safari, don't. This typically makes `mp3` and `ogg` files the best combination to use. If you only use one file type, try to use `mp3`s.

You can use [**absolute** or **relative** URLs](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_is_a_URL#Absolute_URLs_vs_relative_URLs) as sources for your tracks. The URL you use should point to a file, not a web player; this makes some audio hosting services hard to use for multimedia hosting. If you do host your audio somewhere, review the Terms of Service of the service you use, as hotlinking may be forbidden.

Once you've got your source URLs squared away, there are three ways to define a track: using the `hal.tracks` special passage (recommended), using the `(newtrack:)` macro, and using the `A.newTrack()` method in JavaScript.

### The Track Definition Special Passage

The recommended way to define your tracks is through a special passage called `hal.tracks`.

<image src='./assets/tracks.jpg' title='The tracks special passage.' alt='The tracks special passage.'>

After creating a passage and naming it `hal.tracks`, you can type in the name of the track, a colon, and then a list of source URLs separated by commas.

```
beep: ./audio/beep.mp3, ./audio/beep.ogg
que-pena: ./audio/que-pena.mp3, ./audio/que-pena.ogg
```

If you only have one source URL, that's fine too.

### Using the API or Macros

You can use the JavaScript API or the macro API to define tracks as well. Track definitions should happen early in your story; either in a `startup`-tagged passage or in your Story JavaScript.

<!-- tabs:start -->

#### **Macros**

**Syntax:** `(newtrack: name, source [, source...])`

**Arguments:**  
- `name` the name to give the track.
- `source` at least one source URL must be passed to the macro.

```
(newTrack: 'beep', './audio/beep.mp3', './audio/beep.ogg')
(newTrack: 'que-pena', './audio/que-pena.mp3', './audio/que-pena.ogg')
```

#### **JavaScript**

If you use JavaScript, either in a passage using a `<script>` tag or directly in your Story JavaScript, you will use the `A.newTrack()` method to define a new track.

**Syntax:** `A.newTrack(name, sourceList)`

**Arguments:**  
- `name` (*string*) the name to give the track.
- `sourceList` (*string*|*string array*) a list of source URLs, which may be provided as a number of individual arguments, or as a single array of strings.

**Example:**  
```
// sources passed as a series of arguments:
A.newTrack('beep', './audio/beep.mp3', './audio/beep.ogg');

// sources passed as a single array of strings:
A.newTrack('que-pena', ['./audio/que-pena.mp3', './audio/que-pena.ogg']);
```

<!-- tabs:end -->

Once you've defined all your tracks you're ready to play them! You'll put your tracks to use with [**track commands**](#track-commands), and can organize them using [playlists](#defining-playlists) and [groups](#defining-groups).

### Preloading Audio

By default, audio in HAL is *preloaded*, meaning tracks begin loading their source files when the game starts, not when the tracks are used. You can change this in the [configuration settings](#configuration), but this is *not* recommended.

You can show a loading screen to force the user to wait (in accordance with the loading limits set in the configuration settings) on non-mobile browsers by using the `A.preload();` method or the `(masteraudio: 'preload')` macro command. All tracks defined before this command is called (this includes all tracks defined in the `hal.tracks` special passage) will be loaded while a loading screen is shown.

<!-- tabs:start -->

#### **Macros**

To preload previously defined tracks, use the `(masteraudio:)` macro with the `preload` command.

```
(newtrack: 'beep', './audio/beep.mp3', './audio/beep.ogg')
(newtrack: 'que-pena', './audio/que-pena.mp3', './audio/que-pena.ogg')

(masteraudio: 'preload') <!-- a loading screen will be displayed while the above two tracks are loaded -->

(newtrack: 'boop', './audio/boop.mp3', './audio/boop.ogg') <!-- this track *may* finish loading in time, but will not cause the loading screen to continue if it isn't -->
```

#### **JavaScript**

To preload previously defined tracks, use the `A.preload()` method.

**Example:**  
```
A.newTrack('beep', './audio/beep.mp3', './audio/beep.ogg');
A.newTrack('que-pena', ['./audio/que-pena.mp3', './audio/que-pena.ogg']);

A.preload() // a loading screen will be displayed while the above two tracks are loaded

A.newTrack('boop', './audio/boop.mp3', './audio/boop.ogg'); // this track *may* finish loading in time, but will not cause the loading screen to continue if it isn't
```

<!-- tabs:end -->

Preloading audio with the loading screen has some benefits, but it's recommended that you:

1. don't use the loading screen feature at all if you don't need to, and  
2. if you do need to, only preload tracks that play early in the game or sound effects that need to match user interactions, like beeps when the user clicks links.

Neither of these features has any effect on mobile devices, since preloading audio on mobile is unwise on many levels.

## Track Commands

You can use the `(track:)` macro or the `A.track()` method to get a track. The macro version accepts a command and arguments directly in the macro, while the JavaScript version allows you to call methods directly on the returned track instance. The JavaScript methods can generally be chained unless they need to return a specific value.

> [!NOTE]
> Commands passed to macros are case-insensitive, so `play` and `PLaY` will both work.

<!-- tabs:start -->

#### **Macros**

**Syntax:** `(track: name, command [, arguments...])`

**Arguments:**  
- `name` the name of a previously defined track.
- `command` the name of a track command.
- `arguments` a list of arguments to pass to the command

```
(track: 'beep', 'play')
(track: 'que-pena', 'loop', true)
```

#### **JavaScript**

**Syntax:** `A.track(name)`

**Returns:** a `Track` instance, which you can then run methods on.

**Arguments:**  
- `name` (*string*) the name to give the track.

**Example:**  
```
A.track('beep').play();
A.track('que-pena').loop(true);
```

<!-- tabs:end -->

### Playing, Pausing, and Stopping Tracks

> [!TIP]
> Playing tracks can be a bit more complex then it sounds, as autoplay (that is, playing any audio before the user interacts with the page) is not allowed on most modern browsers. To get around this, you can either use a splash screen (something the user has to click past at the start of your game, before any sound plays), tie your first sound to a link, or use the `playwhenpossible` command or the `track#playWhenPossible()` method.

<!-- tabs:start -->

#### **Macros**

Commands:  
- `play`: Plays the indicated track, if possible.
  - Arguments: none.
  - Returns: nothing.


- `playwhenpossible`: Plays the indicated track, is possible, or waits for a valid user interaction to start playback.
  - Arguments: none.
  - Returns: nothing.


- `stop`: Stops a playing track (pauses it and resets it).
  - Arguments: none.
  - Returns: nothing.


- `pause`: Pauses playback of the indicated track, if it's playing.
  - Arguments: none.
  - Returns: nothing.


```
<!-- play a track -->
(track: 'que-pena', 'play')

<!-- play a track when the user clicks a link -->
{
(link-repeat: 'Click me!')[
    (track: 'beep', 'play')
]
}

<!-- play a looping background track -->
(track: 'que-pena', 'loop', true)
(track: 'que-pena', 'play')

<!-- play a track when the ability to do so becomes unlocked -->
(track: 'que-pena', 'playwhenpossible')

<!-- stop a track -->
(track: 'que-pena', 'stop')

<!-- pause a track -->
(track: 'que-pena', 'pause')
<!-- resume a paused a track -->
(track: 'que-pena', 'play')
```

#### **JavaScript**

Methods:  
- `track#play()`: Plays the indicated track, if possible.
  - Arguments: none.
  - Returns: this track (chainable).
  

- `track#playWhenPossible()`: Plays the indicated track, is possible, or waits for a valid user interaction to start playback.
  - Arguments: none.
  - Returns: this track (chainable).
  

- `track#stop()`: Stops a playing track (pauses it and resets it).
  - Arguments: none.
  - Returns: this track (chainable).
  

- `track#pause()`: Pauses playback of the indicated track, if it's playing.
  - Arguments: none.
  - Returns: this track (chainable).
  

```
<!-- play a track -->
<script>A.track('que-pena').play()</script>

<!-- play a track when the user clicks a link -->
{
(link-repeat: 'Click me!')[
    <script>A.track('beep').play()</script>
]
}

<!-- play a looping background track -->
<script>A.track('que-pena').loop(true).play()</script>

<!-- play a track when the ability to do so becomes unlocked -->
<script>A.track('que-pena').playWhenPossible()</script>

<!-- stop a track -->
<script>A.track('que-pena').stop()</script>

<!-- pause a track -->
<script>A.track('que-pena').pause()</script>
<!-- resume a paused a track -->
<script>A.track('que-pena').play()</script>
```

<!-- tabs:end -->

### Looping Tracks

A "looping" track is like hitting the "repeat" button on a music player; when a looping track ends, it will start over again from the beginning. 

> [!NOTE]
> Because HAL leverages the `HTMLMediaElements` API internally, it is not capable of true gapless playback--looped tracks will have a short stop between the end and the beginning that can not be removed.

<!-- tabs:start -->

#### **Macros**

Commands:  
- `loop`: Change the loop state of the indicated track.
  - Arguments:
    - (*boolean*) pass the value `true` to turn looping on, `false` to turn it off.
  - Returns: nothing.


- `toggleloop`: Toggle the loop state of the indicated track (setting it to `true` if it's false and vice versa).
  - Arguments: none.
  - Returns: nothing.


- `islooping`: Returns `true` if the indicated track is looping.
  - Arguments: none.
  - Returns: *boolean*.


```
<!-- set a track to loop and play it -->
(track: 'que-pena', 'loop', true)
(track: 'que-pena', 'play')

<!-- test to see if a track is looping -->
(if: (track: 'que-pena', 'islooping'))[
    The track is looping!
]

<!-- stop a track from looping -->
(track: 'que-pena', 'loop', false)

<!-- toggle the loop state of a track -->
(track: 'que-pena', 'toggleloop')
```

#### **JavaScript**

Methods:  
- `track#loop(bool)`: Change the loop state of the track.
  - Arguments:
    - `bool` (*boolean*) pass the value `true` to turn looping on, `false` to turn it off.
  - Returns: nothing.


- `track#toggleLoop()`: Toggle the loop state of the track (setting it to `true` if it's false and vice versa).
  - Arguments: none.
  - Returns: nothing.


- `track#isLooping()`: Returns `true` if the track is looping.
  - Arguments: none.
  - Returns: *boolean*.
  

```
<!-- set a track to loop and play it -->
<script>A.track('que-pena').loop(true).play()</script>

<!-- test to see if a track is looping -->
(if: A.track('que-pena').isLooping())[
    The track is looping!
]

<!-- stop a track from looping -->
<script>A.track('que-pena').loop(false)</script>

<!-- toggle the loop state of a track -->
<script>A.track('que-pena').toggleLoop()</script>
```

<!-- tabs:end -->

### Adjusting Time and Volume

You can adjust the time (called *seeking*) of a track as well as it's volume and mute states as well.

<!-- tabs:start -->

#### **Macros**

Commands:  
- `seek`: Change the time of the indicated track, moving to the indicated time for playback.
  - Arguments:
    - (*number*) pass a number of seconds; negative values will seek from the end of the track.
  - Returns: nothing.


- `mute`: Mute the indicated track.
  - Arguments:
    - (*boolean*) pass the value `true` to mute the track, `false` to unmute it. 
  - Returns: nothing.


- `togglemute`: Toggle the mute state of the indicated track (muting it if it isn't muted and vice versa).
  - Arguments: none. 
  - Returns: nothing.


- `ismuted`: Returns whether the indicated track is currently muted.
  - Arguments: none. 
  - Returns: *boolean*.


- `volume`: Sets the volume of the indicated track.
  - Arguments: 
    - (*number*) pass a number between 0 (silent) and 1 (full volume) to set the volume of a track.
  - Returns: nothing.


- `getvolume`: Returns the indicated track's current *own* volume--this does not account for the state of the master volume.
  - Arguments: none.
  - Returns: *number*.


```
<!-- seek to thirty seconds in then start playback of a track -->
(track: 'que-pena', 'seek', 30)
(track: 'que-pena', 'play')

<!-- mute a track -->
(track: 'que-pena', 'mute', true)

<!-- unmute a track -->
(track: 'que-pena', 'mute', false)

<!-- test to see if a track is muted -->
(if: (track: 'que-pena', 'ismuted'))[
    The track is muted!
]

<!-- toggle the mute state of a track -->
{
(link-repeat: 'Mute the BGM')[
    (track: 'que-pena', 'togglemute')
]
}

<!-- set a track's volume -->
(track: 'que-pena', 'volume', 0.5)

<!-- print a track's volume -->
(print: (track: 'que-pena', 'getvolume'))
```

#### **JavaScript**

Methods:  
- `track#seek(time)`: Change the time of the track, moving to the indicated time for playback.
  - Arguments:
    - `time` (*number*) pass a number of seconds; negative values will seek from the end of the track.
  - Returns: nothing.


- `track#mute(bool)`: Mute the track.
  - Arguments:
    - `bool` (*boolean*) pass the value `true` to mute the track, `false` to unmute it. 
  - Returns: nothing.


- `track#toggleMute()`: Toggle the mute state of the track (muting it if it isn't muted and vice versa).
  - Arguments: none. 
  - Returns: nothing.


- `track#isMuted()`: Returns whether the track is currently muted.
  - Arguments: none. 
  - Returns: *boolean*.


- `track#volume(vol)`: Sets the volume of the track.
  - Arguments: 
    - `vol` (*number*) pass a number between 0 (silent) and 1 (full volume) to set the volume of a track.
  - Returns: *boolean*.


- `track#getVolume()`: Returns the track's current *own* volume--this does not account for the state of the master volume.
  - Arguments: none.
  - Returns: *number*.
  

```
<!-- seek to thirty seconds in then start playback of a track -->
<script>A.track('que-pena').seek(30).play()</script>

<!-- mute a track -->
<script>A.track('que-pena').mute(true)</script>

<!-- unmute a track -->
<script>A.track('que-pena').mute(false)</script>

<!-- test to see if a track is muted -->
(if: A.track('que-pena').isMuted())[
    The track is muted!
]

<!-- toggle the mute state of a track -->
{
(link-repeat: 'Mute the BGM')[
    <script>A.track('que-pena').toggleMute()</script>
]
}

<!-- set a track's volume -->
<script>A.track('que-pena').volume(0.5)</script>

<!-- print a track's volume -->
(print: A.track('que-pena').getVolume())
```

<!-- tabs:end -->

### Fading Tracks

### Testing Tracks

## Master Audio Commands

## Defining Playlists

## Playlist Commands

## Defining Groups

## Group Commands

## Examples

## Pitfalls and Gotchas

## Additional JavaScript Features

### Additional Methods

#### Tracks

#### Master Audio

#### Playlists

#### Groups

### The Loading Screen

### The Controls API

### The Menu API

### Events

### Extensions