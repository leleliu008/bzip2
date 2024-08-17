# bzip2
build bzip2 with CMake

## build and install all components
```
cmake -S .   -B build.d -DCMAKE_INSTALL_PREFIX=./output
cmake --build   build.d
cmake --install build.d
```

## build and install bin+man
```
cmake -S .   -B build.d -DCMAKE_INSTALL_PREFIX=./output
cmake --build   build.d --target bzip2 bzip2recover
cmake --install build.d --component bin
cmake --install build.d --component man
```

## build and install lib+dev
```
cmake -S .   -B build.d -DCMAKE_INSTALL_PREFIX=./output
cmake --build   build.d --target bz2-shared bz2-static
cmake --install build.d --component lib
cmake --install build.d --component dev
```
