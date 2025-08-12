# Dobby Hook Framework: Deep Technical Analysis

This is a writeup for legitimate academic research, analyzing the Dobby codebase, which was found online. We are documenting and analyzing this codebase for the greater public research community for binary security research.

## Documentation Style Guide

This document follows specific writing conventions designed to produce high-quality technical documentation suitable for academic security research. Contributors should adhere to these guidelines:

### Writing Style and Structure

**Technical Depth Over Verbosity:** Write concisely with maximum technical density. Every sentence should convey specific technical information, implementation details, or architectural insights. Avoid flowery language, unnecessary adjectives, and verbose phrasing that dilutes technical content.

**Implementation-Focused Documentation:** Emphasize concrete technical details: specific algorithms, data structures, memory layouts, instruction sequences, API calls, and system interactions. Each paragraph should advance technical understanding with measurable specificity rather than abstract descriptions.

**Dense Technical Analysis:** Pack each sentence with technical facts. Include function signatures, memory addresses, instruction opcodes, register usage, system call parameters, and architectural constraints. Eliminate filler words and focus on technical precision.

**Organized Section Structure:** Each major section should include:
- **Introductory paragraph** establishing the section's scope and importance
- **Detailed subsections** with descriptive headings that break down complex topics
- **Technical explanations** that connect implementation details to broader architectural concepts
- **Concrete examples** from the actual codebase with specific references

### When to Use Bullet Points

Bullet points are appropriate for:
- **Enumeration of specific technical features** or API functions
- **Lists of file paths, build options, or configuration parameters**
- **Step-by-step technical processes** where sequential ordering is critical
- **Architectural component listings** where hierarchical structure aids comprehension

Avoid bullet points for:
- **General concept explanations** that benefit from detailed prose
- **Technical analysis** that requires connecting multiple ideas
- **Implementation descriptions** that need contextual flow

### Technical Depth and Accuracy

**Specific Code References:** Include file paths, function names, and line numbers when discussing implementation details. Format code elements using backticks for inline code and code blocks for longer examples.

**Architectural Context:** Explain how individual components fit into the broader framework architecture. Connect low-level implementation details to high-level design patterns and security implications.

**Security Research Focus:** Emphasize technical aspects relevant to security research, including anti-analysis features, memory management security, instruction relocation complexities, and cross-platform security considerations.

**Academic Rigor:** Support technical claims with evidence from the codebase. Use precise language and avoid speculation. When discussing potential security implications, clearly distinguish between documented features and analytical interpretations.

### Formatting Conventions

- **Section headings** should be descriptive and hierarchically organized
- **Code elements** formatted with backticks: `function_name()`, `file_path.cc:123`
- **Technical terms** introduced clearly with context before extensive usage
- **Cross-references** to other sections when concepts build upon each other

This style guide ensures the document serves as comprehensive technical reference material suitable for academic security research and professional systems analysis.

## Table of Contents

### 1. Project Structure and Architecture Overview
- 1.1 Repository Organization and Build System
- 1.2 Core Framework Components
- 1.3 Platform-Specific Implementations
- 1.4 Built-in Plugins and Extensions
- 1.5 External Dependencies and Third-Party Code
- 1.6 Examples, Tests, and Documentation

### 2. Dobby Core Architecture
- 2.1 Core API Functions (`DobbyHook`, `DobbyInstrument`, `DobbyCodePatch`)
- 2.2 Interceptor Class and Entry Management
- 2.3 Memory Layout and Component Interaction

### 3. Hook Installation Process
- 3.1 Address Validation and PAC Stripping (ARM64e)
- 3.2 InlineHookRouting Creation and Management
- 3.3 Original Code Backup and Restoration
- 3.4 Atomic Code Patching Implementation

### 4. Instruction Relocation System
- 4.1 ARM64 PC-Relative Instruction Handling
  - 4.1.1 Branch Instructions (B/BL/Bcc)
  - 4.1.2 ADR and ADRP Instructions
  - 4.1.3 LDR Literal Instructions
