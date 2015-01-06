---
layout:     post
title:      Ember CLI&#58; where to reopen framework classes
date:       2015-01-02 15:31:19
summary:    How and where to put framework specific code in EmberJS
author:     josh
---

Overal EmberJS is a fantastic framework, and we’re very happy with our choice. It's made developing [Matterhorn](http://matterhorn.io/) an enjoyable and for the most part pretty straight forward process.

However, there are times when it is in infuriating! We’re going to try and document all of these issues so you hopefully don’t have to!

An issue which came up recently was how to reopen the text area class so that is allows `cmd/ctrl + enter` to save a form.

## Options 1 - Initializers
My first thought was to place the code in an initializer. This appeared to be a relatively good solution as it's auto-loaded by Ember.

<script src="https://gist.github.com/onlymejosh/9e7adcb2e73f95a08a2e.js"></script>

However, it has its downsides: [Initializers can be run multiple times in test environments](https://github.com/ember-cli/ember-cli/issues/1143#issuecomment-46933890)

## Option 2 - Import
After doing some research I came across a better option, which I’ll be using from now on. It relies on a new folder called app/overrides. This seems to be a better solution as it's a logical place to store all extensions to EmberJS’s default functionality.

<script src="https://gist.github.com/onlymejosh/6c2ca6ef7d65e55b6865.js"></script>

Inside of app/app.coffee you simply add:

{% highlight coffee %}
`import TextArea from './overrides/textarea';`
{% endhighlight %}

The last thing is to change is the textarea, and this stumped me for a bit:

<script src="https://gist.github.com/onlymejosh/0fa6a4ea2d35589212e2.js"></script>

Turns out ```modifiedSubmit``` allows the text area to save when cmd/ctrl + enter is pressed. *And thats it!*

#### Other Useful Resources:
* [Stackoverflow: Ember CLI: where to reopen framework classes](http://stackoverflow.com/questions/27154886/ember-cli-where-to-reopen-framework-classes)
* [Github Ember Issue](https://github.com/ember-cli/ember-cli/issues/1143#issuecomment-46933890)
* [Medium article: Submit an Ember TextArea with Command or Ctrl + Enter](https://medium.com/the-ember-way/submit-an-ember-textarea-with-command-or-ctrl-enter-a933b4325b3b)






