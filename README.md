# Llama2
This repository is part of my personal experiment to run LLAMA2 in my local MacBook. It does not contain the model itself, but rather the readme gude to run it in your local machine and access the inference from LLM API such as LangChain.

> **Writer**: Hafizh Zuhdi
>
> **Date**: 30th Sept 2023

[TOC]

## Llama2 🦙🦙

LLM has been emerged in our day to day life in these past years. New models popped up every months and competition begun. One of many models out there that catch my eye is Llama2 from `Facebook`. 


Why ? Simply it's open source, free for research and commercial use. I want to make sure that every inference does not cost penny's 😅 . At some point i also want to deploy this in my own cluster, but this is another story.

## Requirements for MacOS
- RAM:
  - 8GB: 3B models
  - 16GB: 7B models
  - 32GB: 13B models
- Python/Conda Environment
- wget:
  - `brew install wget`
- Request access to llama2 model [here](https://ai.meta.com/resources/models-and-libraries/llama-downloads/) in advance.

## How-to

After you've obtained llama2 access from Meta, please keep and store the link wisely.

### 1. Clone the `llama.cpp` repository

`Llama.cpp` is the inference of Llama in C++/C. There are multiple platforms supported other than MacOS such as Windows, Linux or in Docker container.

Other models supported are not only Llama2, but also Alpaca, Falcon, etc.

You can use these following UI that compatible with `llama.cpp`:
- [nat/openplayground](https://github.com/nat/openplayground)
- [oobabooga/text-generation-webui](https://github.com/oobabooga/text-generation-webui)
- [withcatai/catai](https://github.com/withcatai/catai)

#### 1.1. Clone the repo

```bash
git clone https://github.com/ggerganov/llama.cpp.git
```

#### 1.2. Create a new conda environment

```bash
# go to llama.cpp repo folder
cd llama.cpp
make
conda create --name llama2 python=3.11
conda activate llama2
```
#### 1.3. Install requirements

```bash
pip install requirements.txt
```


### 2. Clone the `llama` repository

The model is not located in the repository. The repository is intended as a wrapper to download the model using an access link given after you requested from the prerequisite section.

To download it, it's very simple:

#### 2.1. Clone the repo

```bash
git clone https://github.com/facebookresearch/llama.git
```

#### 2.2. Download the model

```bash
cd llama
bash download.sh
# And follow the step and choose your desired model
# In our case it's 7B-chat
```

### 3. Model Conversion to f16 format

> **Notes**:
>
> Run this inside the `llama.cpp` directory.

```bash
python convert.py --outfile models/7B/ggml-model-f16.bin --outtype f16 ../llama/llama-2-7b-chat --vocab-dir ../llama/llama
```

It will create the `f16` model format which sized around 13.5 GB for 7B parameters.

### 4. Make the model run efficiently by quantisizing 

> **Notes**:
>
> Run this inside the `llama.cpp` directory.

```bash
./quantize  ./models/7B/ggml-model-f16.bin ./models/7B/ggml-model-q4_0.bin q4_0
```

Now it will give the model around: 3.5 GB in terms of size.

Quantisizing is a technique by reducing the precision of the models weights, so you can save memory and speed up inference while preserving most of the model's performance.

### 5. Run the model & start prompting

> **Notes**:
>
> - Run this inside the `llama.cpp` directory.
> - I used the prompts example that has a filename `chat-with-bob.txt`

```bash
./main -m ./models/7B/ggml-model-q4_0.bin -n 1024 --repeat_penalty 1.0 --color -i -r "User:" -f ./prompts/chat-with-bob.txt
```

```
User: Hello, Bob.
Bob: Hello. How may I help you today?
User: Please tell me the largest city in Europe.
Bob: Sure. The largest city in Europe is Moscow, the capital of Russia.
```

## Integration with LangChain

To be continued here:
- https://python.langchain.com/docs/guides/local_llms
- https://python.langchain.com/docs/use_cases/question_answering/how_to/local_retrieval_qa
