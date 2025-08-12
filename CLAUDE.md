# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Build Commands
The Dobby framework uses CMake with multiple build approaches:

**Standard CMake Build (recommended for development):**
```bash
# Configure build
cmake -G Ninja -B build-macos

# Build library 
cmake --build build-macos --parallel

# Build with debug logging enabled
cmake -G Ninja -B build-debug -DDOBBY_DEBUG=ON
cmake --build build-debug --parallel
```

**Cross-platform Builder Script:**
```bash
# Build for macOS (all architectures: x86_64, arm64, arm64e)
python3 scripts/platform_builder.py --platform=macos --arch=all

# Build for specific iOS architectures
python3 scripts/platform_builder.py --platform=iphoneos --arch=all

# Build for Linux (requires setup)
sh scripts/setup_linux_cross_compile.sh
python3 scripts/platform_builder.py --platform=linux --arch=all --cmake_dir=$HOME/opt/cmake-3.25.2 --llvm_dir=$HOME/opt/llvm-15.0.6

# Build for Android (requires NDK)
python3 scripts/platform_builder.py --platform=android --arch=all --cmake_dir=$HOME/opt/cmake-3.25.2 --llvm_dir=$HOME/opt/llvm-15.0.6 --android_ndk_dir=$HOME/opt/ndk-r25b
```

**Important Build Options:**
- `DOBBY_DEBUG=ON/OFF` - Enable debug logging (default: OFF in Release, ON in Debug)
- `NearBranch=ON/OFF` - Enable near branch trampolines for performance (default: ON)
- `FullFloatingPointRegisterPack=ON/OFF` - Save all ARM64 floating-point registers (default: OFF)
- `Plugin.SymbolResolver=ON/OFF` - Enable symbol resolution plugin (default: ON)
- `DOBBY_BUILD_EXAMPLE=ON/OFF` - Build usage examples (default: OFF)
- `DOBBY_BUILD_TEST=ON/OFF` - Build test suite (default: OFF)

### Testing Commands

**Build and Run Tests (requires capstone and unicorn libraries):**
```bash
# Install test dependencies (macOS)
brew install capstone unicorn

# Build with tests enabled
cmake -G Ninja -B build-test -DDOBBY_BUILD_TEST=ON
cmake --build build-test --parallel

# Run architecture-specific instruction relocation tests
./build-test/test_insn_relo_arm64
./build-test/test_insn_relo_arm  
./build-test/test_insn_relo_x64
./build-test/test_native
```

**Individual Test Execution:**
Each test executable focuses on specific architecture instruction relocation validation and can be run independently.

### Build Outputs
- **Shared Library:** `libdobby.dylib` (macOS), `libdobby.so` (Linux), `dobby.dll` (Windows)
- **Static Library:** `libdobby.a` 
- **Framework (macOS):** `DobbyX.framework` when built with Xcode generator

## High-Level Architecture

### Core Framework Design
Dobby implements a **layered architecture** for cross-platform dynamic binary instrumentation:

1. **Public API Layer** (`include/dobby.h`)
   - Clean C interface with three primary functions: `DobbyHook()`, `DobbyInstrument()`, `DobbyCodePatch()`
   - Architecture-specific register contexts for complete state preservation
   - Platform-agnostic symbol resolution utilities

2. **Core Management Layer** (`source/dobby.cpp`, `source/Interceptor.h`)
   - Global singleton interceptor registry managing all active hooks
   - Entry lifecycle management with automatic backup/restoration of original code
   - Routing coordination between different hook types and trampoline strategies

3. **Platform Abstraction Layer** (`source/Backend/`)
   - **UserMode/KernelMode separation:** Different implementations for user-space and kernel-space operation
   - **Platform-specific utilities:** Darwin (Mach VM), Linux (POSIX), Windows (VirtualAlloc/VirtualProtect)
   - **Memory management:** Executable memory allocation, code patching, cache invalidation

4. **Instruction Processing Engine**
   - **Instruction Relocation** (`source/InstructionRelocation/`): Architecture-specific parsers for ARM/ARM64/x86/x64 that handle PC-relative instructions during code relocation
   - **Trampoline Generation** (`source/TrampolineBridge/`): Dynamic code generation for hooks, with near-branch optimization and closure trampolines for instrumentation
   - **Assembly Backend** (`source/core/`): Low-level assemblers and code generators for each architecture

5. **Plugin Ecosystem** (`builtin-plugin/`)
   - **Symbol Resolution:** Cross-format symbol lookup (ELF/Mach-O/PE) with dyld shared cache support
   - **Import Table Replacement:** GOT/IAT hooking capabilities
   - **Application Monitoring:** File, memory, network, and system call monitoring plugins
   - **Anti-Analysis Features:** Anti-debugging bypasses and detection evasion

### Key Technical Components

**Memory Allocation Strategy:**
- **Standard Allocator:** Page-based linear allocation with RWX permission management
- **Near-Memory Allocator:** Three-stage allocation strategy to place trampolines within architectural branch ranges (±128MB on ARM64) for performance optimization
- **Code Gap Detection:** Advanced memory scanning to find suitable code cave locations

**Instruction Relocation Engine:**
- **PC-Relative Instruction Handling:** Sophisticated parsing of branch, ADR/ADRP, and literal load instructions that must be adjusted when relocated
- **Cross-Architecture Support:** Unified relocation interface with architecture-specific implementations for complex instruction encodings
- **Semantic Preservation:** Maintains program correctness while relocating instruction sequences to new memory locations

**Trampoline Bridge System:**
- **Hook Trampolines:** Minimal jump sequences (direct branches when possible, absolute loads for distant targets)
- **Closure Trampolines:** Complete register state preservation and callback invocation for instrumentation
- **Architecture Optimization:** ARM64 uses direct branches or ADRP+ADD+BR sequences; x86/x64 uses relative or absolute jumps

**Platform Integration:**
- **Darwin:** Mach VM API integration (`vm_protect`, `mach_vm_region_recurse`) with dyld shared cache analysis
- **Linux:** POSIX memory management with ELF symbol parsing and Android Bionic linker support  
- **Windows:** VirtualAlloc/VirtualProtect with PE format integration and import table manipulation

### Threading and Concurrency
The framework maintains thread-safe global state through the singleton interceptor registry, but individual hook installation operations should be serialized by calling code. The architecture supports concurrent hook execution once installed.

### Security and Anti-Analysis Features
- **ARM64e PAC Support:** Automatic pointer authentication stripping for Apple Silicon compatibility
- **Anti-Debugging:** PT_DENY_ATTACH neutralization and P_TRACED flag removal capabilities  
- **System Call Monitoring:** Comprehensive syscall interception with parameter modification support
- **Cache Coherency:** Proper instruction cache invalidation across all processor cores

This architecture enables Dobby to provide powerful dynamic analysis capabilities while maintaining high performance through near-memory optimization and minimal overhead trampolines.