JDA
===

C++ implementation of Joint Cascade Face Detection and Alignment.

### Fetech Code

I recommend using [Git](https://git-scm.com/) to fetch the source code. If you are not familiar with Git, there is a [tutorial](https://git-scm.com/book/en/v2) you can follow.

```
$ git clone --recursive https://github.com/luoyetx/JDA.git
```

OR

```
$ git clone https://github.com/luoyetx/JDA.git
$ cd JDA
$ git submodule update --init
```

If you directly download the zip file, please remember to download [luoyetx/liblinear][luoyetx/liblinear] and [luoyetx/jsmnpp][luoyetx/jsmnpp], then extract the source code to `3rdparty`. liblinear is used for global regression training and jsmnpp is used for json config parsing.

### Build

We use [CMake][cmake] to build the project, I highly recommend you to use this build tool. We also need [OpenCV][opencv]. If you are on Windows, make sure you have set environment variable `OpenCV_DIR` to OpenCV's build directory like `D:/3rdparty/opencv2.4.11/build`. You may also need [Visual Studio][vs] to compile the source code. If you are on Linux or Unix, install the development packages of OpenCV via your system's Package Manager like `apt-get` on Ubuntu or `yum` on CentOS. However, Compile the source code of OpenCV will be the best choice of all.

```
$ cd JDA
$ mkdir build && cd build
$ cmake ..
$ make
```

### Config

We use `config.json` for configuration. `config.template.json` is a template, please copy one and rename it to `config.json`. **Attention**, all relative path is start from `build` directory, and please use `/` instead of `\\` even if you are on Windows platform.

### Data

You should prepare your own data. You need two kinds of data, face with landmarks and background images. You also need to create two text file `face.txt` and `background.txt` which can be changed in `config.json`. Every line of `face.txt` indicates a face image's path with its landmarks. The number of landmarks can be changed in `config.json` and the order of landmarks does not matter.

```
../data/face/00001.jpg x1 y1 x2 y2 ........
../data/face/00002.jpg x1 y1 x2 y2 ........
....
....
```

The face images should be resized to the pre-defined size and you should do any data augmentation by yourself, the code will exactly use the face images you provide. `background.txt` is much more simpler. Every line indicates where the background image in the file system.

```
../data/bg/000001.jpg
../data/bg/000002.jpg
../data/bg/000003.jpg
....
....
```

Background images should have no face and we will do data augmentation during the hard negative mining. Of course, you can use absolute path to indicate where is your face images and background images.

### Train

```
$ ./jda train
```

If you are using Visual Studio, make sure you know how to pass command line arguments to the program. All trained model file will be saved to `model` directory.

### Model Layout

All model file is saved as a binary file. The model parameters have two data type, 4 byte `int` and 8 byte `double`, please pay attention to the [endianness][endianness] of you CPU.

```
|-- mask (int)
|-- meta
|    |-- T (int)
|    |-- K (int)
|    |-- landmark_n (int)
|    |-- tree_depth (int)
|    |-- current_stage_idx (int) // training status
|    |-- current_cart_idx (int)
|-- mean_shape (double, size = 2*landmark_n)
|-- stages
|    |-- stage_1
|    |    |-- cart_1
|    |    |-- cart_2
|    |    |-- ...
|    |    |-- cart_K
|    |    |-- global regression weight
|    |-- stage_2
|    |-- ...
|    |-- stage_T
|-- mask (int)
```

For more details of the model file layout, please refer to `cascador.cpp` and `cart.cpp`.

### FDDB Benchmark

[FDDB][fddb] is widely used for face detection evaluation, download the data and extract to `data` directory.

```
|-- data
|    |-- fddb
|         |-- images
|         |    |-- 2002
|         |    |-- 2003
|         |-- FDDB-folds
|         |    |-- FDDB-fold-01.txt
|         |    |-- FDDB-fold-01-ellipseList.txt
|         |    |-- ....
|         |-- result
```

prepare your data and model file, you also need to set `current_stage_idx` and `current_cart_idx` correctly in `config.json`. All result text file used by [npinto/fddb-evaluation][npinto/fddb-evaluation] is under `result` directory.

```
$ ./jda fddb
```

### Attention

It's a shame that I still not get a fully trained model now. There is many detailed problems of JDA algorithm which need to be discussed. My code may have some hidden bugs. Welcome any bug report and any question or idea through the [issues](https://github.com/luoyetx/JDA/issues).

### QQ Group

There is a QQ group 347185749. If you are a [Tencent QQ][qq] user, welcome to join this group and we can discuss more there.

### License

BSD 3-Clause

### References

- [Joint Cascade Face Detection and Alignment](http://home.ustc.edu.cn/~chendong/JointCascade/ECCV14_JointCascade.pdf)
- [Face Alignment at 3000 FPS via Regressing Local Binary Features](http://research.microsoft.com/en-us/people/yichenw/cvpr14_facealignment.pdf)
- [FaceDetect/jointCascade_py](https://github.com/FaceDetect/jointCascade_py)
- [luoyetx/face-alignment-at-3000fps](https://github.com/luoyetx/face-alignment-at-3000fps)
- [cjlin1/liblinear](https://github.com/cjlin1/liblinear)
- [luoyetx/jsmnpp](https://github.com/luoyetx/jsmnpp)


[opencv]: http://opencv.org/
[luoyetx/jsmnpp]: https://github.com/luoyetx/jsmnpp
[luoyetx/liblinear]: https://github.com/luoyetx/liblinear
[cmake]: https://cmake.org/
[vs]: https://www.visualstudio.com/
[endianness]: https://en.wikipedia.org/wiki/Endianness
[qq]: http://im.qq.com/
[fddb]: http://vis-www.cs.umass.edu/fddb/
[npinto/fddb-evaluation]: https://github.com/npinto/fddb-evaluation
