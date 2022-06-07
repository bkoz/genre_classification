# genre_classification
MLops Course 2 Exercise


Component: download

Needed to upgrade `wandb==0.12.17` in `download/conda.yml`

Run commands:
```
mlflow run . -P hydra_options="main.execute_steps='download'"

mlflow run . -P hydra_options="main.execute_steps='download,preprocess,check_data,segregate,random_forest,evaluate'"
```

