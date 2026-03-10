drive.google.com/drive/folders/1ehWRcdDtlJtkRH6wJ0qdnEYKFBlns6qN
## Deep Learning

Several layers on an artificial neural network, which is a computational graph
![[Pasted image 20260304141055.png]]

Each small node is a weighted average of the inputs

![[Pasted image 20260304141135.png]]

- Weights given by the edges
- Non-linearity introduced by f

Activation function examples (f):

![[Pasted image 20260304141235.png]]


### Convolution

![[Pasted image 20260304141329.png]]

- Discrete
- 2D
- Finite Domain


**Input Image** convoluted with the **Filter Kernel Feature** = **Filter Response Feature map**

Imagine the feature to be a sliding window over the image:
![[Pasted image 20260304141532.png]]

## Convolutional Neural Networks

- Idea: Use neural network to process images
	- Problem: Neural networks take 1D Vector input; images are 2D
	- Solution: Flatten pixels, treat every pixel as an input
	- Problem: Lost spatial locality
- Idea: Design a neural network to compute convolution
	- Compute weighted averages
	- Problem: Dense connections are too many
	- Solution: Re-write convolution as weight matrix multiplication with flattened input image
		- Transform kernel into appropriate weight matrix

### Step 1: Kernel and input are the same size:

![[Pasted image 20260304142051.png]]

### Step 2: Input image is wider

![[Pasted image 20260304142125.png]]

![[Pasted image 20260304142212.png]]


## Key Points

- Convolutional Neural Networks are sparse neural network with repeating weights
- Zeros in weight matrix indicate missing edges
- Sparsity in block matrix achieves spatial locality
- Repeating kernel block simulates sliding convolution kernel over input image

## Object Classification

![[Pasted image 20260304142432.png]]

Feed pixels into CNN. 1-hot encoding for class of object

## Programming Notes

https://colab.research.google.com/drive/1TGIh0hQPKZ9Kt0oMWTAnnMcdx6PH6B2i#scrollTo=n7gCzyRk_50M

- Kernels do not need to be specified. Initialized randomly and at the end have values to accomplish a task
- A **tensor** in PyTorch is the fundamental data structure — essentially an n-dimensional array, similar to NumPy arrays but with added capabilities
- Number of input channels is the same as the number of kernels
- To define a machine learning model:
	- Type of mode: artificial neural network, decision tree, etc.
	- Structure of mode: for ANN -> num of layers, the nodes in each layer, number of connections
	- Values of parameters of the model -> connection weights -> done by training
- ReLU or Pooling layer: type of activation in each layer

![[Pasted image 20260304144743.png]]

- Combining the layers in the right number and arrangement requires experience and experimentation
- Generally, Convolutional layers come first, followed by the fully connected layers
- In each layer, the number of inputs must match the number of outputs for the last layer
- Output values for final layers are called logits

Loss function:
![[Pasted image 20260304151858.png]]

![[Pasted image 20260304151925.png]]

#### Training and testing

![[Pasted image 20260304152000.png]]

We can achieve higher accuracy by passing the data through the model several times (epochs).

## Transfer Testing

You can use pre-trained models by adapting them to our task

Can be achieved by transfer learning: Adding or replacing some layers