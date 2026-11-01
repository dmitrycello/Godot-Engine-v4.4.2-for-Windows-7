# Godot Engine v4.4.2 for Windows 7.1
The Godot Engine versions 4.5+ have officially dropped support for Windows 7. They use the latest libraries which are not available in such OS. Making this version work under Windows 7 demands a huge workaround. So, the only affordable solution is the version 4.4. Under Windows 7 the official executables only works up to version 4.0. The official releases of versions 4.1-4.4 are crashing under this OS with access violation error 0xc0000005.

However, the source code of this version is fully compatible with Windows 7. After analyzing the issue, I managed to isolate the crash in Godot source 4.4. The memory access violation exception is thrown in the line **670** of the source file **drivers\gles3\shader_gles3.cpp**. It looks like the developers have missed the cases where the _zero program id_ is passed to **glGetProgramiv** function, which leads to a crash. To patch it, between the lines **668-669** in the original file insert the following:
```
/* BEGIN PATCH */
if (specialization->id == 0) {
    f->store_32(0);
    continue;
}
/* END PATCH */
```
The patched file is included in the repository. If you find it difficult to compile from Godot 4.4 source, please feel free to download the executables compiled with **MinGW** from this repository. Enjoy!
