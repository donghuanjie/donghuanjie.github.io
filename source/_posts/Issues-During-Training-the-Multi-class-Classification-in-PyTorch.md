---
title: Issues of Training the Multi-class Classification in PyTorch
date: 2024-02-04 16:53:18
categories:
  - [Deep Learning]
tags:
  - Multi-class Classification
  - PyTorch Basics
---

### Idea

This post includes some different problems I encountered during the training process of multi-class classification problems using PyTorch. It is used to remind me of some concepts and issues handling methods might happen again in the future.

### Code

#### Create the data with preprocessing

During the preprocessing, we need to notice that the `y_blob` is assigned to be `LongTensor` because in PyTorch, when using the `nn.CrossEntropyLoss` for computing the loss, the target tensor (label) must be of type `torch.long`. This is because the loss function expects the target tensor to contain class indices as `long` integer to deal with large range of classification labels. **`torch.nn.CrossEntropyLoss` require label tensor to be LongTensor**.

```python
import torch
import matplotlib.pyplot as plt
from sklearn.datasets import make_blobs
from sklearn.model_selection import train_test_split

device = "cuda" if torch.cuda.is_available() else "cpu"

NUM_CLASSES = 4
NUM_FEATURES = 2
RANDOM_SEED = 42

# create multiclass data
X_blob, y_blob = make_blobs(n_samples = 1000,
                            n_features=NUM_FEATURES,
                            centers=NUM_CLASSES,
                            cluster_std=1.5,
                            random_state=RANDOM_SEED)

# transform from numpy arrays to tensors
X_blob = torch.from_numpy(X_blob).type(torch.float) 
y_blob = torch.from_numpy(y_blob).type(torch.LongTensor) # must be long type because loss functions do not accept float indices

# split the data
X_blob_train, X_blob_test, y_blob_train, y_blob_test = train_test_split(X_blob,
                                                                        y_blob,
                                                                        test_size=0.2,
                                                                        random_state=RANDOM_SEED)

# plot the data
plt.figure(figsize=(10, 7))
plt.scatter(X_blob[:, 0], X_blob[:, 1], c=y_blob, cmap=plt.cm.RdYlBu)
```

#### Build the model

We can define the constructor to have multiple parameters explicitly, but only the `input_features` is needed during the training because `forward` function takes only one parameter. 

```python
class BlobModel(nn.Module):
    def __init__(self, input_features, output_features, hidden_units=8):
        super().__init__()
        self.linear_layer_stack = nn.Sequential(
            nn.Linear(in_features=input_features, out_features=hidden_units),
            nn.Linear(in_features=hidden_units, out_features=hidden_units),
            nn.Linear(in_features=hidden_units, out_features=output_features),
        )
    
    def forward(self, x):
        return self.linear_layer_stack(x)

model_4 = BlobModel(input_features=NUM_FEATURES,
                    output_features=NUM_CLASSES,
                    hidden_units=8).to(device)
```

#### Define loss function and optimizer

```python
# CrossEntropyLoss is probably the only choice for multi-classification problem
loss_fn = nn.CrossEntropyLoss()

# the most common optimizers are SGD and Adam
optimizer = torch.optim.SGD(params=model_4.parameters(), lr=0.01) 
```

#### Train the model

Note here, the `nn.CrossEntropyLoss()` only accepts the logits input (which means it does not want the value after softmax). However, we still have a `y_pred` after softmax because we need it to calcualte the accuracy.

ALso note very important thing here, `dim=1` means we want to calculate the metrics by rows, based on columns, which means our `softmax` and `argmax` function are all getting the results from each row, and doing calculation based on the columns. `dim=1` literally stands for ***"given the row not changed, get the result from different columns in that row"***.

```python
torch.manual_seed(42)
torch.cuda.manual_seed(42)

X_blob_train, X_blob_test = X_blob_train.to(device), X_blob_test.to(device)
y_blob_train, y_blob_test = y_blob_train.to(device), y_blob_test.to(device)

epochs = 1000

for epoch in range(epochs):
    model_4.train()
    
    y_logits = model_4(X_blob_train)
    y_pred = torch.softmax(y_logits, dim=1).argmax(dim=1) # note here

    loss = loss_fn(y_logits, y_blob_train)
    acc = accuracy_fn(y_true=y_blob_train, y_pred=y_pred)

    optimizer.zero_grad()
    loss.backward()
    optimizer.step()

    # test
    model_4.eval()
    with torch.inference_mode():
        test_logits = model_4(X_blob_test)
        test_pred = torch.softmax(test_logits, dim=1).argmax(dim=1) # note here

        test_loss = loss_fn(test_logits, y_blob_test)
        acc = accuracy_fn(y_true=y_blob_test, y_pred=test_pred)

    if epoch % 100 == 0:
        print(f"Epoch: {epoch} | Loss: {loss:.4f}, Acc: {acc:.2f}% | Test Loss: {test_loss:.4f}, Test Acc: {test_acc:.2f}%")
```

#### Evaluate the model

```python
model_4.eval()
with torch.inference_mode():
    y_logits = model_4(X_blob_test)

# remember to manually activate the logits by applying softmax and argmax
y_pred_probs = torch.softmax(y_logits, dim=1)
y_preds = torch.argmax(y_pred_probs, dim=1)

plt.figure(figsize=(12,6))
plt.subplot(1,2,1)
plt.title("Train")
plot_decision_boundary(model_4, X_blob_train, y_blob_train)
plt.subplot(1,2,2)
plt.title("Test")
plot_decision_boundary(model_4, X_blob_test, y_blob_test)
```

{% img evaluation /images/pytorch/classification/result.png 800 400 "evaluation" "alt"' %}

We can find from the evaluation plot that the model divides the points into 4 parts by 4 **straight** lines. That is because we only used linear layers in our model, and their combination will still be linear, which means in a 2D plane there will be only 1 line. However, since we introduced a softmax function and agrmax function, the model has the ability to draw 4 different lines based on the score of 4 outputs in each observation to seperate the points. The the softmax and argmax functions can be regarded as introducing a **score** baseline for different points being divided into different groups. Though softmax is not linear, it only introduce a function to divide the results but won't have any effect on non-linear decision boundary. That is why there is no curve in the evaluation plot.