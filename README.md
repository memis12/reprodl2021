# Reproducible Deep Learning
## Extra: Axplatform in PyTorch for hyperparameters tuning using Bayesian optimization 
### Author: [gditeodoro](https://github.com/gditeodoro)
[[Official website](https://www.sscardapane.it/teaching/reproducibledl/)] 

This is an extra branch of the **exercise 1** to tune hyperparameters with Bayesian optimization. Adaptive experimentation is the machine-learning guided process of iteratively exploring a (possibly infinite) parameter space in order to identify optimal configurations in a resource-efficient manner, avoiding exhausting random search and grid search processes.

## Goal
Implement Bayesian optimiziation to tune hyperparameters and find automatically the optimal ones in Pytorch using [Ax](https://ax.dev/).
The details of Bayesian optimization ca be find on the [Ax website](https://ax.dev/docs/bayesopt.html)

## Prerequisites 

Install requirements: 
- Install the ax platform 
```bash
pip3 install ax-platform
```
## Instructions 

1. Import Optimize, train and evaluate function from the Ax package 
```python

from ax.service.managed_loop import optimize
from ax.utils.tutorials.cnn_utils import train, evaluate

```
2. Open the script train.py that have to be modified 
3. Modify the train function in a way that:
    -  the model is inizialized and the network is ready-to-train. The parameterization argument is a dictionary containing the hyperparameters.
    -  the train() function is called by the Baesyan optimizer on every run. A new set of hyperparameters is generated in parameterization by the optimizer, tids set is passed to        this function and the returned evaluation results are analyzed. 
    
```python
# constructing a new training data loader allows us to tune the batch size and the learning rate 
train_loader = torch.utils.data.DataLoader(train_data, batch_size=parameterization.get("batchsize", 8), shuffle=True)
val_loader = torch.utils.data.DataLoader(val_data, batch_size=parameterization.get("batchsize", 8))
test_loader = torch.utils.data.DataLoader(test_data, batch_size=parameterization.get("batchsize", 8))
audionet = AudioNet(lr=parameterization.get("lr", 1e-3))

# return the evaluation results
return evaluate(
        net=audionet,
        data_loader=test_loader,
        dtype=dtype,
        device=device,
    )
```
4. Optimize, specifing he hyperparameters you want to sweep across and pass that to Ax’s optimize() function:
```python
best_parameters, values, experiment, model = optimize(
        parameters=[
            {"name": "lr", "type": "range", "bounds": [1e-6, 0.4], "log_scale": True},
            {"name": "batchsize", "type": "range", "bounds": [8, 32]}
        ],

        evaluation_function=train,
        objective_name='accuracy',
    )
```
