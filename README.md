# Godot-4.4.2-for-Windows-7.1
The Godot Engine versions 4.5+ have officially dropped support for Windows 7. They use the latest libraries which are not available in such OS. Making this version work under Windows 7 demands a huge workaround. So, the only affordable solution is version 4.4. The official release only works up to version 4.0. The official executables of versions 4.1-4.4 are crashing under Windows 7.1 with access violation error 0xc0000005.

After analyzing the issue, I managed to isolate the crash in Godot source 4.4. The memory access violation exception is thrown in the line **670** of the source file **drivers\gles3\shader_gles3.cpp**. It looks like the developers have missed the zero program id passed to **glGetProgramiv** function, which leads to a crash. To patch it, between the lines **668-669** in the original file insert the following:
```
/* BEGIN PATCH */
if (specialization->id == 0) {
    f->store_32(0);
    continue;
}
/* END PATCH */
```
If you find it difficult to compile from Godot 4.4 source, please feel free to download the executables compiled using **MinGW** using this repository.
