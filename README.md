# Blackfyre

**Blackfyre** is an open-source platform designed to standardize and streamline binary analysis. It offers tools and APIs for extracting, analyzing, and storing binary data in a disassembler-agnostic and architecture-agnostic manner, enabling consistent workflows for advanced tasks in reverse engineering that leverage AI/ML, NLP, and LLMs
## What is Blackfyre?

Blackfyre consists of two core components:
1. **Disassembler Plugins**: Extract structured data and metadata from binaries and save them in the **Binary Context Container (BCC)** format. This ensures compatibility and standardization for subsequent analysis across different tools.
2. **Python Library**: Provides APIs for working with BCC files, including analysis of binary data, functions, basic blocks, instructions, and their relationships.

### Integration with PyVEX and Vex IR

A key feature of Blackfyre is its integration with **pyvex**, a library for lifting disassembly code to an intermediate representation (IR) called **Vex IR**. This enables users to perform architecture-agnostic analysis across a wide range of supported architectures. With Vex IR, you can analyze binaries at a higher level of abstraction while maintaining precise control over low-level details.

#### Architectures Supported by Vex IR
By leveraging pyvex, Blackfyre supports analysis for the following architectures:
- **x86 (32-bit and 64-bit)**
- **ARM (32-bit and 64-bit)**
- **AArch64**
- **MIPS (32-bit and 64-bit)**
- **PowerPC (32-bit and 64-bit)**

This architecture coverage ensures Blackfyre can be applied to a wide range of binaries, making it an essential tool for cross-platform analysis.

---

## Key Features

1. **Disassembler-Agnostic**:
   - Integrates with multiple disassemblers, including **Ghidra**, and allows additional plugins to be developed for **IDA Pro** and **Binary Ninja**.

2. **Architecture-Agnostic**:
   - By using pyvex and Vex IR, Blackfyre enables analysis across all architectures supported by Vex IR, ensuring a consistent workflow for heterogeneous binaries.

3. **Comprehensive Data Extraction**:
   - Strings, imports, exports, constants, functions, basic blocks, and raw binary data can all be extracted and analyzed.

4. **Advanced Analysis APIs**:
   - Explore decompiled functions, instruction details, basic blocks, and function relationships (callers and callees).

5. **Optimized for AI/ML Integration**:
   - Blackfyre’s structured output makes it easy to integrate binary analysis workflows into AI/ML pipelines, enabling advanced research and automation.

---

## Key Data Captured by Blackfyre Plugins

| Data Type       | Description                                                                 |
|------------------|-----------------------------------------------------------------------------|
| **Strings**      | Text and address references.                                               |
| **Imports**      | Library function references and library names.                             |
| **Exports**      | References of the exported functions.                                      |
| **Constants**    | Data and address constants.                                                |
| **Functions**    | Decompiled code, basic blocks, callers, callees, size, and names.          |
| **Basic Blocks** | Instruction mnemonics and opcodes.                                         |
| **Binary Metadata** | Architecture, file type, endianness, and disassembler details.           |
| **Raw Binary**   | (Optional) The raw binary data for additional analysis.                    |

---

## Installation

### Prerequisites
- Python 3.x
- Ghidra (optional, for Ghidra Plugin)

### Installing the Blackfyre Python Library
1. Clone the Blackfyre repository:
   ```sh
   git clone https://github.com/kye4u2/Blackfyre
   cd Blackfyre
   ```

2. Install the Python library:
   ```sh
   cd src/python
   pip install -e .
   ```
3. Verify the install by running the example python script:
   ```sh
    python examples/python/example_displaying_binary_metadata.py
    ```
   If the script runs without errors, the installation was successful.

---


## Getting Started
Note: For the following examples, We will assume the blackfyre repo is located in '/opt/Blackfyre'. Change the path to the Blackfyre repository as needed.

