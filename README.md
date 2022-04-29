# TensorFlow (nightly) on M1 without conda
Below steps allow to build and install Tensorflow on M1 Macs. Use this steps
instead of [Apples' install instructions](https://developer.apple.com/metal/tensorflow-plugin/) if you:
* Don't want to use conda;
* Want TensorFlow 2.9 or nightly;
* Have an hour to go through the build process;
* Have the an M1 SoC and macOS 12.

## Install generall dependencies
Install Apple developer tools
```{bash}
brew install bazel
```

## Create virtual environment
```{bash}
python3 -m venv .venv
. .venv/bin/activate
python -m pip install --upgrade pip
```

## Install tensorflow-io and tensorflow-io-gcs-filesystem
```{bash}
git clone https://github.com/tensorflow/io
cd io
python3 setup.py -q bdist_wheel
setup.py -q bdist_wheel --project tensorflow_io_gcs_filesystem
python3 -m pip install dist/tensorflow_io-0.25.0-cp39-cp39-macosx_12_0_arm64.whl
pip install dist/tensorflow_io_gcs_filesystem-0.25.0-cp39-cp39-macosx_12_0_arm64.whl
```

## Build and install TensorFlow
Building TensorFlow takes approx. 1 hour on an M1 Max 64G
```{bash}
pip install pip numpy wheel packaging keras_preprocessing
git clone https://github.com/tensorflow/tensorflow.git
cd tensorflow
git checkout r2.9
./configure
bazel build --config opt --cpu=darwin_arm64 --host_cpu=darwin_arm64 //tensorflow/tools/pip_package:build_pip_package
./bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
pip install /tmp/tensorflow_pkg/tensorflow-2.9.0rc1-cp39-cp39-macosx_12_0_arm64.whl
```

## Maybe install tensorflow metal
Depending on the training task this may or may not speed up training.
```{bash}
python -m pip install tensorflow-metal
```

## Verify installation
Make sure to make it through below tutorial without errors.
https://www.tensorflow.org/tutorials/quickstart/beginner
