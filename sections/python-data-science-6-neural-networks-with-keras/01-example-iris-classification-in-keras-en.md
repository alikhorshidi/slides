# Example: iris classification in keras

<!-- duplicate -->

## Supervised learning in keras

steps:

- create an input array `x` and a target array `y`
- build a model from various layers (e.g. preprocessing layers, neural layers, ...)
- compile via `model.compile()` and "learn" via `model.fit(x, y)`
- predict more results via `model.predict(...)`

## Example

loading data:

```py
from sklearn import datasets

iris = datasets.load_iris()

x = iris.data
y = iris.target
```

## Example

Training the neural network:

```py
import keras

model = keras.Sequential([
    keras.layers.Dense(12, activation="relu"),
    keras.layers.Dense(3, activation="softmax")
])
model.compile(
    loss="sparse_categorical_crossentropy",
    metrics=["accuracy"]
)
model.fit(x, y, epochs=100, validation_split=0.1)
```

## Example

Applying classification to new data:

```py
test_data = [
    [5.3, 3.4, 1.9, 0.6],
    [6.0, 3.0, 4.7, 1.5],
    [6.5, 3.1, 5.0, 1.7]
]

y_pred = model.predict(test_data)
# [[1.  0.  0. ]
#  [0.  0.8 0.2]
#  [0.  0.7 0.3]]
```
