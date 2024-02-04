# Private GPT Installation on Ubuntu 22.04

## Requirements
**LLM Format: GGUF** (Private GPT is using llama.ccp to run these models, therefore you need to use GGUF format for any LLMs you want to try.)</br>
**Local Vector DB: Chroma**

## Instalation Steps

1. **Create a New Conda Environment**: 
   You can create a new Conda environment specifically for this project. This environment can have Python 3.11, which is required for `privateGPT`. 

   ```bash
   conda create -n privategpt-py3.11 python=3.11
   conda activate privategpt-py3.11
   ```

2. **Clone the Repository**: 
   Clone the `privateGPT` repository as in the original instructions.

   ```bash
   git clone https://github.com/imartinez/privateGPT
   cd privateGPT
   ```

3. **Install Poetry**: 
      ```bash
   curl -sSL https://install.python-poetry.org | python3 -
   ```

4. **Update Path to peotry executable (make sure to activate privategpt-py3.11 again)**: 
   ```bash
   cd
   vi ./bash_env
   export PATH="$HOME/.local/bin:$PATH"
   . .bashrc

   conda activate privategpt-py3.11
   ```

5. **Install Dependencies with Poetry**: 
   Use Poetry to install the project dependencies. 

   ```bash
   poetry install --with ui,local
   ```

6. **Run Setup Scripts**:
   Continue with the setup scripts as in the original command.

   ```bash
   poetry run python scripts/setup
   ```
7. **Add GCC Environment Requirements**
   ```bash
   sudo apt update && sudo apt install -y build-essential cmake pkg-config
   ```
8. **Install llama.cpp and llama-python.ccp**</br>
Ref: https://llama-cpp-python.readthedocs.io/en/latest/</br>
This will build **llama.cpp** from source using cmake and your system's c compiler (required) and install the library alongside this python package.
   ```bash
   conda activate privategpt-py3.11
   python -m pip install llama-cpp-python
   ```
9. **Validate cuBLAS Intalled and Working**
   ```bash
   ls /usr/local/cuda/lib64/libcublas.so*
   /usr/local/cuda/lib64/libcublas.so  /usr/local/cuda/lib64/libcublas.so.12  /usr/local/cuda/lib64/libcublas.so.12.3.4.1
   
   echo $LD_LIBRARY_PATH
   /usr/local/cuda-12.3/lib64:/usr/local/cuda-12.3/lib64

   
   git clone https://github.com/NVIDIA/CUDALibrarySamples
   cd cuBLAS/Level-1/amax
   cmake .
   make
   ./cublas_amax_example 
   A
   1.00 2.00 3.00 4.00 
   =====
   result
   4
   =====

   ```
10. **If cuBLAS not Installed - cuBLAS Installation for CUDA GPU Support (Not Tested)**</br>
Ref: https://github.com/ggerganov/llama.cpp#build</br>
This provides BLAS acceleration using the CUDA cores of your Nvidia GPU. Make sure to have the CUDA toolkit installed. You can download it from your Linux distro's package manager (e.g. apt install nvidia-cuda-toolkit) or from here: CUDA Toolkit (https://developer.nvidia.com/cuda-downloads).
   ```bash
   mkdir build
   cd build
   cmake .. -DLLAMA_CUBLAS=ON
   cmake --build . --config Release
   ```

11. **Install llama.cpp with CUDA GPU Support**
    ```bash
    CMAKE_ARGS='-DLLAMA_CUBLAS=on' poetry run pip install --force-reinstall --no-cache-dir llama-cpp-python
    ```
12. **Launch PrivateGPT (Configures use of Local LLM)**
    ```bash
    cd privateGPT
    CUDA_VISIBLE_DEVICES=0 PGPT_PROFILES=local make run
    ```
13. **Without Local LLM - Launch PrivateGPT**: 
   Since you're in the Conda environment, you can directly use `python` instead of specifying `python3.11`. 

    ```bash
    poetry run python -m private_gpt
    ```

