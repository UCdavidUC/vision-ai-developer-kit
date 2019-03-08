# TensorFlow MobileNet V1 and V2 SSD Models

### MobileNet V2 SSD model pre-trained on the COCO dataset: ssd_mobilenet_v2_coco
  - Download and expand the pre-trained [**ssd_mobilenet_v2_coco model**](http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v2_coco_2018_03_29.tar.gz).
  - Copy the extracted **frozen_inference_graph.pb** to **sample-solution-1\MachineLearning\models\ssd_mobilenet_v2_coco** folder.
  - Overwrite **current_config.py** by **ssd_mobilenet_v2_coco.py** in **sample-solution-1\MachineLearning\scripts\model_configs** folder.
  - Launch **Visual Studio Code** and execute **01-convert-model-containerize.py** in **sample-solution-1\MachineLearning\scripts** folder to convert model, create a container image and generate **deployment.json** for deploying the pre-trained **ssd_mobilenet_v2_coco** model.

### Sample to retrain a MobileNet V1 SSD model with a custom dataset poker3
  - **poker3** folder is a custom dataset and includes:
    - **image\*.tfrecord** files in **poker3\data\train** and **poker3\data\test** folders are the train and the test data used to train a new **ssd_mobilenet_v1 model**. They are generated by [**Microsoft VoTT: Visual Object Tagging Tool v1.7.2**](https://github.com/Microsoft/VoTT).
    - **tf_label_map.pbtxt** in **poker3\config** folder is the labels file for **poker3** dataset and there are **3** object classes: **Ace**, **Seven**, and **King**.
    - **ssd_mobilenet_v1_coco.config** in **poker3\config** folder is modified from https://github.com/tensorflow/models/blob/r1.5/research/object_detection/samples/configs/ssd_mobilenet_v1_coco.config file.
    - **object_detection_ssdv1_retrain.ipynb** in **poker3** folder is modified from https://github.com/tensorflow/models/blob/r1.5/research/object_detection/object_detection_tutorial.ipynb file and used to evaluate the accruacy for the retrained **frozen_inference_graph.pb**.
    - **detection1.jpg** and **detection2.jpg** in **test_results** folder are the detection results for the retraind **ssd_mobilenet_v1** model with **poker3** dataset.
  - Steps to retrain **poker3** dataset under **Windows 10**:
    1. Create a new Windows 10 v1809 Azure VM or prepare a clean installed Windows 10 environment.
    2. Install [Anaconda Python 3.6.5](https://repo.anaconda.com/archive/Anaconda3-5.2.0-Windows-x86_64.exe) and add Anaconda path to system **Path** environment variable.
    3. Launch **Anaconda Prompt** and execute the following commands:
        ```<language>
         pip install msgpack
         pip install tensorflow==1.5.0
         conda install -c anaconda protobuf
         ```
    4. Download or clone TensorFlow models [**r1.5 branch**](https://github.com/tensorflow/models/tree/r1.5).
    5. Rename **models-r1.5** to be **models** and copy the full **models** foder to a new folder, for example: **c:\tf**.
    6. Add the following system environment variable:
        ```<language>
         PYTHONPATH=c:\tf\models;c:\tf\models\research;c:\tf\models\research\slim
         ```
    7. Launch **Anaconda Prompt**, change directory to **c:\tf\models\research**, and execute the following commands:
        ```<language>
        protoc --python_out=. .\object_detection\protos\anchor_generator.proto .\object_detection\protos\argmax_matcher.proto .\object_detection\protos\bipartite_matcher.proto .\object_detection\protos\box_coder.proto .\object_detection\protos\box_predictor.proto .\object_detection\protos\eval.proto .\object_detection\protos\faster_rcnn.proto .\object_detection\protos\faster_rcnn_box_coder.proto .\object_detection\protos\grid_anchor_generator.proto .\object_detection\protos\hyperparams.proto .\object_detection\protos\image_resizer.proto .\object_detection\protos\input_reader.proto .\object_detection\protos\losses.proto .\object_detection\protos\matcher.proto .\object_detection\protos\mean_stddev_box_coder.proto .\object_detection\protos\model.proto .\object_detection\protos\optimizer.proto .\object_detection\protos\pipeline.proto .\object_detection\protos\post_processing.proto .\object_detection\protos\preprocessor.proto .\object_detection\protos\region_similarity_calculator.proto .\object_detection\protos\square_box_coder.proto .\object_detection\protos\ssd.proto .\object_detection\protos\ssd_anchor_generator.proto .\object_detection\protos\string_int_label_map.proto .\object_detection\protos\train.proto .\object_detection\protos\keypoint_box_coder.proto
        ```
        ```<language>
        python setup.py build
        python setup.py install
        ```
    8. Download and expand [**ssd_mobilenet_v1_coco model**](http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v1_coco_2017_11_17.tar.gz).
    9. Copy the extracted **ssd_mobilenet_v1_coco_2017_11_17** folder to **c:\tf\models\research\objection_detection** folder.
    10. Copy **poker3** folder to **c:\tf\models\research\objection_detection** folder.
    11. Launch **Anaconda Prompt**, change directory to **c:\tf\models\research\objection_detection** and execute the following command to retrain a new **ssd_mobilenet_v1** model with **poker3** dataset:
        ```<language>
        python train.py --logtostderr --train_dir=./poker3/ckpt/ --pipeline_config_path=./poker3/config/ssd_mobilenet_v1_coco.config
        ```
    12. Launch **Anaconda Prompt**, change directory to **C:\tf\models\research\objection_detection** and execute the following command to generate a new **frozen_inference_graph.pb** in **.\poker3\frozen_graph** folder (rename **model.ckpt-10000** to be the **model.ckpt-xxx** file generated in **.\poker3\ckpt** folder):
        ```<language>
        python export_inference_graph.py --input_type image_tensor --pipeline_config_path ./poker3/config/ssd_mobilenet_v1_coco.config --trained_checkpoint_prefix ./poker3/ckpt/model.ckpt-10000 --output_directory ./poker3/frozen_graph
        ```
  - Deploy the new **ssd_mobilenet_v1_retrain_poker3** model:
    - Copy the new **frozen_inference_graph.pb** generated by the above retrain steps to Visual Studio Code sample **sample-solution-1\MachineLearning\models\ssd_mobilenet_v1_retrain_poker3** folder.
    - Overwrite **current_config.py** by **ssd_mobilenet_v1_retrain_poker3.py** in **sample-solution-1\MachineLearning\scripts\model_configs** folder.
    - Launch **Visual Studio Code** and execute **01-convert-model-containerize.py** in **sample-solution-1\MachineLearning\scripts** folder to convert model, create a container image and generate **deployment.json** for deploying the new **ssd_mobilenet_v1_retrain_poker3** model.



    