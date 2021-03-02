# pyarrow-arm-whl

## System dependencies

### Native Raspberry Pi OS / Raspbian

```
sudo apt-get install \
     libatomic-ops-dev \
     libjemalloc-dev \
     libboost-dev \
     libboost-filesystem-dev \
     libboost-system-dev \
     libboost-regex-dev \
     autoconf \
     flex \
     bison \
     python3-dev
```

### Native Gentoo (Clang)

@todo

### Crosstool

@todo

## Workspace Setup

```
git clone https://github.com/apache/arrow.git
pushd arrow
export RELEASE_TAG=apache-arrow-3.0.0
git checkout "$RELEASE_TAG"
git submodule init
git submodule update
export PARQUET_TEST_DATA="${PWD}/cpp/submodules/parquet-testing/data"
export ARROW_TEST_DATA="${PWD}/testing/data"
popd

python3 -m venv pyarrow
source pyarrow/bin/activate
pip install -r arrow/python/requirements-build.txt \
     -r arrow/python/requirements-test.txt
mkdir dist
export ARROW_HOME=$(pwd)/dist
export LD_LIBRARY_PATH=$(pwd)/dist/lib:$LD_LIBRARY_PATH
export LDFLAGS=-latomic
```

## Arrow C++ libs

```
mkdir arrow/cpp/build
pushd arrow/cpp/build

cmake -DCMAKE_INSTALL_PREFIX=$ARROW_HOME \
      -DCMAKE_INSTALL_LIBDIR=lib \
      -DARROW_WITH_BZ2=ON \
      -DARROW_WITH_ZLIB=ON \
      -DARROW_WITH_ZSTD=ON \
      -DARROW_WITH_LZ4=ON \
      -DARROW_WITH_SNAPPY=ON \
      -DARROW_WITH_BROTLI=ON \
      -DARROW_PARQUET=ON \
      -DARROW_PYTHON=ON \
      -DARROW_BUILD_TESTS=ON \
      -DARROW_DEPENDENCY_SOURCE=AUTO \
      ..
make -j4
make install
popd
```

## Self-contained Wheel
```
pushd arrow/python
export PYARROW_WITH_PARQUET=1
pip install wheel  # if not installed
python setup.py build_ext --build-type=release \
       --bundle-arrow-cpp bdist_wheel
popd
```
