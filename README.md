# essim

![build](https://github.com/facebookresearch/essim/actions/workflows/cmake.yml/badge.svg)

eSSIM[1] is an evolution of SSIM which improves correlation with subjective
scores by employing Minkowski pooling and reduces complexity by employing box
filters and striding.

This is similar to the original algorithm implemented at
https://github.com/utlive/enhanced_ssim but this project is designed for use at
scale and uses optimized floating point and integer SIMD instructions.

In addition this project replaced the CoV pooling defined in [1] with Minkowski
pooling due to edge cases with uncorrelated inputs causing abnormal scores. In
testing using Minkowski 3 (or 4) pooling preserves the subjective correlations
of CoV pooling with more stable behavior across a wide range of inputs.

In addition to the Minkowski pooled eSSIM score this library can also calculate
the arithmetic mean pooled SSIM score for reference.

[1] A. K. Venkataramanan, C. Wu, A. C. Bovik, I. Katsavounidis and Z. Shahid, "A
Hitchhiker’s Guide to Structural Similarity," in IEEE Access, vol. 9, pp.
28872-28896, 2021, doi: 10.1109/ACCESS.2021.3056504
https://ieeexplore.ieee.org/document/9344648

## Building

### Building eSSIM

0. Set up build directory
  ```
  mkdir build
  cd build
  ```

1. Enter build directory & configure with CMake
  ```
  cmake .. -G Ninja -DBUILD_TESTS=OFF  # disable tests for this example
  cmake --build . --parallel
  ```

2. Install eSSIM library
  ```
  cd ..  # exit build dir
  cmake --install build
  ```

3. Copy relevant headers
  ```
  sudo cp essim/inc/*.h /usr/local/include/essim
  sudo cp essim/essim.h /usr/local/include
  ```

### Building FFmpeg

0. Clone FFmpeg source
  ```
  git clone https://code.ffmpeg.org/FFmpeg/FFmpeg.git
  cd FFmpeg
  git checkout n6.0
  ```

1. Apply patch
  ```
  git apply ~/Projects/essim/ffmpeg/vf_essim.ffmpeg-6.0.patch
  ```

0. Run `configure` & build
  ```
  PKG_CONFIG_PATH=/usr/local/lib/pkgconfig ./configure --enable-libessim
  make -j$(nproc)
  ```

## Build x86 on ARM based MacOS

To build x86 code instead of ARM on MacOS machines with M1 or later ARM CPUs you
can use the `CMAKE_OSX_ARCHITECTURES` flag

```sh
cmake .. -DCMAKE_OSX_ARCHITECTURES=x86_64
```

## FFmpeg Integration

To build ffmpeg with eSSIM support, apply the patch to an ffmpeg 6.0 checkout
and run `./configure` with `--enable-libessim`.

## License

ESSIM is licensed the BSD-3 license, see LICENSE file in the root of this source
tree for details.

The ffmpeg filter `vf_essim.c` is licensed under the LGPL license, same as
vf_ssim.c in FFmpeg itself, upon which it is based. The FFmpeg patch is under
this same license.
