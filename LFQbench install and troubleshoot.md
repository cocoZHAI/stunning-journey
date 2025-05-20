# LFQbench install in R

The orginial repository can be found: https://github.com/IFIproteomics/LFQbench

## Install the devtool
```bash
install.packages("devtools")
```

```bash
library(devtools)
```

## Call LFQbench

```bash
install_github("IFIproteomics/LFQbench")
```

```bash
library(LFQbench)
```

## See examples on how to use LFQbench, use this:

```bash
vignette("LFQbench")
```

---

#  Troubleshooting

This guide documents the journey of troubleshooting and resolving R package installation and compilation issues on a macOS ARM64 system (like M1 or M2 chips). It requires the Mac users install homebrew for easier package installation and compilation. If you don't have homebrew yet, install in terminal:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

---

##  Summary of Issues Encountered

### 1. Compilation Errors in C++ Packages

**Common Errors:**
```
fatal error: 'iostream' file not found
fatal error: 'climits' file not found
fatal error: 'cstring' file not found
```

**Explanation:**  
Standard headers not found — typically indicates missing or broken Xcode Command Line Tools.

**Fix:** (If not already installed)
```bash
xcode-select --install
```

**Fix:** (If already installed)
- Reinstall them:

```bash
sudo rm -rf /Library/Developer/CommandLineTools
xcode-select --install
```
- Once it's done, check that it is correctly installed:
```bash
xcode-select -p
```

  You should see something like this:
  ```
  /Library/Developer/CommandLineTools
  ```
- Let’s confirm the compiler can find headers:
```bash
clang++ -v -x c++ -E /dev/null
```

Near the bottom, you should see a list of include paths — make sure something like this is in there:

```
/Library/Developer/CommandLineTools/usr/include/c++/v1
```

---

### 2. `classInt` Failed Due to Missing `gfortran`

**Error:**
```
make: /opt/R/arm64/bin/gfortran: No such file or directory
```

**Explanation:**  
R expected a specific Fortran compiler path that didn’t exist. Homebrew's version was present but not linked correctly.

**Fix:**
- Install GCC using Homebrew:
  ```bash
  brew install gcc
  ```
- Check the path:
  ```bash
  which gfortran  # → /opt/homebrew/bin/gfortran
  ```
- Create or update `~/.R/Makevars`:
  ```bash
  mkdir -p ~/.R
  nano ~/.R/Makevars
  ```
  
  ```make
  FC = /opt/homebrew/bin/gfortran
  F77 = /opt/homebrew/bin/gfortran
  ```
- Tell you R to use it right now:
```r
Sys.setenv(FC = "/opt/homebrew/bin/gfortran")
Sys.setenv(F77 = "/opt/homebrew/bin/gfortran")
```

Then confirm:
```r
Sys.getenv("F77")
```

Now you should see something like:
```
"/opt/homebrew/bin/gfortran"
```

---

### 3. Linker Errors Due to Missing Fortran Libraries

**Error:**
```
ld: library 'gfortran' not found
ld: warning: search path '/opt/R/arm64/gfortran/lib' not found
```

**Explanation:**  
The compiler was found, but linking failed because R couldn’t find the actual Fortran runtime libraries.
We got past the compile step, but now the linker is still trying to use the old R-installed gfortran path (/opt/R/arm64/gfortran) instead of your Homebrew one.

**Fix:**
Find the correct Homebrew lib path:
```bash
ls -d /opt/homebrew/Cellar/gcc/*/lib/gcc/* | head -n 1
```

Add to `~/.R/Makevars`:
```make
FLIBS = -L/opt/homebrew/Cellar/gcc/13.2.0/lib/gcc/13 -lgfortran -lquadmath -lm
```

(Replace the version with your actual installed version)

###  Final `~/.R/Makevars` Example

```make
FC = /opt/homebrew/bin/gfortran
F77 = /opt/homebrew/bin/gfortran
FLIBS = -L/opt/homebrew/Cellar/gcc/13.2.0/lib/gcc/13 -lgfortran -lquadmath -lm
```
