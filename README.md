# accessmath-textboxes
A fork of the [TextBoxes]() project for finetuning and deploying a neural network for detection of handwritten whiteboard content in lecture videos

# Setting up Textboxes

1. Clone this repository. We will call the clone directory `$CAFFE_ROOT`
  ```Shell
  git clone https://github.com/bhargavaurala/accessmath-textboxes.git
  
  cd TextBoxes
  
  make -j8
  
  make py
  
  export PYTHONPATH=$PYTHONPATH:$CAFFE_ROOT/python
  ```
 
 
 ### Download
1. Models trained on ICDAR 2013: [Dropbox link](https://www.dropbox.com/s/g8pjzv2de9gty8g/TextBoxes_icdar13.caffemodel?dl=0) [BaiduYun link](http://pan.baidu.com/s/1qY73XHq)
2. Fully convolutional reduced (atrous) VGGNet: [Dropbox link](https://www.dropbox.com/s/qxc64az0a21vodt/VGG_ILSVRC_16_layers_fc_reduced.caffemodel?dl=0) [BaiduYun link](http://pan.baidu.com/s/1slQyMiL)
3. Compiled mex file for evaluation(for multi-scale test evaluation: evaluation_nms.m): [Dropbox link](https://www.dropbox.com/s/xtjuwvphxnz1nl8/polygon_intersect.mexa64?dl=0) [BaiduYun link](http://pan.baidu.com/s/1jIe9UWA)
4. Frame version of the AccessMath dataset from [here](). Place this folder on your disk and export the path to shell
```
export AM_DATA_DIR=/path/to/AccessMathVOC
```

### Generate training, validation and test LMDBs.

1. `cd $CAFFE_ROOT/data/AccessMath`
2. `./create_data.sh`

### Train
1. In `models/VGGNet/text/longer_conv_300x300/` Modify `data_param` in the first layer (`data`) in `train.prototxt` and `test.prototxt` as shown below
```
  data_param {
    source: "/path/to/AccessMathVOC/AccessMath/lmdb/AccessMath_train_lmdb"
    batch_size: 32
    backend: LMDB
  }
 ```
2. Use `cd $CAFFE_ROOT/build/tools ./caffe train_net -iterations 10000 -solver models/VGGNet/text/longer_conv_300x300/solver.prototxt -weights /path/to/model_trained_on_icdar2013`
3. You should see around 77.5% as the final validation performance.
4. Transfer the model to `models/text_detection` in the [AccessMath]() root folder
