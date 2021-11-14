# Enum proposal

[Roadmap, design goal and relationship to the prior proposals #2](https://github.com/Jack-Works/proposal-enum/discussions/2)

Please go to [discussion forum](https://github.com/Jack-Works/proposal-enum/discussions) for design details!

## Example

### Plain enum

```js
enum Days for number {
    Monday = 0,
    Friday = 4,
}

enum Days for string {
    Monday, Friday
}

enum Days for symbol {
    Monday, Friday
}

enum Days for bigint {
    Monday = 0n,
    Friday = 4n,
}
```

### ADT enum

```js
enum Message for symbol with MessageCreator {
    Quit,
    Move { x, y },
    Write(message),
    ChangeColor(red, green, blue),
}

const msg = MessageCreator.Quit()
const msg2 = MessageCreator.Move({ x: 0, y: 1 })
const msg3 = MessageCreator.Write("")
const msg4 = MessageCreator.ChangeColor(255, 255, 255)

msg.type === Message.Quit // it's a symbol
Object.hasOwn(msg, "data") // false

msg2.type === Message.Move
msg2.x === 0
msg2.y === 1

msg3.type === Message.Write
msg3.data === ""

msg4.type === Message.ChangeColor
Array.isArray(msg4.data) && msg4.data.join(',') === "255,255,255"
```

#### Use with pattern matching

```js
const M = MessageCreator
match (msg) {
    when ^M.Quit { process.exit() },
    when ^M.Move as { x, y } { entity.move(x, y) },
    when ^M.Write as message { console.log(message) },
    when ^M.ChangeColor as [r, g, b] { todo() }
}
```

#### Use with TypeScript

```js
enum Message<T> of symbol with MessageCreator {
    Quit,
    Move { x: number, y: number },
    Write(message: string),
    ChangeColor(red: number, green: number, blue: number),
    Generics(data: T)
}
```
