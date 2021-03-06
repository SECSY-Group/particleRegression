# Astigmatic Particle Tracking Velocimetry (APTV) using a Deep Neural Network (DNN)
Source code of the DNN used in: "On the use of a cascaded convolutional neural network for three-dimensional flow measurements using astigmatic PTV"

DOI: https://doi.org/10.1088/1361-6501/ab7bfd

Please cite as:

``` 
@article{10.1088/1361-6501/ab7bfd,
	author={Jörg König and Minqian Chen and Wiebke Rösing and David Boho and Patrick Mäder and Christian Cierpka},
	title={On the use of a cascaded convolutional neural network for three-dimensional flow measurements using astigmatic PTV},
	journal={Measurement Science and Technology},
	url={http://iopscience.iop.org/10.1088/1361-6501/ab7bfd},
	year={2020}
}
```

## Dependencies
Particle regression depends on the following libraries:
*   Keras==2.2.4
*   scikit-image==0.15.0
*   scikit-learn==0.20.2
*   opencv-contrib-python==3.4.0.12
*   pandas==0.23.4
*   tqdm==4.31.1


This implementation is based on [Tensorflow object Detection Installation instructions](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md). Therefore the installation is the same as original object detection API.
## Particle detection
* In order to generate TFRecord file format, you need to convert your dataset to this file format.
```bash
    python3 generate_tfrecord.py --csv_input=data/train_labels.csv  --output_path=train.record
```
* The dataset (TFRecord files) and its corresponding label map. Example of how to create label maps can be found in the folder data.
  ```
  item {
  id: 1
    name: 'particle'
  }
  ```
* Configuring the Particle Detection Training Pipeline in [particle_detection/configs](particle_detection/configs)
  * Users should substitute the `input_path` and `label_map_path` arguments and insert the input configuration into the `train_input_reader` and `eval_input_reader` fields in the skeleton configuration.
  * The `train_config` defines parts of the training process:

    1. Model parameter initialization.
    2. Input preprocessing.
    3. SGD parameters.
  * In order to speed up the training process, it is recommended to reuse the pre-existing object detection checkpoint. The pre-trained checkpoints can be found [here](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md), please download the pre-trained model `faster_rcnn_resnet101_coco`. `fine_tune_checkpoint` should provide a path to the pre-existing checkpoint (ie:"/usr/home/username/checkpoint/model.ckpt-#####").


* After you created the required input file, in `research/object_detection` you can train your model.
  ```bash
  python3 train.py  --logtostderr --pipeline_config_path=/faster_rcnn_resnet101_kali.config  --train_dir=
  ```

* After training the model, you can get bounding box coordinates by running `particle_detection_bb.ipynb`.



# Particle regression
* Before you train the particle regression model you should crop the particle images to 180 x 180 fixed size images. In [particle_regression](particle_regression):
```bash
    python3 particle_crop.py
```

* You can train particle regression by running the following command in [particle_regression](particle_regression).
```bash
    python3 train_regression_pos.py
```
