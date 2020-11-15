
# How to `opencv` with support of `opencl`

Content:
  - installation
  - run basic examples
  - pointer to opencl optimisation (docs & code)
  - run benchmark (`opencv_perf_*` examples) with opencl enabled (on GPU GeForce GTX 560 Ti)
    

## Install (from sources)
references:
  - https://docs.opencv.org/master/d0/d3d/tutorial_general_install.html
  - from: https://docs.opencv.org/master/df/d65/tutorial_table_of_content_introduction.html
  - from: https://docs.opencv.org/master/d9/df8/tutorial_root.html
  - from: https://opencv.org/

### Get sources

Generic commands:
```
git clone https://github.com/opencv/opencv
git -C opencv checkout <some-tag>
# optionally
git clone https://github.com/opencv/opencv_contrib
git -C opencv_contrib checkout <same-tag-as-opencv>
# optionally
git clone https://github.com/opencv/opencv_extra
git -C opencv_extra checkout <same-tag-as-opencv>
```

The commands used for this "Howto"
```
git clone https://github.com/opencv/opencv
git -C opencv checkout 4.5.0
git clone https://github.com/opencv/opencv_extra
git -C opencv_extra checkout 4.5.0
```

### Preliminary 'make'
Prepare the build in a dedicated `build` directory
```
cd opencv
mkdir build
cd build
cmake ..
```

### Configure
In `build` directory, type the command below to check or edit the configuration
```
ccmake ..
```
It will display a nice user friendly menu :
```
                                                    Page 3 of 5
 Eigen3_DIR                       Eigen3_DIR-NOTFOUND
 GENERATE_ABI_DESCRIPTOR          OFF
 INSTALL_CREATE_DISTRIB           OFF
 INSTALL_C_EXAMPLES               OFF
 INSTALL_PYTHON_EXAMPLES          OFF
 INSTALL_TESTS                    OFF
 INSTALL_TO_MANGLED_PATHS         OFF
 LAPACKE_INCLUDE_DIR              LAPACKE_INCLUDE_DIR-NOTFOUND
 LAPACK_CBLAS_H
 LAPACK_IMPL                      Unknown
 LAPACK_INCLUDE_DIR
 LAPACK_LAPACKE_H
 LAPACK_LIBRARIES
 MKL_INCLUDE_DIRS                 MKL_ROOT_DIR-NOTFOUND/include
 MKL_LAPACKE_INCLUDE_DIR          MKL_LAPACKE_INCLUDE_DIR-NOTFOUND
 MKL_ROOT_DIR                     MKL_ROOT_DIR-NOTFOUND
 MKL_WITH_OPENMP                  OFF
 MKL_WITH_TBB                     OFF
 OPENCL_FOUND                     ON
 OPENCV_CONFIG_FILE_INCLUDE_DIR   /home/kapfer/Project/opencv/build
 OPENCV_DNN_CUDA                  OFF
 OPENCV_DNN_OPENCL                ON
 OPENCV_DOWNLOAD_PATH             /home/kapfer/Project/opencv/.cache
 OPENCV_DUMP_HOOKS_FLOW           OFF
 OPENCV_ENABLE_ALLOCATOR_STATS    ON
 OPENCV_ENABLE_ATOMIC_LONG_LONG   ON
 OPENCV_ENABLE_MEMALIGN           ON
 OPENCV_ENABLE_MEMORY_SANITIZER   OFF
 OPENCV_ENABLE_NONFREE            OFF
 OPENCV_EXTRA_MODULES_PATH

Press [enter] to edit option Press [d] to delete an entry                                                               CMake Version 3.13.4
Press [c] to configure
Press [h] for help           Press [q] to quit without generating
Press [t] to toggle advanced mode (Currently Off)
```

You probably need to install `ccmake` support:
  - On debian 10: `sudo apt install cmake-curses-gui`

### Build
Once you have checked your configurationn, let's compile the project, possibly in a parallel build
In `build` directory, do:
    ```
    make -j 8
    ```

### Install
Now, you can install the lib. You may use `DESTDIR` to change install dir at install time.
So, to install locally:
```
   cd build
   make DESTDIR=~/.local install
```
To keep the configured installation dir (`/usr/local` by default):
```
   cd build
   sudo make install
```



