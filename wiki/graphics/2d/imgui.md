---
title: ImGui
---
An **alternative GUI for libGDX** is [Dear ImGui](https://github.com/ocornut/imgui), a bloat-free graphical user interface library in C++. It outputs optimized vertex buffers that you can render anytime in your 3D-pipeline enabled application. It is fast, portable, renderer agnostic and self-contained (few dependencies).

_**There are a couple of different JVM bindings you can choose from: [kotlin-graphics/imgui](https://github.com/kotlin-graphics/imgui), [ice1000/jimgui](https://github.com/ice1000/jimgui) and [SpaiR/imgui-java](https://github.com/SpaiR/imgui-java).**_

# General Information

Dear ImGui is designed to enable fast iteration and empower programmers to create content creation tools and visualization/ debug tools (as opposed to UI for the average end-user). It favors simplicity and productivity toward this goal, and thus lacks certain features normally found in more high-level libraries.

_A common misunderstanding is to think that immediate mode gui == immediate mode rendering, which usually implies hammering your driver/GPU with a bunch of inefficient draw calls and state changes, as the gui functions are called by the user. This is NOT what Dear ImGui does. Dear ImGui outputs vertex buffers and a small list of draw calls batches. It never touches your GPU directly. The draw call batches are decently optimal and you can render them later, in your app or even remotely._

Dear ImGui is particularly suited to integration in realtime 3D applications, fullscreen applications, embedded applications, games, or any applications on consoles platforms where operating system features are non-standard.

This is an example demonstrating what ImGui is capable of:
![Sample](/assets/wiki/images/imgui1.png)

<br/>

# Option 1: [kotlin-graphics' Bindings](https://github.com/kotlin-graphics/imgui)

There is an elaborate wiki entry over in the Kotlin-graphics's repo, detailing how ImGui can be used together with libGDX: [https://github.com/kotlin-graphics/imgui/wiki/Using-libGDX](https://github.com/kotlin-graphics/imgui/wiki/Using-libGDX)

These are some very simple examples, how its usage may look like: 

### Kotlin
```kotlin
var f = 0f
with(ImGui) {
    text("Hello, world %d", 123)
    button("OK"){
        // react
    }
    inputText("string", buf)
    sliderFloat("float", ::f, 0f, 1f)
}
```

### Java
```java
ImGui imgui = ImGui.INSTANCE;
float[] f = {0f};
imgui.text("Hello, world %d", 123);
if(imgui.button("OK")) {
    // react
}
imgui.inputText("string", buf);
imgui.sliderFloat("float", f, 0f, 1f);
```

![screenshot of sample code alongside its output with ImGui](/assets/wiki/images/imgui1.png)

ImGui supports also other languages, such as Japanese, initiliazed [here](https://github.com/pakoito/imgui/blob/master/src/test/kotlin/imgui/gl/test%20lwjgl.kt#L79) as:

```kotlin
IO.fonts.addFontFromFileTTF("extraFonts/ArialUni.ttf", 18f, glyphRanges = IO.fonts.glyphRangesJapanese)!!
```

# Option 2: [SpaiR's Bindings](https://github.com/SpaiR/imgui-java)
### Required Dependencies for the LWJGL 3 Subproject
```groovy
api "io.github.spair:imgui-java-binding:<version>"
api "io.github.spair:imgui-java-lwjgl3:<version>"
api "io.github.spair:imgui-java-natives-linux:<version>"
api "io.github.spair:imgui-java-natives-macos:<version>"
api "io.github.spair:imgui-java-natives-windows:<version>"
```

### Example Minimal Usage
The following instructions detail how ImGui can be used on top of a 3D scene in libGDX.

1. Initialize ImGui at the beginning of the program:

   ```java
   ImGuiImplGlfw imGuiGlfw = new ImGuiImplGlfw();
   ImGuiImplGl3 imGuiGl3 = new ImGuiImplGl3();
   ```

2. In `create()`:

   ```java
   // create 3D scene
   GLFWErrorCallback.createPrint(System.err).set();
   if (!glfwInit())
   {
      throw new IllegalStateException("Unable to initialize GLFW");
   }
   ImGui.createContext();
   final ImGuiIO io = ImGui.getIO();
   io.setIniFilename(null);
   ImGuiTools.setupFonts(io);
   
   windowHandle = ((Lwjgl3Graphics) Gdx.graphics).getWindow().getWindowHandle();
   
   imGuiGlfw.init(windowHandle, true);
   imGuiGl3.init(glslVersion);
   ```

3. In `render()`:

   ```java
   // render 3D scene
   imGuiGlfw.newFrame();
   ImGui.newFrame();
   ImGui.button("I'm a Button!");
   ImGui.render();
   imGuiGl3.renderDrawData(ImGui.getDrawData());
   glfwSwapBuffers(windowHandle);
   glfwPollEvents();
   ```

4. In `dispose()`:

   ```java
   imGuiGl3.dispose();
   imGuiGlfw.dispose();
   ImGui.destroyContext();
   ```

5. The result:

   <img src="/assets/wiki/images/imgui2.png" alt="Screenshot of ImGui in libGDX" width="500"/>
