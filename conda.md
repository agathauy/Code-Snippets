- [Create an env](#create-an-env)
- [List envs](#list-envs)
- [Activate env](#activate-env)
- [Clone env](#clone-env)

https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-with-commands
# Create an env

```
conda create --name myenv
conda create -n myenv python=3.4
```

From yml file
```
conda env create -f environment.yml
```

# List envs
```
conda env list
```

# Activate env
```
conda activate myenv
```

# Clone env
```
conda create --name myclone --clone myenv
```