# Build tensorflow to /tf
build and package a wheel. Takes approx. 1h

## Get the image

```
docker pull tensorflow/tensorflow:latest-devel-py3
```

```
docker run -it -w /tensorflow -v $PWD:/mnt -e HOST_PERMS="$(id -u):$(id -g)" \
tensorflow/tensorflow:latest-devel-py3 bash
    
```
--
install bazel bazel-0.15.0-installer-linux-x86_64.sh

```
wget https://github.com/bazelbuild/bazel/releases/download/0.15.0/bazel-0.15.0-installer-linux-x86_64.sh
chmod +x bazel-0.15.0-installer-linux-x86_64.sh
./bazel-0.15.0-installer-linux-x86_64.sh
```
### Get the souce

```
git clone https://github.com/tensorflow/tensorflow.git
git checkout v1.12.2
```
--
### Configure 
Configure select `No` on each question exept arch, set to no-avx `-mno-avx`

```
./configure
```
### Build

```
bazel build --config=opt //tensorflow/tools/pip_package:build_pip_package
```
### Package

```
./bazel-bin/tensorflow/tools/pip_package/build_pip_package /mnt \
 && chown $HOST_PERMS /mnt/tensorflow-version-tags.whl
```
### Verify

```
pip uninstall tensorflow  # remove current version

pip install /mnt/tensorflow-1.12.0-cp35-cp35m-linux_x86_64.whl
cd /tmp  # don't import from source directory
python -c "import tensorflow as tf; print(tf.__version__)"
```

