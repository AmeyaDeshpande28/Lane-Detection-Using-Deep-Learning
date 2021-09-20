# Lane Detection using Deep Learning

### Probelm Statement
Implement Deep Learning algorithm to detect lanes on the street images.

### Steps
#### Data Preparation
For this project a [part of BDD100K Lane Marking Dataset](https://s3.amazonaws.com/assignment-lane-marking-data/data.zip) was used (501 images of size 1280x720). The dataset was divided manually into three mutually exclusive substes - train (300 images), val (100 images) and test (101 images). Data from each subset was bundled into .npy files (Refer - DataPreparation.ipynb).

#### Deep Learning model selection
Although there are lot of choices to select from, since the underlying probelm is a segementation problem, a Unet based model was a preffered choice. The standard Unet was modified to reduce the model footprint which in turn decreases the inference time as well. This will be helpfull especially while doing inference on embedded/mobile platforms.

In unet, the standard convolutions were replaced by depthwise convolutions to reduce the number of trainable parameters. Here, I would like to name the modified unet as **Unet_depthWise**. To compare the number of parameters, standard unet (with same number of filters as those of used in the current model) will have 31 million parameters while the Unet_depthWise has 2.3 million parameters. 

References : [Unet architecture](https://github.com/zhixuhao/unet), [MobileNet](https://github.com/Zehaos/MobileNet)

#### Training
The **Unet_depthWise** was trained using an Adam optimizer with custom learning rate scheduler. Each data point (i.e an image) was normalised by subtracting data mean and dividing by data standard deviation. This was done as the images were captured into varying light conditions. By normalising this way, each data point will follow a Noraml Distribution.

In the first iteration of training (trained for around 150 epoch), the model was overfitting. In the second iteration, a model was chosen from the first iteration as a **pre-trained model** and the training was carried out by modifying learning rate schedular.

In each training epoch, categorical cross entropy loss, dice score and foreground dice score were monitored. 

A final model used for inferencing is available [here](https://drive.google.com/file/d/1prgT3GHdz7MRsq6QyyE7VFhFcyRDT9GH/view?usp=sharing)

#### Inferencing and Evalution
The model was infered on all three subsets - train, val and test to get scores on each subset.
|  | Train | Val | Test |
| :---: | :---: | :---: | :---: |
| Mean Dice | 0.8084 | 0.7011 | 0.6785 |
| Foreground Dice | 0.6223 | 0.4101 | 0.3654 |