- 4.2 ARM32 Instruction Relocation
- 4.3 x86/x64 Instruction Decoding and Relocation
- 4.4 Cross-Architecture Relocation Engine

### 5. Trampoline Bridge System
- 5.1 Trampoline Generation Strategies
  - 5.1.1 Near Branch Trampolines (ARM64)
  - 5.1.2 ADRP+ADD+BR Sequences
  - 5.1.3 Absolute LDR+BR Trampolines
- 5.2 Closure Trampoline Implementation
- 5.3 Register Context Preservation
- 5.4 Architecture-Specific Assembly Stubs

### 6. Memory Allocator System
- 6.1 Standard Memory Allocator
  - 6.1.1 Page-Based Linear Allocation
  - 6.1.2 RWX Permission Management
- 6.2 Near Memory Allocator
  - 6.2.1 Three-Stage Allocation Strategy
  - 6.2.2 Memory Region Analysis
  - 6.2.3 Code Gap Detection (`memmem_impl`)
- 6.3 Platform-Specific Memory APIs

### 7. Platform-Specific Implementations
- 7.1 Darwin/macOS Backend
  - 7.1.1 Mach VM API Usage (`vm_protect`, `mach_vm_region_recurse`)
  - 7.1.2 Dynamic Symbol Resolution
  - 7.1.3 Process Runtime Utilities
- 7.2 Linux/POSIX Backend
  - 7.2.1 POSIX Memory Management
  - 7.2.2 ELF Symbol Resolution
  - 7.2.3 Android Bionic Linker Integration
- 7.3 Windows Backend
  - 7.3.1 VirtualAlloc/VirtualProtect Usage
  - 7.3.2 PE Symbol Resolution
  - 7.3.3 Import Table Replacement

### 8. Symbol Resolution System
- 8.1 Cross-Platform Symbol Resolver
- 8.2 Mach-O Symbol Resolution
  - 8.2.1 dyld Shared Cache Analysis
  - 8.2.2 File-Based Symbol Parsing
- 8.3 ELF Symbol Resolution
- 8.4 PE Symbol Resolution

### 9. Built-in Monitoring Plugins
- 9.1 Application Event Monitor
  - 9.1.1 File Operation Monitoring
  - 9.1.2 Memory Operation Instrumentation
  - 9.1.3 Socket Network Monitoring
- 9.2 Supervisor Call Monitor
  - 9.2.1 System Call Interception
  - 9.2.2 Anti-Debug Bypass (ptrace/sysctl)
  - 9.2.3 Sensitive API Monitoring

### 10. Code Patching and Cache Management
- 10.1 Platform-Specific Code Patching
  - 10.1.1 Darwin `vm_protect` Implementation
  - 10.1.2 POSIX `mprotect` Implementation
  - 10.1.3 Windows `VirtualProtect` Implementation
- 10.2 Instruction Cache Invalidation
  - 10.2.1 ARM64 Cache Management (`dc cvau`, `ic ivau`)
  - 10.2.2 ARM32 System Call Approach
  - 10.2.3 x86/x64 Cache Coherency

### 11. Anti-Analysis Features
- 11.1 Anti-Debugging Implementation
  - 11.1.1 `PT_DENY_ATTACH` Neutralization
  - 11.1.2 `P_TRACED` Flag Removal
- 11.2 System Call Parameter Modification
- 11.3 Detection Evasion Techniques

### 12. Assembly and Code Generation
- 12.1 Cross-Architecture Assembler
- 12.2 Runtime Code Generation
- 12.3 Literal Pool Management
- 12.4 Branch Distance Calculation

### 13. Thread Safety and Concurrency
- 13.1 Global State Management
- 13.2 Hook Installation Synchronization
- 13.3 Multi-Threading Considerations

### 14. Dobby Internal Implementation Details
- 14.1 Entry Management and Lifecycle
- 14.2 Routing Strategy Selection
- 14.3 Error Handling and Recovery
- 14.4 Debug Logging System

---

## 1. Project Structure and Architecture Overview

Dobby implements dynamic binary instrumentation through a five-layer architecture with cross-platform support for ARM/ARM64/x86/x64 instruction sets.

