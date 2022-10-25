<p align="center">
  <img width="600" height="120" src="https://user-images.githubusercontent.com/107309002/175033667-a58dab6e-ca8f-4739-a371-10a10b51e7e9.jpg">
</p>

<p align="center">
<img width="141" alt="Screenshot 2022-10-19 at 3 55 14 PM" src="https://user-images.githubusercontent.com/107309002/197163290-755ec86e-2e3b-45f2-a199-8a74f3b5d942.png">
</p>
  
<p align="center">
  <b> Accelerate your machine learning and deep learning models by upto 10X </b> 
</p>

<hr style="border:0.5px solid gray">

**voltaML** is an open-source lightweight library to accelerate your machine learning and deep learning models. VoltaML can optimize, compile and deploy your models to your target CPU and GPU devices, with just ***one line of code.***

<br>

<p align="center">
  <img src="https://user-images.githubusercontent.com/107309002/196096583-2072c64b-67de-44c9-8715-f2746db1de19.gif" alt="animated" />
</p>


#### Out of the box support for 


✅ FP16 Quantization<br/>

✅ Int8 Quantization*<br/>

✅ Hardware specific compilation

<br>

<img width="1100" alt="Screenshot 2022-10-17 at 12 06 26 PM" src="https://user-images.githubusercontent.com/107309002/196105845-5ad48b61-8512-40fc-9688-1f2023aac9e6.png">
<br>

**voltaML has compilation support for the following:**


<img width="1102" alt="Screenshot 2022-06-13 at 3 43 03 PM" src="https://user-images.githubusercontent.com/107309002/173331905-e7f506a8-f675-45ae-aff1-b84f65972f90.png">


## Installation

### Own setup:

Requirements

CUDA VERSION >11.x
TensorRT == 8.4.1.2
PyTorch == 1.12 cu11.x
NVIDIA DRIVER > 510

````
git clone https://github.com/VoltaML/voltaML.git

cd voltaML

python setup.py install

````


### Docker Container 🐳

`$ docker pull voltaml/voltaml:v0.4`


````
git clone https://github.com/VoltaML/voltaML.git

cd voltaML

python setup.py install

````

## Usage

Using **`VoltaCPUCompiler`**:

```python
import torch
from voltaml.compile import VoltaCPUCompiler
from voltaml.inference import cpu_performance

model = torch.load("path/to/model/dir")

# compile the model by giving paths
compiler = VoltaCPUCompiler(
        model=model,
        output_dir="destination/path/of/compiled/model",
        input_shape=(1, 3, 224, 224) # example input shape
    )

# returns the compiled model
compiled_model = compiler.compile()

# compute and compare performance
cpu_performance(compiled_model, model, compiler="voltaml", input_shape=(1, 3, 224, 224))
```

Using **`VoltaGPUCompiler`**:


```python
import torch
from voltaml.compile import VoltaGPUCompiler
from voltaml.inference import gpu_performance

model = torch.load("path/to/model/dir")

# compile the model by giving paths
compiler = VoltaGPUCompiler(
        model=model,
        output_dir="destination/path/of/compiled/model",
        input_shape=(1, 3, 224, 224), # example input shape
        precision="fp16" # specify precision, one of [fp32, fp16, int8]
    )

# returns the compiled model
compiled_model = compiler.compile()

# compute and compare performance
gpu_performance(compiled_model, model, input_shape=(1, 3, 224, 224))
```

Using **`TVMCompiler`** (Experimental and limited support currently): 

```python
import torch
from voltaml.compile import TVMCompiler
from voltaml.inference import cpu_performance

model = torch.load("path/to/model/dir")

# compile the model by giving paths
compiler = TVMCompiler(
        model=model,
        output_dir="destination/path/of/compiled/model",
        input_shape=(1, 3, 224, 224), # example input shape
        target="llvm" # specify target device
    )

# returns the compiled model
compiled_model = compiler.compile()

# compute and compare performance
cpu_performance(compiled_model, model, compiler="tvm", input_shape=(1, 3, 224, 224))
```


