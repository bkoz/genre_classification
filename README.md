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

MacOS and RHEL x86_64

```
conda create --name=test python=3.9.13 mlflow wandb
conda activate test
wandb artifact get genre_classification/model_export:prod --root model
wandb artifact get genre_classification/my_artifact_root_test.csv:latest
```
```
mlflow models predict -t json -i model/input_example.json -m model

2022/06/08 17:57:42 INFO mlflow.models.cli: Selected backend for flavor 'python_function'
2022/06/08 17:57:44 INFO mlflow.utils.conda: Conda environment mlflow-68c0ec35f0d6ca2df4c3c3872e3d889149327a36 already exists.
2022/06/08 17:57:44 INFO mlflow.pyfunc.backend: === Running command 'source /home/ec2-user/.local/miniforge3/bin/../etc/profile.d/conda.sh && conda activate mlflow-68c0ec35f0d6ca2df4c3c3872e3d889149327a36 1>&2 && python -c "from mlflow.pyfunc.scoring_server import _predict; _predict(model_uri='file:///home/ec2-user/model', input_path='model/input_example.json', output_path=None, content_type='json', json_format='split')"'
["Rap", "RnB"]

```
mlflow models predict -t csv -i artifacts/my_artifact_root_test.csv\:v0/my_artifact_root_test.csv -m model
```

MacOS M1
```
mlflow models predict -t json -i model/input_example.json -m model

2022/06/08 12:53:16 INFO mlflow.models.cli: Selected backend for flavor 'python_function'
2022/06/08 12:53:17 INFO mlflow.utils.conda: === Creating conda environment mlflow-68c0ec35f0d6ca2df4c3c3872e3d889149327a36 ===
Collecting package metadata (repodata.json): done
Solving environment: - 
Found conflicts! Looking for incompatible packages.
This can take several minutes.  Press CTRL-C to abort.
failed                                                                                                                                                     
Solving environment: - 
Found conflicts! Looking for incompatible packages.
This can take several minutes.  Press CTRL-C to abort.
failed                                                                                                                                                     

UnsatisfiableError: The following specifications were found to be incompatible with each other:

Output in format: Requested package -> Available versions
Note that strict channel priority may have removed packages required for satisfiability.

Traceback (most recent call last):
  File "/Users/koz/.local/miniforge3/envs/test/bin/mlflow", line 11, in <module>
    sys.exit(cli())
  File "/Users/koz/.local/miniforge3/envs/test/lib/python3.9/site-packages/click/core.py", line 1130, in __call__
    return self.main(*args, **kwargs)
  File "/Users/koz/.local/miniforge3/envs/test/lib/python3.9/site-packages/click/core.py", line 1055, in main
    rv = self.invoke(ctx)
  File "/Users/koz/.local/miniforge3/envs/test/lib/python3.9/site-packages/click/core.py", line 1657, in invoke
    return _process_result(sub_ctx.command.invoke(sub_ctx))
  File "/Users/koz/.local/miniforge3/envs/test/lib/python3.9/site-packages/click/core.py", line 1657, in invoke
    return _process_result(sub_ctx.command.invoke(sub_ctx))
  File "/Users/koz/.local/miniforge3/envs/test/lib/python3.9/site-packages/click/core.py", line 1404, in invoke
    return ctx.invoke(self.callback, **ctx.params)
  File "/Users/koz/.local/miniforge3/envs/test/lib/python3.9/site-packages/click/core.py", line 760, in invoke
    return __callback(*args, **kwargs)
  File "/Users/koz/.local/miniforge3/envs/test/lib/python3.9/site-packages/mlflow/models/cli.py", line 125, in predict
    return _get_flavor_backend(
  File "/Users/koz/.local/miniforge3/envs/test/lib/python3.9/site-packages/mlflow/pyfunc/backend.py", line 120, in predict
    conda_env_name = get_or_create_conda_env(
  File "/Users/koz/.local/miniforge3/envs/test/lib/python3.9/site-packages/mlflow/utils/conda.py", line 214, in get_or_create_conda_env
    process._exec_cmd(
  File "/Users/koz/.local/miniforge3/envs/test/lib/python3.9/site-packages/mlflow/utils/process.py", line 94, in _exec_cmd
    raise ShellCommandException.from_completed_process(comp_process)
mlflow.utils.process.ShellCommandException: Non-zero exit code: 1
Command: ['/Users/koz/.local/miniforge3/bin/conda', 'env', 'create', '-n', 'mlflow-68c0ec35f0d6ca2df4c3c3872e3d889149327a36', '--file', '/private/var/tmp/deploy/model/conda.yaml']

```
