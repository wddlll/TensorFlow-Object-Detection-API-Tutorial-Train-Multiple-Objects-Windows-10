# How To Train an Object Detection Classifier for Multiple Objects Using TensorFlow (GPU) on Windows 10

## Brief Summary
*Last updated: 9/26/2018 with TensorFlow v1.10*

*Changes: Added note that the train.py file is now located in the /object_detection/legacy folder and must be moved into the main folder before issuing the training command.*

This repository is a tutorial for how to use TensorFlow's Object Detection API to train an object detection classifier for multiple objects on Windows 10, 8, or 7. (It will also work on Linux-based OSes with some minor changes.) It was originally written using TensorFlow version 1.5, but will also work for newer versions of TensorFlow.

I also made a YouTube video that walks through this tutorial. Any discrepancies between the video and this written tutorial are due to updates required for using newer versions of TensorFlow. 

**If there are differences between this written tutorial and the video, follow the written tutorial!**

[![Link to my YouTube video!](https://raw.githubusercontent.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10/master/doc/YouTube%20video.jpg)](https://www.youtube.com/watch?v=Rgpfk6eYxJA)
视频存放于YouTube，国内无法直接打开观看，需采取些其他措施，你懂的。

This readme describes every step required to get going with your own object detection classifier: 
1. [Installing TensorFlow-GPU](https://github.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10#1-install-tensorflow-gpu-15-skip-this-step-if-tensorflow-gpu-15-is-already-installed)
2. [Setting up the Object Detection directory structure and Anaconda Virtual Environment](https://github.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10#2-set-up-tensorflow-directory-and-anaconda-virtual-environment)
3. [Gathering and labeling pictures](https://github.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10#3-gather-and-label-pictures)
4. [Generating training data](https://github.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10#4-generate-training-data)
5. [Creating a label map and configuring training](https://github.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10#5-create-label-map-and-configure-training)
6. [Training](https://github.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10#6-run-the-training)
7. [Exporting the inference graph](https://github.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10#7-export-inference-graph)
8. [Testing and using your newly trained object detection classifier](https://github.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10#8-use-your-newly-trained-object-detection-classifier)

[Appendix: Common Errors](https://github.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10#appendix-common-errors)

The repository provides all the files needed to train a "Pinochle Deck" playing card detector that can accurately detect nines, tens, jacks, queens, kings, and aces. The tutorial describes how to replace these files with your own files to train a detection classifier for whatever your heart desires. It also has Python scripts to test your classifier out on an image, video, or webcam feed.

<p align="center">
  <img src="doc/detector1.jpg">
</p>

## Introduction
The purpose of this tutorial is to explain how to train your own convolutional neural network object detection classifier for multiple objects, starting from scratch. At the end of this tutorial, you will have a program that can identify and draw boxes around specific objects in pictures, videos, or in a webcam feed.

There are several good tutorials available for how to use TensorFlow’s Object Detection API to train a classifier for a single object. However, these usually assume you are using a Linux operating system. If you’re like me, you might be a little hesitant to install Linux on your high-powered gaming PC that has the sweet graphics card you’re using to train a classifier. The Object Detection API seems to have been developed on a Linux-based OS. To set up TensorFlow to train a model on Windows, there are several workarounds that need to be used in place of commands that would work fine on Linux. Also, this tutorial provides instructions for training a classifier that can detect multiple objects, not just one.

The tutorial is written for Windows 10, and it will also work for Windows 7 and 8. The general procedure can also be used for Linux operating systems, but file paths and package installation commands will need to change accordingly. 

TensorFlow-GPU allows your PC to use the video card to provide extra processing power while training, so it will be used for this tutorial. In my experience, using TensorFlow-GPU instead of regular TensorFlow reduces training time by a factor of about 8 (3 hours to train instead of 24 hours). Regular TensorFlow can also be used for this tutorial, but it will take longer. If you use regular TensorFlow, you do not need to install CUDA and cuDNN in Step 1. I used TensorFlow-GPU v1.5 while writing this tutorial, but it will likely work for future versions of TensorFlow.


## Steps
### 1. Install TensorFlow-GPU 1.5 or TensorFlow-CPU 1.12.0 (skip this step if TensorFlow-GPU 1.5 is already installed)
Install TensorFlow-GPU by following the instructions in [this YouTube Video by Mark Jay](https://www.youtube.com/watch?v=RplXYjxgZbw).

The video is made for TensorFlow-GPU v1.4, but the “pip install --upgrade tensorflow-gpu” command will automatically download version 1.5. Download and install CUDA v9.0 and cuDNN v7.0 (rather than CUDA v8.0 and cuDNN v6.0 as instructed in the video), because they are supported by TensorFlow-GPU v1.5. As future versions of TensorFlow are released, you will likely need to continue updating the CUDA and cuDNN versions to the latest supported version.

Be sure to install Anaconda with Python 3.6 as instructed in the video, as the Anaconda virtual environment will be used for the rest of this tutorial.

CPU版，我安装的是Anaconda3-5.0.1-Windows-x86_64.exe版本，Python 3.5.0，TensorFlow-CPU 1.12.0，可以成功安装。
GPU版（我的GPU为最新出产的笔记本GTX1660ti显卡），我安装的是GeForce_Experience_v3.19.0.107图形驱动+cuda9.0（cuda_9.0.176_win10.exe等，包含4个补丁，必须全部安装）+Cudnn7.3.1 for CUDA 9.0+Anaconda3-5.0.1-Windows-x86_64.exe版本，Python 3.6.8，TensorFlow1.12 
::安装cuda后，再安装 Cudnn7.3.1, 把cudnn-9.0-windows10-x64-v7.6.0.64\cuda下面的 lib include bin三个目录拷贝到如下目录下
C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0（记得不是C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA文件夹下，否则可能出现明明有有文件，但找不到。 Could not find 'cudnn64_7.dll'，目录不对）


参照https://blog.csdn.net/weixin_39290638/article/details/80045236 安装gpu版TensorFlow版
Visit [TensorFlow's website](https://www.tensorflow.org/install/install_windows) for further installation details, including how to install it on other operating systems (like Linux). The [object detection repository](https://github.com/tensorflow/models/tree/master/research/object_detection) itself also has [installation instructions](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md).

### 2. Set up TensorFlow Directory and Anaconda Virtual Environment
The TensorFlow Object Detection API requires using the specific directory structure provided in its GitHub repository. It also requires several additional Python packages, specific additions to the PATH and PYTHONPATH variables, and a few extra setup commands to get everything set up to run or train an object detection model. 

This portion of the tutorial goes over the full set up required. It is fairly meticulous, but follow the instructions closely, because improper setup can cause unwieldy errors down the road.

#### 2a. Download TensorFlow Object Detection API repository from GitHub
Create a folder directly in C: and name it “tensorflow1”. This working directory will contain the full TensorFlow object detection framework, as well as your training images, training data, trained classifier, configuration files, and everything else needed for the object detection classifier.

Download the full TensorFlow object detection repository located at https://github.com/tensorflow/models by clicking the “Clone or Download” button and downloading the zip file. Open the downloaded zip file and extract the “models-master” folder directly into the C:\tensorflow1 directory you just created. Rename “models-master” to just “models”.
(Note, this tutorial was done using this [GitHub commit](https://github.com/tensorflow/models/tree/079d67d9a0b3407e8d074a200780f3835413ef99) of the TensorFlow Object Detection API. If portions of this tutorial do not work, it may be necessary to download and use this exact commit rather than the most up-to-date version.)

#### 2b. Download the Faster-RCNN-Inception-V2-COCO model from TensorFlow's model zoo
TensorFlow provides several object detection models (pre-trained classifiers with specific neural network architectures) in its [model zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md). Some models (such as the SSD-MobileNet model) have an architecture that allows for faster detection but with less accuracy, while some models (such as the Faster-RCNN model) give slower detection but with more accuracy. I initially started with the SSD-MobileNet-V1 model, but it didn’t do a very good job identifying the cards in my images. I re-trained my detector on the Faster-RCNN-Inception-V2 model, and the detection worked considerably better, but with a noticeably slower speed.

<p align="center">
  <img src="doc/rcnn_vs_ssd.jpg">
</p>

You can choose which model to train your objection detection classifier on. If you are planning on using the object detector on a device with low computational power (such as a smart phone or Raspberry Pi), use the SDD-MobileNet model. If you will be running your detector on a decently powered laptop or desktop PC, use one of the RCNN models. 

This tutorial will use the Faster-RCNN-Inception-V2 model. [Download the model here.](http://download.tensorflow.org/models/object_detection/faster_rcnn_inception_v2_coco_2018_01_28.tar.gz) Open the downloaded faster_rcnn_inception_v2_coco_2018_01_28.tar.gz file with a file archiver such as WinZip or 7-Zip and extract the faster_rcnn_inception_v2_coco_2018_01_28 folder to the C:\tensorflow1\models\research\object_detection folder. (Note: The model date and version will likely change in the future, but it should still work with this tutorial.)

#### 2c. Download this tutorial's repository from GitHub
Download the full repository located on this page (scroll to the top and click Clone or Download) and extract all the contents directly into the C:\tensorflow1\models\research\object_detection directory. (You can overwrite the existing "README.md" file.) This establishes a specific directory structure that will be used for the rest of the tutorial. 

At this point, here is what your \object_detection folder should look like:

<p align="center">
  <img src="doc/object_detection_directory.jpg">
</p>

This repository contains the images, annotation data, .csv files, and TFRecords needed to train a "Pinochle Deck" playing card detector. You can use these images and data to practice making your own Pinochle Card Detector. It also contains Python scripts that are used to generate the training data. It has scripts to test out the object detection classifier on images, videos, or a webcam feed. You can ignore the \doc folder and its files; they are just there to hold the images used for this readme.

If you want to practice training your own "Pinochle Deck" card detector, you can leave all the files as they are. You can follow along with this tutorial to see how each of the files were generated, and then run the training. You will still need to generate the TFRecord files (train.record and test.record) as described in Step 4. 

You can also download the frozen inference graph for my trained Pinochle Deck card detector [from this Dropbox link](https://www.dropbox.com/s/va9ob6wcucusse1/inference_graph.zip?dl=0) and extract the contents to \object_detection\inference_graph. This inference graph will work "out of the box". You can test it after all the setup instructions in Step 2a - 2f have been completed by running the Object_detection_image.py (or video or webcam) script.

If you want to train your own object detector, delete the following files (do not delete the folders):
- All files in \object_detection\images\train and \object_detection\images\test
- The “test_labels.csv” and “train_labels.csv” files in \object_detection\images
- All files in \object_detection\training
-	All files in \object_detection\inference_graph

Now, you are ready to start from scratch in training your own object detector. This tutorial will assume that all the files listed above were deleted, and will go on to explain how to generate the files for your own training dataset.

#### 2d. Set up new Anaconda virtual environment
Next, we'll work on setting up a virtual environment in Anaconda for tensorflow-gpu. From the Start menu in Windows, search for the Anaconda Prompt utility, right click on it, and click “Run as Administrator”. If Windows asks you if you would like to allow it to make changes to your computer, click Yes.

In the command terminal that pops up, create a new virtual environment called “tensorflow1” by issuing the following command:
```
C:\> conda create -n tensorflow1 pip python=3.5.0
```
Then, activate the environment by issuing:
```
C:\> activate tensorflow1
```
Install tensorflow-gpu in this environment by issuing:
```
(tensorflow1) C:\> pip install --ignore-installed --upgrade tensorflow-gpu
CPU版本，Install tensorflow-cpu in this environment by issuing:
```pip install --upgrade tensorflow==1.12.0
```
Install the other necessary packages by issuing the following commands:
```
(tensorflow1) C:\> conda install -c anaconda protobuf
(tensorflow1) C:\> pip install pillow
(tensorflow1) C:\> pip install lxml
(tensorflow1) C:\> pip install Cython
(tensorflow1) C:\> pip install jupyter
(tensorflow1) C:\> pip install matplotlib
(tensorflow1) C:\> pip install pandas
(tensorflow1) C:\> pip install opencv-python
```
(Note: The ‘pandas’ and ‘opencv-python’ packages are not needed by TensorFlow, but they are used in the Python scripts to generate TFRecords and to work with images, videos, and webcam feeds.)
全部软件安装完之后，安装包可逐个核对列表，看是否与我的相符（主要检查上述几个安装包是否正确，最好确保python、TensorFlow版本一致）
[Packages in environment] (https://github.com/wddlll/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10/blob/master/doc/Packages%20in%20environment.txt)
#### 2e. Configure PYTHONPATH environment variable
A PYTHONPATH variable must be created that points to the \models, \models\research, and \models\research\slim directories. Do this by issuing the following commands (from any directory):
```
(tensorflow1) C:\> set PYTHONPATH=C:\tensorflow1\models;C:\tensorflow1\models\research;C:\tensorflow1\models\research\slim
```
(Note: Every time the "tensorflow1" virtual environment is exited, the PYTHONPATH variable is reset and needs to be set up again.)

#### 2f. Compile Protobufs and run setup.py
Next, compile the Protobuf files, which are used by TensorFlow to configure model and training parameters. Unfortunately, the short protoc compilation command posted on TensorFlow’s Object Detection API [installation page](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md) does not work on Windows. Every  .proto file in the \object_detection\protos directory must be called out individually by the command.

In the Anaconda Command Prompt, change directories to the \models\research directory and copy and paste the following command into the command line and press Enter:
```
protoc --python_out=. .\object_detection\protos\anchor_generator.proto .\object_detection\protos\argmax_matcher.proto .\object_detection\protos\bipartite_matcher.proto .\object_detection\protos\box_coder.proto .\object_detection\protos\box_predictor.proto .\object_detection\protos\eval.proto .\object_detection\protos\faster_rcnn.proto .\object_detection\protos\faster_rcnn_box_coder.proto .\object_detection\protos\grid_anchor_generator.proto .\object_detection\protos\hyperparams.proto .\object_detection\protos\image_resizer.proto .\object_detection\protos\input_reader.proto .\object_detection\protos\losses.proto .\object_detection\protos\matcher.proto .\object_detection\protos\mean_stddev_box_coder.proto .\object_detection\protos\model.proto .\object_detection\protos\optimizer.proto .\object_detection\protos\pipeline.proto .\object_detection\protos\post_processing.proto .\object_detection\protos\preprocessor.proto .\object_detection\protos\region_similarity_calculator.proto .\object_detection\protos\square_box_coder.proto .\object_detection\protos\ssd.proto .\object_detection\protos\ssd_anchor_generator.proto .\object_detection\protos\string_int_label_map.proto .\object_detection\protos\train.proto .\object_detection\protos\keypoint_box_coder.proto .\object_detection\protos\multiscale_anchor_generator.proto .\object_detection\protos\graph_rewriter.proto

cd C:\tensorflow1\models\research\
protoc --python_out=. .\object_detection\protos\calibration.proto .\object_detection\protos\eval.proto .\object_detection\protos\post_processing.proto
```
This creates a name_pb2.py file from every name.proto file in the \object_detection\protos folder.

**(Note: TensorFlow occassionally adds new .proto files to the \protos folder. If you get an error saying ImportError: cannot import name 'something_something_pb2' , you may need to update the protoc command to include the new .proto files.)**

Finally, run the following commands from the C:\tensorflow1\models\research directory:
```
(tensorflow1) C:\tensorflow1\models\research> python setup.py build
(tensorflow1) C:\tensorflow1\models\research> python setup.py install
```

#### 2g. Test TensorFlow setup to verify it works
The TensorFlow Object Detection API is now all set up to use pre-trained models for object detection, or to train a new one. You can test it out and verify your installation is working by launching the object_detection_tutorial.ipynb script with Jupyter. From the \object_detection directory, issue this command:

::You can test that you have correctly installed the Tensorflow Object Detection API by running the following command:
::https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md
```
python object_detection/builders/model_builder_test.py
```
::成功将返回0个失败

::若出现以下情况，表示jupyter notebook安装未成功，可在anaconda中当前环境（tensorflow1.12）下安装；若最新版本不可安装，可以选择5.6.0版本
```
(tensorflow1.12) C:\tensorflow1\models\research>jupyter notebook
```
::报错信息 ImportError: cannot import name 'Type'
::若报错，尝试使用conda安装
conda install jupyter notebook
conda install pillow
conda install lxml
conda install Cython
conda install matplotlib
conda install pandas
conda install opencv-python
```
(tensorflow1) C:\tensorflow1\models\research\object_detection> jupyter notebook object_detection_tutorial.ipynb
```
:: 如果能运行到最后，但是plt.imshow(image_np)不能出图，则可能matplotlib安装失败，可充气电脑，或重新使用conda安装以上包
This opens the script in your default web browser and allows you to step through the code one section at a time. You can step through each section by clicking the “Run” button in the upper toolbar. The section is done running when the “In [ * ]” text next to the section populates with a number (e.g. “In [1]”). 

(Note: part of the script downloads the ssd_mobilenet_v1 model from GitHub, which is about 74MB. This means it will take some time to complete the section, so be patient.)

Once you have stepped all the way through the script, you should see two labeled images at the bottom section the page. If you see this, then everything is working properly! If not, the bottom section will report any errors encountered. See the [Appendix](https://github.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10#appendix-common-errors) for a list of errors I encountered while setting this up.

<p align="center">
  <img src="doc/jupyter_notebook_dogs.jpg">
</p>

若出现问题：ValueError: First step cannot be zero.
请修改文件C:\tensorflow1\models\research\object_detection\training\faster_rcnn_inception_v2_pets.config
将下面内容
```
train_config: {
  batch_size: 1
  optimizer {
    momentum_optimizer: {
      learning_rate: {
        manual_step_learning_rate {
          initial_learning_rate: 0.0002
          schedule {
            step: 0
            learning_rate: .0002
          }
          schedule {
            step: 900000
            learning_rate: .00002
          }
          schedule {
            step: 1200000
            learning_rate: .000002
          }
        }
      }
      momentum_optimizer_value: 0.9
    }
    use_moving_average: false
  }
 ```
 改为
 ```
 train_config: {
  batch_size: 1
  optimizer {
    momentum_optimizer: {
      learning_rate: {
        manual_step_learning_rate {
          initial_learning_rate: 0.0002
          schedule {
            step: 9000
            learning_rate: .0002
          }
          schedule {
            step: 900000
            learning_rate: .00002
          }
          schedule {
            step: 1200000
            learning_rate: .000002
          }
        }
      }
      momentum_optimizer_value: 0.9
    }
    use_moving_average: false
  }
  ```
  原来作者的说明中存在错误，本人已经修改该文件，并上传
### 3. Gather and Label Pictures
Now that the TensorFlow Object Detection API is all set up and ready to go, we need to provide the images it will use to train a new detection classifier.

#### 3a. Gather Pictures
TensorFlow needs hundreds of images of an object to train a good detection classifier. To train a robust classifier, the training images should have random objects in the image along with the desired objects, and should have a variety of backgrounds and lighting conditions. There should be some images where the desired object is partially obscured, overlapped with something else, or only halfway in the picture. 

For my Pinochle Card Detection classifier, I have six different objects I want to detect (the card ranks nine, ten, jack, queen, king, and ace – I am not trying to detect suit, just rank). I used my iPhone to take about 40 pictures of each card on its own, with various other non-desired objects in the pictures. Then, I took about another 100 pictures with multiple cards in the picture. I know I want to be able to detect the cards when they’re overlapping, so I made sure to have the cards be overlapped in many images.

<p align="center">
  <img src="doc/collage.jpg">
</p>

You can use your phone to take pictures of the objects or download images of the objects from Google Image Search. I recommend having at least 200 pictures overall. I used 311 pictures to train my card detector.

Make sure the images aren’t too large. They should be less than 200KB each, and their resolution shouldn’t be more than 720x1280. The larger the images are, the longer it will take to train the classifier. You can use the resizer.py script in this repository to reduce the size of the images.

After you have all the pictures you need, move 20% of them to the \object_detection\images\test directory, and 80% of them to the \object_detection\images\train directory. Make sure there are a variety of pictures in both the \test and \train directories.

#### 3b. Label Pictures
Here comes the fun part! With all the pictures gathered, it’s time to label the desired objects in every picture. LabelImg is a great tool for labeling images, and its GitHub page has very clear instructions on how to install and use it.

[LabelImg GitHub link](https://github.com/tzutalin/labelImg)

[LabelImg download link](https://www.dropbox.com/s/tq7zfrcwl44vxan/windows_v1.6.0.zip?dl=1)

Download and install LabelImg, point it to your \images\train directory, and then draw a box around each object in each image. Repeat the process for all the images in the \images\test directory. This will take a while! 

<p align="center">
  <img src="doc/labels.jpg">
</p>

LabelImg saves a .xml file containing the label data for each image. These .xml files will be used to generate TFRecords, which are one of the inputs to the TensorFlow trainer. Once you have labeled and saved each image, there will be one .xml file for each image in the \test and \train directories.

Also, you can check if the size of each bounding box is correct by running sizeChecker.py

```
(tensorflow1) C:\tensorflow1\models\research\object_detection> python sizeChecker.py --move
```

### 4. Generate Training Data
With the images labeled, it’s time to generate the TFRecords that serve as input data to the TensorFlow training model. This tutorial uses the xml_to_csv.py and generate_tfrecord.py scripts from [Dat Tran’s Raccoon Detector dataset](https://github.com/datitran/raccoon_dataset), with some slight modifications to work with our directory structure.

First, the image .xml data will be used to create .csv files containing all the data for the train and test images. From the \object_detection folder, issue the following command in the Anaconda command prompt:
```
(tensorflow1) C:\tensorflow1\models\research\object_detection> python xml_to_csv.py
```
This creates a train_labels.csv and test_labels.csv file in the \object_detection\images folder. 

Next, open the generate_tfrecord.py file in a text editor. Replace the label map starting at line 31 with your own label map, where each object is assigned an ID number. This same number assignment will be used when configuring the labelmap.pbtxt file in Step 5b. 

For example, say you are training a classifier to detect basketballs, shirts, and shoes. You will replace the following code in generate_tfrecord.py:
```
# TO-DO replace this with label map
def class_text_to_int(row_label):
    if row_label == 'nine':
        return 1
    elif row_label == 'ten':
        return 2
    elif row_label == 'jack':
        return 3
    elif row_label == 'queen':
        return 4
    elif row_label == 'king':
        return 5
    elif row_label == 'ace':
        return 6
    else:
        return None
```
With this:
```
# TO-DO replace this with label map
def class_text_to_int(row_label):
    if row_label == 'basketball':
        return 1
    elif row_label == 'shirt':
        return 2
    elif row_label == 'shoe':
        return 3
    else:
        return None
```
Then, generate the TFRecord files by issuing these commands from the \object_detection folder:
```
python generate_tfrecord.py --csv_input=images\train_labels.csv --image_dir=images\train --output_path=train.record
python generate_tfrecord.py --csv_input=images\test_labels.csv --image_dir=images\test --output_path=test.record
```
These generate a train.record and a test.record file in \object_detection. These will be used to train the new object detection classifier.

### 5. Create Label Map and Configure Training
The last thing to do before training is to create a label map and edit the training configuration file.

#### 5a. Label map
The label map tells the trainer what each object is by defining a mapping of class names to class ID numbers. Use a text editor to create a new file and save it as labelmap.pbtxt in the C:\tensorflow1\models\research\object_detection\training folder. (Make sure the file type is .pbtxt, not .txt !) In the text editor, copy or type in the label map in the format below (the example below is the label map for my Pinochle Deck Card Detector):
```
item {
  id: 1
  name: 'nine'
}

item {
  id: 2
  name: 'ten'
}

item {
  id: 3
  name: 'jack'
}

item {
  id: 4
  name: 'queen'
}

item {
  id: 5
  name: 'king'
}

item {
  id: 6
  name: 'ace'
}
```
The label map ID numbers should be the same as what is defined in the generate_tfrecord.py file. For the basketball, shirt, and shoe detector example mentioned in Step 4, the labelmap.pbtxt file will look like:
```
item {
  id: 1
  name: 'basketball'
}

item {
  id: 2
  name: 'shirt'
}

item {
  id: 3
  name: 'shoe'
}
```

#### 5b. Configure training
Finally, the object detection training pipeline must be configured. It defines which model and what parameters will be used for training. This is the last step before running training!

Navigate to C:\tensorflow1\models\research\object_detection\samples\configs and copy the faster_rcnn_inception_v2_pets.config file into the \object_detection\training directory. Then, open the file with a text editor. There are several changes to make to the .config file, mainly changing the number of classes and examples, and adding the file paths to the training data.

Make the following changes to the faster_rcnn_inception_v2_pets.config file. Note: The paths must be entered with single forward slashes (NOT backslashes), or TensorFlow will give a file path error when trying to train the model! Also, the paths must be in double quotation marks ( " ), not single quotation marks ( ' ).
- feature_extractor：表示用于特征提取的backbone网络的选型
First_stage_features_stride表示第一阶段特征提取步长，根据经验，训练时可以保持 16 不变，如果待检测目标比较密集且较小，则可以尝试将其修改为8，以降低特征提取步长，提高特征提取密度，从而提升模型效果。修改为4的话会导致及结算量巨大，而且容易导致训练的过度抖动，难以拟合，因此建议最小改成8。如果目标较大，步长为8，totalloss也难以收敛拟合，可以改为16. https://blog.csdn.net/wubingwei12/article/details/88184140
- Line 9. Change num_classes to the number of different objects you want the classifier to detect. For the above basketball, shirt, and shoe detector, it would be num_classes : 3 .
- Line 110. Change fine_tune_checkpoint to:
  - fine_tune_checkpoint : "C:/tensorflow1/models/research/object_detection/faster_rcnn_inception_v2_coco_2018_01_28/model.ckpt"

- Lines 126 and 128. In the train_input_reader section, change input_path and label_map_path to:
  - input_path : "C:/tensorflow1/models/research/object_detection/train.record"
  - label_map_path: "C:/tensorflow1/models/research/object_detection/training/labelmap.pbtxt"

- Line 132. Change num_examples to the number of images you have in the \images\test directory.

- Lines 140 and 142. In the eval_input_reader section, change input_path and label_map_path to:
  - input_path : "C:/tensorflow1/models/research/object_detection/test.record"
  - label_map_path: "C:/tensorflow1/models/research/object_detection/training/labelmap.pbtxt"

Save the file after the changes have been made. That’s it! The training job is all configured and ready to go!

### 6. Run the Training
**UPDATE 9/26/18:** 
*As of version 1.9, TensorFlow has deprecated the "train.py" file and replaced it with "model_main.py" file. I haven't been able to get model_main.py to work correctly yet (I run in to errors related to pycocotools). Fortunately, the train.py file is still available in the /object_detection/legacy folder. Simply move train.py from /object_detection/legacy into the /object_detection folder and then continue following the steps below.*

Here we go! From the \object_detection directory, issue the following command to begin training:
Simply move train.py from /object_detection/legacy into the /object_detection，确保object_detection根目录下存在train.py文件
```
cd C:\tensorflow1\models\research\object_detection
copy .\legacy\train.py .\
python train.py --logtostderr --train_dir=training/ --pipeline_config_path=training/faster_rcnn_inception_v2_pets.config
```
If everything has been set up correctly, TensorFlow will initialize the training. The initialization can take up to 30 seconds before the actual training begins. When training begins, it will look like this:

<p align="center">
  <img src="doc/training.jpg">
</p>

:: 若出现pandas core属性不存在，表明当前环境下未安装pandas，请确认安装是否正确。错误信息：from object_detection.builders import dataset_builder
如果需要中断，可随时按 Ctrl+C，退出运行，需要时再重新启动。
Each step of training reports the loss. It will start high and get lower and lower as training progresses. For my training on the Faster-RCNN-Inception-V2 model, it started at about 3.0 and quickly dropped below 0.8. I recommend allowing your model to train until the loss consistently drops below 0.05, which will take about 40,000 steps, or about 2 hours (depending on how powerful your CPU and GPU are). Note: The loss numbers will be different if a different model is used. MobileNet-SSD starts with a loss of about 20, and should be trained until the loss is consistently under 2.

You can view the progress of the training job by using TensorBoard. To do this, open a new instance of Anaconda Prompt, activate the tensorflow1 virtual environment, change to the C:\tensorflow1\models\research\object_detection directory, and issue the following command:
```
(tensorflow1) C:\tensorflow1\models\research\object_detection>tensorboard --logdir=training
```
This will create a webpage on your local machine at YourPCName:6006（YourPCName:6006在很多电脑不可用，可能与用户名有关） or http://localhost:6006, which can be viewed through a web browser. The TensorBoard page provides information and graphs that show how the training is progressing. One important graph is the Loss graph, which shows the overall loss of the classifier over time.
在Google Chrome或QQ浏览器（选择Google Chrome模式）中输入
```
http://localhost:6006
```
<p align="center">
  <img src="doc/loss_graph.JPG">
</p>
需等待出现一段时间出现：
W0710 22:34:45.765799 Reloader tf_logging.py:120] Found more than one graph event per run, or there was a metagraph containing a graph_def, as well as one or more graph events.  Overwriting the graph with the newest event.

The training routine periodically saves checkpoints about every five minutes. You can terminate the training by pressing Ctrl+C while in the command prompt window. I typically wait until just after a checkpoint has been saved to terminate the training. You can terminate training and start it later, and it will restart from the last saved checkpoint. The checkpoint at the highest number of steps will be used to generate the frozen inference graph.

### 7. Export Inference Graph
Now that training is complete, the last step is to generate the frozen inference graph (.pb file). From the \object_detection folder, issue the following command, where “XXXX” in “model.ckpt-XXXX” should be replaced with the highest-numbered .ckpt file in the training folder:
```
python export_inference_graph.py --input_type image_tensor --pipeline_config_path training/faster_rcnn_inception_v2_pets.config --trained_checkpoint_prefix training/model.ckpt-XXXX --output_directory inference_graph
```
This creates a frozen_inference_graph.pb file in the \object_detection\inference_graph folder. The .pb file contains the object detection classifier.

### 8. Use Your Newly Trained Object Detection Classifier!
The object detection classifier is all ready to go! I’ve written Python scripts to test it out on an image, video, or webcam feed.

Before running the Python scripts, you need to modify the NUM_CLASSES variable in the script to equal the number of classes you want to detect. (For my Pinochle Card Detector, there are six cards I want to detect, so NUM_CLASSES = 6.)

To test your object detector, move a picture of the object or objects into the \object_detection folder, and change the IMAGE_NAME variable in the Object_detection_image.py to match the file name of the picture. Alternatively, you can use a video of the objects (using Object_detection_video.py), or just plug in a USB webcam and point it at the objects (using Object_detection_webcam.py).
```
cd C:\tensorflow1\models\research\object_detection
python Object_detection_image.py
or 
python Object_detection_video.py
or 
python Object_detection_webcam.py
```
To run any of the scripts, type “idle” in the Anaconda Command Prompt (with the “tensorflow1” virtual environment activated) and press ENTER. This will open IDLE, and from there, you can open any of the scripts and run them.

If everything is working properly, the object detector will initialize for about 10 seconds and then display a window showing any objects it’s detected in the image!

<p align="center">
  <img src="doc/detector2.jpg">
</p>

If you encounter errors, please check out the Appendix: it has a list of errors that I ran in to while setting up my object detection classifier. You can also trying Googling the error. There is usually useful information on Stack Exchange or in TensorFlow’s Issues on GitHub.

9. Run the Evaluation
运行评估前，需要安装git和pycocotools

```
在网上搜索下载安装vc++2015（或vc++2017，14版本）
conda install git
pip install git+https://github.com/philferriere/cocoapi.git#subdirectory=PythonAPI

把安装到C:\ProgramData\Anaconda3\envs\tensorflow-cpu\Lib\site-packages\pycocotools的pycocotools文件夹复制到C:\tensorflow1\models\research\文件夹下
```

From the \object_detection directory, issue the following command to begin evaluation:
```
python eval.py --checkpoint_dir==training/ --eval_dir==images/eval/ --pipeline_config_path==training/faster_rcnn_inception_v2_pets.config  
```
Check the evaluation in tensorboard

```
tensorboard --logdir=images/eval 
```
--logdir=images/eval 为#path to log dirction
运行中遇到NameError: name 'unicode' is not defined，这是因为python 3.X版本中已经没有unicode函数，可将Unicode（）替换为str（）
将C:\tensorflow1\models\research\object_detection\utils\object_detection_evaluation.py，line 213中
```
try:
         category_name = unicode(category_name, 'utf-8')
       except TypeError:
```
修改为
```
try:
          category_name = str(category_name, 'utf-8')
        except TypeError:
```
line 354，中
```
        try:
          category_name = str(category_name, 'utf-8')
```
修改为
```
        try:
          category_name = str(category_name, 'utf-8')
```
If you encounter errors, please check out the Appendix: it has a list of errors that I ran in to while setting up my object detection classifier. You can also trying Googling the error. There is usually useful information on Stack Exchange or in TensorFlow’s Issues on GitHub.
## 通过VS Code调试 tensorflow
参照 https://jingyan.baidu.com/article/e5c39bf5e89c3039d660336a.html 打开编辑器后，点击左下方的齿轮状图标
在弹出菜单中，点击【settings】子菜单
在弹出的菜单中，点击【show modified settings】子菜单，找到下面的Edit in settings.json 超链接
例如，安装环境为 C:\ProgramData\Anaconda3\envs\tensorflow-cpu\Lib\site-packages
参照https://blog.csdn.net/Briliantly/article/details/82847804
将settings.json文件修改为如下
{
    "python.pythonPath": "C:\\ProgramData\\Anaconda3\\envs\\tensorflow-cpu"
    "python.autoComplete.extraPaths": [
        "C:\\ProgramData\\Anaconda3\\envs\\tensorflow-cpu",
        "C:\\ProgramData\\Anaconda3\\envs\tensorflow-cpu\\Lib\\site-packages"
    ],
    "python.autoComplete.addBrackets": true,
}
常见问题
## Appendix: Common Errors
It appears that the TensorFlow Object Detection API was developed on a Linux-based operating system, and most of the directions given by the documentation are for a Linux OS. Trying to get a Linux-developed software library to work on Windows can be challenging. There are many little snags that I ran in to while trying to set up tensorflow-gpu to train an object detection classifier on Windows 10. This Appendix is a list of errors I ran in to, and their resolutions.

#### 1. ModuleNotFoundError: No module named 'nets' or ModuleNotFoundError: No module named 'deployment' 

This error occurs when you try to run object_detection_tutorial.ipynb or train.py and you don’t have the PATH and PYTHONPATH environment variables set up correctly. Exit the virtual environment by closing and re-opening the Anaconda Prompt window. Then, issue “activate tensorflow1” to re-enter the environment, and then issue the commands given in Step 2e. 

You can use “echo %PATH%” and “echo %PYTHONPATH%” to check the environment variables and make sure they are set up correctly.

Also, make sure you have run these commands from the \models\research directory:
```
setup.py build
setup.py install
```
#### 2. ImportError: cannot import name 'preprocessor_pb2'

#### ImportError: cannot import name 'string_int_label_map_pb2'

#### (or similar errors with other pb2 files)

This occurs when the protobuf files (in this case, preprocessor.proto) have not been compiled. Re-run the protoc command given in Step 2f. Check the \object_detection\protos folder to make sure there is a name_pb2.py file for every name.proto file.

#### 3. object_detection/protos/.proto: No such file or directory

This occurs when you try to run the
```
“protoc object_detection/protos/*.proto --python_out=.”
```
command given on the TensorFlow Object Detection API installation page. Sorry, it doesn’t work on Windows! Copy and paste the full command given in Step 2f instead. There’s probably a more graceful way to do it, but I don’t know what it is.

#### 4. Unsuccessful TensorSliceReader constructor: Failed to get "file path" … The filename, directory name, or volume label syntax is incorrect.
  
This error occurs when the filepaths in the training configuration file (faster_rcnn_inception_v2_pets.config or similar) have not been entered with backslashes instead of forward slashes. Open the .config file and make sure all file paths are given in the following format:
```
“C:/path/to/model.file”
```

#### 5. ValueError: Tried to convert 't' to a tensor and failed. Error: Argument must be a dense tensor: range(0, 3) - got shape [3], but wanted [].

The issue is with models/research/object_detection/utils/learning_schedules.py Currently it is
```
rate_index = tf.reduce_max(tf.where(tf.greater_equal(global_step, boundaries),
                                      range(num_boundaries),
                                      [0] * num_boundaries))
```
Wrap list() around the range() like this:

```
rate_index = tf.reduce_max(tf.where(tf.greater_equal(global_step, boundaries),
                                     list(range(num_boundaries)),
                                      [0] * num_boundaries))
```

[Ref: Tensorflow Issue#3705](https://github.com/tensorflow/models/issues/3705#issuecomment-375563179)

#### 5. 针对小目标对象识别，修改步长.为使得最终的feature map不至于太小，需修改步长（features_stride）
主要是在
C:\tensorflow1\models\research\object_detection\training\faster_rcnn_inception_v2_pets.config
```
line 20,
	# first_stage_features_stride,height_stride,width_stride为提案框的步长
	# 小目标识别，可根据需要设置调整小为8
    first_stage_anchor_generator {
      grid_anchor_generator {
        scales: [0.25, 0.5, 1.0, 2.0]
        aspect_ratios: [0.5, 1.0, 2.0]
        height_stride: 16
        width_stride: 16
      }
    }
```
\models\research\slim\nets
涉及下采样的位置包括
```
line 235, net = slim.max_pool2d(net, [3, 3], stride=2, scope='pool1')
line 317-320,
      resnet_v1_block('block1', base_depth=64, num_units=3, stride=2),
      resnet_v1_block('block2', base_depth=128, num_units=4, stride=2),
      resnet_v1_block('block3', base_depth=256, num_units=23, stride=2),
      resnet_v1_block('block4', base_depth=512, num_units=3, stride=1),
```
改为
```
  blocks = [
      resnet_v1_block('block1', base_depth=64, num_units=3, stride=2),
      resnet_v1_block('block2', base_depth=128, num_units=4, stride=2),
      resnet_v1_block('block3', base_depth=256, num_units=6, stride=1),
      resnet_v1_block('block4', base_depth=512, num_units=3, stride=1),
  ]
```

上述总计下采样(stride=2)总计出现4次， 2*2*2*2=16倍，因此feature map宽度 600/16=37.5
若一只动物的标记框有32*16像素，在最终的feature map只有2*1个像素，考虑到标记框内真实动物对象的信息更少，在最终的feature map将不足2*1个像素。
若减少1次下采样，即下采样features_stride从16改为8，则该动物将增加一倍，标记框则有4*2个像素，动物对象占有的像素将超过1个像素，留给分类器的参考信息更多，因此精度更高。

6、在faster_rcnn_inception_v2_pets.config文件中
  ```
  image_resizer {
      keep_aspect_ratio_resizer {
        min_dimension: 600
        max_dimension: 1000
      }
    }
  ```
  修改为
  ```
    image_resizer {
      keep_aspect_ratio_resizer {
        min_dimension: 224
        max_dimension: 400
      }
    }
  ```

  ```
    first_stage_anchor_generator {
      grid_anchor_generator {
	height: 64
	width: 64
        scales: [0.25, 0.5, 1.0, 2.0]
        aspect_ratios: [0.5, 1.0, 2.0]
        height_stride: 16
        width_stride: 16
      }
  ```
    修改为：    
  ```
      first_stage_anchor_generator {
      grid_anchor_generator {
	#height: 256  此行及下一行没有，默认提案框大小为256，对小目标不能识别，小汽车用64*64，无人机动物为32*32
	#width: 256
        scales: [0.25, 0.5, 1.0, 2.0]
        aspect_ratios: [0.5, 1.0, 2.0]
        height_stride: 16
        width_stride: 16
      }
  ```
7.通过eval.py 导出结果
  修改安装环境下的C:\ProgramData\Anaconda3\envs\tensorflow-cpu\Lib\site-packages\object_detection-0.1-py3.6.egg\object_detection\eval_util.py
  在line 188，增加
  ```
  vis_utils.visualize_boxes_and_labels_on_image_array(
      image,
      detection_boxes,
      detection_classes,
      detection_scores,
      category_index,
      instance_masks=detection_masks,
      instance_boundaries=detection_boundaries,
      keypoints=detection_keypoints,
      use_normalized_coordinates=False,
      max_boxes_to_draw=max_num_predictions,
      min_score_thresh=min_score_thresh,
      agnostic_mode=agnostic_mode,
      skip_scores=skip_scores,
      skip_labels=skip_labels)


  #输出评估后的图像
  if export_dir:
    if keep_image_id_for_visualization_export and result_dict[fields.
                                                              InputDataFields()
                                                              .key]:
      if  not os.path.exists(export_dir):#如果路径不存在
          os.makedirs(export_dir)
      export_path = os.path.join(export_dir, '{}'.format(
         result_dict[fields.InputDataFields().key].decode("gb2312")))
    else:
      export_path = os.path.join(export_dir, 'export-{}.jpg'.format(tag))
    vis_utils.save_image_array_as_png(image, export_path)

  #输出评估总结到tensorboard
  summary = tf.Summary(value=[
      tf.Summary.Value(
          tag=tag,
          image=tf.Summary.Image(
              encoded_image_string=vis_utils.encode_image_array_as_png_str(
                  image)))
  ])
  summary_writer = tf.summary.FileWriterCache.get(summary_dir)
  summary_writer.add_summary(summary, global_step)

  tf.logging.info('Detection visualizations written to summary with tag %s.',
                  tag)
   ```
   修改安装环境下的C:\ProgramData\Anaconda3\envs\tensorflow-cpu\Lib\site-packages\object_detection-0.1-py3.6.egg\object_detection\utils\object_detection_evaluation.py
   在line873， def evaluate(self)函数下，
   ```
   self.average_precision_per_class[class_index] = average_precision
      logging.info('average_precision: %f', average_precision)
      
      #输出output tp fp gt 
      tp=np.sum(tp_fp_labels)
      fp=tp_fp_labels.size-np.sum(tp_fp_labels)
      print('results of class:', class_index + self.label_id_offset)
      print('tp:',tp)
      print('fp:',fp)
      print('gt:',self.num_gt_instances_per_class[class_index])
  
      #output precision and recall for plot PR curve
#      file_dir='images/eval/precision_recall_class'+str(class_index + self.label_id_offset)+'.txt'
#      with open(file_dir,'w+') as PR_TXT:
#          PR_TXT.writelines('precision'+'\n')
#          for pr_element in precision:
#             PR_TXT.writelines(str(pr_element)+'\n')
#          PR_TXT.writelines('Recall'+'\n')
#          for re_element in recall:
#             PR_TXT.writelines(str(re_element)+'\n')
      file_dir='images/eval/precision_recall_class'+str(class_index + self.label_id_offset)+'.csv'
      with open(file_dir,'w+') as pr:
          pr.write('precision,recall\n')
          for pr_element ,re_element in zip(precision,recall):
              pr.write("{},{}\n".format(pr_element,re_element))
     ```   