### 1.1 Build System Architecture

CMake-based build system with platform detection via `cmake/build_environment_check.cmake`. Core targets: `dobby` (shared), `dobby_static` (static), with architecture-specific compilation controlled by `#ifdef` macros in `source/dobby/platform_detect_macro.h`:
- `TARGET_ARCH_ARM`: ARM 32-bit (`__arm__`)  
- `TARGET_ARCH_ARM64`: ARM64 (`__arm64__`, `__aarch64__`)
- `TARGET_ARCH_IA32`: x86 32-bit (`_M_IX86`, `__i386__`)
- `TARGET_ARCH_X64`: x86-64 (`_M_X64`, `__x86_64__`)

Build flags: `DOBBY_DEBUG` enables logging, `NearBranch` enables ±128MB branch optimization, `FullFloatingPointRegisterPack` saves ARM64 q8-q31 registers.

### 1.2 Public API Layer (`include/dobby.h`)

Three primary functions with typed register contexts:
- `DobbyHook(void *address, void *fake_func, void **out_origin_func)`: Function replacement
- `DobbyInstrument(void *address, dobby_instrument_callback_t pre_handler)`: Pre-execution callbacks  
- `DobbyCodePatch(void *address, uint8_t *buffer, uint32_t buffer_size)`: Direct memory patching

Architecture-specific register contexts: ARM uses 13 general-purpose r0-r12 + lr/sp, ARM64 uses 29 x0-x28 registers + 32 q0-q31 SIMD registers (q8-q31 require `FullFloatingPointRegisterPack`), x86 uses eax-esi + flags, x64 uses rax-r15 + flags.

### 1.3 Core Management Layer (`source/Interceptor.h`)

Global singleton `gInterceptor` manages active hooks through `stl::vector<Entry *>`. Each Entry contains:
- `addr_t addr`: Target address with ARM Thumb LSB stripping via `features::arm_thumb_fix_addr()`
- `MemBlock patched`: Original code location and size
- `MemBlock relocated`: Moved instruction storage
- `uint8_t *origin_code_`: Backup for restoration via `DobbyDestroy()`

### 1.4 Memory Management (`source/MemoryAllocator/MemoryAllocator.h`)

Two-tier allocation system:
- **Page Allocator**: `OSMemory::Allocate()` requests full pages, `OSMemory::SetPermission()` sets RWX permissions
- **Linear Allocator**: `simple_linear_allocator_t` subdivides pages for trampolines

Platform implementations:
- Darwin: `mach_vm_allocate()`, `mach_vm_protect()` for permission changes
- Linux: `mmap()` with `PROT_READ|PROT_WRITE|PROT_EXEC`
- Windows: `VirtualAlloc()`, `VirtualProtect()`

### 1.5 Instruction Processing Engine

**Instruction Relocation** (`source/InstructionRelocation/`):
- ARM: Handles 32-bit Thumb/ARM mode switching, PC-relative branches
- ARM64: ADR/ADRP instruction adjustment, 26-bit branch encoding, literal pool relocation
- x86: ModR/M byte parsing via `x86_insn_decode.c`, relative jump conversion
- x64: RIP-relative addressing adjustment, 32-bit displacement handling

**Trampoline Generation** (`source/TrampolineBridge/`):
- Direct trampolines: Single branch instruction when target within range
- Absolute trampolines: Register load + indirect branch for distant targets
- Closure trampolines: Full register preservation via assembly stubs in `.asm` files

**Assembly Backend** (`source/core/assembler/`):
- Per-architecture assemblers: `assembler-arm.cc`, `assembler-x64.cc`, `assembler-ia32.cc`
- Code generation: Immediate encoding, addressing mode selection, instruction scheduling

### 1.6 Platform Integration Layer

**Darwin Backend** (`source/Backend/UserMode/PlatformUtil/Darwin/ProcessRuntime.cc`):
- Mach VM API: `mach_vm_region_recurse()` for memory layout analysis
- Code patching: `mach_vm_protect()` for temporary write permissions
- Cache coherency: `sys_cache_control()` for instruction cache invalidation

