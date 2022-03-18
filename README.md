# The most convenient way to debug applications

![ezgif com-gif-maker](https://user-images.githubusercontent.com/1832800/159041027-ed8c5888-9edf-4e5d-b0a4-2283a94ca094.gif)


In this article, I will show you an interesting way to debug applications. This is somewhat different from the usual methods that you use in your daily work.

## How do we debug today

* 🖨️ Print to stdout
* ▶️ Step-by-step debugger in IDE
* 🔙 Maybe even time-travel debugger
* 📜 Viewing logs

## Disadvantages of current debug techniqes

#### 🖨️ Print to stdout

* 🐌 Slower debug performance
* ↔️ Context switch between your code and stdout
* 🔁 Often need to rerun your program again and again

#### ▶️ Step-by-step debugger in IDE

* 🐌 Slower debug performance
* 🔁 Often need to rerun your program again and again

#### 🔙 Time-travel debugger

* 🏋️ Often very hard to implement
* 💲 Often not free

#### 📜 Viewing logs

* 🐌 Slower debug performance
* ↔️ Context switch between your code and logs

## Another way to debug - Smart Logs

![image](https://user-images.githubusercontent.com/1832800/157913106-dbf1c6b5-0774-424b-9f58-0c8cf63a7e34.png)

As you see on the screenshot, **Smart Logs** attached to functions and trace call of every function: parameters, response, possible error and so on. Pros:

* 🚀 Faster debug performance
* 🤝 No context switch between your code and logs
* 😊 No need to run the program again and again

## Implementation example

Further we will see an implementation example. **It's based on JavaScript/TypeScript and Node.js runtime**, but it can be implemented on lot programming languages and runtimes. **VSCode used as IDE**, but other IDE should be suitable too for this purpose.

#### Requirements

* 🔎 Ability to reflect function location in code (https://github.com/midrissi/func-loc)
* 🗒️ Line note ability in IDE (https://marketplace.visualstudio.com/items?itemName=tkrkt.linenote)

#### Code

```javascript
import { locate } from 'func-loc';
import { appendFile, mkdir } from 'fs/promises';
import endent from 'endent';

const ln = fn => {
  return function (...params) {
    const resp = fn.apply(this, params);

    Promise.allSettled([locate(fn), resp]).then(async ([loc, respOrErr]) => {
      await mkdir(`${process.cwd()}/.vscode/linenote`, { recursive: true });
      await appendFile(
        loc.value.path.replace(process.cwd(), `${process.cwd()}/.vscode/linenote`) + `#L${loc.value.line}.md`,
        endent.default`
          params: ${params},
          ${respOrErr.value ? `response: ${respOrErr.value}` : `error: ${respOrErr.reason}`}
          \\n\\n
        `
      );
    });

    return resp;
  };
};
```

* `ln` (alias to line note) is wrapper of target function `fn`
* `ln` intercepts parameters and response of `fn` or possible error inside `fn`
* `ln` reflects `fn` location in code
* using `fn` code location, `ln` create/append **Line Note** file on proper line with paramaters and response of `fn` in content

## By the way

* In code above ☝️ parameters and response interpolated to string "as is". On real production code you deal with complex JSON structures, which should be properly serialized
* You can add your own additional info: duration, timestamp and so on
* You can write TypeScript class decorator, which wrap all class methods for **Smart Logs** support
* On example used FILO via `appendFile`, but with https://github.com/hemanth/node-prepend-file you can change to FIFO
* You may get `EBUSY` error via concurrent access to same **Line Note** file. This sutation can be fixed via repeat strategy, using something like https://github.com/sindresorhus/p-retry
* You can write custom import to **Smart Logs**. For example: ELK logs -> **Smart Logs**


## What about compiled to JavaScript languages like TypeScript?

**func-loc** supports source maps https://github.com/midrissi/func-loc#using-source-maps

## What about frontend?

**func-loc** uses Node.js inspector underhood https://nodejs.org/dist/latest-v17.x/docs/api/inspector.html

But despite this, I think you can use **Smart Logs** with frontend using Electron, which support Node.js API and Browser API simuateniously https://www.electronjs.org/

## What if my programming language / runtime can't reflect function location dynamically?

Maybe it can reflect function location statically via compiler/transpiler/macro and so on.

