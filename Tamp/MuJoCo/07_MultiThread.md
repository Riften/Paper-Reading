# Multi Threading
- [Multithreading GLFW](https://discourse.glfw.org/t/multithreading-glfw/573)
- [StackOverflow: What does glew do and why do i need it](https://stackoverflow.com/questions/17809237/what-does-glew-do-and-why-do-i-need-it)

与 MuJoCo 渲染相关的有三个库：
- OpenGL：图形库本身，MuJoCo 用 OpenGL 实现了渲染。
- GLEW：简言之不用管。The OpenGL Extension Wrangler Library，是一个可以方便使用 OpenGL 的工具，OpenGL 本身接口众多，不同平台支持情况也不同，GLEW 可以在运行期间检测接口支持情况，提供可用的 OpenGL 接口。
- GLFW：Graphics Library Framework，是一个封装了窗口、硬件输入、context管理的库。本质上也是调用 GL 接口。

MuJoCo 并不依赖于 GLFW，如果只是为了渲染和显示，完全可以通过 GL 接口来实现。但是 GLFW 在处理各种外设输入、窗口事件上真的很方便，所以最方便的 MuJoCo 渲染结果的查看方法就是用 GLFW 的窗口来查看。

但是大多数平台的外设事件是必须从 main thread 访问的，