## Run
### pre installed examples

After installation, the following examples are availalble on your install path (here it is the `~/.local`)
```
/home/kapfer/.local/usr/local/bin:
  total used in directory 292K available 723697076
  drwxr-xr-x 6 kapfer kapfer 4,0K nov.  15 10:17 ..
  -rwxr-xr-x 1 kapfer kapfer  35K nov.  15 11:32 opencv_annotation
  -rwxr-xr-x 1 kapfer kapfer 146K nov.  15 11:32 opencv_interactive-calibration
  -rwxr-xr-x 1 kapfer kapfer  35K nov.  15 11:32 opencv_version
  -rwxr-xr-x 1 kapfer kapfer  57K nov.  15 11:32 opencv_visualisation
  -rwxr-xr-x 1 kapfer kapfer  674 nov.  15 08:59 setup_vars_opencv4.sh
```

To run it, set the `LD_LIBRARY_PATH` in case of local installation
```
$ cd /home/kapfer/.local/usr/local/bin
$ LD_LIBRARY_PATH=~/.local/usr/local/lib ./opencv_annotation
```
Or
```
$ export PATH=$PATH:~/.local/usr/local/bin
$ export LD_LIBRARY_PATH=~/.local/usr/local/lib`
$ opencv_annotation
```


### basic example (cmake)

Here after is the commands to build and run the example of cmake.
Note that in our case the opencl install dir is `~/.local`)

```
cd opencv/samples/cpp/example_cmake
mkdir build
cd build
CMAKE_PREFIX_PATH=~/.local/usr/local/lib/cmake/opencv4/ cmake --debug-find ..
make
./opencv_example
```

The option `--debug-find` trigger the `find_package` debug mode. 
This may be usefull to help to set `CMAKE_PREFIX_PATH` value on `OpenCVConfig.cmake` file directory

### other examples

First, check that you have enabled the build of example

In `build` directory, type the command below to check or edit the configuration `ccmake ..`
Check the line `BUILD_EXAMPLES                   ON`


If not, select the line `BUILD_EXAMPLES   OFF`, type "`ENTER`" to enable it, active this configuration (type "`c`") and redo build (`make -J 8`) and installation (`make DESTDIR=~/.local install` or `sudo make install`)

All example are generated here: `opencv/build/bin`

There are up to 260 examples:
```
  /home/kapfer/Project/opencv/build/bin:
  total used in directory 85M available 723697080
  drwxr-xr-x 19 kapfer kapfer  4,0K nov.  15 11:39 ..
  -rwxr-xr-x  1 kapfer kapfer   61K nov.  15 11:33 example_cpp_3calibration
  -rwxr-xr-x  1 kapfer kapfer   29K nov.  15 11:33 example_cpp_application_trace
  -rwxr-xr-x  1 kapfer kapfer   52K nov.  15 11:33 example_cpp_asift
  -rwxr-xr-x  1 kapfer kapfer   29K nov.  15 11:33 example_cpp_bgfg_segm
  -rwxr-xr-x  1 kapfer kapfer   85K nov.  15 11:33 example_cpp_calibration
  -rwxr-xr-x  1 kapfer kapfer   30K nov.  15 11:33 example_cpp_camshiftdemo
  -rwxr-xr-x  1 kapfer kapfer   32K nov.  15 11:33 example_cpp_cloning_demo
  -rwxr-xr-x  1 kapfer kapfer   42K nov.  15 11:33 example_cpp_cloning_gui
  -rwxr-xr-x  1 kapfer kapfer   17K nov.  15 11:33 example_cpp_CMakeCXXCompilerId
  -rwxr-xr-x  1 kapfer kapfer   28K nov.  15 11:33 example_cpp_connected_components
  -rwxr-xr-x  1 kapfer kapfer   28K nov.  15 11:33 example_cpp_contours2
  -rwxr-xr-x  1 kapfer kapfer   23K nov.  15 11:33 example_cpp_convexhull
  -rwxr-xr-x  1 kapfer kapfer   28K nov.  15 11:33 example_cpp_cout_mat
  ...
