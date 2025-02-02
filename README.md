# Ork

[Ork home page](http://ork.gforge.inria.fr/)

## Introduction

Ork, for OpenGL rendering kernel, provides a C++ API on top of OpenGL, which greatly simplifies the development of 3D applications.

## Example 

Suppose that you want to draw a mesh in an offscreen framebuffer, with a program that uses a texture. Assuming that these objects are already created, with the OpenGL API you need something like this:

```C++
    glUseProgram(myProgram);
    glActiveTexture(GL_TEXTURE0 + myUnit);
    glBindTexture(GL_TEXTURE_2D, myTexture);
    glUniform1i(glGetUniformLocation(myProgram, "mySampler"), myUnit);
    glBindBuffer(GL_ARRAY_BUFFER, myVBO);
    glVertexAttribPointer(0, 4, GL_FLOAT, false, 16, 0);
    glEnableVertexAttribArray(0);
    glBindFramebuffer(GL_FRAMEBUFFER, myFramebuffer);
    glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);
```

With the Ork API you simply need two steps (and the first one does not need to be repeated before each draw, unless you want a different texture for each draw):

```C++
    myProgram->getUniformSampler("mySampler")->set(myTexture);
    myFramebuffer->draw(myProgram, *myMesh);
```

## Requirements

> Notes:
>
> When compiling with CMake, a `-DCMAKE_INSTALL_PREFIX=` option is recommended. For spaces may cause include errors or NOTFOUND errors, install prefix should be a path without spaces.

> 提示：
> 
> 使用CMake进行编译时，建议使用`-DCMAKE_INSTALL_PREFIX=`指定安装路径。安装路径中存在空格可能导致错误，建议指定不含空格的安装路径。

`.pc` files should be found in env `PKG_CONFIG_PATH`.

[Chocolatey](https://chocolatey.org/) provides the `pkg-config` command on Windows.

通过 [Chocolatey](https://chocolatey.org/) 包管理器安装Windows版本的 `pkg-config` 命令。

CMake命令会调用 `pkg-config` ，在 `PKG_CONFIG_PATH` 环境变量中查找 `.pc` 文件，自动生成 `_INCLUDE_DIRS` 和 `_LIBRARY_DIRS` 的CMake变量。

所有代码编译时使用同一的 `Win32` 或 `X64` 配置。

Libraries required for `ork` is listed as follows:

- Glew
- GLU (Provided by OS).
- OpenGL (Provided by OS).
- GLFW.
- FreeGLUT.
- AntTweakBar (Required by **proland** project).
- pThreads.
- TIFF (Required by **proland** project).

#### Glew

Glew's homepage is [Glew](http://glew.sourceforge.net/).

For VS2017 or VS2019, the [Perlmint](https://github.com/Perlmint/glew-cmake) fork should be preferred.

A command line example to generate Visual Studio project is showing as follows:

```batch
cmake ./cmake -Bvs16 -DCMAKE_INSTALL_PREFIX=bin/ -G"Visual Studio 16 2019"
```

> Glu is required by Glew.
>
> Glu's source can be get from [MESA](https://gitlab.freedesktop.org/mesa/glu).
>
> For the `GLU32` lib has been provided by Windows, we should comment the `requireslib` parameter in CMakeLists.txt.
>
> ```cmake
> # set (requireslib glu)
> ```

使用 [Perlmint](https://github.com/Perlmint/glew-cmake) 的Glew fork以使用CMake进行编译。同时生成`.pc`文件以便Proland通过`pkg-config`命令引用。

#### GLFW3

GLFW3 can be download from [the GLFW official site](https://www.glfw.org/).

下载后使用CMake进行编译，通过`pkg-config`命令引用include和lib路径。

#### Freeglut

Glut has been deprecated. Use freeglut instead.

Freeglut can be get from [HERE](http://freeglut.sourceforge.net/index.php#download) or unofficial repo on [github](https://github.com/dcnieho/FreeGLUT).

建议通过GitHub下载后，使用CMake进行编译，通过`pkg-config`命令引用include和lib路径。

#### AntTweakBar

AntTweakBar is required by **proland** project

AntTweakBar should use the version modified by me in this [repo](https://github.com/cnDelbert/AntTweakBar).

AntTweakBar的pre-build二进制中编译了使用 `texture2D` 的shader，会导致demo编译时崩溃。下载后打开 `AntTweakBar_VS2012.sln` 使用Visual Studio进行编译即可。

#### pThreads

Pthreads-win32 currently implements a large subset of the POSIX standard threads related API. The latest version is 2.9.1(2012-05-27).

The source tree and precompiled .DLL, .LIB and necessary header files are included in the zip file named "pthread-w32-v-v-v-release.zip" at:
<ftp://sourceware.org/pub/pthreads-win32>.

Unzip the zip file and set the include dirs and lib dirs inside `Pre-built.2` folder. 

Add the following line to the top of header file `pthreads.h` to avoid `timespec` re-definition error:

```c++
#define HAVE_STRUCT_TIMESPEC 1
```

根据以下选项使用相应的pThreads库：

```text
In general:
	pthread[VG]{SE,CE,C}[c].dll
	pthread[VG]{SE,CE,C}[c].lib

where:
	[VG] indicates the compiler
	V	- MS VC, or
	G	- GNU C

	{SE,CE,C} indicates the exception handling scheme
	SE	- Structured EH, or
	CE	- C++ EH, or
	C	- no exceptions - uses setjmp/longjmp

	c	- DLL compatibility number indicating ABI and API
		  compatibility with applications built against
		  a snapshot with the same compatibility number.
		  See 'Version numbering' below.
```

在 CMake 指定安装路径时，include路径指定 `Pre-built.2` 路径下的 `include` 文件夹；lib路径指定 `lib` 下的 `x86` （Win32）或 `x64` (X64)路径。

#### TIFF

LibTIFF is required by **proland** project

Tiff is library and tools for TIFF images.

The Master HTTP Site http://www.remotesensing.org/libtiff has been deprecated.

The latest version of `libtiff` on site <http://www.libtiff.org/> is v3.6.1, while the version of [Tiff for Windows](http://gnuwin32.sourceforge.net/packages/tiff.htm) is v3.8.2.

The GnuWin32 distribution comes in two versions. The ordinary version uses the standard Unix equivalents, such as `fopen` and `read`, for input and output, the other version ([Tiff-win32](http://gnuwin32.sourceforge.net/packages/tiff-win32.htm)) uses the Win32 API functions, such as CreateFile and ReadFile, for input and output. The ordinary version ([Tiff](http://gnuwin32.sourceforge.net/packages/tiff.htm)) is more suitable for porting Unix programs, the Win32-API version is more suitable for writing Win32 programs.

When we clicked the Mirror Download Site <http://libtiff.maptools.org/dl/>, a redirection to <http://download.osgeo.org/libtiff> is performed. The latest version of libtiff can be found in the mirror site is v4.3.0rc1 which released on 2021-Apr-16. 

It seems that the [SimpleSystems](http://www.simplesystems.org/) hold the latest libtiff: <http://www.simplesystems.org/libtiff/>.

Libtiff v3.6.1 does not have a CMakeLists.txt, we use v4.3.0 instead.

Zlib1 and JPEG62 are recomended. Or, TIFF warning would show up.

> TBD: TIFF编译建议指定使用 zlib 和 JPEG 库，否则在运行 demo 时会有相关 Warning 并引起渲染错误。

## LF contribution

I am playing with integrating Proland in a project, and Proland runs on Ork. Since Ork does not allways build and run out of the box, the are some modifications to be done.

