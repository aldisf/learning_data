# Setting Environment Variables For Your Project 

After you create an environment using conda and activating the environment using `source activate`, you might want to set environment variables that does not conflict your global variables. 

First, you can check the location of your environment using  `echo $CONDA_PREFIX`

Then, create the following directories and files:

```
cd $CONDA_PREFIX
mkdir -p ./etc/conda/activate.d
mkdir -p ./etc/conda/deactivate.d
touch ./etc/conda/activate.d/env_vars.sh
touch ./etc/conda/deactivate.d/env_vars.sh
```


Edit `./etc/conda/activate.d/env_vars.sh` as follows (this acts similar to a ~/.bashprofile when we activate the environment):
```
#!bin/sh

export VARIABLE_1="VARIABLE_VALUE"
```


Edit `./etc/conda/deactivate.d/env_vars.sh` as follows (run when we deactivate the environment):
```
#!bin/sh

unset VARIABLE_1
```