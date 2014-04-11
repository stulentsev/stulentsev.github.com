---
layout: post
title: "How to make custom commands in Atom extensions"
date: 2014-04-11 19:58
comments: true
categories: 
---

A couple of days ago I finally got my invite to [Atom editor](http://atom.io). A bit late, but I still was excited. 

It makes overall positive impression and has some essential things bundled (save on lost focus, trim trailing whitespaces, etc.) But still there are some things lacking. So I decided to check out its praised extensibility.

Again, docs look good and cover some first steps. But I spent a good couple of hours today, trying to invoke my custom command.

``` coffeescript
module.exports =
  activate: (state) ->

  process: ->
    console.log 'doing custom action'
    editor = atom.workspace.getActiveEditor()
    editor.insertText('hello')

```

`process` in the listing above is a simple action. It gets current editor and inserts string "hello" at the cursor. I defined an editor shortcut, like this:

``` json
'.editor':
  'cmd-ctrl-shift-e': 'myext:process'
```

where `myext` is the name of my extension. But upon pressing the hotkey, nothing happened. A couple of hours later I got the solution. My mistake was in assuming that Atom will somehow discover and dynamically call that exported method `process`. It's exported for a reason, right? Well, no. It turns out that you have to bind commands manually.

``` coffeescript
module.exports =
  activate: (state) ->
    atom.workspaceView.command "myext:process", => @process()

  process: ->
    console.log 'doing custom action'
    editor = atom.workspace.getActiveEditor()
    editor.insertText('hello')
```

Next time, we'll do something useful. :)
