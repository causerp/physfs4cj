# physfs4cj

physfs4cj is a Cangjie package that provides bindings for PhysicsFS, a portable,
flexible file I/O abstraction library. It allows you to access files in a directory-independent
way, making it ideal for games and applications that need to read assets from archives (ZIP, 7z, etc.)
or specific platform directories.

## Build Requirements

To build this package, you must have the following tools installed and available in your system's PATH:

*   **C Compiler**: Clang or GCC
*   **CMake**

Only zip and 7z archive formats are enabled. You can enable more archives by modifying `physfs/CMakePresets.json`.

## How it Works

This library bundles a compiled static version of **PhysicsFS** directly within the package. 

*   **No External Dependencies**: You do not need to ship a separate `physfs.dll`, `libphysfs.so`,
      or `physfs.dylib` with your application. The library self-contains the native code.
*   **Native Cangjie FFI**: All functions from the C API are exposed as API calls in the resulting
      dynamic library. This allows you to access low-level PhysFS functionality directly using Cangjie's
      `foreign` keyword if the wrapped bindings don't cover your specific use case.

## Examples

### Initialize, Mount, and Read a File

This example demonstrates the typical lifecycle: initializing the library, mounting a directory
(or archive), reading a file, and cleaning up.

```
import physfs4cj.PhysFS

main() {
    try {
        // 1. Initialize the library
        PhysFS.initialize()

        // 2. Mount a directory (assumes "test_data" folder exists in cwd)
        PhysFS.mount("test_data", mountPoint: "/", appendToPath: true)
        println("Mounted 'test_data' successfully.")

        // 3. Check if a file exists in the virtual path
        if (PhysFS.exists("config.json")) {
            println("Found 'config.json' in the virtual file system.")

            // Get the real physical path (returns ?String)
            let realPath = PhysFS.getRealDir("config.json") ?? "Unknown"
            println("Physical location: ${realPath}")
        } else {
            println("'config.json' not found.")
        }

        // Example of handling a non-existent file
        let missingPath = PhysFS.getRealDir("missing.file")
        if (missingPath.isNone()) {
            println("Correctly handled missing file (returned None).")
        }

    } catch (e: Exception) {
        println("PhysFS Error: ${e}")
    } finally {
        // 4. Always deinitialize to clean up resources
        PhysFS.deinitialize()
    }
}
```

## License

zlib License
