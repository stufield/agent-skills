# Chapter 10 — Deep Learning

Neural networks have a long history (the perceptron is from 1958), but they
became dominant only in the 2010s, when three things converged: large labeled
datasets, GPU computing, and clever regularization tricks (dropout, batch
norm, ReLU). Today, deep learning is the default for problems with
**unstructured** data — images, audio, text, video — and increasingly for
tabular data as well.

This chapter develops the ideas from a standard regression starting point,
shows the modern building blocks (convolution, recurrence, transformers), and
walks through three labs: a fully connected network for tabular data, a CNN
for image classification, and an RNN for time series.

## 10.1 Single Layer Neural Networks

Start with a single hidden layer. With p inputs X₁, …, X_p and K hidden units:

$$ A_k = h_k(X) = g\left(w_{k0} + \sum_{j=1}^{p} w_{kj} X_j\right), \quad k = 1, \ldots, K, $$
$$ f(X) = \beta_0 + \sum_{k=1}^{K} \beta_k A_k. $$

The hidden activations A_k are nonlinear transformations of linear
combinations of X. The output f(X) is a linear combination of the hidden
activations. The activation function g is the only thing keeping this from
being a complicated linear model.

### Activation functions

**Sigmoid**: g(z) = 1/(1 + e^{−z}). Smooth S-shape from 0 to 1. Used in early
networks but has the **vanishing gradient** problem: outside ±5 or so the
gradient is essentially zero, so backpropagation can't update earlier layers.

**Hyperbolic tangent (tanh)**: g(z) = (e^z − e^{−z})/(e^z + e^{−z}). Like
sigmoid but ranges from −1 to 1. Slightly better than sigmoid because it's
zero-centered.

**Rectified Linear Unit (ReLU)**: g(z) = max(0, z). The default for hidden
layers in modern networks. Cheap to compute, no vanishing gradient on the
positive side, and induces sparsity (many activations are zero). The
"dying ReLU" problem — neurons stuck at 0 — is mitigated by **leaky ReLU**
(g(z) = max(αz, z) for small α).

**Softmax** (for K-class classification output):
$$ f_k(X) = \frac{e^{Z_k}}{\sum_{l=1}^{K} e^{Z_l}}, \quad Z_k = \beta_{k0} + \sum_{m} \beta_{km} A_m. $$
The softmax outputs sum to 1 and can be interpreted as probabilities.

**Linear** (for regression output): no transformation, ŷ = Z.

### Universal approximation

A single-hidden-layer network with enough hidden units can approximate any
continuous function arbitrarily well. So why bother with deep networks?
Because "enough" is often astronomical. **Depth is a far more efficient way
to add capacity than width**: a deep network with modest width can represent
functions that would require an exponentially wide shallow network.

## 10.2 Multilayer Neural Networks

Stack L hidden layers. Layer ℓ has K_ℓ units. The activations are

$$ A^{(\ell)}_k = g\left(w^{(\ell)}_{k0} + \sum_{j=1}^{K_{\ell-1}} w^{(\ell)}_{kj} A^{(\ell-1)}_j\right), $$

with A^{(0)} = X (the input). The output is f(X) = β_0 + Σ_k β_k A^{(L)}_k for
regression, or a softmax over the final layer for classification.

### Why deep?

In a CNN trained on images, layer 1 learns simple features (edges, color
blobs), layer 2 combines those into textures, layer 3 into object parts,
deeper layers into objects. Each layer composes features from the previous
layer. This **hierarchical composition** is what gives deep networks their
power. Empirically, deeper networks (with appropriate regularization) almost
always beat shallow ones.

### Training: stochastic gradient descent and backpropagation

The loss for a regression network is

$$ L = \frac{1}{n} \sum_{i=1}^{n} (y_i - f(x_i))^2. $$

For classification (with softmax) the loss is **cross-entropy**:

$$ L = -\frac{1}{n} \sum_{i=1}^{n} \sum_{k=1}^{K} y_{ik} \log f_k(x_i), $$

where y_{ik} = 1 if observation i is in class k, else 0.

Total parameters in the network: tens of thousands to billions. Gradient
descent: compute ∂L/∂w for every weight w, take a small step in the negative
direction. Repeat until convergence (or until you stop improving).

**Backpropagation** computes the gradient efficiently. Forward pass:
compute activations layer by layer. Backward pass: apply the chain rule from
the output back to the inputs, accumulating partial derivatives. The
algorithm is O(network size) per training example — same as the forward
pass.

**Stochastic gradient descent (SGD)**: instead of computing the gradient on
the full dataset, use a **mini-batch** (32, 64, 128 observations). The
mini-batch gradient is a noisy estimate of the full gradient, and the noise
acts as implicit regularization.

One full pass through the dataset is an **epoch**. Typical training: 50 to
1,000 epochs.

**Adam optimizer**: an adaptive variant of SGD that keeps per-parameter
running averages of gradients and squared gradients. Excellent default.

### Regularization

**Weight decay**: add λ Σ w² to the loss (ridge regularization on weights).

