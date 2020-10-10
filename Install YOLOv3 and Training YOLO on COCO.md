# Install YOLOv3 and Training YOLO on COCO
by Zhang Yingdong

### What is YOLO

YOLO: You Look Only Once，指**只需要**浏览一次就可以识别出图中的物体的类别和位置。

YOLO是一个最先进的实时目标检测系统

> You only look once (YOLO) is a state-of-the-art, real-time object detection system. On a Pascal Titan X it processes images at 30 FPS and has a mAP of 57.9% on COCO test-dev.

YOLO是目标检测模型。

目标检测是计算机视觉中比较简单的任务，用来在一张图篇中找到**某些特定的物体**，目标检测不仅要求我们识别这些物体的**种类**，同时要求我们标出这些物体的**位置**。

因为只需要看一次，YOLO被称为Region-free方法，相比于Region-based方法，YOLO不需要提前找到**可能存在目标的Region**。

也就是说，一个典型的Region-base方法的流程是这样的：先通过计算机图形学（或者深度学习）的方法，对图片进行分析，找出若干个可能存在物体的区域，将这些区域裁剪下来，放入一个图片分类器中，由分类器分类。

因为YOLO这样的Region-free方法只需要一次扫描，也被称为**单阶段**（1-stage）模型。Region-based方法方法也被称为**两阶段**（2-stage）方法。

### Install YOLOv3

YOLOv3是目前最稳定的版本，下面将会提供YOLOv3的安装教程

[参考链接1](https://github.com/ultralytics/yolov3)

[参考链接2](https://pjreddie.com/darknet/yolo/?spm=a2c6h.12873639.0.0.34c2548etHwKg6)

**Requirements**

##### **Python 3.8 or later** with all [requirements.txt](https://github.com/ultralytics/yolov3/blob/master/requirements.txt) dependencies installed, including `torch>=1.6`. To install run:p

```
$ pip install -r requirements.txt
```

**git clone**

```
$ git clone https://github.com/ultralytics/yolov3.git
```

**install Darknet**

If you don't already have Darknet installed, you should [do that first](https://pjreddie.com/darknet/install/). Or instead of reading all that just run:

```
git clone https://github.com/pjreddie/darknet.git
cd darknet
make
```

If this works you should see a whole bunch of compiling information fly by:

```
mkdir -p obj
gcc -I/usr/local/cuda/include/  -Wall -Wfatal-errors  -Ofast....
gcc -I/usr/local/cuda/include/  -Wall -Wfatal-errors  -Ofast....
gcc -I/usr/local/cuda/include/  -Wall -Wfatal-errors  -Ofast....
.....
gcc -I/usr/local/cuda/include/  -Wall -Wfatal-errors  -Ofast -lm....
```

If you have any errors, try to fix them? If everything seems to have compiled correctly, try running it!

```
./darknet
```

You should get the output:

```
usage: ./darknet <function>
```

**YOLOv3:**

`python3 detect.py --cfg cfg/yolov3.cfg --weights yolov3.pt`

### Training YOLO on COCO

**Get The COCO Data**

To train YOLO you will need all of the COCO data and labels. The script `scripts/get_coco_dataset.sh` will do this for you. Figure out where you want to put the COCO data and download it, for example:

```
cp scripts/get_coco_dataset.sh data
cd data
bash get_coco_dataset.sh
```

Now you should have all the data and the labels generated for Darknet.

**Modify cfg for COCO**

Now go to your Darknet directory. We have to change the `cfg/coco.data` config file to point to your data:

```
  1 classes= 80
  2 train  = <path-to-coco>/trainvalno5k.txt
  3 valid  = <path-to-coco>/5k.txt
  4 names = data/coco.names
  5 backup = backup
```

You should replace `<path-to-coco>` with the directory where you put the COCO data.

You should also modify your model cfg for training instead of testing. `cfg/yolo.cfg` should look like this:

```
[net]
# Testing
# batch=1
# subdivisions=1
# Training
batch=64
subdivisions=8
....
```

**Train The Model**

Now we can train! Run the command:

```
./darknet detector train cfg/coco.data cfg/yolov3.cfg darknet53.conv.74
```

If you want to use multiple gpus run:

```
./darknet detector train cfg/coco.data cfg/yolov3.cfg darknet53.conv.74 -gpus 0,1,2,3
```

If you want to stop and restart training from a checkpoint:

```
./darknet detector train cfg/coco.data cfg/yolov3.cfg backup/yolov3.backup -gpus 0,1,2,3
```

### Detection Using A Pre-Trained Model

You already have the config file for YOLO in the `cfg/` subdirectory. You will have to download the pre-trained weight file [here (237 MB)](https://pjreddie.com/media/files/yolov3.weights). Or just run this:

```
wget https://pjreddie.com/media/files/yolov3.weights
```

Then run the detector!

```
./darknet detect cfg/yolov3.cfg yolov3.weights data/dog.jpg
```

You will see some output like this:

```
layer     filters    size              input                output
    0 conv     32  3 x 3 / 1   416 x 416 x   3   ->   416 x 416 x  32  0.299 BFLOPs
    1 conv     64  3 x 3 / 2   416 x 416 x  32   ->   208 x 208 x  64  1.595 BFLOPs
    .......
  105 conv    255  1 x 1 / 1    52 x  52 x 256   ->    52 x  52 x 255  0.353 BFLOPs
  106 detection
truth_thresh: Using default '1.000000'
Loading weights from yolov3.weights...Done!
data/dog.jpg: Predicted in 0.029329 seconds.
dog: 99%
truck: 93%
bicycle: 99%
```

![img](https://pjreddie.com/media/image/Screen_Shot_2018-03-24_at_10.48.42_PM.png)

Darknet prints out the objects it detected, its confidence, and how long it took to find them. We didn't compile Darknet with `OpenCV` so it can't display the detections directly. Instead, it saves them in `predictions.png`. You can open it to see the detected objects. Since we are using Darknet on the CPU it takes around 6-12 seconds per image. If we use the GPU version it would be much faster.