## Benchmarks
### 🖼️ Classification Models Inference Latency (on GPU) ⏱️
Classification has been done on Imagenet data, `batch size = 1` and `imagesize = 224` on NVIDIA RTX 2080Ti. In terms of top 1% and 5% accuracy for `int8` models, we have not seen an accuracy drop of more than 1%. 

| Model         | Pytorch (ms) | VoltaGPU FP16 (ms) | VoltaGPU int8 (ms) | Pytorch vs Int8 Speed |
|---------------|--------------|--------------------|--------------------|-----------------------|
| squeezenet1_1 | 1.6          | 0.2                | 0.2                | 8.4x                  |
| resnet18      | 2.7          | 0.4                | 0.3                | 9.0x                  |
| resnet34      | 4.5          | 0.7                | 0.5                | 9.0x                  |
| resnet50      | 6.6          | 0.7                | 0.5                | 13.2x                 |
| resnet101     | 13.6         | 1.3                | 1.0                | 13.6x                 |
| densenet121   | 15.7         | 2.4                | 2.0                | 7.9x                  |
| densenet169   | 22.0         | 4.4                | 3.8                | 5.8x                  |
| densenet201   | 26.8         | 6.3                | 5.0                | 5.4x                  |
| vgg11         | 2.0          | 0.9                | 0.5                | 4.0x                  |
| vgg16         | 3.5          | 1.2                | 0.7                | 5.0x                  |

### 🧐 Object Detection (YOLO) Models Inference Latency (on GPU) ⏱️
Object Detection inference was done on a dummy data with `imagesize = 640` and `batch size = 1` on NVIDIA RTX 2080Ti.

| Model        | Pytorch (ms) | VoltaGPU FP16 (ms) | Pytorch vs FP16 Speed |
|--------------|--------------|--------------------|-----------------------|
| YOLOv5n      | 5.2          | 1.2                | 4.3x                  |
| YOLOv5s      | 5.1          | 1.6                | 3.2x                  |
| YOLOv5m      | 9.1          | 3.2                | 2.8x                  |
| YOLOv5l      | 15.3         | 5.1                | 3.0x                  |
| YOLOv5x      | 30.8         | 6.4                | 4.8x                  |
| YOLOv6s      | 8.8          | 3.0                | 2.9x                  |
| YOLOv6l_relu | 23.4         | 5.5                | 4.3x                  |
| YOLOv6l      | 18.1         | 4.1                | 4.4x                  |
| YOLOv6n      | 9.1          | 1.6                | 5.7x                  |
| YOLOv6t      | 8.6          | 2.4                | 3.6x                  |
| YOLOv5m      | 15.5         | 3.5                | 4.4x                  |


### 🎨 Segmentation Models Inference Latency (on GPU) ⏱️
Segmentation inference was done on a dummy data with `imagesize = 224` and `batch size = 1` on NVIDIA RTX 2080Ti.

| Model                       | Pytorch (ms) | VoltaGPU FP16 (ms)  | VoltaGPU Int8 (ms) | Speed Up (X) |
|-----------------------------|--------------|------------------------|------------------------|--------------|
| FCN_Resnet50                | 8.3          | 2.3                    | 1.8                    | 3.6x         |
| FCN_Resnet101               | 14.7         | 3.5                    | 2.5                    | 5.9x         |
| DeeplabV3_Resnet50          | 12.1         | 2.5                    | 1.3                    | 9.3x         |
| DeeplabV3_Resnet101         | 18.7         | 3.6                    | 2.0                    | 9.4x         |
| DeeplabV3_MobileNetV3_Large | 6.1          | 1.5                    | 0.8                    | 7.6x         |
| DeeplabV3Plus_ResNet50      | 6.1          | 1.1                    | 0.8                    | 7.6x         |
| DeeplabV3Plus_ResNet34      | 4.7          | 0.9                    | 0.8                    | 5.9x         |
| UNet_ResNet50               | 6.2          | 1.3                    | 1                      | 6.2x         |
| UNet_ResNet34               | 4.3          | 1.1                    | 0.8                    | 5.4x         |
| FPN_ResNet50                | 5.5          | 1.2                    | 1                      | 5.5x         |
| FPN_ResNet34                | 4.2          | 1.1                    | 1                      | 4.2x         |


