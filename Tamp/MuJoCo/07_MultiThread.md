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
- 渲染线程：按照固定帧率运行，主要负责调用 `glfwPollEvents()`， `glfwSwapBuffers(window)`，`mjv_updateScene` `mjr_render(viewport, scene, context)`
  - 理论上，渲染线程可以进一步区分成用来处理窗口的 窗口线程 和用来渲染的 渲染线程，这样可以以更高的频率运行窗口线程以响应输入事件。但是在这里没有必要。
- 模拟线程：主要负责调用 `mj_step()`，完成模拟
- 同步要求：`mjv_updateScene` 需要读取 mujoco 状态到 scene 中，`mj_step` 负责修改 mujoco 状态，两者之间需要有保护措施。

