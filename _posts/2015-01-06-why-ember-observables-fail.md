---
layout: post
title: 'Why Ember observables sometimes fail, and how to make them stop'
summary:
date: 2015-01-06 15:11:29
author: ale
---

### Computed properties and observers
A computed property is a value that gets updated when one or more other properties change. Say your `User` has a first and a last name and you want to combine them, you would define a property:

```coffeescript
`import DS from 'ember-data'`

User = DS.Model.extend
  first_name:
    DS.attr('string')
  last_name:
    DS.attr('string')
  email:
    DS.attr('string')

  full_name: "#{first_name} #{last_name}"

`export default User`

```

Ember lets us define this so that we don't need to manually update it if either the `first_name` or `last_name` change. This can be achieved by creating a computed property:

```coffee
full_name: (->
  return "#{@get('first_name')} #{@get('last_name')}"
).property('first_name', 'last_name')
```
From now on, every time either of `first_name` or `last_name` change, the `User` will update its `full_name` property to be the return value of our computed property.

Computed properties should not have any side effects, they should only be used as a means of assembling together properties that can change. If we wanted to create a button to select all the emails in a list, for instance, we could use an observer to react to the flip of a `select_all` flag:

```coffee
`import Ember from 'ember'`

EmailListController = Ember.ObjectController.extend
  select_all: false # false by default

  setEmailsAsSelected: (->
    if @get('select_all')
      @get('emails').map (email) ->
        email.set('selected', true)
  ).observes('select_all')

`export default EmailListController`
```

Observers work much the same way as computed properties but they do not take on any value when they execute and are therefore used only to cause side effects.

In computed properties and observers you can listen for changes in simple values like strings, numbers, booleans, array lengths and in model relationships like `belongsTo` and `hasMany`.

####In general we can:

* Listen to a change in a simple value: `.observes('email')`, `.property('first_name')`
* Listen to a change in the length of an array: `.observes('emails.[]')` or `.observes('emails.length')`
* Listen to a change to any one of the elements of an array of simple values: `.observes('emails.@each')`
  - Note that this is triggered also if an element is added or removed
* Listen to a change in a `belongsTo` relationship, eg. our `User` belongs to a `Team`, that has a `name` and a list of `User`s:
  - If we want to react to a change in the team itself eg. `user_1`'s team has changed from `team_1` to `team_2` or to null: `.observes('team')`
    + This will not execute if we change `team_1`'s `name`
  - If we're interested in the team's properties changing, we do: `.observes('team.name')`
    + This will also trigger if we change the team itself.
* Listen to a change in a `hasMany` relationship, eg. our `Team` has many `User`s
  - As in arrays of simple values we can do `.observes('users.[]')` and `.observes('users.@each')`, where `@each` behaves like `.observes('team')`, so it is only triggered if the user itself is changed, not its properties.
  - If we want to check for a change in any of the users' properties we need to do for example `.observes('users.@each.first_name')`
    + Note that we can also observe computed properties `'.observes('users.@each.full_name')`

<div class="question">
  What if I have nested relationship? Can I just do @each.@each?
</div>

```Yeah, basically, except not.```

### beforeObserves, observesImmediately, on


### What triggers an observer and [what doesn't](https://github.com/emberjs/data/issues/1367)