<p align="center">
  <img src="https://user-images.githubusercontent.com/107309002/196167246-0a080c03-252b-4ce3-85fe-54e6cc136148.png" />
</p>

# 🤗 Accelerating Huggingface Models using voltaML 

We're adding support to accelerate Huggingface NLP models with **voltaML**. This is still in the early stages and only few models listed in the below table are supported. We're working to add more models soon.

```python
from voltaml.compile import VoltaNLPCompile
from voltaml.inference import nlp_performance


model='bert-base-cased'
backend=["tensorrt","onnx"] 
seq_len=[1, 1, 1] 
task="classification"
batch_size=[1,1,1]

VoltaNLPCompile(verbose=False, device='cuda', backend=backend,seq_len=seq_len, model=model)

nlp_performance(verbose=False, device='cuda', backend=backend, seq_len=seq_len, model=model)

```

| Model                                           | Pytorch (ms) | VoltaML FP16 (ms) | SpeedUp |
|-------------------------------------------------|--------------|-------------------|---------|
| bert-base-uncased                               | 6.4          | 1                 | 6.4x     |
| Jean-Baptiste/camembert-ner                     | 6.3          | 1                 | 6.3x     |
| gpt2                                            | 6.6          | 1.2               | 5.5x     |
| xlm-roberta-base                                | 6.4          | 1.08              | 5.9x     |
| roberta-base                                    | 6.6          | 1.09              | 6.1x     |
| bert-base-cased                                 | 6.2          | 0.9               | 6.9x     |
| distilbert-base-uncased                         | 3.5          | 0.6               | 5.8x     |
| roberta-large                                   | 11.9         | 2.4               | 5.0x     |
| deepset/xlm-roberta-base-squad2                 | 6.2          | 1.08              | 5.7x     |
| cardiffnlp/twitter-roberta-base-sentiment       | 6            | 1.07              | 5.6x     |
| sentence-transformers/all-MiniLM-L6-v2          | 3.2          | 0.42              | 7.6x     |
| bert-base-chinese                               | 6.3          | 0.97              | 6.5x     |
| distilbert-base-uncased-finetuned-sst-2-english | 3.4          | 0.6               | 5.7x     |
| albert-base-v2                                  | 6.7          | 1                 | 6.7x     |


# voltaTrees ⚡🌴 -> [Link](https://github.com/VoltaML/volta-trees)

A LLVM-based compiler for XGBoost and LightGBM decision trees.

`voltatrees` converts trained XGBoost and LightGBM models to optimized machine code, speeding-up prediction by ≥10x.

## Example

```python
import voltatrees as vt

model = vt.XGBoostRegressor.Model(model_file="NYC_taxi/model.txt")
model.compile()
model.predict(df)
```

## Installation

```bash
git clone git clone https://github.com/VoltaML/volta-trees.git
cd volta-trees/
pip install -e .
```

## Benchmarks

On smaller datasets, voltaTrees is 2-3X faster than Treelite by DMLC. Testing on large scale dataset is yet to be conducted.

### Enterpise Platform 🛣️
- [x] Hardware targeted optimised dockers for maximum performance.
- [ ] One-click deployment of the compiled models. 
- [ ] Cost-benefit analysis dashboard for optimal deployment.
- [ ] NVIDIA Triton optimzed dockers for large-scale GPU deployment.
- [ ] Quantization-Aware-Training (QAT) 
