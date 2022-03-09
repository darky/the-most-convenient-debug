# The most convenient way to debug applications

In this article, I will show you an interesting way to debug applications. This is somewhat different from the usual methods that you use in your daily work.

## How do we debug today

* 🖨️ Print some variable value to stdout
* ▶️ Step-by-step debugger in IDE
* 🔙 Maybe even time-travel debugger
* 📜 Viewing logs

## Disadvantages of current debug techniqes

#### 🖨️ Print some variable value to stdout

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

As you see in the screenshot, **Smart Logs** attached to your functions or class methods and trace every function call, call parameters, response, duration and so on. Pros:

* 🚀 Faster debug performance
* 🤝 No context switch between your code and logs
* 😊 No need to run the program again and again

## How it works

Further we will see an implementation example. It's based on JavaScript/TypeScript and Node.js runtime, but it can be implemented on lot programming languages and runtimes. VSCode used as IDE, but other IDE should be suitable too for this purpose.

#### Requirements

* 🔎 Ability to reflect function location in code (https://github.com/midrissi/func-loc)
* 🗒️ Line note ability in IDE (https://marketplace.visualstudio.com/items?itemName=tkrkt.linenote)
