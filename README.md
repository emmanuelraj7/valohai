# Update simple model 

## What news? 
1. Add Python parser to work with Valohai.yaml
2. Unzip, untar example
3. Add log output path, saved path

## Unzip, untar

Because the datasets are **".tar.gz"** format so we need to unzip it before training.

We can unzip to any folder and tell Python where exactly our training data is. 

The Docker image is based on Ubuntu so we use bash script to unzip the file.

For example I unzip the train, val, test dataset to **tmp** folder by the command: 

```css
# Create 3 folder train, test, validation:

mkdir /tmp/train 
mkdir /tmp/test
mkdir /tmp/validation 

# Untar command: 

tar -xzf /valohai/inputs/train/train-set-images.tar.gz -C /tmp/train/
tar -zxf /valohai/inputs/test/test-set-images.tar.gz -C /tmp/test/
tar -zxf /valohai/inputs/validation/validation-set-images.tar.gz -C /tmp/validation/

```

## Train path, test path, validation path: 

Python argparse example: 

```css 

#Define Argparse: 

import argparse

def get_args():
    parser = argparse.ArgumentParser("Add parser")
    parser.add_argument("--data_path", type=str, default="/valohai/inputs/")
    parser.add_argument("--saved_path", type=str, default="/valohai/outputs/")
    parser.add_argument("--log_path", type=str, default="/valohai/outputs")
    parser.add_argument("--epochs", type=int, default=20)
    parser.add_argument("--workers", type=int, default=4)
    args = parser.parse_args()
    return args

#Using parser: 
opt = get_args()

datapath = opt.data_path
logdir=os.path.join(opt.log_path,"logs","dvc-simple-"+datetime.datetime.now().strftime('%Y-%m-%d_%H-%M-%S'))

epochs = opt.epochs
workers = opt.workers

```

Then, we can **parse** the arguments from outside to run our Python script with this example command:

> python keras-dvc-cnn-simple.py --data_path /tmp/ --log_path /tmp/ --epochs 20 --workers 10

* By using Python argParse, you dont need to modify your code whenever you want to change the parameters like epochs, or change the dataset path or change the workers number.

* Also using ArgParse to interact with valohai.yaml. The **parameters** section in valohai.yaml is exactly your **Python argparse**. 

* You can check my **valohai.yaml** and _**keras-dvc-cnn-simple.py**_ file to know how it works. For example in my valohai.yaml file I defined:

```
parameters:  
- default: 20
description: Epochs
name: Minibatch size
pass-as: --epochs {v}
type: integer

- default: 4
description: Number of Worker
name: Number of worker 
pass-as: --workers {v}
type: integer

- default: /tmp/
description: Input Path to dataset
name: train path 
pass-as: --data_path {v}
type: string

```

The **"pass-as: --epochs {v} "** in the valohai.yaml file is the **"parser.add_argument("--epochs", type=int, default=20)"** in _**keras-dvc-cnn-simple.py**_ file .

## Note: 

* The _/valohai/inputs/_ folder is read- only, you can not unzip there.
* Our download dataset will not locate exactly in _/valohai/inputs/_ but it will be downloaded in seperated folder which the folder name is the **Name** you define in **valohai.yaml** file. See **inputs:** section in **valohai.yaml** file. 


Finally we have dataset and Python script ready to train. You can try to modify other Python file with argparse and valohai.yaml. Have fun! 



