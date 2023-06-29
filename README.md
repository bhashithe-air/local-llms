# LLMs on your laptop

If your desktop or laptop does not have a GPU installed, one way to run faster inference on LLM would be to use Llama.cpp. This was originally written so that Facebooks Llama could be run on laptops with [4-bit quantization](https://github.com/ggerganov/llama.cpp). It was written in c/c++ and this means that it can be compiled to run on many platforms with cross compilation.

There are a few prerequisites that you need to install before you can install this, if your OS is not windows you can find many good resources at the [official repository](https://github.com/ggerganov/llama.cpp). In this repository, I will give windows specific instructions to get Llama.cpp (and most likely many similar implementations) to run.

## Prerequisites

- Install [Visual Studio Community](https://visualstudio.microsoft.com/downloads/)
    - This is _not_ VSCode
    - Please select the following options in the installation wizard 
        - Desktop development with C++
        - Python Development
        - Linux embedded development with C++
        - Node.js (optional, if you want to build webapps with Node.js) 
    - This installs the C++ development libraries and may take a few minutes to install as it is a large distribution
    - You can select to update if it prompts

- [Install CMAKE](https://cmake.org/install/)
    - in the installation wizard, select to add CMAKE to path

- [Install git](https://git-scm.com/download/win)

## Setting up the environment

We are going to get the python bindings for Llama.cpp at the same time we are installing it, because of that we can compile and install `llama-cpp-python` package. In order to do that, we are going to clone that repository.

``` bash
git clone --recursive -j8 https://github.com/abetlen/llama-cpp-python.git
```

Here the `--recursive` flag is required as this cloning process is going to download the submodule `llama.cpp` as well. We can now set the environment variables to start compiling.

``` bash
cd llama-cpp-python
set FORCE_CMAKE=1
set CMAKE_ARGS=-DLLAMA_CUBLAS=OFF
```

`CMAKE_ARGS=-DLLAMA_CUBLAS=OFF` will set to not install the GPU related parts of this library, if you have an NVIDIA GPU you can go ahead an set this to `CMAKE_ARGS=-DLLAMA_CUBLAS=on`. Now its time to install the python package while compiling llama.cpp.

``` bash
python setup.py clean
python setyp.py install
```

If you do not receive errors, you are smooth sailing from here onwards. Otherwise open an issue we can discuss on how to get it resolved.

## Downloading models

Installing llama.cpp for python does not mean that you can run llama.cpp models, you first need to download them. Good place to search for them is huggingface. Specifically [TheBlokes' page](https://huggingface.co/TheBloke). Select a model which you like to run on and download the `.bin` file associated with it.

There are a few things to consider when selecting a model

- How much memory your machine has
- Architecture of the model (llama.cpp, gpt4all etc.)
- I have had luck with GGML models as it is somewhat "native" for llama.cpp
    - llama-7b
    - llama-13b
    - vicuna-7b
- Number of parameters could indicate how much memory you might need, check the following table from [Llama.cpp repository](https://github.com/ggerganov/llama.cpp#memorydisk-requirements)

| Model | Original size | Quantized size (4-bit) |
|------:|--------------:|-----------------------:|
|    7B |         13 GB |                 3.9 GB |
|   13B |         24 GB |                 7.8 GB |
|   30B |         60 GB |                19.5 GB |
|   65B |        120 GB |                38.5 GB |

- if you are getting `Could not load Llama *** (type=value_error)` errors it is most likely an architecture issue and you might need to try a different model.


Finally, I have attached a Jupyter Notebook which shows how to load and run a model through `Langchain`.