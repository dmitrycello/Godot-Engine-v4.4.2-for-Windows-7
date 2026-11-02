# Godot Engine v4.4.2.rc build for Windows 7
The Godot Engine versions 4.5+ have officially dropped support for Windows 7. They use the latest libraries which are not available in such OS. Making those versions work under Windows 7 demands a huge (and maybe pointless) workarounds. So, the only realistic solution is the version 4.4. Under Windows 7 the official executables only works up to version 4.0. The official releases of versions 4.1-4.4 are crashing under this OS with access violation error _0xC0000005_.

However, the source code of these versions is fully compatible with Windows 7. After analyzing the issue, I managed to isolate the crash in Godot source 4.4. The memory access violation exception is thrown in the line **670** of the source file **drivers\gles3\shader_gles3.cpp**. It looks like the developers have missed the cases where the _zero program id_ is passed to **glGetProgramiv** function, which leads to a crash. To patch it, insert the following block immediately after the line **668** of the original file:
```
/* BEGIN PATCH */
if (specialization->id == 0) {
    f->store_32(0);
    continue;
}
/* END PATCH */
```
_P.S. It is absolutely possible that the developers have fixed the issue by the time being. They are still showing some activity in 4.4 branch, but I was unable to report the issue through [the project page](https://github.com/godotengine/godot/tree/4.4), perhaps because it was not allowed._

The patched file is included in the repository for reference. If you find it difficult to compile from Godot 4.4 source, feel free to download all executables under Releases section, which contains six archives with ready-to-run application, as well as the source code used in the process.

### Compiler version used for x64 builds:
```
MinGW-W64 x86_64-msvcrt-posix-seh, built by Brecht Sanders, r2 16.2.0
```
### Commands used for x64 builds:
```
scons platform=windows target=editor arch=x86_64 production=yes d3d12=no engine_update_check=no target_win_version=0x0601 use_mingw=yes mingw_prefix=C:\mingw64
scons platform=windows target=template_debug arch=x86_64 production=yes d3d12=no engine_update_check=no target_win_version=0x0601 use_mingw=yes mingw_prefix=C:\mingw64
scons platform=windows target=template_release arch=x86_64 production=yes d3d12=no engine_update_check=no target_win_version=0x0601 use_mingw=yes mingw_prefix=C:\mingw64
```
### Compiler version used for x86 builds:
```
MinGW-W64 i686-msvcrt-posix-dwarf, built by Brecht Sanders, r3 16.2.0
```
### Commands used for x86 builds:
```
scons platform=windows target=editor arch=x86_32 production=yes d3d12=no engine_update_check=no target_win_version=0x0601 use_mingw=yes mingw_prefix=C:\mingw32
scons platform=windows target=template_debug arch=x86_32 production=yes d3d12=no engine_update_check=no target_win_version=0x0601 use_mingw=yes mingw_prefix=C:\mingw32
scons platform=windows target=template_release arch=x86_32 production=yes d3d12=no engine_update_check=no target_win_version=0x0601 use_mingw=yes mingw_prefix=C:\mingw32
```
Enjoy!