### Example 1: Exploring Binary Metadata with Python APIs
Retrieve metadata about a binary using the Blackfyre Python API.
```python
import os.path
from blackfyre.datatypes.contexts.binarycontext import BinaryContext

# Change the path to the Blackfyre repository
PATH_TO_BLACKFYRE_REPO = "/opt/Blackfyre"

# Test bcc file path included in the repository
bcc_file_path = os.path.join(PATH_TO_BLACKFYRE_REPO, "test/bison_arm_9409117ee68a2d75643bb0e0a15c71ab52d4e90f_9409117ee68a2d75643bb0e0a15c71ab52d4e90fa066e419b1715e029bcdc3dd.bcc")

# Load the appropriate binary context with the determined cache path
binary_context = BinaryContext.load_from_file(bcc_file_path)

# Display basic meta-data about the binary
print(f"Binary Name: {binary_context.name}")
print(f"Binary SHA256: {binary_context.sha256_hash}")
print(f"Binary File Type: {binary_context.file_type}")
print(f"Processor Type: {binary_context.proc_type}")
print(f"Number of Functions: {len(binary_context.function_context_dict)}")
print(f"Number of Strings: {len(binary_context.string_refs)}")
print(f"Number of Import Functions: {len(binary_context.import_symbols)}")
print(f"Number of Export Functions: {len(binary_context.export_symbols)}")
print(f"Number of Defined Data: {len(binary_context.defined_data_map)}")
print(f"Disassembly Type: {binary_context.disassembler_type}")
print(f"Endianness: {binary_context.endness}")
print(f"Disassembler Version: {binary_context.disassembler_version}")
print(f"BCC File Version: {binary_context.bcc_version}")

# Print the function names in the binary context container (bcc) file
num_functions_to_display = 5
max_decompiled_code_length = 100
print(f"\nDisplaying up to {num_functions_to_display} Functions:")
counter = 0
for index, function_context in enumerate(binary_context.function_contexts):

    print("=" * 100)
    print(f"  - Function Name: {function_context.name}")
    print(f"  - Function Start Address: {function_context.address}")
    print(f"  - Function End Address: {function_context.end_address}")
    print(f"  - Function Size: {function_context.size}")
    print(f"  - Function Number of Blocks: {len(function_context.basic_block_context_dict)}")
    print(f"  - Function Number of Callers: {len(function_context.callers)}")
    print(f"  - Function Number of Callees: {len(function_context.callees)}")
    print(f"  - Function Number of Unique Callees: {len(set(function_context.callees))}")
    if hasattr(function_context, "all_callees"):
        print(f"  - Function Unique All Callees: {len(function_context.all_callees)}")
    if hasattr(function_context, "num_all_call_sites"):
        print(f"  - Function Number of All Call Sites: {function_context.num_all_call_sites}")
    print(f"  - Function Decompiled Code: {function_context.decompiled_code[:max_decompiled_code_length]}")
    print("=" * 100 + "\n")

    counter += 1
    if counter >= num_functions_to_display:
        break

```

### Exploring Functions and Basic Blocks with Blackfyre API using Vex IR: 
```python
import os
from blackfyre.datatypes.contexts.vex.vexbinarycontext import VexBinaryContext
from blackfyre.datatypes.contexts.vex.vexfunctioncontext import VexFunctionContext
from blackfyre.common import IRCategory
from blackfyre.datatypes.contexts.vex.vexinstructcontext import VexInstructionContext

# Step 0: Load the binary context
# Change the path to the Blackfyre repository
PATH_TO_BLACKFYRE_REPO = "/opt/Blackfyre"

# Test bcc file path included in the repository
bcc_file_path = os.path.join(PATH_TO_BLACKFYRE_REPO, "test/bison_arm_9409117ee68a2d75643bb0e0a15c71ab52d4e90f_9409117ee68a2d75643bb0e0a15c71ab52d4e90fa066e419b1715e029bcdc3dd.bcc")

vex_binary_context = VexBinaryContext.load_from_file(bcc_file_path)

# Step 1: Select a function context by address
FUNCTION_ADDRESS = 0x2986c  # Replace with an actual function address from your binary
vex_function_context: VexFunctionContext = vex_binary_context.function_context_dict[FUNCTION_ADDRESS]

# Step 2: Get the entry basic block
entry_bb_context = vex_function_context.entry_basic_block_context

# Step 3: Print the first 10 instructions and their categories
print(f"Function: 0x{vex_function_context.start_address:x} ({vex_function_context.name})")
print(f"Entry Basic Block: 0x{entry_bb_context.start_address:x} -> 0x{entry_bb_context.end_address:x}")

# Iterate through the instructions in the entry basic block
print("\nFirst 10 Instructions in Entry Basic Block:")
vex_instruction_context: VexInstructionContext
for i, vex_instruction_context in enumerate(entry_bb_context.vex_instruction_contexts):
    # Break after the first 10 instructions
    if i >= 10:
        break

    # Extract instruction address and category
    instruction_address = vex_instruction_context.native_address
    category = vex_instruction_context.category.name if vex_instruction_context.category else "Unknown"

    # Print the instruction details
    print(f"  Instruction 0x{instruction_address:x}: Vex Instruction: {vex_instruction_context.instruction}   Vex Mnemonic - {vex_instruction_context.mnemonic} | Category - {category}")

    # Additional: Handle branches (optional)
    if vex_instruction_context.category == IRCategory.branch:
        jump_target_address = vex_instruction_context.jump_target_addr
        if jump_target_address is not None:
            print(f"    Jump target address: 0x{jump_target_address:x}")

```

