
# 🧪 R Package Compilation Troubleshooting on macOS ARM64 (M1/M2)

This guide documents the journey of troubleshooting and resolving R package installation and compilation issues on a macOS ARM64 system (like M1 or M2 chips).

---

## 🔍 Summary of Issues Encountered

### 1. `devtools` Failed Due to Missing `usethis`

**Error:**
```
Error: package ‘usethis’ required by ‘devtools’ could not be found
```

**Explanation:**  
`devtools` depends on `usethis`, which requires other dependencies.

---

### 2. `usethis` Failed Due to Missing `gert`

**Error:**
```
installation of package ‘gert’ had non-zero exit status
```

**Explanation:**  
`gert` needs system-level dependencies like `libgit2`, often not configured on macOS by default.

---

### 3. Compilation Errors in C++ Packages

**Common Errors:**
```
fatal error: 'iostream' file not found
fatal error: 'climits' file not found
fatal error: 'cstring' file not found
```

**Explanation:**  
Standard headers not found — typically indicates missing or broken Xcode Command Line Tools.

**Fix:**
```bash
xcode-select --install
```

---

### 4. `classInt` Failed Due to Missing `gfortran`

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
  ```make
  FC = /opt/homebrew/bin/gfortran
  F77 = /opt/homebrew/bin/gfortran
  ```

---

### 5. Linker Errors Due to Missing Fortran Libraries

**Error:**
```
ld: library 'gfortran' not found
ld: warning: search path '/opt/R/arm64/gfortran/lib' not found
```

**Explanation:**  
The compiler was found, but linking failed because R couldn’t find the actual Fortran runtime libraries.

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

---

## ✅ Final `~/.R/Makevars` Example

```make
FC = /opt/homebrew/bin/gfortran
F77 = /opt/homebrew/bin/gfortran
FLIBS = -L/opt/homebrew/Cellar/gcc/13.2.0/lib/gcc/13 -lgfortran -lquadmath -lm
```

---

## 💡 Lessons Learned

- macOS ARM64 requires **manual configuration** for R compilation.
- Always install **Xcode Command Line Tools** and **Homebrew GCC**.
- Keep an eye on **where compilers and libraries live** — R doesn’t always find them on its own.

---

## 🙌 Happy Coding!

If you're reading this from GitHub — feel free to fork and update for newer versions of R or macOS 🎉
