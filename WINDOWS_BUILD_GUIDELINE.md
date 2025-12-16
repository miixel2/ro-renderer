# zrenderer — Windows Build Guide (Step-by-Step)

This guide provides comprehensive instructions for building **zrenderer** on **Windows 10/11** from source code.

**What you'll build:**
- **CLI tool** (command-line interface for rendering sprites)
- **Server** (web service for rendering sprites)

**Important notes:**
- Windows builds require the **MSVC toolchain** because native dependencies (libpng, lua5.1) are compiled during the first build
- The project uses **dub** (D language build tool) and **LDC2** compiler (recommended - DMD may have linking issues on Windows)
- Estimated time: 30-60 minutes for first-time setup

---

## Table of Contents

- [1. Overview of Required Tools](#1-overview-of-required-tools)
- [2. Step-by-Step Installation](#2-step-by-step-installation)
  - [2.1 Install Git](#21-install-git)
  - [2.2 Install Visual Studio Build Tools (C/C++ Compiler)](#22-install-visual-studio-build-tools-cc-compiler)
  - [2.3 Install D Language Compiler (LDC2) and Build Tool (dub)](#23-install-d-language-compiler-ldc2-and-build-tool-dub)
- [3. Clone the Repository](#3-clone-the-repository)
- [4. Open the Visual Studio Developer Command Prompt](#4-open-the-visual-studio-developer-command-prompt)
- [5. Build the Project](#5-build-the-project)
  - [5.1 Build the CLI Tool](#51-build-the-cli-tool)
  - [5.2 Build the Server](#52-build-the-server)
  - [5.3 Optional: Debug Build with Outlines](#53-optional-debug-build-with-outlines)
- [6. Verify Your Build](#6-verify-your-build)
- [7. Troubleshooting Common Issues](#7-troubleshooting-common-issues)
- [8. Next Steps: Running zrenderer](#8-next-steps-running-zrenderer)

---

## 1. Overview of Required Tools

Here's what you need to install and why:

| Tool | Purpose | Download Size |
|------|---------|---------------|
| **Git** | Clone the source code repository and fetch submodules | ~50 MB |
| **Visual Studio Build Tools** | C/C++ compiler (MSVC) needed to compile native dependencies | ~1-5 GB |
| **LDC2** | D language compiler (recommended for this project) | ~150 MB |
| **dub** | D language build tool and package manager (included with LDC2) | Included |

---

## 2. Step-by-Step Installation

### 2.1 Install Git

Git is required to download the source code from GitHub.

**Steps:**

1. **Download Git for Windows:**
   - Visit: https://git-scm.com/download/win
   - Click the download link for the latest version

2. **Run the installer:**
   - Use default settings (recommended)
   - Click "Next" through the installation wizard

3. **Verify Git is installed:**
   - Open **Command Prompt** or **PowerShell**
   - Type:
     ```shell
     git --version
     ```
   - You should see output like: `git version 2.x.x`

---

### 2.2 Install Visual Studio Build Tools (C/C++ Compiler)

The MSVC toolchain is required because the project compiles C libraries (libpng and lua5.1) during the first build.

**Steps:**

1. **Download Build Tools for Visual Studio:**
   - Visit: https://aka.ms/vs/stable/vs_BuildTools.exe
   - Or go to: https://visualstudio.microsoft.com/downloads/
   - Scroll down to "Tools for Visual Studio"
   - Download "Build Tools for Visual Studio 2022" or later

2. **Run the installer:**
   - When the Visual Studio Installer opens, select **"Desktop development with C++"**
   - On the right side, ensure these components are checked:
     - ✅ MSVC v143 - VS 2022 C++ x64/x86 build tools (latest)
     - ✅ Windows SDK (latest version)
     - ✅ C++ core features
   - Click **Install**
   - **Note:** This download is large (~1-5 GB) and may take 15-30 minutes

3. **After installation completes:**
   - You don't need to restart Windows
   - The "Developer Command Prompt" will be available in your Start Menu

**What this installs:**
- MSVC compiler (`cl.exe`)
- Linker (`link.exe`)
- Windows SDK headers and libraries
- Developer Command Prompt environment

---

### 2.3 Install D Language Compiler (LDC2) and Build Tool (dub)

LDC2 is the D language compiler. The `dub` build tool comes bundled with it.

**Steps:**

1. **Download LDC2:**
   - Visit: https://github.com/ldc-developers/ldc/releases
   - Find the latest release (e.g., `v1.39.0`)
   - Download the Windows installer or archive:
     - For installer: `ldc2-1.39.0-windows-x64.exe` (recommended)
     - For archive: `ldc2-1.39.0-windows-x64.7z` (requires manual extraction)

2. **Install LDC2:**

   **Option A: Using the installer (easier):**
   - Run the `.exe` installer
   - Follow the installation wizard
   - ✅ Check "Add LDC2 to PATH" when prompted
   - Complete the installation

   **Option B: Using the archive (manual):**
   - Extract the `.7z` file to a location like `C:\ldc2`
   - Add LDC2 to your PATH manually:
     1. Open Windows Settings → Search for "Environment Variables"
     2. Click "Edit the system environment variables"
     3. Click "Environment Variables" button
     4. Under "User variables", select `Path` and click "Edit"
     5. Click "New" and add: `C:\ldc2\bin` (adjust path as needed)
     6. Click "OK" to save

3. **Verify installation:**
   - **Close any open Command Prompt/PowerShell windows** (important!)
   - Open a **new Command Prompt** or **PowerShell**
   - Type:
     ```shell
     ldc2 --version
     ```
   - You should see output like: `LDC - the LLVM D compiler (1.39.0)`

   - Then verify dub:
     ```shell
     dub --version
     ```
   - You should see output like: `DUB version 1.x.x`

**If commands are not found:**
- Make sure LDC2's `bin` folder is in your PATH
- Close and reopen your terminal
- Try restarting your computer if PATH changes don't take effect

---

## 3. Clone the Repository

Now download the zrenderer source code from GitHub.

**Steps:**

1. **Open PowerShell or Command Prompt**

2. **Navigate to where you want to store the project:**
   ```shell
   cd C:\Users\YourName\Projects
   ```
   (Create a folder first if needed: `mkdir C:\Users\YourName\Projects`)

3. **Clone the repository:**
   ```shell
   git clone https://github.com/zhad3/zrenderer.git
   ```

4. **Navigate into the repository:**
   ```shell
   cd zrenderer
   ```

5. **⚠️ IMPORTANT: Initialize submodules:**
   ```shell
   git submodule update --init
   ```

   **Why?** The project depends on external libraries stored as git submodules (e.g., LuaD). Skipping this step will cause build errors.

**Expected result:**
- You should see a folder structure like:
  ```
  zrenderer/
  ├── cli/
  ├── server/
  ├── LuaD/
  ├── README.md
  └── ...
  ```

---

## 4. Open the Visual Studio Developer Command Prompt

**⚠️ CRITICAL STEP:** All build commands **must** be run in the Visual Studio Developer Command Prompt, not a regular Command Prompt or PowerShell. This special command prompt sets up environment variables for the MSVC compiler and linker.

**Steps:**

1. **Open the Start Menu**

2. **Search for one of these:**
   - `x64 Native Tools Command Prompt for VS 2022` **(recommended for 64-bit builds)**
   - `x86 Native Tools Command Prompt for VS 2022` (for 32-bit builds)

3. **Click to open it**
   - You'll see a command prompt window with text like:
     ```
     **********************************************************************
     ** Visual Studio 2022 Developer Command Prompt v17.x.x
     ** Copyright (c) 2022 Microsoft Corporation
     **********************************************************************
     ```

4. **Navigate to your zrenderer directory:**
   ```shell
   cd C:\Users\YourName\Projects\zrenderer
   ```
   (Adjust the path to match where you cloned the repository)

**Architecture note:**
- **x64** prompt → builds **64-bit** executables (recommended)
- **x86** prompt → builds **32-bit** executables

**Verification:**
- Type `cl` and press Enter
- You should see the Microsoft C/C++ compiler version info
- If you see `'cl' is not recognized`, you're not in the Developer Command Prompt

---

## 5. Build the Project

Now you're ready to compile zrenderer! Make sure you're in the **Visual Studio Developer Command Prompt** (see previous section).

**First build notes:**
- The first build will take longer (5-15 minutes) because `dub` downloads dependencies and compiles native libraries (libpng, lua5.1)
- Subsequent builds will be much faster
- You'll see lots of compiler output - this is normal

---

### 5.1 Build the CLI Tool

The CLI tool is the command-line version for rendering sprites locally.

**Development build (with debug symbols, faster compilation):**
```shell
dub build --compiler=ldc2 :cli
```

**Release build (optimized, recommended for production use):**
```shell
dub build --compiler=ldc2 --build=release :cli
```

**What happens during the build:**
1. `dub` downloads D language dependencies (if not cached)
2. Native dependencies (libpng, lua5.1) are compiled using MSVC
3. All D source files are compiled
4. Executable is linked and output to the project directory

**Expected output:**
- Last line should be: `Linking...` or `Performing "release" build using ldc2...`
- Executable will be created: `zrenderer.exe` (or `zrenderer-cli.exe` depending on configuration)

**Build time:**
- First build: 5-15 minutes (downloads + compilation)
- Subsequent builds: 1-3 minutes

---

### 5.2 Build the Server

The server is a web service that provides an HTTP API for rendering sprites.

**Development build:**
```shell
dub build --compiler=ldc2 :server
```

**Release build (recommended):**
```shell
dub build --compiler=ldc2 --build=release :server
```

**Expected output:**
- Similar to CLI build
- Executable will be created: `zrenderer-server.exe`

**Note:** The server requires additional setup (resources, configuration file) before it can run. See [Section 8](#8-next-steps-running-zrenderer) for details.

---

### 5.3 Optional: Debug Build with Outlines

For debugging sprite rendering, you can build with outline boxes that show sprite boundaries.

**CLI with outlines:**
```shell
dub build --compiler=ldc2 --debug=outline :cli
```

**Server with outlines:**
```shell
dub build --compiler=ldc2 --debug=outline :server
```

**What this does:**
- Adds colored boxes around sprite components to visualize positioning and boundaries
- Useful for debugging rendering issues

---

## 6. Verify Your Build

After building, verify that the executables were created successfully.

**Check for executables:**

In the Visual Studio Developer Command Prompt, run:
```shell
dir *.exe
```

**You should see:**
- `zrenderer.exe` or `zrenderer-cli.exe` (if you built the CLI)
- `zrenderer-server.exe` (if you built the server)

**Test the CLI:**
```shell
zrenderer.exe -h
```
or
```shell
zrenderer-cli.exe -h
```

**Expected output:**
- Help text showing all command-line options
- If you see `'zrenderer.exe' is not recognized`, the build may have failed

**Test the server:**
```shell
zrenderer-server.exe -h
```

**Expected output:**
- Same help text as CLI (server uses the same command-line parser)

**File sizes (approximate):**
- Debug build: 15-30 MB
- Release build: 3-8 MB (optimized and stripped)

---

## 7. Troubleshooting Common Issues

### 7.1 `ldc2` not found

**Error message:**
```
'ldc2' is not recognized as an internal or external command
```

**Solutions:**
1. Verify LDC2 is installed:
   - Run: `where ldc2` in Command Prompt
   - If nothing is found, reinstall LDC2

2. Check your PATH:
   - Open Command Prompt and run: `echo %PATH%`
   - Look for LDC2's `bin` directory (e.g., `C:\ldc2\bin`)
   - If missing, add it to PATH (see [Section 2.3](#23-install-d-language-compiler-ldc2-and-build-tool-dub))

3. Restart your terminal:
   - Close and reopen the Developer Command Prompt
   - PATH changes require a fresh terminal session

---

### 7.2 MSVC/Linker errors (`link.exe` missing, cannot find libraries)

**Error messages:**
```
link.exe is not recognized
LINK : fatal error LNK1104: cannot open file 'msvcrt.lib'
cl.exe is not recognized
```

**Cause:**
- Not running in the Visual Studio Developer Command Prompt
- MSVC environment variables are not set

**Solutions:**
1. **Close your current terminal**
2. **Open the Visual Studio Developer Command Prompt** (see [Section 4](#4-open-the-visual-studio-developer-command-prompt))
   - Search Start Menu for: `x64 Native Tools Command Prompt for VS 2022`
3. **Navigate back to the zrenderer directory**
4. **Retry the build command**

**Verify you're in the correct prompt:**
```shell
cl
```
- Should show MSVC compiler version
- If you see `'cl' is not recognized`, you're in the wrong terminal

---

### 7.2a Architecture mismatch errors (x86 vs x64 library conflicts)

**Error messages:**
```
warning LNK4272: library machine type 'x86' conflicts with target machine type 'x64'
error LNK2019: unresolved external symbol lua_pushstring
error LNK1120: 66 unresolved externals
```

**Cause:**
- Native C libraries (libpng, lua5.1) were previously built for the wrong architecture
- Cached libraries in AppData are for x86 (32-bit) but you're building for x64 (64-bit)
- `dub clean` doesn't remove cached native library builds

**Solution - Clear cached native libraries:**

1. **Verify you're in the x64 Developer Command Prompt:**
   ```shell
   echo %VSCMD_ARG_TGT_ARCH%
   ```
   Should show: `x64` (if it shows `x86`, switch to the x64 prompt)

2. **Clean all build artifacts including cached native libraries:**
   ```shell
   dub clean
   rmdir /s /q LuaD\c\build
   rmdir /s /q "%LOCALAPPDATA%\dub\packages\libpng-apng-1.0.5_apng.1.6.37\libpng-apng\c\build"
   ```

   **Note:** If the libpng version differs, adjust the path accordingly. Check with:
   ```shell
   dir "%LOCALAPPDATA%\dub\packages\libpng-apng-*"
   ```

3. **Rebuild for x64:**
   ```shell
   dub build --compiler=ldc2 --arch=x86_64 :cli
   ```

4. **Verify the libraries are x64 after rebuild:**
   ```shell
   dumpbin /headers "LuaD\c\build\x86_64-debug\liblua5.1.lib" | findstr "machine"
   ```
   Should show: `machine (x64)` (NOT `machine (x86)`)

**Important notes:**
- Always use `--arch=x86_64` for 64-bit builds
- Never use `--arch=x86` or `--arch=x86_mscoff` unless specifically building 32-bit
- Cached native libraries in AppData can cause architecture conflicts
- When switching architectures, always clean the native library build directories

---

### 7.3 Submodule-related errors

**Error messages:**
```
Error: Could not resolve module 'luad'
source/.../*.d: Error: cannot find source code for module 'LuaD.all'
```

**Cause:**
- Git submodules were not initialized
- The `LuaD` submodule directory is empty

**Solution:**
1. Navigate to your zrenderer directory
2. Run:
   ```shell
   git submodule update --init
   ```
3. Verify the `LuaD` folder exists and contains files:
   ```shell
   dir LuaD
   ```
   - Should show files like `dub.json`, `source`, etc.
4. Retry the build

**Alternative:** If still failing, try forcing a submodule refresh:
```shell
git submodule update --init --recursive --force
```

---

### 7.4 Network/Corporate proxy issues (dub downloads fail)

**Error messages:**
```
Failed to download package
Connection timeout
SSL certificate verification failed
```

**Cause:**
- Corporate firewall or proxy blocking `dub` package downloads
- SSL/TLS certificate issues

**Solutions:**

**Option 1: Configure proxy for dub:**
Set environment variables in your terminal:
```shell
set http_proxy=http://proxy.company.com:8080
set https_proxy=http://proxy.company.com:8080
```

**Option 2: Disable SSL verification (not recommended for production):**
```shell
set DUB_NO_SSL=1
```

**Option 3: Use a VPN or different network:**
- Connect to a network without proxy restrictions
- Download dependencies once, then they'll be cached locally

**Option 4: Download dependencies manually:**
- Some dependencies can be manually downloaded and placed in `%APPDATA%\dub\packages\`
- See `dub` documentation: https://dub.pm/

---

### 7.5 OpenSSL version errors (vibe-d linking issues)

**Error messages:**
```
Error: undefined reference to 'SSL_...'
Could not link vibe-d
```

**Cause:**
- Using OpenSSL 1.x.x instead of 3.x.x
- vibe-d version incompatibility

**Solution:**

1. Check OpenSSL version:
   ```shell
   openssl version
   ```

2. If you have OpenSSL 1.x.x, downgrade vibe-d:
   - Edit `server/dub.json`
   - Find the `dependencies` section
   - Change:
     ```json
     "vibe-d:http": "==0.9.6"
     ```
     to:
     ```json
     "vibe-d:http": "==0.9.3"
     ```

3. Delete cached selections:
   ```shell
   del server\dub.selections.json
   ```

4. Rebuild:
   ```shell
   dub build --compiler=ldc2 :server
   ```

---

### 7.6 Out of memory during compilation

**Error messages:**
```
Out of memory
lld-link: error: out of memory
```

**Cause:**
- Large codebase + debug symbols can consume lots of RAM
- 32-bit linker on very large projects

**Solutions:**

1. **Build in release mode** (uses less memory):
   ```shell
   dub build --compiler=ldc2 --build=release :cli
   ```

2. **Use 64-bit Developer Command Prompt:**
   - Open `x64 Native Tools Command Prompt` instead of `x86`

3. **Close other applications** to free up RAM

4. **Add incremental linking:**
   - Edit `dub.json` and add linker flags if needed

---

### 7.7 Permission denied errors

**Error messages:**
```
Access denied
Permission denied when writing to file
```

**Cause:**
- Executable is currently running
- Antivirus blocking file writes
- File permissions issue

**Solutions:**

1. **Close any running zrenderer processes:**
   - Check Task Manager for `zrenderer.exe` or `zrenderer-server.exe`
   - End the process
   - Retry build

2. **Run terminal as Administrator:**
   - Right-click Developer Command Prompt
   - Select "Run as Administrator"

3. **Check antivirus:**
   - Temporarily disable antivirus
   - Add zrenderer directory to antivirus exclusions

4. **Clean and rebuild:**
   ```shell
   dub clean
   dub build --compiler=ldc2 :cli
   ```

---

## 8. Next Steps: Running zrenderer

You've successfully built zrenderer! But before you can render sprites, you need:

### 8.1 Required Resources

zrenderer needs game assets from Ragnarok Online to function. See the [RESOURCES.md](RESOURCES.md) file in the repository for:
- Required game data files (GRF archives, data folder)
- Where to place resource files
- How to extract assets

**Quick overview:**
- You need data from an official Ragnarok Online client installation
- Required files: `.grf` archives or extracted `data` folder
- Files must be placed in a `resources` directory (configurable)

### 8.2 Configuration File

The tools use a configuration file to locate resources.

**Create `zrenderer.conf`:**
```ini
resourcepath=C:\path\to\ragnarok\resources
outdir=output
```

**Adjust paths as needed:**
- `resourcepath`: Directory containing GRF files or extracted game data
- `outdir`: Where rendered sprites will be saved

### 8.3 Running the CLI

**Basic usage:**
```shell
zrenderer.exe --job=1001
```

**This will:**
- Render monster/sprite ID 1001
- Use default resources from `resourcepath` in config
- Output to the `output` directory
- Generate an APNG animation

**More examples:**
```shell
REM Render character with equipment
zrenderer.exe --job=4012 --headgear=125 --weapon=2 --action=0

REM Render specific frame only
zrenderer.exe --job=1870 --action=16 --frame=10

REM Use custom output directory
zrenderer.exe --job=1001 --outdir=my_sprites
```

See the [README.md](README.md) for more examples and complete CLI options.

### 8.4 Running the Server

**Start the server:**
```shell
zrenderer-server.exe
```

**On first run:**
- An access token will be automatically generated
- The token will be printed to the console and saved to `accesstokens.conf`
- Copy this token - you'll need it for API requests

**API documentation:**
- OpenAPI specs: [server/api-spec](server/api-spec)
- HTML docs: https://z0q.neocities.org/ragnarok-online-tools/zrenderer/api/

**Testing the server:**
```shell
curl http://localhost:11011/render?job=1001 -H "Authorization: Bearer YOUR_TOKEN"
```

---

## Summary Checklist

✅ **Installation complete:**
- [x] Git installed and verified
- [x] Visual Studio Build Tools installed (Desktop development with C++)
- [x] LDC2 and dub installed and in PATH
- [x] Repository cloned with submodules initialized

✅ **Build successful:**
- [x] Built in Visual Studio Developer Command Prompt (not regular terminal)
- [x] CLI executable created (`zrenderer.exe`)
- [x] Server executable created (`zrenderer-server.exe`)
- [x] Help command works (`zrenderer.exe -h`)

✅ **Ready to use:**
- [x] Resources obtained (see [RESOURCES.md](RESOURCES.md))
- [x] Configuration file created (`zrenderer.conf`)
- [x] Tested rendering a sprite

---

## Additional Resources

- **Main README:** [README.md](README.md) - Full documentation and usage examples
- **Resources Guide:** [RESOURCES.md](RESOURCES.md) - How to obtain and setup game assets
- **API Documentation:** https://z0q.neocities.org/ragnarok-online-tools/zrenderer/api/
- **LDC2 Documentation:** https://wiki.dlang.org/LDC
- **Dub Documentation:** https://dub.pm/

---

**Need help?**
- Open an issue on GitHub: https://github.com/zhad3/zrenderer/issues
- Check existing issues for solutions to common problems