```

## OpenCL optimisations
references:
  - https://github.com/opencv/opencv/wiki/OpenCL-optimizations

### Have a look on sources

That's the location of `opencl` sources directories  (result of `find . -type d | grep opencl`)
```
./samples/opencl
./modules/photo/src/opencl
./modules/photo/perf/opencl
./modules/calib3d/src/opencl
./modules/calib3d/test/opencl
./modules/calib3d/perf/opencl
./modules/imgproc/src/opencl
./modules/imgproc/perf/opencl
./modules/core/src/opencl
./modules/core/src/opencl/runtime
./modules/core/perf/opencl
./modules/core/include/opencv2/core/opencl
./modules/core/include/opencv2/core/opencl/runtime
./modules/dnn/src/opencl
./modules/objdetect/src/opencl
./modules/objdetect/test/opencl
./modules/objdetect/perf/opencl
./modules/features2d/src/opencl
./modules/features2d/perf/opencl
./modules/video/src/opencl
./modules/video/perf/opencl
./modules/stitching/src/opencl
./modules/stitching/perf/opencl
```

Here is the content of `opencv/modules/core/src/opencl`

```
  total used in directory 252K available 724346892
  drwxr-xr-x 5 kapfer kapfer 4,0K nov.  15 08:53 ..
  -rw-r--r-- 1 kapfer kapfer  17K nov.  15 08:53 arithm.cl
  -rw-r--r-- 1 kapfer kapfer 3,4K nov.  15 08:53 convert.cl
  -rw-r--r-- 1 kapfer kapfer 4,9K nov.  15 08:53 copymakeborder.cl
  -rw-r--r-- 1 kapfer kapfer 5,2K nov.  15 08:53 copyset.cl
  -rw-r--r-- 1 kapfer kapfer 7,7K nov.  15 08:53 cvtclr_dx.cl
  -rw-r--r-- 1 kapfer kapfer  28K nov.  15 08:53 fft.cl
  -rw-r--r-- 1 kapfer kapfer 6,9K nov.  15 08:53 flip.cl
  -rw-r--r-- 1 kapfer kapfer 3,0K nov.  15 08:53 gemm.cl
  -rw-r--r-- 1 kapfer kapfer 3,2K nov.  15 08:53 halfconvert.cl
  -rw-r--r-- 1 kapfer kapfer 5,3K nov.  15 08:53 inrange.cl
  -rw-r--r-- 1 kapfer kapfer  51K nov.  15 08:53 intel_gemm.cl
  -rw-r--r-- 1 kapfer kapfer 6,3K nov.  15 08:53 lut.cl
  -rw-r--r-- 1 kapfer kapfer 3,8K nov.  15 08:53 meanstddev.cl
  -rw-r--r-- 1 kapfer kapfer 9,5K nov.  15 08:53 minmaxloc.cl
  -rw-r--r-- 1 kapfer kapfer 3,3K nov.  15 08:53 mixchannels.cl
  -rw-r--r-- 1 kapfer kapfer 3,7K nov.  15 08:53 mulspectrums.cl
  -rw-r--r-- 1 kapfer kapfer 2,3K nov.  15 08:53 normalize.cl
  -rw-r--r-- 1 kapfer kapfer 7,7K nov.  15 08:53 reduce2.cl
  -rw-r--r-- 1 kapfer kapfer  20K nov.  15 08:53 reduce.cl
  -rw-r--r-- 1 kapfer kapfer 1,7K nov.  15 08:53 repeat.cl
  drwxr-xr-x 4 kapfer kapfer 4,0K nov.  15 08:53 runtime
  -rw-r--r-- 1 kapfer kapfer 3,9K nov.  15 08:53 set_identity.cl
  -rw-r--r-- 1 kapfer kapfer 4,4K nov.  15 08:53 split_merge.cl
  -rw-r--r-- 1 kapfer kapfer 5,2K nov.  15 08:53 transpose.cl