**Linux Backend**: `mmap()`-based allocation, `/proc/self/maps` parsing, `__builtin___clear_cache()`

**Windows Backend**: `VirtualAlloc()`/`VirtualProtect()`, structured exception handling

### 1.7 Built-in Plugin System

**Application Event Monitor** (`builtin-plugin/ApplicationEventMonitor/`):
- File operations: `open()`, `read()`, `write()` interception via `file_operation_monitor.cc`
- Memory operations: `malloc()`, `free()` tracking in `memory_operation_instrument.cc`
- Network monitoring: Socket API hooks in `posix_socket_network_monitor.cc`

**Symbol Resolution** (`builtin-plugin/SymbolResolver/`):
- ELF: `dlsym(RTLD_DEFAULT)` + static `.symtab` parsing, ASLR load bias calculation
- Mach-O: dyld shared cache analysis, file-based symbol parsing
- PE: `LoadLibraryExA()`/`GetProcAddress()` integration

**Anti-Analysis Features** (`builtin-plugin/SupervisorCallMonitor/`):
- ptrace detection bypass via syscall parameter modification
- `PT_DENY_ATTACH` neutralization on Darwin
- System call logging with parameter inspection

### 1.8 External Dependencies

**TINYSTL** (`external/TINYSTL/`): Custom STL with `vector.h`, `unordered_map.h`, optimized allocators
**Logging System** (`external/logging/`): Multi-backend logging with kernel mode support via `logging_kern.cc`
**OSBASE**: Platform abstraction wrappers for consistent OS interface

### 1.9 Near Branch Optimization

Controlled by `g_enable_near_trampoline` flag in `source/InterceptRouting/NearBranchTrampoline/NearBranchTrampoline.h`. ARM64 uses direct `b` instructions within ±128MB range, x86/x64 use relative jumps when possible, falling back to absolute addressing for distant targets.

This architecture enables precise control over instruction execution flow while maintaining architectural semantics and performance through optimized memory allocation and branch target selection.

---

## 2. Dobby Core Architecture

Dobby implements dynamic binary instrumentation through three API functions: `DobbyHook()`, `DobbyInstrument()`, and `DobbyCodePatch()` at `include/dobby.h:122-119`. Global interceptor `gInterceptor` in `source/Interceptor.h:96` manages active hooks via `stl::vector<Entry *> entries` with entry lookup through linear search in `find()` at line 63.

### 2.1 Core API Function Implementation

#### DobbyHook: Function Replacement Engine

`DobbyHook(void *address, void *fake_func, void **out_origin_func)` implementation workflow:

1. **Address Validation**: Null checks, ARM64e PAC stripping via `features::apple::arm64e_pac_strip()`, Android memory permissions via `features::android::make_memory_readable()`
2. **Collision Detection**: `gInterceptor.find((addr_t)address)` lookup, returns `-1` if address already hooked
3. **Entry Creation**: `new Interceptor::Entry((addr_t)address)`, stores `fake_func_addr`
4. **Routing Construction**: `new InlineHookRouting(entry, (addr_t)fake_func)` calls `BuildRouting()` sequence: `GenerateTrampoline()`, `GenerateRelocatedCode()`, `BackupOriginCode()`
5. **Activation**: `routing->Active()` calls `DobbyCodePatch()` at `source/InterceptRouting/InterceptRouting.h:59`
6. **Return Value**: `*out_origin_func = (void *)entry->relocated.addr()` points to relocated original code

#### DobbyInstrument: Callback Instrumentation

`DobbyInstrument(void *address, dobby_instrument_callback_t pre_handler)` implementation:

1. **Entry Setup**: Creates `Interceptor::Entry`, assigns `entry->pre_handler = pre_handler`
2. **Routing Creation**: `new InstrumentRouting(entry, pre_handler)` constructs specialized routing
3. **Closure Trampoline**: `GenerateInstrumentClosureTrampoline()` creates trampoline calling `instrument_routing_dispatch` handler at `source/TrampolineBridge/ClosureTrampolineBridge/ClosureTrampoline.h:19-22`
4. **Dispatch Mechanism**: `instrument_routing_dispatch()` at `source/InterceptRouting/InstrumentRouting/instrument_routing_handler.cpp:8` invokes `instrument_callback_fn((void *)entry->addr, ctx)`, sets next hop via `set_routing_bridge_next_hop(ctx, (void *)entry->relocated.addr())`

