# Bash Build Script Tutorial

This tutorial will break down each part of a bash script designed for building a project using CMake, with options to specify different build modes and to clean build directories.

## Introduction

A build script helps to automate the process of compiling, linking, and managing project dependencies. It's main purpose is to streamline the build process, by helping to enhance ease-of-use, as well as prevent build errors.

### Main benefits:
1. **Automation** - Everyone uses the same script, therefore same process, which saves time.
2. **Simplification** - Allows for the entire project to be built using a single command.
3. **Debug/Testing** - Can allow for debug or testing modes, which ensure that new changes don't break existing functionality.

## A Complete Build Script
This Bash script serves as an automated tool to streamline the building, testing, and cleaning processes of a C project. Users can specify options such as 'debug', 'test', or 'clean' as command line arguments to trigger different behaviors. The 'debug' option configures the build system for debugging, the 'test' option builds the project and runs tests, and the 'clean' option removes build artifacts. 

```bash
#!/bin/bash

# This script is used for building the project, with options for debug, testing, and cleaning.

# Initialize flags
DEBUG=0
TEST=0
CLEAN=0

# Function to display the usage of the script
usage() {
  echo "USAGE: $0 [ debug | test | clean ]"
  exit 1
}

# Function to clean build directories
clean() {
  rm -rf build bin
}

# Parse command line arguments
if [ $# -eq 1 ]; then
  case $1 in
    debug)
      DEBUG=1
      ;;
    test)
      TEST=1
      ;;
    clean)
      clean
      exit 0
      ;;
    *)
      usage
      ;;
  esac
elif [ $# -gt 1 ]; then
  usage
fi

# Create build directory and navigate into it
mkdir -p build
cd build || exit 1 # Exit if cd fails

# Stop on error
set -e

# Configure the build system
if [ $DEBUG -eq 1 ]; then
  cmake .. -DCMAKE_BUILD_TYPE=Debug
else
  cmake ..
fi

# Build the project
make -j$(nproc)

# Run tests if requested
if [ $TEST -eq 1 ]; then
  ctest --output-on-failure
fi

# Return to project root directory
cd .. || exit 1 # Exit if cd fails
```

## Script Overview

```bash
#!/bin/bash
```

This is the shebang line. It tells the system that this file is a bash script and should be executed using the `/bin/bash` shell. You'll want this at the top of all of your build scripts.

---

```bash
# Initialize flags
DEBUG=0
TEST=0
CLEAN=0
```

Here, we initialize three variables (`DEBUG`, `TEST`, and `CLEAN`) and set them all to `0`. These variables act as flags to determine what actions the script should take.

---

```bash
# Function to display the usage of the script
usage() {
  echo "USAGE: $0 [ debug | test | clean ]"
  exit 1
}
```

This defines a function called `usage`, which prints a message to the console explaining how to use the script. It then exits the script with an exit status of `1`, indicating an error.

---

```bash
# Function to clean build directories
clean() {
  rm -rf build bin
}
```

This function, named `clean`, removes (`rm`) the `build` and `bin` directories and their contents, forcefully (`-f`) and recursively (`-r`).

**`NOTE:` Feel free to add any other directories you want to remove in addition to 'build' and 'bin'.**

---

```bash
# Parse command line arguments
if [ $# -eq 1 ]; then
  ...
elif [ $# -gt 1 ]; then
  usage
fi
```

This block of code parses command line arguments. `$#` gives the number of arguments. If there is more than one argument, it calls the `usage` function. If there is exactly one argument, it processes it in the nested `case` statement.

---

```bash
case $1 in
  debug)
    DEBUG=1
    ;;
  test)
    TEST=1
    ;;
  clean)
    clean
    exit 0
    ;;
  *)
    usage
    ;;
esac
```

This `case` statement checks the value of the first argument (`$1`). Depending on the value, it sets the appropriate flags or calls the appropriate functions.

---

```bash
# Create build directory and navigate into it
mkdir -p build
cd build || exit 1 # Exit if cd fails
```

This creates a directory called `build` (if it doesn't already exist) and then changes into that directory. If the `cd` command fails, the script exits with status `1`.

---

```bash
# Stop on error
set -e
```

This line sets the script to exit if any command returns a non-zero exit status (indicative of an error).

---

```bash
# Configure the build system
if [ $DEBUG -eq 1 ]; then
  cmake .. -DCMAKE_BUILD_TYPE=Debug
else
  cmake ..
fi
```

This `if` statement checks the `DEBUG` flag. If `DEBUG` is `1`, it runs `cmake` with the `Debug` build type. Otherwise, it just runs `cmake`.

---

```bash
# Build the project
make -j$(nproc)
```

This line builds the project using `make`, with parallel jobs equal to the number of available processing units, which is determined by `$(nproc)`.

---

```bash
# Run tests if requested
if [ $TEST -eq 1 ]; then
  ctest --output-on-failure
fi
```

If the `TEST` flag is set, it runs the tests using `ctest`. The `--output-on-failure` option means that if a test fails, its output is shown.

---

```bash
# Return to project root directory
cd .. || exit 1 # Exit if cd fails
```

Finally, the script returns to the project root directory. If this `cd` command fails, the script exits with status `1`.

---
