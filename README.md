## Overview

Fully Homomorphic Encryption (FHE) is a technique that makes it possible to perform computations on encrypted data without the need to decrypt it first. While this technique is promising, multiple obstacles have prevented its widespread adoption, including its complexity and slowness. Compilers were developed to simplify the process for non-FHE expert developers by offering a user-friendly frontend to write their programs and optimize them for the best runtime.

CHEHAB is an FHE compiler that offers a simple Domain Specific Language (DSL) to define inputs and outputs and describe the desired computations. CHEHAB abstracts FHE complexities by automating the selection of optimal parameters that can support the generated noise and inserting ciphertext maintenance operations. It also supports multiple optimization techniques like Constant Folding (CF), Common Sub-expression Elimination (CSE), and Term Rewriting System (TRS) as the main optimization technique. Currently, CHEHAB supports only the BFV scheme and targets the SEAL library.

## Example

Here is an example written in our compiler's DSL

```cpp
#include "fheco/fheco.hpp"
using namespace fheco;
void example()
{
  Ciphertext c0("c0");
  Ciphertext c1 = c0 << 1;
  Ciphertext c2 = c0 << 5;
  Ciphertext c3 = c0 << 6;
  Ciphertext c4 = c1 + c0;
  Ciphertext c5 = c2 + c3;
  Ciphertext c6 = c4 + c5;
  c6.set_output("c6");
}
```

## Building CHEHAB
In order to build and use the compiler, you need to have installed:

- Cmake
- GCC compiler
- SEAL library version 4.1
- Python requirements for the RL framework

Start by cloning the Repo to your local machine:
```shell
git clone https://github.com/Abderraouf-D/CHEHAB_FHE_Compiler_RL.git
```
Setup a python environement and install the requirementsl: 

```shell 
cd CHEHAB_FHE_Compiler_RL
python3 -m venv ./RL/rl_venv 
Source ./RL/rl_venv/bin/activate
pip3 install-r ./RL/requirements.txt 
#make sure to update numpy to the latest version 
pip3 install numpy --upgrade
```

To build CHEHAB, you need to follow these steps:
1. Navigate to the cloned repository.
```shell
cd CHEHAB_FHE_Compiler_RL
```
2. Create a directory to build the compiler in
```shell
mkdir build
cmake -S . -B build
```
3. Build the compiler
```shell
cd build
make
```
### Running the benchmarks

Benchmark codes can be found in the `benchmarks` directory in the repository's main directory, while their executables can be found in the `benchmarks` directory within the `build` directory.

A benchmark is run in two phases. The first execution triggers our compiler to translate the program written in our DSL into a program using FHE native primitives. The second execution corresponds to the concrete homomorphic evaluation. In the following steps, we use the **box blur** benchmark as an example.

1. While in the build directory, navigate to the corresponding benchmark directory and run its executable.

```shell
cd benchmarks/box_blur
./box_blur 1 0 1 1 4 1
```

The general command to execute a benchmark is; 

```shell
./benchmark_name <vectorize_code> <window> <call_quantifier> <cse> <slot_count> <const_folding> 
```


- **vectorized_code:**	Boolean (0/1)	to enable vectorized vs scalar code generation  
- **window:**	Integer	to specify the Vectorization window size  
- **call_quantifier:**	Boolean (0/1)	to enable performance analysis and metrics  
- **cse:**	Boolean (0/1)	to enable Common Subexpression Elimination  
- **slot_count:**	Integer	to Specify the input size/dimensions for the benchmark  
- **const_folding:**	Boolean (0/1)	to enable constant folding  


2. Navigate to the `he` directory, where you can find the created files to build the final executable. This automatically links the generated file with the SEAL library.

```shell
cd he
mkdir build
cmake -S . -B build
cd build
make
```
3. Within the `build` directory, you will find the `main` executable that triggers the concrete homomorphic evaluation. You only need to execute the `main` file.

```shell
./main
```

This will use the `box_blur_io_example.txt` file, included in the `build` directory to extract the input values, encode them, and then encrypt them for use in the homomorphic evaluation.

