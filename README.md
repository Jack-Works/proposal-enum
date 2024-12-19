# Enum proposal

## Champion group & Authors

-   [Ron Buckton](https://github.com/rbuckton/)
-   [Rick Waldron](https://github.com/rwaldron/)
-   [Jack Works](https://github.com/Jack-Works/)

## Motivation

There is a common need today to create related but _distinguishable values_, and there are many different ways to do it,
and you can easily make mistakes.

Let's take an example: https://nodejs.org/dist/latest-v17.x/docs/api/fs.html#fs_file_open_constants

```js
import { open, constants } from 'node:fs'

const { O_RDWR, S_IFDIR } = constants
```

-   No difference between file open and file type constants other than convention (i.e.,`O_`,`S_`)
-   No useful information when debugging (i.e., what does a mode of `1280` mean?)

```js
export const MESSAGE_TYPE = Object.freeze({
    CONNECT: 'connect',
    DISCONNECT: 'disconnect',
    MESSAGE: 'message',
    ERROR: 'error',
})

export function createMessage(type, ...args) {
    if (type === MESSAGE_TYPE.CONNECT) return { type: MESSAGE_TYPE.CONNECT }
    if (type === MESSAGE_TYPE.DISCONNECT) return { type: MESSAGE_TYPE.DISCONNECT }
    if (type === MESSAGE_TYPE.MESSAGE) return { type: MESSAGE_TYPE.MESSAGE, message: args[0] }
    if (type === MESSAGE_TYPE.ERROR) return { type: MESSAGE_TYPE.ERROR, error: args[0] }
    throw new TypeError('Invalid message type')
}
```

We want to provide a better way to create those values.

Enum can help:

-   Make code more readable
-   Hint useful information to the toolchain (e.g. constant inlining, exhaustiveness checks, ...)
-   All distinct values are naturally grouped in the syntax

```js
enum MessageType {
    CONNECT,
    DISCONNECT,
    MESSAGE,
    ERROR,
}
match (val) {
// ~~~~~~~~ Error: Missing case unhandled: val.type might be MessageType.ERROR
    when ({type: ${MessageType.CONNECT}}) -> startListening()
    when ({type: ${MessageType.DISCONNECT}}) -> stopListening()
    when ({type: ${MessageType.MESSAGE}, message}) -> event.emit('message', message)
}
```

## Goal

-   number enums
-   string enums
-   symbol enums
-   Some helper functions. e.g.:

```js
Enum.format(FileOpen, mode) // ["CREAT", "EXCL"]
```

## Why a frozen object is not enough?

Even you can create an enum today like:

```js
export const MESSAGE_TYPE = Object.freeze({
    CONNECT: 'connect',
    DISCONNECT: 'disconnect',
    MESSAGE: 'message',
    ERROR: 'error',
})
```

You will still need to create helper functions to

-   lookup enum key from the value
-   turn bitflag into human-readable flags
-   parsing (if a primitive value is a member of the enum)

Engine will still need to do a runtime lookup when accessing those values.

## Future steps, ADT enum

**What is ADT enum?**

Plain enum is "related distinguishable **primitive values**", and ADT enum is "related distinguishable **rich data
structures**".

With ADT enum, the example above can be rewritten as:

```js
enum MessageType {
    CONNECT,
    DISCONNECT,
    MESSAGE(message),
    ERROR(error),
}
match (val) {
// ~~~~~~~~ Error: Missing case in the match clause: MessageType.ERROR
    when (${MessageType.CONNECT}) -> startListening()
    when (${MessageType.DISCONNECT}) -> stopListening()
    when (${MessageType.MESSAGE} with message) -> event.emit('message', message)
}
```

This part is more complicated, but also much more useful. It'll be a challenge to work on this, therefore we want to
exclude it from the current proposal.

We're interested to develop the idea of ADT enum and make sure that plain enum will not block the possibility that we
adding this feature in the future.
