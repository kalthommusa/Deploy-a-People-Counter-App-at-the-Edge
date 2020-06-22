# Project Write-Up

I run this project  on the Udacity workspace that worked on Python 3.6 and openvino 2019 r3


## Model used:

* I converted the tensorflow model "ssd_mobilenet_v2_coco_2018_03_29" into an IR format for generating .bin and. xml files using Intel openvino model optimizer, using these commands after source the envornimont in Udacity workspace:

- Download the ssd_mobilenet_v2_coco_2018_03_29 model on the Udacity workspace:
``
wget http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v2_coco_2018_03_29.tar.gz
``

- Extract the model:
``
tar -xvf ssd_mobilenet_v2_coco_2018_03_29.tar.gz
``

- Navigate to the model directory:
``
cd ssd_mobilenet_v2_coco_2018_03_29
``

- Convert the pb to a .bin and xml files using the model optimizer
``
python /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model frozen_inference_graph.pb --tensorflow_object_detection_api_pipeline_config pipeline.config --reverse_input_channels --tensorflow_use_custom_operations_config /opt/intel/openvino/deployment_tools/model_optimizer/extensions/front/tf/ssd_v2_support.json
``


* Run the project:

``
python main.py -i resources/Pedestrian_Detect_2_1_1.mp4 -m ssd_mobilenet_v2_coco_2018_03_29/frozen_inference_graph.xml -l /opt/intel/openvino/deployment_tools/inference_engine/lib/intel64/libcpu_extension_sse4.so -d CPU -pt 0.6 | ffmpeg -v warning -f rawvideo -pixel_format bgr24 -video_size 768x432 -framerate 24 -i - http://0.0.0.0:3004/fac.ffm
``



## Explaining Custom Layers

* What is Supported Layers?

The Intel® Distribution of OpenVINO™ toolkit supports neural network model layers in multiple frameworks including TensorFlow, Caffe, MXNet, Kaldi and ONYX. The list of known layers is different for each of the supported frameworks. 

* What is Custom Layers?

Custom layers are layers that are not included in the list of known layers. If your topology contains any layers that are not in the list of known layers, the Model Optimizer classifies them as custom. 

The process behind coverting custom layers involves with two parts. One with the Model Optimizer and the other with the Inference Engine, In my case here, I didn't need to convert any custom layers for the chosen tensorflow model.

Here is a [documentation](https://docs.openvinotoolkit.org/2019_R3.1/_docs_HOWTO_Custom_Layers_Guide.html) for more information.

## Comparing Model Performance


| Comparison term | Pre-Conversion (pain TF) | Post-Conversion (IR TF) |
| ------------- | ------------- | ----------|
| Size | 69.68 MB  | 67.38 MB |
| Speed | 27 ms  | 61 ms |



## Assess Model Use Cases

In these days that we are trying to take healthy precautions during COVID pandemic, one of the potential use cases of the people counter app is that it could be used as an automatic counting system for customers in Restaurants, cafes, salons and gyms for monitoring the number of customers in certin area and keep it at the recommended safe number, which can help achieve social distancing.


## Assess Effects on End User Needs

Lighting, model accuracy, and camera focal length/image size have different effects on a
deployed edge model. The potential effects of each of these are as follows...
Bad lighting can make the model's precision drop and make incorrect predictions in our edge app, the same thing happens when we want to identify people or objects in the dark. A model with low precision will result in incorrect predictions, therefore the more precision is better.Angle of view of camera is based on camera focal length. Low focal length camera gives you wider angle, can useful in indoor applications & based on customer requirements. High focal length cameras are good outdoor application but model will extract less information about objects.

