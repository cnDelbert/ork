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

`.pc` files should be found in env `PKG_CONFIG_PATH`.

[Chocolatey](https://chocolatey.org/) provides the `pkg-config` command on Windows.

- Glew
- GLU(Provided by OS).
- OpenGL(Provided by OS).
- GLFW.
- FreeGLUT.
- AntTweakBar.
- pThreads.
- TIFF.

#### Glew

Glew's homepage is [Glew](http://glew.sourceforge.net/).

For VS2017 or VS2019, [Perlmint](https://github.com/Perlmint/glew-cmake) one should be preferred.

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

#### GLFW3

GLFW3 can be download from [the GLFW official site](https://www.glfw.org/).

#### Freeglut

Glut has been deprecated. Use freeglut instead.

Freeglut can be get from [HERE](http://freeglut.sourceforge.net/index.php#download) or unofficial repo on [github](https://github.com/dcnieho/FreeGLUT).

#### AntTweakBar

AntTweakBar should use the version modified by me in this [repo](https://github.com/cnDelbert/AntTweakBar).

#### pThreads

Pthreads-win32 currently implements a large subset of the POSIX standard threads related API. The latest version is 2.9.1(2012-05-27).

The source tree and precompiled .DLL, .LIB and necessary header files are included in the zip file named "pthread-w32-v-v-v-release.zip" at:
<ftp://sourceware.org/pub/pthreads-win32>.

Unzip the zip file and set the include dirs and lib dirs inside `Pre-built.2` folder. 

Add the following line to the top of header file `pthreads.h` to avoid `timespec` re-definition error:

```c++
#define HAVE_STRUCT_TIMESPEC 1
```

#### TIFF

Tiff is library and tools for TIFF images.

The Master HTTP Site http://www.remotesensing.org/libtiff has been deprecated.

The latest version of `libtiff` on site <http://www.libtiff.org/> is v3.6.1, while the version of [Tiff for Windows](http://gnuwin32.sourceforge.net/packages/tiff.htm) is v3.8.2.

The GnuWin32 distribution comes in two versions. The ordinary version uses the standard Unix equivalents, such as `fopen` and `read`, for input and output, the other version ([Tiff-win32](http://gnuwin32.sourceforge.net/packages/tiff-win32.htm)) uses the Win32 API functions, such as CreateFile and ReadFile, for input and output. The ordinary version ([Tiff](http://gnuwin32.sourceforge.net/packages/tiff.htm)) is more suitable for porting Unix programs, the Win32-API version is more suitable for writing Win32 programs.

When we clicked the Mirror Download Site <http://libtiff.maptools.org/dl/>, a redirection to <http://download.osgeo.org/libtiff> is performed. The latest version of libtiff can be found in the mirror site is v4.3.0rc1 which released on 2021-Apr-16. 

It seems that the [SimpleSystems](http://www.simplesystems.org/) hold the latest libtiff: <http://www.simplesystems.org/libtiff/>.

Libtiff v3.6.1 does not have a CMakeLists.txt, we use v4.3.0 instead.

## LF contribution

I am playing with integrating Proland in a project, and Proland runs on Ork. Since Ork does not allways build and run out of the box, the are some modifications to be done.