### Analyzing Function Relationships in Blackfyre: Exploring  Callees and Callers
```python
import os
from blackfyre.datatypes.contexts.vex.vexbinarycontext import VexBinaryContext
from blackfyre.datatypes.contexts.vex.vexfunctioncontext import VexFunctionContext
from typing import List

# Step 0: Load the binary context
# Change the path to the Blackfyre repository
PATH_TO_BLACKFYRE_REPO = "/opt/Blackfyre"

# Test bcc file path included in the repository
bcc_file_path = os.path.join(PATH_TO_BLACKFYRE_REPO, "test/bison_arm_9409117ee68a2d75643bb0e0a15c71ab52d4e90f_9409117ee68a2d75643bb0e0a15c71ab52d4e90fa066e419b1715e029bcdc3dd.bcc")

vex_binary_context = VexBinaryContext.load_from_file(bcc_file_path)

# Step 1: Pick a function context by address
FUNCTION_ADDRESS = 0x2986c  # Replace with an actual function address from your binary
vex_function_context: VexFunctionContext = vex_binary_context.function_context_dict[FUNCTION_ADDRESS]

# Step 2: Explore the function's basic blocks
print(f"Function 0x{vex_function_context.start_address:x}: {vex_function_context.name}")
print(f"Number of basic blocks: {len(vex_function_context.basic_block_context_dict)}")

# Step 3: Print basic block details
print("\nBasic Blocks:")
for bb in vex_function_context.basic_block_contexts:
    print(f"  Block 0x{bb.start_address:x} -> 0x{bb.end_address:x}")

# Step 4: List direct callees (functions called by this function)
print("\nDirect Callees:")
if vex_function_context.callees:
    for callee_address in vex_function_context.callees:
        callee_function = vex_binary_context.function_context_dict.get(callee_address)
        callee_name = callee_function.name if callee_function else "Unnamed"
        print(f"  0x{callee_address:x} ({callee_name})")
else:
    print("  None")

# Step 5: Find and list direct callers (functions calling this function)
print("\nDirect Callers:")
direct_callers = [
    func_ctx for func_ctx in vex_binary_context.function_context_dict.values()
    if FUNCTION_ADDRESS in func_ctx.callees
]

if direct_callers:
    for caller in direct_callers:
        caller_name = caller.name if caller.name else "Unnamed"
        print(f"  0x{caller.start_address:x} ({caller_name})")
else:
    print("  None")



```

## Ghidra Plugin 

The **Blackfyre Ghidra Plugin** enables streamlined extraction of binary data into the BCC format. This plugin is specifically designed for users of the Ghidra reverse engineering tool and serves as a central component of Blackfyre's data extraction pipeline.

### Features
- **Seamless Integration**: Adds Blackfyre’s capabilities directly to Ghidra.
- **Consistent Output**: Ensures data is captured in the standardized BCC format.
- **Extensibility**: Additional plugins can be written for other disassemblers like **IDA Pro** and **Binary Ninja**.

### Installation and Usage
*  The latest  ghidra_*.zip plugin can be found in the most release  in the release section of the Blackfyre repository.
* Note the plugin is tied to the version of Ghidra that was used to build the plugin.
---

## Contributing

We welcome contributions to improve Blackfyre! Here's how you can contribute:
1. Fork the repository and create a new branch.
2. Submit a pull request with a clear description of your changes.
3. Ensure your contributions adhere to the repository's coding standards.

---

## License

Blackfyre is licensed under the [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0)
