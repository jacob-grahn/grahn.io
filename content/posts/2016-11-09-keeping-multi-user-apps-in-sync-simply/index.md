---
title: "Keeping Multi User Apps In Sync (Simply)"
date: "2016-11-09T19:16:00-05:00"
author: "Jacob Grahn"
description: "In a magical world where nothing goes wrong, how can two clients update the same state in realtime?"
lastmod: "2020-02-13T19:03:39-05:00"
---

Here is a simple method to keep multiple users in sync with a shared state (in real time). The core concepts here are flexible enough to work anything from a collaborative document editor to a real-time multiplayer game.

To help keep things simple, we'll build a super fun game called "Push the Button First!". This game will be played by Bob and Sue, and BOY do they both want to be the first person to push that button.

# State?

State is the data that you want to share between two people. For our "Push the Button" game, our state will be very simple.

```
// state
const state = {  
  personWhoPushedFirst: null
}
```

# Changing the State

The state of our game will change when Bob or Sue click the button. To keep things simple, we will impose a rule: State can only be changed by emitting actions. We will have a list of mutations that take emitted actions, and use them to change the state.

```
// state
cosnt state = {  
  personWhoPushedFirst: null
}

// all possible ways to change the state go here
const mutations = {  
  clickTheButton (action) {
    if (!state.personWhoPushedFirst) {
      state.personWhoPushedFirst = action.person
    }
  }
}

// action emitter
function emit(action) {  
  const type = action.type
  const mutation = mutations[type]
  mutation(action)
}
```

# Push That Button!

Let's say Bob and Sue both want to win, so they both push the button as fast as they can. On Bob's computer, this action will be emitted:

```
emit({  
  type: 'clickTheButton',
  person: 'Bob'
})
// now state.personWhoPushedFirst equals 'Bob' on Bob's computer
```
...However, on Sue's computer, this action will be emitted:

```
emit({  
  type: 'clickTheButton',
  person: 'Sue'
})
// now state.personWhoPushedFirst equals 'Sue' on Sue's computer
```
This is all well and good, but we want Bob and Sue to share their state. There can be only one winner of this game!

# We Need a Hero

We'll use a server named "Hero" to keep Bob and Sue connected. Hero will create a definitive record of what happened. It does this by emiting actions back to Bob and Sue in the order that they were received.

```
// server code
function handleActionFromClient(action) {  
  emitToClients(action) // send this action to all connected clients
}
```

# Sorting it All Out

Bob and Sue need to emit their actions to Hero, and then Hero will emit those actions back to Bob and Sue in order. Bob and Sue will believe whatever Hero says, because Hero is a very trustworthy server!
We need to modify our client code, so that Bob and Sue send their actions to Hero, and listen for Hero's actions.

```
// action emitter
function emit(action) {  
  emitToServer(action)
}

// listen to what Hero says
function handleActionFromServer(action) {  
  const type = action.type
  const mutation = mutations[type]
  mutation(action)
}
```
# The Final Code

### Client Code:

```
// state
cosnt state = {  
  personWhoPushedFirst: null
}

// all possible ways to change the state go here
const mutations = {  
  clickTheButton (action) {
    if (!state.personWhoPushedFirst) {
      state.personWhoPushedFirst = action.person
    }
  }
}

// action emitter
function emit(action) {  
  emitToServer(action)
}

// listen to what Hero says
function handleActionFromServer(action) {  
  const type = action.type
  const mutation = mutations[type]
  mutation(action)
}
```
### Server Code:

```
// server code
function handleActionFromClient(action) {  
  emitToClients(action) // send this action to all connected clients
}
```
# Let's Play

Now we're ready for Bob and Sue to play "Push the Button" again! Bob and Sue both push the button as fast as they can!
On Bob's computer, this action will be emitted:

```
emit({  
  type: 'clickTheButton',
  person: 'Bob'
})
// this action as been sent to Hero
...However, on Sue's computer, this action will be emitted:

emit({  
  type: 'clickTheButton',
  person: 'Sue'
})
// this action has been sent to Hero
```
Hero gets Sue's action first, and then Bob's. Hero sends those actions back to Bob and Sue in order. Bob and Sue both run the actions using their "clickTheButton" mutation, and both Bob and Sue determine Sue to be the winner! Congratulations Sue!

# Going Further

These are the core concepts of sharing a state between multiple people, and it can serve as a starting point for making a real-time multi-user app.

Here are some additional things that would make this better:

- It would be easy use the same state and mutations on the server to keep the server's state in sync with the clients. This would be useful for storing backups of the state, or sending the state to a new user.
- Optimistic updates could be implemented by creating a record of un-verified actions that have been emitted, and trusting them up until an official action is received back from the server.
- Actions can be sent peer-to-peer and used optimistically using the same strategy as above.
