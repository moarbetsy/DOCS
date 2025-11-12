# Ghidra - Documentation

This document contains all documentation extracted from Ghidra MCP server and Ghidra reverse engineering framework.

**Last Updated:** November 12, 2025

**Source:** https://ghidra-sre.org/

**Documentation:** https://ghidra-sre.org/CheatSheet.html

---

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [Function Management](#function-management)
4. [Class and Namespace Management](#class-and-namespace-management)
5. [Decompilation](#decompilation)
6. [Disassembly](#disassembly)
7. [Data Management](#data-management)
8. [Segments and Memory](#segments-and-memory)
9. [Imports and Exports](#imports-and-exports)
10. [Cross-References](#cross-references)
11. [Comments and Annotations](#comments-and-annotations)
12. [String Analysis](#string-analysis)
13. [API Reference](#api-reference)

---

## Introduction

Ghidra is a software reverse engineering (SRE) framework developed by the National Security Agency (NSA). It provides a suite of tools for analyzing compiled code and understanding how programs work.

### Key Features

- **Disassembly**: Convert machine code to assembly language
- **Decompilation**: Convert machine code to high-level language (C)
- **Cross-References**: Track where functions and data are used
- **Scripting**: Automate analysis with Python and Java
- **Collaboration**: Share analysis projects with teams
- **Extensibility**: Plugin architecture for custom tools

### Use Cases

- Malware analysis
- Vulnerability research
- Software security auditing
- Legacy code understanding
- Binary patching
- Educational purposes

---

## Getting Started

### Installation

Ghidra is available as a Java application. Download from the official website and extract to your desired location.

### MCP Integration

Ghidra MCP server provides programmatic access to Ghidra's analysis capabilities. Configure it in your MCP settings:

```json
{
  "mcpServers": {
    "ghidra": {
      "command": "ghidra-mcp-server",
      "args": []
    }
  }
}
```

### Opening a Program

Before using most Ghidra MCP functions, you need to have a program loaded in Ghidra. The MCP server connects to an active Ghidra instance.

---

## Function Management

### List Functions

List all functions in the program.

**MCP Function:** `mcp_ghidra_list_functions`

**Parameters:**
- None

**Returns:** Array of all function names in the program

**Example Usage:**

```python
functions = mcp_ghidra_list_functions()
for func in functions:
    print(func)
```

### List Methods (Paginated)

List all function names with pagination.

**MCP Function:** `mcp_ghidra_list_methods`

**Parameters:**
- `offset` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum number of results (default: 100)

**Returns:** Array of function names

**Example Usage:**

```python
# Get first 50 functions
functions = mcp_ghidra_list_methods(offset=0, limit=50)

# Get next 50 functions
next_batch = mcp_ghidra_list_methods(offset=50, limit=50)
```

### Search Functions by Name

Search for functions whose name contains a substring.

**MCP Function:** `mcp_ghidra_search_functions_by_name`

**Parameters:**
- `query` (string, required): Substring to search for
- `offset` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum number of results (default: 100)

**Returns:** Array of matching function names

**Example Usage:**

```python
# Search for functions containing "main"
main_functions = mcp_ghidra_search_functions_by_name(
    query="main"
)

# Search for encryption functions
crypto_funcs = mcp_ghidra_search_functions_by_name(
    query="encrypt"
)
```

### Get Function by Address

Get a function by its address.

**MCP Function:** `mcp_ghidra_get_function_by_address`

**Parameters:**
- `address` (string, required): Function address in hex format (e.g., "0x1400010a0")

**Returns:** Function information object

**Example Usage:**

```python
func = mcp_ghidra_get_function_by_address(
    address="0x1400010a0"
)
```

### Get Current Function

Get the function currently selected by the user.

**MCP Function:** `mcp_ghidra_get_current_function`

**Parameters:**
- None

**Returns:** Current function information

**Example Usage:**

```python
current_func = mcp_ghidra_get_current_function()
```

### Get Current Address

Get the address currently selected by the user.

**MCP Function:** `mcp_ghidra_get_current_address`

**Parameters:**
- None

**Returns:** Current address string

**Example Usage:**

```python
address = mcp_ghidra_get_current_address()
```

---

## Class and Namespace Management

### List Classes

List all namespace/class names in the program.

**MCP Function:** `mcp_ghidra_list_classes`

**Parameters:**
- `offset` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum number of results (default: 100)

**Returns:** Array of class/namespace names

**Example Usage:**

```python
classes = mcp_ghidra_list_classes()
```

### List Namespaces

List all non-global namespaces in the program.

**MCP Function:** `mcp_ghidra_list_namespaces`

**Parameters:**
- `offset` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum number of results (default: 100)

**Returns:** Array of namespace names

**Example Usage:**

```python
namespaces = mcp_ghidra_list_namespaces()
```

---

## Decompilation

### Decompile Function

Decompile a specific function by name and return the decompiled C code.

**MCP Function:** `mcp_ghidra_decompile_function`

**Parameters:**
- `name` (string, required): Function name

**Returns:** Decompiled C code string

**Example Usage:**

```python
# Decompile a function
c_code = mcp_ghidra_decompile_function(name="main")
print(c_code)
```

### Decompile Function by Address

Decompile a function at the given address.

**MCP Function:** `mcp_ghidra_decompile_function_by_address`

**Parameters:**
- `address` (string, required): Function address in hex format

**Returns:** Decompiled C code string

**Example Usage:**

```python
c_code = mcp_ghidra_decompile_function_by_address(
    address="0x1400010a0"
)
```

---

## Disassembly

### Disassemble Function

Get assembly code (address: instruction; comment) for a function.

**MCP Function:** `mcp_ghidra_disassemble_function`

**Parameters:**
- `address` (string, required): Function address in hex format

**Returns:** Assembly code with addresses, instructions, and comments

**Example Usage:**

```python
assembly = mcp_ghidra_disassemble_function(
    address="0x1400010a0"
)
```

---

## Data Management

### List Data Items

List defined data labels and their values.

**MCP Function:** `mcp_ghidra_list_data_items`

**Parameters:**
- `offset` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum number of results (default: 100)

**Returns:** Array of data items with addresses and values

**Example Usage:**

```python
data_items = mcp_ghidra_list_data_items()
```

### Rename Data

Rename a data label at the specified address.

**MCP Function:** `mcp_ghidra_rename_data`

**Parameters:**
- `address` (string, required): Data address in hex format
- `new_name` (string, required): New name for the data label

**Returns:** Success status

**Example Usage:**

```python
mcp_ghidra_rename_data(
    address="0x140001000",
    new_name="g_config"
)
```

---

## Segments and Memory

### List Segments

List all memory segments in the program.

**MCP Function:** `mcp_ghidra_list_segments`

**Parameters:**
- `offset` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum number of results (default: 100)

**Returns:** Array of memory segments

**Example Usage:**

```python
segments = mcp_ghidra_list_segments()
for segment in segments:
    print(f"Segment: {segment['name']} at {segment['address']}")
```

---

## Imports and Exports

### List Imports

List imported symbols in the program.

**MCP Function:** `mcp_ghidra_list_imports`

**Parameters:**
- `offset` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum number of results (default: 100)

**Returns:** Array of imported symbols

**Example Usage:**

```python
imports = mcp_ghidra_list_imports()
for imp in imports:
    print(f"Import: {imp['name']} from {imp['library']}")
```

### List Exports

List exported functions/symbols.

**MCP Function:** `mcp_ghidra_list_exports`

**Parameters:**
- `offset` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum number of results (default: 100)

**Returns:** Array of exported symbols

**Example Usage:**

```python
exports = mcp_ghidra_list_exports()
for exp in exports:
    print(f"Export: {exp['name']} at {exp['address']}")
```

---

## Cross-References

### Get Cross-References To

Get all references to the specified address (xref to).

**MCP Function:** `mcp_ghidra_get_xrefs_to`

**Parameters:**
- `address` (string, required): Target address in hex format
- `offset` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum number of references (default: 100)

**Returns:** List of references to the specified address

**Example Usage:**

```python
# Find all places that call a function
xrefs = mcp_ghidra_get_xrefs_to(
    address="0x1400010a0"
)
for xref in xrefs:
    print(f"Referenced from: {xref['from_address']}")
```

### Get Cross-References From

Get all references from the specified address (xref from).

**MCP Function:** `mcp_ghidra_get_xrefs_from`

**Parameters:**
- `address` (string, required): Source address in hex format
- `offset` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum number of references (default: 100)

**Returns:** List of references from the specified address

**Example Usage:**

```python
# Find what a function calls
xrefs = mcp_ghidra_get_xrefs_from(
    address="0x1400010a0"
)
for xref in xrefs:
    print(f"References: {xref['to_address']}")
```

### Get Function Cross-References

Get all references to the specified function by name.

**MCP Function:** `mcp_ghidra_get_function_xrefs`

**Parameters:**
- `name` (string, required): Function name to search for
- `offset` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum number of references (default: 100)

**Returns:** List of references to the specified function

**Example Usage:**

```python
xrefs = mcp_ghidra_get_function_xrefs(name="main")
```

---

## Comments and Annotations

### Set Decompiler Comment

Set a comment for a given address in the function pseudocode.

**MCP Function:** `mcp_ghidra_set_decompiler_comment`

**Parameters:**
- `address` (string, required): Address in hex format
- `comment` (string, required): Comment text

**Returns:** Success status

**Example Usage:**

```python
mcp_ghidra_set_decompiler_comment(
    address="0x1400010a0",
    comment="This function initializes the encryption key"
)
```

### Set Disassembly Comment

Set a comment for a given address in the function disassembly.

**MCP Function:** `mcp_ghidra_set_disassembly_comment`

**Parameters:**
- `address` (string, required): Address in hex format
- `comment` (string, required): Comment text

**Returns:** Success status

**Example Usage:**

```python
mcp_ghidra_set_disassembly_comment(
    address="0x1400010a0",
    comment="Jump to error handler"
)
```

---

## Function Renaming and Modification

### Rename Function

Rename a function by its current name.

**MCP Function:** `mcp_ghidra_rename_function`

**Parameters:**
- `old_name` (string, required): Current function name
- `new_name` (string, required): New function name

**Returns:** Success status

**Example Usage:**

```python
mcp_ghidra_rename_function(
    old_name="sub_1400010a0",
    new_name="initialize_encryption"
)
```

### Rename Function by Address

Rename a function by its address.

**MCP Function:** `mcp_ghidra_rename_function_by_address`

**Parameters:**
- `function_address` (string, required): Function address in hex format
- `new_name` (string, required): New function name

**Returns:** Success status

**Example Usage:**

```python
mcp_ghidra_rename_function_by_address(
    function_address="0x1400010a0",
    new_name="decrypt_data"
)
```

### Set Function Prototype

Set a function's prototype.

**MCP Function:** `mcp_ghidra_set_function_prototype`

**Parameters:**
- `function_address` (string, required): Function address in hex format
- `prototype` (string, required): Function prototype (e.g., "int func(int arg1, char *arg2)")

**Returns:** Success status

**Example Usage:**

```python
mcp_ghidra_set_function_prototype(
    function_address="0x1400010a0",
    prototype="int decrypt_data(char *buffer, int length)"
)
```

### Rename Variable

Rename a local variable within a function.

**MCP Function:** `mcp_ghidra_rename_variable`

**Parameters:**
- `function_name` (string, required): Function name
- `old_name` (string, required): Current variable name
- `new_name` (string, required): New variable name

**Returns:** Success status

**Example Usage:**

```python
mcp_ghidra_rename_variable(
    function_name="main",
    old_name="var_4",
    new_name="user_input"
)
```

### Set Local Variable Type

Set a local variable's type.

**MCP Function:** `mcp_ghidra_set_local_variable_type`

**Parameters:**
- `function_address` (string, required): Function address in hex format
- `variable_name` (string, required): Variable name
- `new_type` (string, required): New type (e.g., "int", "char *", "struct config *")

**Returns:** Success status

**Example Usage:**

```python
mcp_ghidra_set_local_variable_type(
    function_address="0x1400010a0",
    variable_name="buffer",
    new_type="char *"
)
```

---

## String Analysis

### List Strings

List all defined strings in the program with their addresses.

**MCP Function:** `mcp_ghidra_list_strings`

**Parameters:**
- `offset` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum number of strings (default: 2000)
- `filter` (string, optional): Filter to match within string content

**Returns:** List of strings with their addresses

**Example Usage:**

```python
# List all strings
strings = mcp_ghidra_list_strings()

# Search for specific strings
password_strings = mcp_ghidra_list_strings(
    filter="password"
)
```

---

## API Reference

### Function Management

- `mcp_ghidra_list_functions()` - List all functions
- `mcp_ghidra_list_methods(offset, limit)` - List functions with pagination
- `mcp_ghidra_search_functions_by_name(query, offset, limit)` - Search functions
- `mcp_ghidra_get_function_by_address(address)` - Get function by address
- `mcp_ghidra_get_current_function()` - Get current function
- `mcp_ghidra_rename_function(old_name, new_name)` - Rename function
- `mcp_ghidra_rename_function_by_address(function_address, new_name)` - Rename by address

### Decompilation

- `mcp_ghidra_decompile_function(name)` - Decompile by name
- `mcp_ghidra_decompile_function_by_address(address)` - Decompile by address

### Disassembly

- `mcp_ghidra_disassemble_function(address)` - Get assembly code

### Data Management

- `mcp_ghidra_list_data_items(offset, limit)` - List data items
- `mcp_ghidra_rename_data(address, new_name)` - Rename data label

### Cross-References

- `mcp_ghidra_get_xrefs_to(address, offset, limit)` - Get references to address
- `mcp_ghidra_get_xrefs_from(address, offset, limit)` - Get references from address
- `mcp_ghidra_get_function_xrefs(name, offset, limit)` - Get function references

### Comments

- `mcp_ghidra_set_decompiler_comment(address, comment)` - Set decompiler comment
- `mcp_ghidra_set_disassembly_comment(address, comment)` - Set disassembly comment

### Variables

- `mcp_ghidra_rename_variable(function_name, old_name, new_name)` - Rename variable
- `mcp_ghidra_set_local_variable_type(function_address, variable_name, new_type)` - Set variable type

### Function Prototypes

- `mcp_ghidra_set_function_prototype(function_address, prototype)` - Set function prototype

### Strings

- `mcp_ghidra_list_strings(offset, limit, filter)` - List strings

### Segments and Memory

- `mcp_ghidra_list_segments(offset, limit)` - List memory segments
- `mcp_ghidra_get_current_address()` - Get current address

### Imports and Exports

- `mcp_ghidra_list_imports(offset, limit)` - List imports
- `mcp_ghidra_list_exports(offset, limit)` - List exports

### Classes and Namespaces

- `mcp_ghidra_list_classes(offset, limit)` - List classes
- `mcp_ghidra_list_namespaces(offset, limit)` - List namespaces

---

## Common Workflows

### Analyzing a Function

```python
# 1. Get function by name
func = mcp_ghidra_get_function_by_address(address="0x1400010a0")

# 2. Decompile it
c_code = mcp_ghidra_decompile_function_by_address(address="0x1400010a0")

# 3. Get disassembly
assembly = mcp_ghidra_disassemble_function(address="0x1400010a0")

# 4. Find where it's called
xrefs = mcp_ghidra_get_xrefs_to(address="0x1400010a0")

# 5. Rename it
mcp_ghidra_rename_function_by_address(
    function_address="0x1400010a0",
    new_name="decrypt_buffer"
)
```

### Finding String Usage

```python
# 1. List all strings
strings = mcp_ghidra_list_strings()

# 2. Find interesting strings
for s in strings:
    if "password" in s['content'].lower():
        # 3. Find where it's used
        xrefs = mcp_ghidra_get_xrefs_to(address=s['address'])
        print(f"String '{s['content']}' used at: {xrefs}")
```

### Function Analysis

```python
# 1. Search for functions
crypto_funcs = mcp_ghidra_search_functions_by_name(query="crypt")

# 2. Analyze each function
for func_name in crypto_funcs:
    # Get function address
    func = mcp_ghidra_get_function_by_address(address=func_name)
    
    # Decompile
    code = mcp_ghidra_decompile_function(name=func_name)
    
    # Add comment
    mcp_ghidra_set_decompiler_comment(
        address=func['address'],
        comment="Cryptographic function - analyze carefully"
    )
```

---

## Best Practices

1. **Use Meaningful Names**: Rename functions and variables to improve readability
2. **Add Comments**: Document your findings with comments
3. **Track Cross-References**: Use xrefs to understand function relationships
4. **Set Types**: Define proper types for variables and function parameters
5. **Organize with Namespaces**: Use namespaces to group related code
6. **Iterative Analysis**: Start with high-level functions and drill down

---

## Additional Resources

- **Ghidra Official Website:** https://ghidra-sre.org/
- **Ghidra Cheat Sheet:** https://ghidra-sre.org/CheatSheet.html
- **Ghidra GitHub:** https://github.com/NationalSecurityAgency/ghidra
- **Ghidra Documentation:** https://ghidra-sre.org/InstallationGuide.html

---

*End of Documentation*