```

### Benchmark

Hereafter is the list of perf test:
```
kapfer@debian-desktop:~/Project/opencv/build/bin$ ls | grep perf
opencv_perf_calib3d
opencv_perf_core
opencv_perf_dnn
opencv_perf_features2d
opencv_perf_gapi
opencv_perf_imgcodecs
opencv_perf_imgproc
opencv_perf_objdetect
opencv_perf_photo
opencv_perf_stitching
opencv_perf_video
opencv_perf_videoio
```

To get it, follow the steps below:

#### check you configuration
First, you have to check that `opencl` is enabled in configuration thanks to
```
cd build
ccmake ..
```
and check the following configuration switch
```
BUILD_EXAMPLES                   ON
BUILD_PERF_TESTS                 ON
INSTALL_BIN_EXAMPLES             ON
OPENCL_FOUND                     ON
WITH_OPENCL                      ON

```

If you do not have `OPENCL_FOUND   ON`, check your `opencl` driver installation. 

The command `clinfo` should detect your device

```
kapfer@debian-desktop:~/Project/opencv_extra/testdata$ clinfo
Number of platforms                               2
  Platform Name                                   NVIDIA CUDA
  Platform Vendor                                 NVIDIA Corporation
  Platform Version                                OpenCL 1.2 CUDA 9.1.84
  Platform Profile                                FULL_PROFILE
  Platform Extensions                             cl_khr_global_int32_base_atomics cl_khr_global_int32_extended_atomics cl_khr_local_int32_base_atomics cl_khr_local_int32_extended_atomics cl_khr_fp64 cl_khr_byte_addressable_store cl_khr_icd cl_khr_gl_sharing cl_nv_compiler_options cl_nv_device_attribute_query cl_nv_pragma_unroll cl_nv_copy_opts cl_nv_create_buffer
  Platform Extensions function suffix             NV

  Platform Name                                   Clover
  Platform Vendor                                 Mesa
  Platform Version                                OpenCL 1.1 Mesa 18.3.6
  Platform Profile                                FULL_PROFILE
  Platform Extensions                             cl_khr_icd
  Platform Extensions function suffix             MESA

```

#### rebuild

In case of any change, redo the build
```
cd build && make
```

#### get data
To run performance test, you need data contains in below repo:
```
git clone https://github.com/opencv/opencv_extra
git -C opencv_extra checkout <same-tag-as-opencv>
```

#### set execute environment
In case of local install, just add the following steps
  - set lib path:  `export LD_LIBRARY_PATH=~/.local/usr/local/lib`
  - set bin path:  `export PATH=$PATH:~/Project/opencv/build/bin/`

#### GO !
Now, you can go to `opencv_extra/testdata` and execute for perf test.
Hereafter is the result of `opencv_perf_photo`

```bash
$ cd opencv_extra/testdata
$ opencv_perf_photo

Time compensation is 0
TEST: Skip tests with tags: 'mem_6gb', 'verylong'
CTEST_FULL_OUTPUT
OpenCV version: 4.5.0
OpenCV VCS version: 4.5.0-dirty
Build type: Release
Compiler: /usr/bin/c++  (ver 8.3.0)
Parallel framework: pthreads (nthreads=8)
CPU features: SSE SSE2 SSE3 *SSE4.1 *SSE4.2 *FP16? *AVX *AVX2? *AVX512-SKX?
Intel(R) IPP version: ippIP SSE4.2 (y8) 2020.0.0 Gold (-) Oct 19 2019
OpenCL Platforms:
    NVIDIA CUDA
        dGPU: GeForce GTX 560 Ti (OpenCL 1.1 CUDA)
    Clover
Current OpenCL device:
    Type = dGPU
    Name = GeForce GTX 560 Ti
    Version = OpenCL 1.1 CUDA
    Driver version = 390.138
    Address bits = 64
    Compute units = 11
    Max work group size = 1024
    Local memory size = 48 KB
    Max memory allocation size = 304 MB 784 KB
    Double support = Yes
    Host unified memory = No
    Device extensions:
        cl_khr_global_int32_base_atomics
        cl_khr_global_int32_extended_atomics
        cl_khr_local_int32_base_atomics
        cl_khr_local_int32_extended_atomics
        cl_khr_fp64
        cl_khr_byte_addressable_store
        cl_khr_icd
        cl_khr_gl_sharing
        cl_nv_compiler_options
        cl_nv_device_attribute_query
        cl_nv_pragma_unroll
        cl_nv_copy_opts
        cl_nv_create_buffer
    Has AMD Blas = No
    Has AMD Fft = No
    Preferred vector width char = 1
    Preferred vector width short = 1
    Preferred vector width int = 1
    Preferred vector width long = 1
    Preferred vector width float = 1
    Preferred vector width double = 1