**Dropout**: during training, randomly set a fraction (e.g., 0.5) of
activations to 0. The network can't rely on any one neuron, so it learns
redundant representations — equivalent to averaging an ensemble of thinned
networks. At test time, all neurons fire and weights are scaled.

**Early stopping**: monitor validation loss; halt training when it plateaus
or starts climbing. Implicit regularization that costs nothing.

**Batch normalization**: normalize each layer's activations within each
mini-batch to mean 0 and SD 1, then learn a scale and shift. Stabilizes
training, allows higher learning rates, and provides mild regularization.

**Data augmentation** (for images): random crops, flips, rotations, color
jitter. Vastly expands the effective training set without new labels.

## 10.3 Convolutional Neural Networks

For image data, the input is a 3D tensor (height × width × channels). A
fully connected network would treat each pixel as an independent feature —
ignoring the spatial structure. Two key ideas make convolutional networks
the right choice:

**Local connectivity**: a neuron in layer 1 sees only a small spatial
neighborhood of the input. (Edges and textures are local.)

**Parameter sharing**: the same set of weights is applied across all spatial
positions. (An edge detector should work the same way wherever the edge is.)

### The convolutional layer

A **filter** (or kernel) is a small (e.g., 3 × 3 × C_{in}) tensor of weights.
The convolution operation slides the filter across the input, computing dot
products at each position. The result is a 2D **feature map** of activations.

