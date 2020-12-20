# About

https://realpython.com/python-data-version-control/

## Setup

```
$ python3 -m venv venv
$ source venv/bin/activate
$ pip install -r requirements.txt
```
## Imagenette Dataset

Imagenette is a subset of 10 easily classified classes from Imagenet (tench, English springer, cassette player, chain saw, church, French horn, garbage truck, gas pump, golf ball, parachute).

The '320 px' and '160 px' versions have their shortest size resized to that size, with their aspect ratio maintained.

Download 160px dataset from https://github.com/fastai/imagenette, which is about 100M.

Run the following commands to download the data to the correct folders:

```
chmod +x get-data.sh
./get-data.sh
```

## Folder Structure

```
data-version-control/
|
├── data/
│   ├── prepared/
│   └── raw/
│       ├── train/
│       │   ├── n01440764/
│       │   ├── n02102040/...
|       |
│       └── val/
│           ├── n01440764/
│           ├── n02102040/...
|
├── metrics/
├── model/
└── src/
    ├── evaluate.py
    ├── prepare.py
    └── train.py
```

There are six folders in your repository:

src/ is for source code.
data/ is for all versions of the dataset.
data/raw/ is for data obtained from an external source.
data/prepared/ is for data modified internally.
model/ is for machine learning models.
data/metrics/ is for tracking the performance metrics of your models.
The src/ folder contains three Python files:

prepare.py contains code for preparing data for training.
train.py contains code for training a machine learning model.
evaluate.py contains code for evaluating the results of a machine learning model.

## Pipleline and Workflow

Follow the commands below to go over the entire DVC pipeline and workflow
```
$ git checkout -b "first_experiment"
$ dvc init
$ dvc config core.analytics false
$ dvc remote add -d remote_storage /Users/harrywang/sandbox/dvc-remote 
$ dvc add data/raw/train
$ dvc add data/raw/val
$ git add --all
$ git commit -m "First commit with setup and DVC files"
$ dvc push # push data to remote storage
$ git push --set-upstream origin first_experiment
```

Now, you data is cached and backed up in the remote storage

```
$ rm -rf data/raw/val
$ dvc checkout data/raw/val.dvc # get data back 
$ dvc pull # fetch + checkout, get all data 
```

Machine learning pipeline part:

```
$ python src/prepare.py # create train.csv and test.csv
$ dvc add data/prepared/train.csv data/prepared/test.csv # add csv to dvc
$ git add --all # commit dvc files
$ git commit -m "Created train and test CSV files"
$ python src/train.py # train the model and save it in model/model.joblib
$ dvc add model/model.joblib
$ git add --all
$ git commit -m "Trained an SGD classifier"
$ python src/evaluate.py # evaluate the model and save the result to metrics/accuracy.json
$ git add --all
$ git commit -m "Evaluate the SGD model accuracy"
```