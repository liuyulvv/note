# Vulkan with Qt as GUI
Vulkan is a graphics API and doesn't offer a window to show the rendering result. So sometimes we need other libraries to make this possible, such as [glfw](https://github.com/glfw/glfw).

When we use glfw, our codes may look like this:

```cpp
glfwInit();
glfwWindowHint(glfw_CLIENT_API, glfw_NO_API);
auto window = glfwCreateWindow(width, height, title, nullptr, nullptr);

VkSurfaceKHR surface = nullptr;
glfwCreateWindowSurface(instance, window, nullptr, &surface);
```

To enable glfw in Vulkan, we need to enable some extensions when create Vulkan instance, which can get through `glfwGetRequiredInstanceExtensions`. With the help of glfw, we don't need to process different windows in different operate systems, and we can show our system's rendering result in this surface. It also gives us the ability to handle keyboard, mouse and window resize events, this make our application more exciting.

GUI is common in desktop software, such as a button, a radio and check box. [Dear ImGUI](https://github.com/ocornut/imgui) is a library widely used in Vulkan or OpenGL based application. It makes it very easy to render a button or other GUI components in rendering system.

`ImGUI` means [immediate mode](https://en.wikipedia.org/wiki/Immediate_mode_GUI) GUI, if you have used `ImGUI`, you will find that we need to draw GUI every frame. There are two types of GUI generally, one is `immediate mode` and another is [retained mode](https://en.wikipedia.org/wiki/Retained_mode). The GUI of Qt is retained mode. I will not compare these two GUI types, chose which type need to consider your own needs.

I want to use rich GUI components and layout system of Qt, and I have learned Qt before, so I think Qt is better for me.

Qt had supported Vulkan, and it offered `QVulkanInstance`  and `QVulkanWindow`. QVulkanInstance and QVulkanWindow free you from making Vulkan instance, selecting physical device, making logical device and many other tedious operations.

But this is not accord with my need, because I have spent much time in these tedious operations when I learn Vulkan :sweat_smile: , I don't want to waste my code, so I have to spend much time to make my code integrated in Qt.

`glfwCreateWindowSurface` help us make `VkSurfaceKHR` in different systems, we can make `VkSurfaceKHR` from Qt, I will show how to make it in Windows.

```cpp
#if !defined(VK_USE_PLATFORM_WIN32_KHR)
#define VK_USE_PLATFORM_WIN32_KHR
#endif  // VK_USE_PLATFORM_WIN32_KHR
#include "vulkan/vulkan.h"

class OurWindow : public QWindow {
public:
    OurWindow() = default;
    VkWin32SurfaceCreateInfoKHR getSurfaceInfo();
}；
```

Firstly, we make `OurWindow` inherit from `QWindow` like `QVulkanWindow`, add a member function `getSurfaceInfo`, return `VkWin32SurfaceCreateInfoKHR`. To enable compile your code you have to define `VK_USE_PLATFORM_WIN32_KHR`.

```cpp
#if defined(_WIN32)
VkWin32SurfaceCreateInfoKHR OurWindow::getSurfaceInfo() {
    auto wid = winId();
    auto* platformInterface = QGuiApplication::platformNativeInterface();
    auto* handle = platformInterface->nativeResourceForWindow("handle", this);
    auto* hwnd = static_cast<HWND>(handle);
    VkWin32SurfaceCreateInfoKHR surfaceInfo{};
    surfaceInfo.hwnd(hwnd);
    surfaceInfo.hinstance(GetModuleHandle(nullptr));
    return surfaceInfo;
}
#endif
```

In `getSurfaceInfo`，we get the `hwnd` of  `OurWindow`, we need to use `hwnd` to make `VkSurfaceKHR` in `vkCreateWin32SurfaceKHR` .

```cpp
vkCreateWin32SurfaceKHR(instance, &surfaceInfo, nullptr, &surface)
```

To be continued.