A typical conv layer has many filters (32, 64, 128, …). Each filter
produces a feature map; together they form a 3D output (H × W × #filters).

Key parameters:
- **Filter size**: usually 3 × 3 or 5 × 5.
- **Number of filters**: 32 to 512 or more.
- **Stride**: how far the filter slides between positions. Stride 1 is
  default; stride 2 halves spatial dimensions.
- **Padding**: zero-pad the edges so the filter can apply at boundary
  positions.

### Pooling

A **max-pooling** layer takes the maximum of small (e.g., 2 × 2) regions in
each feature map, halving spatial dimensions. Reduces compute and creates a
small amount of translation invariance: a slight shift in the input leaves
the pooled output unchanged.

### Typical CNN architecture

```
Input (32×32×3)
→ Conv (3×3, 32 filters) → ReLU → BatchNorm
→ Conv (3×3, 64 filters) → ReLU → BatchNorm → MaxPool (2×2)
→ Conv (3×3, 128 filters) → ReLU → BatchNorm → MaxPool (2×2)
→ Flatten
→ Dense (256) → ReLU → Dropout(0.5)
→ Dense (10) → Softmax
```

Famous architectures:
- **LeNet-5** (1998): the original handwritten-digit network.
- **AlexNet** (2012): broke ImageNet; popularized ReLU and dropout.
- **VGG-16** (2014): proves you can go deep with simple 3×3 conv blocks.
- **ResNet** (2015): residual / skip connections allow networks of 100+
  layers to train. Almost universal today.
- **Vision Transformer (ViT)** (2020): replaces convolution with attention.
  Surpasses CNNs on large image datasets.

## 10.4 Recurrent Neural Networks

Sequence data (text, time series, speech) has temporal structure. A
**recurrent neural network (RNN)** maintains a hidden state that summarizes
everything it has seen so far.

### Basic RNN

At time step t with input x_t and hidden state h_{t−1}:

$$ h_t = g(W_h h_{t-1} + W_x x_t + b), $$
$$ o_t = W_o h_t + b_o. $$

The hidden state h_t is a learned summary of x_1, …, x_t. Output o_t depends
on h_t. The same weights W_h, W_x, W_o are used at every time step
(parameter sharing across time).

### Vanishing and exploding gradients

Backpropagating through many time steps repeatedly multiplies the same weight
matrix. If its largest eigenvalue is < 1, gradients vanish; > 1, they
explode. Either way, the network can't learn long-range dependencies.

### LSTM and GRU

The **Long Short-Term Memory (LSTM)** unit (Hochreiter & Schmidhuber, 1997)
introduces a separate **cell state** c_t alongside the hidden state h_t. Three
sigmoid **gates** decide what to add to the cell state, what to remove, and
what to expose:

- **Forget gate** f_t: decides what to discard from the previous cell state.
- **Input gate** i_t: decides what new information to add.
- **Output gate** o_t: decides what cell state info to output as h_t.

The cell state has a more direct path through time, with element-wise
gating, so gradients flow with much less attenuation. LSTMs handle
dependencies hundreds of time steps long.

The **Gated Recurrent Unit (GRU)** is a slightly simpler alternative with
two gates and no separate cell state. Often as good as LSTM in practice.

### Applications

- **Text classification, sentiment analysis** (IMDb): embed words into
  vectors, run an LSTM, feed the final hidden state into a dense classifier.
- **Translation, summarization**: encoder–decoder RNNs.
- **Time series forecasting**: predict next value from past sequence.

### Transformers

In 2017 the **transformer** architecture replaced recurrence with **self-
attention** — a mechanism that lets every position in a sequence directly
attend to every other position. Transformers are fully parallelizable
(no sequential time steps), train faster, and capture long-range
dependencies better than RNNs. They power modern NLP (BERT, GPT, T5) and
increasingly vision and biology.

ISLR v2 covers RNNs/LSTMs in depth and discusses transformers briefly.

## 10.5 When to Choose Deep Learning

Deep learning is the default for **unstructured** data: images, audio, text,
video. For these problems no other family comes close.

For **structured (tabular)** data, deep learning is often *worse* than
gradient boosting (XGBoost, LightGBM). Trees are excellent at handling
heterogeneous features, missing data, mixed types — exactly the things
tabular data has. Deep learning needs careful feature engineering to be
competitive.

A practical rule:
- Lots of structured data: try gradient boosting first.
- Lots of unstructured data: try deep learning.
- Modest data of any kind: lean on the simpler methods of Chapters 3–9.

## 10.6 Fitting a Neural Network

In R, the `keras3` package wraps Google's Keras (which sits on TensorFlow).
Setup is non-trivial but worthwhile.

```r
library(keras3)
# Once: install_keras()  # creates Python environment
```

### Tabular network on Hitters

```r
library(ISLR2); library(keras3)
Gitters <- na.omit(Hitters)

n <- nrow(Gitters)
set.seed(13)
ntest <- trunc(n / 3)
testid <- sample(1:n, ntest)

x <- scale(model.matrix(Salary ~ . - 1, data=Gitters))
y <- Gitters$Salary

modnn <- keras_model_sequential() |>
  layer_dense(units=50, activation="relu", input_shape=ncol(x)) |>
  layer_dropout(rate=0.4) |>
  layer_dense(units=1)

modnn |> compile(loss="mse",
                 optimizer=optimizer_rmsprop(),
                 metrics=list("mean_absolute_error"))

history <- modnn |> fit(x[-testid, ], y[-testid],
                         epochs=1500, batch_size=32,
                         validation_data=list(x[testid, ], y[testid]),
                         verbose=0)

plot(history)
preds <- modnn |> predict(x[testid, ])
mean(abs(y[testid] - preds))
```

### CNN on CIFAR100

```r
cifar100 <- dataset_cifar100()
x_train <- cifar100$train$x / 255
g_train <- to_categorical(cifar100$train$y, 100)
x_test  <- cifar100$test$x / 255
g_test  <- to_categorical(cifar100$test$y, 100)

model <- keras_model_sequential() |>
  layer_conv_2d(filters=32, kernel_size=c(3,3), padding="same",
                activation="relu", input_shape=c(32,32,3)) |>
  layer_max_pooling_2d(pool_size=c(2,2)) |>
  layer_conv_2d(filters=64, kernel_size=c(3,3), padding="same",
                activation="relu") |>
  layer_max_pooling_2d(pool_size=c(2,2)) |>
  layer_conv_2d(filters=128, kernel_size=c(3,3), padding="same",
                activation="relu") |>
  layer_max_pooling_2d(pool_size=c(2,2)) |>
  layer_conv_2d(filters=256, kernel_size=c(3,3), padding="same",
                activation="relu") |>
  layer_max_pooling_2d(pool_size=c(2,2)) |>
  layer_flatten() |>
  layer_dropout(rate=0.5) |>
  layer_dense(units=512, activation="relu") |>
  layer_dense(units=100, activation="softmax")

model |> compile(loss="categorical_crossentropy",
                 optimizer=optimizer_rmsprop(),
                 metrics="accuracy")

history <- model |> fit(x_train, g_train,
                         epochs=30, batch_size=128,
                         validation_split=0.2)
```

### RNN on IMDb sentiment

```r
imdb <- dataset_imdb(num_words=10000)
x_train <- imdb$train$x; y_train <- imdb$train$y
x_test  <- imdb$test$x;  y_test  <- imdb$test$y

x_train <- pad_sequences(x_train, maxlen=500)
x_test  <- pad_sequences(x_test,  maxlen=500)

model <- keras_model_sequential() |>
  layer_embedding(input_dim=10000, output_dim=32) |>
  layer_lstm(units=32) |>
  layer_dense(units=1, activation="sigmoid")

model |> compile(optimizer="rmsprop",
                 loss="binary_crossentropy",
                 metrics="accuracy")
history <- model |> fit(x_train, y_train,
                         epochs=10, batch_size=128,
                         validation_split=0.2)
```

## 10.7 Practical Wisdom

- **Start small**: shallow network, sensible defaults, get a baseline.
- **Standardize inputs**: zero mean, unit variance.
- **Adam optimizer** is a great default. Learning rate 1e-3 to start.
- **Use early stopping** with patience 5–20 epochs.
- **Monitor training and validation loss** every epoch. Diverging curves
  signal overfitting.
- **GPU** (or Apple Silicon): essential for anything beyond toy networks.
- **Reproducibility**: set random seeds in R, NumPy, and the deep learning
  framework. Even then, GPU non-determinism can cause small variations.

Deep learning is its own art. ISLR's chapter is a starting point; specialized
texts (Goodfellow/Bengio/Courville, *Deep Learning*; Chollet, *Deep Learning
with R/Python*) go further.

---

