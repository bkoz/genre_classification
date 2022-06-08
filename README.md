# genre_classification

## MLops Course 2 Exercise

### Development Environments

RHEL9
```
$ wandb --version
wandb, version 0.12.17
(python-3.8.13) [ec2-user@ip-192-168-0-7 ~]$ mlflow --version
mlflow, version 1.26.1
```

MacOS M1

### Pipeline Components

Component: download

Needed to upgrade `wandb==0.12.17` in `download/conda.yml`

Run commands:
```
mlflow run . -P hydra_options="main.execute_steps='download,preprocess,check_data,segregate,random_forest,evaluate'"

mlflow run . -P hydra_options="main.project_name='genre_classification' main.execute_steps='download,preprocess,check_data,segregate,random_forest,evaluate'"
```

`mlflow run -v version git-url` will run a branch but not a tag.

`main.py` errors w/o passing in execute_steps

```
$ mlflow run .

2022/06/07 19:34:09 INFO mlflow.utils.conda: Conda environment mlflow-0904ff4bb2e29ed699a898fa53e4cf022df5b7d8 already exists.
2022/06/07 19:34:09 INFO mlflow.projects.utils: === Created directory /var/folders/fl/7f9z_vd94h56jhb1wxtzkkvh0000gn/T/tmpppk7dcqv for downloading remote URIs passed to arguments of type 'path' ===
2022/06/07 19:34:09 INFO mlflow.projects.backend.local: === Running command 'source /Users/koz/.local/miniforge3/bin/../etc/profile.d/conda.sh && conda activate mlflow-0904ff4bb2e29ed699a898fa53e4cf022df5b7d8 1>&2 && python main.py $(echo '')' in run with ID '313c255c1ece4b2293b52a6225d2ca50' === 
Traceback (most recent call last):
  File "/Users/koz/src/github/genre_classification/main.py", line 23, in go
    assert isinstance(config["main"]["execute_steps"], list)
AssertionError

Set the environment variable HYDRA_FULL_ERROR=1 for a complete stack trace.
2022/06/07 19:34:10 ERROR mlflow.cli: === Run (ID '313c255c1ece4b2293b52a6225d2ca50') failed ===
```

### Inference testing
```
conda create --name=test python=3.9.13 mlflow wandb

wandb artifact get genre_classification/model_export:prod --root model
wandb artifact get genre_classification/my_artifact_root_test.csv:latest
  
mlflow models predict -t json -i model/input_example.json -m model
mlflow models predict -t csv -i artifacts/my_artifact_root_test.csv\:v0/my_artifact_root_test.csv -m model
```
