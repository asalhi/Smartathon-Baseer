# BASEER

Baseer is a developed automated system that locates visual pollution by relying on artificial intelligence techniques to analyze graphics and extract data.


**This is the repository that will contain all training and inference codes related to theme 1 in Smarathon Challange, this includes diffrent phases.**

## Web App
Here you can find the web app developed on top of the models: [https://github.com/haya-alwarthan/visuallpoll-backend/](https://github.com/haya-alwarthan/visuallpoll-backend/)

## Mobile App
Here you can find the mobile app developed on the top of the models :
[https://drive.google.com/file/d/1wGX3LYnrW2LbjuTj4_vh6LDI975ccGHl/view
](https://drive.google.com/file/d/1wGX3LYnrW2LbjuTj4_vh6LDI975ccGHl/view
)

## Models Weights:
For Models weights you can access them from here : 

https://drive.google.com/drive/folders/1BM6LsH-6JWxn51UYXG1PKgZW7lxKvFQw?usp=sharing


## Training Phases: 

**Training Phase 1: (Training a Detectron2 Model) :**
1. The train data were preprocessed  as follows:
- The Boxes were multiplied by 2 to fix their location.
- Any Box boundary located outside the image frame is converged to the image frame
- All boxes are scaled down by 2 in order to fit the objects better.

2. The processed train data is converted to a coco-format to fit with detectron2 Models. 
3. The data is split into 85% training data and 15% validation data. 
4. The training process was done by 25000 iterations, with a checkpoint for every 250 iterations. 
The best mAP@50 (Validation) was: 35.81%

**Note:** For future work, it's better to use Using the k-Fold technique.
DetectronV2 object detection baseline is selected, we have tested different baselines such as R50-FPN, R101-FPN, X101-FPN, R50-C4, VOC
X101-FPN was the best. 

**Training Phase1 Chart**

<img src="https://github.com/asalhi/Smartathon-Baseer/raw/main/images/phase1.png" height="800">

**Training Phase 2: (Extending Detectron2 Training)**
1. The training images and their coco-format annotated were uploaded to roboflow.com to split and apply augmentation over the data. 
2. The data is split (same split and content as phase one to avoid overfitting)  into 85% training data (7080 images) with flip horizontal, saturation - between -25% and +25% which generated around 21240 images. 
3. The other split (15%) was the validation data.
4. Both data folders were fed to DetectronV2.
5. This time we started from the best checkpoint weights (from phase one) and trained for another 20000 iterations with a checkpoint at every 250 iterations.
6. The best mAP@50 (Validation) was: 51.42 (an increase by 15.61%)
7. The inference (over test data)  of the above training process was a score of  mAP: 61.55%

**Training Phase2 Chart**

<img src="https://github.com/asalhi/Smartathon-Baseer/raw/main/images/phase2.png" height="800">

**Training Phase 3 ( Training Yolov5 x model):**

1. The data which is uploaded to [roboflow](https://roboflow.com/) is split without augmentation to training and validation datasets.
2. The data is converted to Yolov5 format.
3. The training process is done with 75 epochs with Yolov5 x model
4. An inference is directly done with the trained model and a small edit in model input image size (downscaling it for better predictions).
5. The results of the testing dataset are: mAP 59.47%

**Note:** this training is not related to detectron training, in the end, we will do ensembling between their results to get better predictions.

**Training Phase3 Chart**

<img src="https://github.com/asalhi/Smartathon-Baseer/raw/main/images/phase3.png" height="800">

**Training Phase 4 ( Training Yolov8 x model):**

1. The data which is uploaded to roboflow is split without augmentation to training and validation datasets
2. The data is converted to Yolov8 format.
3. The training process is done with 75 epochs with Yolov8 x model
4. An inference is directly done with the trained model and a small edit in model input image size (downscaling it for better predictions).
5. The result of the testing dataset is: mAP 57.67%

**Training Phase4 Chart**

<img src="https://github.com/asalhi/Smartathon-Baseer/raw/main/images/phase4.png" height="800">

**Phase5 : (Ensemble Detectron model, yolov5/yolov8 models together)**

1. Now we have three models that might have different box boundaries for objects detected, so one of the approaches to get the best of everything is to ensemble them.
2. We will be using [Weighted boxes fusion](https://github.com/ZFTurbo/Weighted-Boxes-Fusion), it's a library that ensemble different object detection models and find the best boxes for each detected object.
3. Each model's result is unified to fit the needs of Weighted boxes fusion.
4. Different thresholds are tested.
5. The inference results(over the test data) based on weighted boxes fusion ensembling are increased to reach mAP 65.12% (using detectronV2 with Yolov5).

**WBF (Detectron2 with Yolov5**

<img src="https://github.com/asalhi/Smartathon-Baseer/raw/main/images/phase5.png" height="800">

**Something we tried and didn't help:**
We tried to teach a kNeighbirsRegressor the relationship between images latitude and longitude and thier class (since images have latitude and longitude information ), we thought that this will help give a predicted class to the non-labeled images.
The approach didn't help in improving the results.

**A nice booster:**
For any image that has no labels in prediction, we add a box in the image with "Garbage" as a class, this helped improve the score(we selected "Garbage" becuase its the most frequent class)











