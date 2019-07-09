# What is HAL?

[Try out the demo.](./demo/ ':ignore')

HAL is an audio library for [Harlowe (v2.1.0 or higher)](https://twine2.neocities.org/) designed to give the format feature parity (or close to it) with [SugarCube's audio subsystem](http://www.motoslave.net/sugarcube/2/docs/#simpleaudio-api). HAL was designed from the ground up *specifically* for use with Harlowe, and is intended to be as easy to use as possible for novice programmers without compromising functionality.

## Features

- All the core features you'd expect from such a library: playing across passages, fading, looping, individual track volume levels, etc.
- Support for audio playlists.
- Support for "audio groups," which let authors control track settings on groups of related tracks all at once.
- Includes an optional, user-configurable sidebar with built-in controls, like a master volume control and a master mute button.
- Supports preloading of audio, with an optional loading screen.
- Highly configurable and extensible for users who need it, with sane defaults and a bevy of built-in features for users who don't want to mess with any of that.

In v2.0.0 and higher:
- A set of custom audio macros to simplify usage.
- HAL uses *special passages* for configuration options and allows users to optionally use a special passage to define tracks, further simplifying the usage of HAL.

## Getting the Library

You can download any version of HAL from [here](https://github.com/ChapelR/harlowe-audio/releases). If you're just getting started, it's highly recommended that you grab the latest version. Once you've downloaded HAL, you'll need to read the [guide](#guide) or the [documentation](v2) to learn how to use it.

There are two main version of HAL. The latest version 2 release is recommended for most users. If you're already using version 1 in your project, you don't have to update if you don't want to and can instead grab the latest version 1 release. The version 1 docs are [here](v1).

See [below](#guide) for a guide on the basics of using HAL.

## Quick Links

- [Downloads](https://github.com/ChapelR/harlowe-audio/releases)
- [Changelog](changelog)
- [Demo](./demo/ ':ignore')
- [HAL v2 Complete Documentation](v2)
- [HAL v1 Complete Documentation](v1)

---

# Guide

This is a quick, ten-minute (give or take) guide to getting HAL working. You're here for audio, and this guide aims to please. HAL has a lot of other nice features you'll no doubt love to come to grips with later, but for now, we'll focus on the bare minimum you'll need to know to take your story from a boring, soundless husk to grooving like it's 1999.

> [!NOTE]
> This guide is intended to get users up and running with HAL quickly and without too much fuss. It covers v2.0.0 and higher of HAL, [for v1, check out its docs](v1). For detailed reference documentation for v2, [click here](v2).

## Installing HAL in Twine 2

[See the docs for installing with other compilers.](v2#installation)

To install HAL, all you need is the code, which you can get [on the releases page of the repo](https://github.com/ChapelR/harlowe-audio/releases). You should choose the latest version possible. Download the `harlowe-audio.zip` file and unzip it. There should be three files, `harlowe-audio.min.js`, `harlowe-audio.min.css`, and `LICENSE`. Open the `harlowe-audio.min.js` file in a text editor, copy all of the contents, and paste it into your story JavaScript area in Twine 2. To find the story JavaScript area, click the up arrow next to your story's name in the editor (bottom left-hand side), then click the appropriate menu option.

<image src='./assets/js.jpg' title='The story JavaScript menu option in the Twine 2 editor.' alt='The story JavaScript menu option in the Twine 2 editor.' width='50%'>

You'll then do the same thing with the `harlowe-audio.min.css` file, pasting it into the story Stylesheet area instead.

<image src='./assets/css.jpg' title='The story Stylesheet menu option in the Twine 2 editor.' alt='The story Stylesheet menu option in the Twine 2 editor.' width='50%'>

> [!TIP]
> You should open these files in a **text editor**, not a word processor. Any text editor you have, like notepad, will work fine for this. Opening the file in a word processor may cause the code to stop working, as they sometimes convert certain characters into other characters on loading files&mdash;like turning normal quotes (&quot;...&quot;) into curly quotes (&ldquo;...&rdquo;)&mdash;that can mess up programming code.

Once all the code is in place, try playing your story. If you see a gray sidebar on the left with a white arrow at the top, and you don't encounter any errors popping up, you've installed it right!

<image src='./assets/installed.jpg' title='If you have a sidebar like this and no errors, HAL has been installed correctly.' alt='If you have a sidebar like this and no errors, HAL has been installed correctly.'>

## Defining Tracks

[For more detailed information, see the docs.](v2#defining-tracks)

There are a few ways to define tracks in HAL, but the easiest (and recommended) way is with a *special passage* called `hal.tracks`.

> [!TIP]
> **What is a Special Passage?**
> A special passage is a concept from Twine 1 and SugarCube where certain passages with special names have meaning to the story format. Harlowe doesn't use special passages (though it does have [special tags](https://twine2.neocities.org/#section_passagetag)). All you need to know to create a special passage is what name to give it. So to make the special passage, create a new passage in your story and give it the name `hal.tracks`.

<image src='./assets/tracks.jpg' title='An example of the tracks special passage.' alt='An example of the tracks special passage.'>

In this special passage you'll define each track on its own line. Tracks require a name and a list of URLs that point to audio files (that means they end with an audio extension, like `.mp3` or `.ogg`). You can give a track a number of source URLs; if you do, they should be the same track just in different file formats. If you only want to use one file format, `.mp3` is the safest bet. If you have the capability to create duplicates of the track in different formats, including both `.mp3` and `.ogg` versions is recommended, as that combination will give you the maximum possible browser support.

The sytax to define a track is to list the track name first, then a colon, and finally one or more source URLs. If you include multiple source URLs, the list should be comma-separated. You do not need to quote anything, not the track names or the source URLs.

Track names should only include letters (upper or lower case), numbers, dashes (`-`), underscores (`_`), and dollar signs (`$`). Other characters may work but are not officially supported. That's code for "don't use them even when they seem like they're working."

An example of a `hal.tracks` story that defines two tracks: a beeping sound called `beep` and a song called `que-pena`.

```
beep: ./audio/beep.mp3, ./audio/beep.ogg
que-pena: ./audio/que-pena.mp3, ./audio/que-pena.ogg
```

Most projects will probably want to use [*relative* URLs](#defining-tracks-with-relative-urls) as shown above, but [absolute URLs](#defining-tracks-with-absolute-urls) are also supported. We'll get more into the weeds with these concepts in some [examples](#examples) later.

## Playing Tracks

[For more detailed information, see the docs.](v2#track-commands)

On a basic level, all you need to do to play a track is have this code in a passage `(track: 'beep', 'play')`. However, [reality often resists simplicity](#autoplay-or-why-won39t-my-audio-work-on-the-first-passage).

This is the `(track:)` macro, and you give it the name of a track you've defined and a command to perform. Some commands accept additional arguments, which you can just pass to the macro after the command name. For example, to set a track to loop, you'd write `(track: 'que-pena', 'loop', true)`. This is because the *loop command* accepts an argument, which should be boolean `true` or `false`; `true` to set the track to loop, `false` to stop it from looping.

The lion's share of commands in HAL don't require any arguments, and very few commands require more than one, but when they do, they have to be given to the macro in the correct order, so you will need to check the docs for those.

> [!NOTE]
> When you write one of HAL's macros in the Twine 2 passage editor, it will show up colored in red like macros that don't exist. It is not possible for a library like HAL to edit Harlowe's syntax highlighting feature, so this is unfortunately impossible to fix. Just know that it's expected behavior and doesn't mean you did anything wrong.

## Examples

Now that you've got HAL installed and you know how to define a few tracks and play them, we'll cover a bunch of common use-cases here. To see [more complex examples and use-cases](v2#detailed-examples), and learn more about what you can do with HAL, look at the [reference documentation](v2).

### Defining Tracks with Absolute URLs

### Defining Tracks with Relative URLs

### Autoplay (Or, Why Won't My Audio Work on the First Passage?)

### Stopping a Sound Before Playing Another

### Checking If a Sound Is Already Playing

### Playing Sounds When Links Are Clicked

## This Is Only the Beginning

HAL supports many other features, including [master audio controls](v2#master-audio-commands), [playlists](v2#defining-playlists), and [audio groups](v2#defining-groups), and there are many other [track commands](v2#track-commands) you can use. You can also change HAL's [configuration settings](v2#configuration), use JavaScript event handlers to plug into [various audio events](v2#events), and even [edit the sidebar to display a menu](v2#the-menu-api).

To learn more, check out [HAL's documentation](v2). It's a bit drier and more technical than this guide, but it's worth reading if you have grander designs than what's been covered here.