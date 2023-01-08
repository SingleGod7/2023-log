## 基本概念
### OpenGL是什么
笼统的说，OpenGL就是一个API，但是他本身并不是一个API，他时一个API的规范，不包括具体的细节，只是规定了每个函数的输入和输出。而这个规范的实现全都是由显卡厂商来做的，每个显卡厂商都会对自己的硬件进行优化。
### 核心模式和立即渲染模式
立即渲染模式就是固定渲染管线，可以很容易的进行图形的绘制但是可定制能力很差，做不出来很多我们需要的效果，所以现在已经废除了。核心模式就是我们现在的可编程渲染管线，具有更高定制性和效率，但是学习起来更复杂。
### 拓展
就是一些新特性没写进OpenGL但是又很好用，我们就可以采用两种实现方式，通过判断硬件对新特性的支持来调用不同的执行方式。
### 状态机
OpenGL是一个巨大的状态机，OpenGL的状态被称为context，我们可以使用状态设置函数来设置当前状态和状态使用函数来根据当前状态来执行一些操作。这就是OpenGL的本质（？）。
### 对象
创建一个对象->绑定对象到上下文->设置对象->解绑对象(设置失效)
这样工作流的好处就是如果我们需要渲染一个frame,只需要绑定所有需要的对象就行了,很方便可以增加或者减少场景中的东西(?).
## 创建context和一个窗口
GLFW等库用来提供抽象的接口,使得我们的代码可以跨平台.
GLAD用于获取特定驱动中OpenGL规定的函数指针的位置,我们就不用手动获取,直接使用OpenGL的函数就好了.
### 代码
```Cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>

#include <iostream>

//用于处理当窗口Resize之后,view仍然可以铺满整个window
void framebuffer_size_callback(GLFWwindow* window, int width, int height);
//处理输入
void processInput(GLFWwindow *window);

// settings
const unsigned int SCR_WIDTH = 800;
const unsigned int SCR_HEIGHT = 600;

int main()
{
    // glfw: initialize and configure
    // ------------------------------
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);//设置opengl版本和核心模式

#ifdef __APPLE__
    glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
#endif

    // glfw window creation
    // --------------------
    GLFWwindow* window = glfwCreateWindow(SCR_WIDTH, SCR_HEIGHT, "LearnOpenGL", NULL, NULL);
    if (window == NULL)
    {
        std::cout << "Failed to create GLFW window" << std::endl;
        glfwTerminate();
        return -1;
    }
    glfwMakeContextCurrent(window);
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

    // glad: load all OpenGL function pointers
    // ---------------------------------------
    if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
    {
        std::cout << "Failed to initialize GLAD" << std::endl;
        return -1;
    }    

    // render loop
    // -----------
    while (!glfwWindowShouldClose(window))
    {
        // input
        // -----
        processInput(window);

        // render
        // ------
        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        // glfw: swap buffers and poll IO events (keys pressed/released, mouse moved etc.)
        // -------------------------------------------------------------------------------
        glfwSwapBuffers(window);
        glfwPollEvents();
    }

    // glfw: terminate, clearing all previously allocated GLFW resources.
    // ------------------------------------------------------------------
    glfwTerminate();
    return 0;
}

// process all input: query GLFW whether relevant keys are pressed/released this frame and react accordingly
// ---------------------------------------------------------------------------------------------------------
void processInput(GLFWwindow *window)
{
    if(glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}

// glfw: whenever the window size changed (by OS or user resize) this callback function executes
// ---------------------------------------------------------------------------------------------
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    // make sure the viewport matches the new window dimensions; note that width and 
    // height will be significantly larger than specified on retina displays.
    glViewport(0, 0, width, height);
}
```
挺简单的, 首先创建了一个窗口上下文,然后开始渲染循环,渲染循环中处理了键盘输入和事件,最后用一个颜色来清空每一帧的绘制.