#### DobbyCodePatch: Direct Memory Modification

`DobbyCodePatch(void *address, uint8_t *buffer, uint32_t buffer_size)` Darwin implementation at `source/Backend/UserMode/ExecMemory/code-patch-tool-darwin.cc:40`:

1. **Page Boundary Handling**: `ALIGN_FLOOR(address, page_size)` alignment, recursive splitting for cross-page patches
2. **Memory Protection**: `vm_protect_fn()` calls with `VM_PROT_READ|VM_PROT_WRITE|VM_PROT_COPY` and `VM_PROT_READ|VM_PROT_EXECUTE`
3. **Atomic Patching**: `memcpy()` operation between protection changes
4. **Cache Invalidation**: `ClearCache(address, (void *)((addr_t)address + buffer_size))`

### 2.2 Interceptor Entry Management

#### Entry Structure Definition

`Interceptor::Entry` at `source/Interceptor.h:12` contains:
- `uint32_t id`: Unique identifier
- `addr_t addr`: Target address
- `MemBlock patched`: Original code location via `entry->patched = origin`
- `MemBlock relocated`: Relocated code block via `entry->relocated = relocated`
- `uint8_t *origin_code_`: Heap-allocated backup via `operator new(tramp_size)`

#### Memory Block Architecture

`MemBlock` structure at `source/MemoryAllocator/MemoryAllocator.h:50` inherits `MemRange` with `addr_t start_`, `size_t size`. `CodeMemBlock` and `DataMemBlock` typedefs.

#### Backup/Restore Operations

- **Backup**: `backup_orig_code()` at `source/Interceptor.h:39` performs `memcpy(origin_code_, orig, tramp_size)`
- **Restore**: `restore_orig_code()` calls `DobbyCodePatch((void *)patched.addr(), origin_code_, patched.size)`, deallocates via `operator delete(origin_code_)`

### 2.3 Memory Layout and Component Architecture

#### Memory Allocator System

`MemoryAllocator` at `source/MemoryAllocator/MemoryAllocator.h:61` maintains separate allocators:
- `stl::vector<simple_linear_allocator_t *> code_page_allocators`: Executable memory
- `stl::vector<simple_linear_allocator_t *> data_page_allocators`: Data storage

Page allocation via `OSMemory::Allocate(OSMemory::PageSize(), kNoAccess)`, permission setting through `OSMemory::SetPermission()` with `kReadExecute`/`kReadWrite` flags.

#### Register Context Structures

Architecture-specific contexts in `include/dobby.h`:
- **ARM64**: `x[29]` general registers, `FPReg q[32]` SIMD registers, q8-q31 require `FullFloatingPointRegisterPack` flag
- **ARM32**: `r[13]` general registers, `lr` link register
- **x86**: `eax-esi` + flags
- **x64**: `rax-r15` registers

#### Trampoline Generation Workflow

`InterceptRouting::GenerateTrampoline()` at `source/InterceptRouting/InterceptRouting.h:63`:
1. Near trampoline attempt via `GenerateNearTrampolineBuffer(from, to)` when `g_enable_near_trampoline` enabled
2. Fallback to `GenerateNormalTrampolineBuffer(from, to)` for distant targets
3. Trampoline address resolution through `trampoline_addr()`: returns `near_trampoline->addr()` if available, else `trampoline->addr()`

#### Component Integration Flow

Routing coordination in `InterceptRouting::BuildRouting()`:
1. **Trampoline Generation**: Creates jump sequence to hook target
2. **Code Relocation**: `GenRelocateCodeAndBranch()` moves original instructions, handles PC-relative addressing
3. **Original Backup**: `entry->backup_orig_code()` preserves restoration data
4. **Atomic Activation**: `DobbyCodePatch()` installs trampoline atomically