[==========] Running 8 tests from 3 test cases.
[----------] Global test environment set-up.
[----------] 1 test from OCL_Photo_DenoisingGrayscale
[ RUN      ] OCL_Photo_DenoisingGrayscale.DenoisingGrayscale
[ PERFSTAT ]    (samples=10   mean=80.34   median=79.99   min=79.46   stddev=0.92 (1.1%))
[       OK ] OCL_Photo_DenoisingGrayscale.DenoisingGrayscale (812 ms)
[----------] 1 test from OCL_Photo_DenoisingGrayscale (812 ms total)

[----------] 1 test from OCL_Photo_DenoisingColored
[ RUN      ] OCL_Photo_DenoisingColored.DenoisingColored
[ PERFSTAT ]    (samples=10   mean=173.82   median=159.91   min=159.14   stddev=43.08 (24.8%))
[       OK ] OCL_Photo_DenoisingColored.DenoisingColored (1747 ms)
[----------] 1 test from OCL_Photo_DenoisingColored (1747 ms total)

[----------] 6 tests from InpaintArea_InpaintingMethod_inpaint
[ RUN      ] InpaintArea_InpaintingMethod_inpaint.inpaint/0, where GetParam() = (24x24, INPAINT_NS)
[ PERFSTAT ]    (samples=10   mean=12.26   median=12.16   min=12.13   stddev=0.31 (2.5%))
[       OK ] InpaintArea_InpaintingMethod_inpaint.inpaint/0 (133 ms)
[ RUN      ] InpaintArea_InpaintingMethod_inpaint.inpaint/1, where GetParam() = (24x24, INPAINT_TELEA)
[ PERFSTAT ]    (samples=10   mean=12.31   median=12.28   min=12.24   stddev=0.06 (0.5%))
[       OK ] InpaintArea_InpaintingMethod_inpaint.inpaint/1 (133 ms)
[ RUN      ] InpaintArea_InpaintingMethod_inpaint.inpaint/2, where GetParam() = (32x32, INPAINT_NS)
[ PERFSTAT ]    (samples=10   mean=19.56   median=19.47   min=19.40   stddev=0.16 (0.8%))
[       OK ] InpaintArea_InpaintingMethod_inpaint.inpaint/2 (205 ms)
[ RUN      ] InpaintArea_InpaintingMethod_inpaint.inpaint/3, where GetParam() = (32x32, INPAINT_TELEA)
[ PERFSTAT ]    (samples=10   mean=18.58   median=18.59   min=18.54   stddev=0.03 (0.1%))
[       OK ] InpaintArea_InpaintingMethod_inpaint.inpaint/3 (195 ms)
[ RUN      ] InpaintArea_InpaintingMethod_inpaint.inpaint/4, where GetParam() = (64x64, INPAINT_NS)
[ PERFSTAT ]    (samples=10   mean=66.83   median=66.61   min=66.54   stddev=0.54 (0.8%))
[       OK ] InpaintArea_InpaintingMethod_inpaint.inpaint/4 (678 ms)
[ RUN      ] InpaintArea_InpaintingMethod_inpaint.inpaint/5, where GetParam() = (64x64, INPAINT_TELEA)
[ PERFSTAT ]    (samples=10   mean=59.89   median=59.66   min=59.54   stddev=0.52 (0.9%))
[       OK ] InpaintArea_InpaintingMethod_inpaint.inpaint/5 (609 ms)
[----------] 6 tests from InpaintArea_InpaintingMethod_inpaint (1953 ms total)

[----------] Global test environment tear-down
[==========] 8 tests from 3 test cases ran. (4512 ms total)
[  PASSED  ] 8 tests.

  YOU HAVE 2 DISABLED TESTS

```
