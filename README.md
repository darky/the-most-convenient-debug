# The most convenient way to debug applications

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

Further we will see an implementation example. It's based on JavaScript/TypeScript and Node.js runtime, but it can be implemented on lot programming languages and runtimes. VSCode used as IDE, but other IDE should be suitable too for this purpose.

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

    Promise.resolve(Promise.allSettled([locate(fn), resp])).then(async ([loc, respOrErr]) => {
      await mkdir(`${process.cwd()}/.vscode/linenote`, { recursive: true });
      await appendFile(
        loc.value.path.replace(process.cwd(), `${process.cwd()}/.vscode/linenote`).concat(`#L${loc.value.line}.md`),
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
