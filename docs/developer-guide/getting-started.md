# Developer Getting Started Guide

Welcome to Zen Browser development! This guide will help you set up your development environment and build Zen for the first time.

## Prerequisites

### System Requirements

- **Operating System**: Linux, macOS, or Windows (with WSL recommended)
- **RAM**: At least 8GB (16GB+ recommended for faster builds)
- **Storage**: At least 50GB of free space
- **Internet**: Stable connection for downloading Firefox source and dependencies

### Required Tools

1. **Node.js and npm**
   - Node.js 18+ (specified in `.nvmrc`)
   - npm (comes with Node.js)
   
   ```bash
   # Check versions
   node --version  # Should be 18+
   npm --version
   ```

2. **Python 3**
   - Python 3.9+ (specified in `.python-version`)
   - Required for build scripts and Firefox integration
   
   ```bash
   # Check version
   python3 --version  # Should be 3.9+
   ```

3. **Rust**
   - Latest stable Rust toolchain (specified in `.rust-toolchain`)
   - Required for Firefox components and Zen's Rust tools
   
   ```bash
   # Install Rust
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   
   # Check version
   rustc --version
   cargo --version
   ```

4. **Build Tools** (Platform-specific)

   **Linux (Ubuntu/Debian)**:
   ```bash
   sudo apt update
   sudo apt install build-essential git python3-pip \
                    libgtk-3-dev libx11-dev libxtst6 \
                    libasound2-dev libcurl4-openssl-dev
   ```

   **macOS**:
   ```bash
   # Install Xcode command line tools
   xcode-select --install
   
   # Install Homebrew if not already installed
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   **Windows**:
   - Install [Build Tools for Visual Studio](https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2022)
   - Use WSL2 with Ubuntu for best compatibility

## Initial Setup

### 1. Clone the Repository

```bash
git clone https://github.com/zen-browser/desktop.git zen-desktop
cd zen-desktop
```

### 2. Install Dependencies

```bash
# Install Node.js dependencies
npm install

# Install Python dependencies (if working with scripts)
pip3 install -r requirements.txt
```

### 3. Download Firefox Engine

Zen is built on top of Firefox. The first step is downloading the Firefox source:

```bash
# Download Firefox source code
npm run download
```

This command downloads the Firefox source code matching the version specified in `surfer.json`. This can take 15-30 minutes depending on your internet connection.

### 4. Import and Bootstrap

```bash
# Import Zen modifications into Firefox source
npm run import

# Bootstrap the build environment
npm run bootstrap
```

The import process applies Zen's patches and modifications to the Firefox source. Bootstrap sets up the build configuration.

### 5. Build Zen

```bash
# Build the complete browser
npm run build
```

**Note**: The first build can take 1-3 hours depending on your system. Subsequent builds are much faster due to incremental compilation.

For UI-only changes, you can use a faster build:

```bash
# Build only UI components (faster for frontend changes)
npm run build:ui
```

### 6. Run Zen

```bash
# Start Zen browser
npm start
```

This launches Zen in development mode with debugging enabled.

## Development Workflow

### Daily Development

1. **Pull latest changes**:
   ```bash
   git pull origin dev
   npm install  # In case dependencies changed
   ```

2. **Update Firefox if needed**:
   ```bash
   npm run update-ff
   ```

3. **Make your changes** in the `src/` directory

4. **Build and test**:
   ```bash
   npm run build:ui  # For UI changes
   # or
   npm run build     # For comprehensive changes
   
   npm start         # Test your changes
   ```

5. **Run tests**:
   ```bash
   npm test
   ```

6. **Lint and format**:
   ```bash
   npm run lint      # Check for issues
   npm run lint:fix  # Auto-fix issues
   npm run pretty    # Format code
   ```

### Key Commands Reference

| Command | Purpose |
|---------|---------|
| `npm run build` | Full build of Zen browser |
| `npm run build:ui` | Build only UI components (faster) |
| `npm start` | Run Zen in development mode |
| `npm run import` | Apply Zen modifications to Firefox |
| `npm run export` | Export current changes |
| `npm run download` | Download Firefox source |
| `npm run bootstrap` | Set up build environment |
| `npm run update-ff` | Update to newer Firefox version |
| `npm test` | Run test suite |
| `npm run lint` | Check code style and errors |
| `npm run package` | Create distribution package |

## Common Issues and Solutions

### Build Failures

1. **Out of memory errors**:
   - Increase system RAM or swap space
   - Close other applications during build
   - Use `npm run build:ui` for smaller changes

2. **Missing dependencies**:
   ```bash
   # Reinstall all dependencies
   rm -rf node_modules package-lock.json
   npm install
   ```

3. **Corrupted Firefox source**:
   ```bash
   # Re-download and bootstrap
   npm run reset-ff
   npm run download
   npm run import
   npm run bootstrap
   ```

### Runtime Issues

1. **Browser won't start**:
   - Check console output for error messages
   - Ensure all build steps completed successfully
   - Try rebuilding: `npm run build`

2. **Missing features**:
   - Verify your branch: `git branch`
   - Check if you're on the correct branch (usually `dev`)

### Getting Help

- **Documentation**: Check other guides in `docs/developer-guide/`
- **Issues**: [GitHub Issues](https://github.com/zen-browser/desktop/issues)
- **Discussions**: [GitHub Discussions](https://github.com/zen-browser/desktop/discussions)
- **Community**: [Discord Server](https://discord.gg/zen-browser)

## Next Steps

After successfully building Zen:

1. Read the [Architecture Overview](./architecture.md) to understand how Zen works
2. Explore the [Codebase Structure Guide](./codebase-structure.md) to navigate the code
3. Check [Component Documentation](./components/) for specific features
4. Review the [Testing Guide](./testing.md) for writing tests
5. See [Contributing Guidelines](../contribute.md) for making contributions

## Tips for New Contributors

1. **Start small**: Begin with documentation fixes or minor UI improvements
2. **Use incremental builds**: `npm run build:ui` is much faster for UI changes
3. **Test frequently**: Run `npm start` after changes to catch issues early
4. **Follow the style**: Use `npm run lint:fix` to maintain code consistency
5. **Ask questions**: The community is helpful - don't hesitate to ask!

Welcome to the Zen Browser development community! 🚀