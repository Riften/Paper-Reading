# Multi Threading
- [Multithreading GLFW](https://discourse.glfw.org/t/multithreading-glfw/573)
- [StackOverflow: What does glew do and why do i need it](https://stackoverflow.com/questions/17809237/what-does-glew-do-and-why-do-i-need-it)

## MuJoCo 渲染框架
与 MuJoCo 渲染相关的有三个库：
- OpenGL：图形库本身，MuJoCo 用 OpenGL 实现了渲染。
- GLEW：简言之不用管。The OpenGL Extension Wrangler Library，是一个可以方便使用 OpenGL 的工具，OpenGL 本身接口众多，不同平台支持情况也不同，GLEW 可以在运行期间检测接口支持情况，提供可用的 OpenGL 接口。
- GLFW：Graphics Library Framework，是一个封装了窗口、硬件输入、context管理的库。本质上也是调用 GL 接口。

MuJoCo 并不依赖于 GLFW，如果只是为了渲染和显示，完全可以通过 GL 接口来实现。但是 GLFW 在处理各种外设输入、窗口事件上真的很方便，所以最方便的 MuJoCo 渲染结果的查看方法就是用 GLFW 的窗口来查看。

## 多线程
多线程渲染的主要限制在于
- OpenGL 本身是单线程的，将 `mjr_render` 和显示线程分开会有问题，需要同步机制。
- 操作系统的事件获取有线程限制，`glfwPollEvents()`只能获取到当前线程的 event queue 中的事件，也就是创建和初始化窗口和事件处理需要在一个线程。

> Note that the glfw docs specify the ‘main thread’ but most OSs post events to the same thread as the window was created, so keeping all your glfw init, window and event calls on one thread should work.

所以实现多线程 MuJoCo 渲染的基本思路是
- 把GLFW相关调用放在同一个 thread 里面（窗口线程）
- 把 MuJoCo 的渲染放在一个 thread 里面（渲染线程）
- 窗口线程按照固定 FPS 运行
- 渲染线程可以按照更高的频率运行
- 两个线程之间通过 mutex 同步