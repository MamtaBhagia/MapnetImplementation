# Geometry-Aware Learning of Maps for Camera Localization - Implementation

Our work is based on the model Mapnet by NVLabs. Here is our implementation of their model on our data. You can find their documentation and code here: 
- [Documentation](https://arxiv.org/abs/1712.03342).
- [Code](https://github.com/NVlabs/geomapnet).

# Set Up
- Git clone the NVlabs repository: https://github.com/NVlabs/geomapnet.git 

- Install miniconda with Python 2.7 

- Create the mapnet conda environment: conda env create -f environment.yml 

- Activate the environment: source activate mapnet_release 

At this point, when you try to run the code you might face an error related to mk_lapack library not found. To fix this add miniconda's env lib to your LD_LIBRARY_PATH. 

# Data

NVLabs has shown their work on the data 7Scenes and the RobotCar dataset. We have created our own data of an indoor room, here at IIT Mandi using ZED.

## ZED Installation
- Download Zed SDK from [this link](https://www.stereolabs.com/developers/release/2.4/#sdkdownloads_anchor) according to the specifications of your machine. 

- Zed is by default installed in /usr/local directory.

- Go to /usr/local/zed/sample/positionaltracking/ and run following commands: 
```
mkdir build 
cd build 
cmake .. 
Make
``` 

- Go to /usr/local/zed/sample/svorecording/export/ and run following commands: 

```
mkdir build
cd build
cmake ..
Make
```
## Making Training Data from the Videos
- You can download our svo files from these links : [one](https://cloud.iitmandi.ac.in/d/f05c0fd62b/),[two](https://cloud.iitmandi.ac.in/d/749976caa4/),[three](https://cloud.iitmandi.ac.in/d/39c9af63fc/). 

- Put the input svo files in the /mnt/grounddata/ and rename the files as x.svo where x is the two-digit number starting from 00 up to number of total svo files. 

- Go to /usr/local/zed/sample/svorecording/export/build and run the following command: 

```
./extract.sh 
```

- Go to /usr/local/zed/sample/positionaltracking/build and run following commands: 

```
./ext,sh
python3 do.py
```

- You can download these codes and copy them to the given address from [this link](https://cloud.iitmandi.ac.in/d/e8a33a5266/). 

- This will save the groundtruth data in the home folder of user in the folder named  "grounddata" and for each input file will a folder named "finalx" will be created where x is the two-digit number starting from 00 upto number of total svo files. 

- Put the training sequence from ~/grounddata/ to ~/geomapnet/7Scenes/heads/seq-02.

- Put the test sequence from ~/grounddata/ to ~/geomapnet/7Scenes/heads/seq-01.

# Running the Code

The downloaded models are to be stored in geomapnet/scripts/logs. 

The original trained models of NVLab's geomapnet can be downloaded from [this drive](https://drive.google.com/drive/folders/1J2QG_nHrRTKcDf9CGXRK9MWH1h-GuMLy). 

Our re-trained models can be downloaded from [here](https://cloud.iitmandi.ac.in/d/55996b1903/). 

To create symlinks between your 7Scenes directory and where the code looks for the 7Scenes data use this: 

```
cd data/deepslam_data && ln -s 7SCENES_DIR 7Scenes
```

Please go to the scripts folder to run the code. 

The command to run Mapnet on 7Scenes heads dataset is:

```
$ python eval.py --dataset 7Scenes --scene heads --model mapnet \
--weights logs/7Scenes_heads_mapnet_mapnet_learn_beta_learn_gamma/epoch_250.pth.tar \
--config_file configs/mapnet.ini --val 
```

Remove the --val flag to run the code on train data. 

The command to run our trained model is: 
```
$ python eval.py --dataset 7Scenes --scene heads --model mapnet \ 
--weights logs/7Scenes_heads_mapnet_mapnet_learn_beta_learn_gamma/loungemodel_mapnet_skips15_freeze0.tar \ 
--config_file configs/mapnet.ini --val 
```

# Training

Before training on any dataset, you must run the code dataset_mean.py. For the dataset 7Scenes and the scene heads, the command is: 

```
$ python dataset_mean.py --dataset 7Scenes --scene heads 
```

The executable training script is scripts/train.py. 

The command to train mapnet on 7scenes heads data set, with pre-trained weights is (run this command from the scripts folder): 

```
$ python train.py --dataset 7Scenes --scene heads --config_file configs/mapnet.ini --checkpoint logs/7Scenes_heads_mapnet_mapnet_learn_beta_learn_gamma/epoch_250.pth.tar --model mapnet --device 0 --learn_beta --learn_gamma 
``` 

For best results of training on our data, please use the config file available [here](https://cloud.iitmandi.ac.in/d/e8a33a5266/). It corresponds to loungemodel_mapnet_skipsvariable_dropout50_freeze0.tar model and should be added to scripts/configs. 
