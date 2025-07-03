# ASDF Environment Management

## Purpose
Provides comprehensive ASDF version manager configuration for managing multiple runtime versions of different programming languages and tools in a single, consistent interface.

## ASDF Strategy

### Primary Tool: ASDF Version Manager
- **Multi-language support**: Manage Node.js, Python, Ruby, Go, Rust, and many other tools
- **Plugin ecosystem**: Extensive plugin system for different languages and tools  
- **Version consistency**: Lock specific versions per project with `.tool-versions`
- **Global defaults**: Set global default versions while allowing project overrides

### Environment Detection and Setup
1. **Check for ASDF files**: Look for `.tool-versions`, `.asdfrc`
2. **Validate ASDF installation**: Ensure ASDF is installed and configured
3. **Plugin management**: Install required plugins automatically
4. **Version synchronization**: Ensure specified versions are installed

## ASDF Installation and Configuration

### Initial ASDF Setup
```bash
# Install ASDF (Linux/macOS)
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.13.1

# Add to shell configuration
echo '. "$HOME/.asdf/asdf.sh"' >> ~/.bashrc
echo '. "$HOME/.asdf/completions/asdf.bash"' >> ~/.bashrc

# Reload shell
source ~/.bashrc

# Verify installation
asdf --version
```

### Global Configuration (.asdfrc)
```bash
# ~/.asdfrc
legacy_version_file = yes
use_release_candidates = no
always_keep_download = no
plugin_repository_last_check_duration = 60
disable_plugin_short_name_repository = no
```

## Project-Level Version Management

### Basic .tool-versions File
```bash
# .tool-versions
nodejs 20.9.0
python 3.11.6
go 1.21.4
rust 1.74.0
postgres 15.4
redis 7.2.3
```

### Advanced .tool-versions with Comments
```bash
# .tool-versions - Development environment versions
# Web development
nodejs 20.9.0        # LTS version for stability
typescript 5.2.2     # Latest stable TypeScript

# Backend development  
python 3.11.6        # Latest stable Python
go 1.21.4           # Latest Go version

# Database and caching
postgres 15.4        # Database server
redis 7.2.3         # Cache and session store

# DevOps tools
kubectl 1.28.4      # Kubernetes CLI
terraform 1.6.3     # Infrastructure as code
helm 3.13.2         # Kubernetes package manager

# Development tools
awscli 2.13.25      # AWS command line
gh 2.37.0           # GitHub CLI
```

## Language-Specific Plugin Management

### Node.js Development
```bash
# Install Node.js plugin
asdf plugin add nodejs https://github.com/asdf-vm/asdf-nodejs.git

# Install specific Node.js versions
asdf install nodejs 18.18.2
asdf install nodejs 20.9.0
asdf install nodejs latest

# Set global default
asdf global nodejs 20.9.0

# Set project-specific version
echo "nodejs 20.9.0" >> .tool-versions

# Install npm packages globally for specific version
asdf exec nodejs 20.9.0 npm install -g typescript eslint prettier
```

### Python Development
```bash
# Install Python plugin
asdf plugin add python

# Install specific Python versions
asdf install python 3.10.13
asdf install python 3.11.6
asdf install python 3.12.0

# Set versions
asdf global python 3.11.6
echo "python 3.11.6" >> .tool-versions

# Install Python tools
asdf exec python pip install poetry black ruff mypy
```

### Go Development
```bash
# Install Go plugin
asdf plugin add golang https://github.com/kennyp/asdf-golang.git

# Install Go versions
asdf install golang 1.20.11
asdf install golang 1.21.4

# Set versions
asdf global golang 1.21.4
echo "go 1.21.4" >> .tool-versions

# Install Go tools
asdf exec golang go install golang.org/x/tools/cmd/goimports@latest
asdf exec golang go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest
```

### Multi-Language Project Configuration
```bash
# .tool-versions for full-stack project
nodejs 20.9.0
python 3.11.6  
go 1.21.4
rust 1.74.0
ruby 3.2.2

# Database tools
postgres 15.4
redis 7.2.3
mongodb 7.0.2

# DevOps tools
docker-compose 2.23.0
kubectl 1.28.4
helm 3.13.2
terraform 1.6.3

# Development utilities
gh 2.37.0
awscli 2.13.25
jq 1.7
```

## Automated Environment Setup

### Project Setup Script
```bash
#!/bin/bash
# scripts/setup-env.sh

set -e

echo "Setting up ASDF environment..."

# Check if ASDF is installed
if ! command -v asdf &> /dev/null; then
    echo "ASDF not found. Please install ASDF first."
    exit 1
fi

# Read .tool-versions and install missing plugins/versions
while IFS= read -r line; do
    # Skip empty lines and comments
    [[ -z "$line" || "$line" =~ ^[[:space:]]*# ]] && continue
    
    # Parse tool and version
    tool=$(echo "$line" | awk '{print $1}')
    version=$(echo "$line" | awk '{print $2}')
    
    echo "Processing $tool $version..."
    
    # Install plugin if not exists
    if ! asdf plugin list | grep -q "^$tool$"; then
        echo "Installing plugin: $tool"
        asdf plugin add "$tool"
    fi
    
    # Install version if not exists
    if ! asdf list "$tool" | grep -q "$version"; then
        echo "Installing $tool $version..."
        asdf install "$tool" "$version"
    fi
    
done < .tool-versions

echo "Installing packages for each tool..."

# Node.js global packages
if asdf list nodejs &> /dev/null; then
    echo "Installing Node.js global packages..."
    npm install -g typescript @types/node eslint prettier nodemon
fi

# Python global packages
if asdf list python &> /dev/null; then
    echo "Installing Python global packages..."
    pip install poetry black ruff mypy pytest
fi

# Go global tools
if asdf list golang &> /dev/null; then
    echo "Installing Go global tools..."
    go install golang.org/x/tools/cmd/goimports@latest
    go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest
fi

echo "Environment setup complete!"
```

### Automatic Plugin Installation
```bash
#!/bin/bash
# scripts/install-asdf-plugins.sh

# Common plugins with their repositories
declare -A plugins=(
    ["nodejs"]="https://github.com/asdf-vm/asdf-nodejs.git"
    ["python"]="https://github.com/danhper/asdf-python.git"
    ["golang"]="https://github.com/kennyp/asdf-golang.git"
    ["rust"]="https://github.com/code-lever/asdf-rust.git"
    ["ruby"]="https://github.com/asdf-vm/asdf-ruby.git"
    ["postgres"]="https://github.com/smashedtoatoms/asdf-postgres.git"
    ["redis"]="https://github.com/smashedtoatoms/asdf-redis.git"
    ["kubectl"]="https://github.com/asdf-community/asdf-kubectl.git"
    ["terraform"]="https://github.com/asdf-community/asdf-hashicorp.git"
    ["helm"]="https://github.com/Antiarchitect/asdf-helm.git"
)

# Install plugins
for plugin in "${!plugins[@]}"; do
    if ! asdf plugin list | grep -q "^$plugin$"; then
        echo "Installing $plugin plugin..."
        asdf plugin add "$plugin" "${plugins[$plugin]}"
    else
        echo "$plugin plugin already installed"
    fi
done

echo "All plugins installed!"
```

## Development Workflow Integration

### Standard ASDF Commands
```bash
# Version management
asdf current                    # Show all current versions
asdf current nodejs            # Show current Node.js version
asdf list nodejs               # List installed Node.js versions
asdf list all nodejs           # List all available Node.js versions

# Installation
asdf install                   # Install all versions from .tool-versions
asdf install nodejs 20.9.0    # Install specific version
asdf install nodejs latest    # Install latest version

# Version switching
asdf local nodejs 18.18.2     # Set local version (creates .tool-versions)
asdf global nodejs 20.9.0     # Set global version
asdf shell nodejs 20.9.0      # Set version for current shell

# Cleanup
asdf uninstall nodejs 18.18.2 # Uninstall specific version
asdf plugin remove nodejs     # Remove plugin and all versions
```

### Environment Activation Scripts
```bash
#!/bin/bash
# scripts/activate-env.sh

# Check for .tool-versions file
if [ ! -f .tool-versions ]; then
    echo "No .tool-versions file found"
    exit 1
fi

# Install all versions from .tool-versions
echo "Installing versions from .tool-versions..."
asdf install

# Show current versions
echo "Current tool versions:"
asdf current

# Set up development environment
echo "Setting up development environment..."

# Install project dependencies based on detected tools
if asdf current nodejs &> /dev/null; then
    if [ -f package.json ]; then
        echo "Installing Node.js dependencies..."
        npm install
    fi
fi

if asdf current python &> /dev/null; then
    if [ -f requirements.txt ]; then
        echo "Installing Python dependencies..."
        pip install -r requirements.txt
    elif [ -f pyproject.toml ]; then
        echo "Installing Poetry dependencies..."
        poetry install
    fi
fi

if asdf current golang &> /dev/null; then
    if [ -f go.mod ]; then
        echo "Downloading Go dependencies..."
        go mod download
    fi
fi

echo "Environment activated successfully!"
```

## Legacy Version File Support

### Integration with Existing Tools
```bash
# .asdfrc configuration for legacy support
legacy_version_file = yes

# This allows ASDF to read:
# .node-version (Node.js)
# .python-version (Python)  
# .ruby-version (Ruby)
# .go-version (Go)
# And convert them to .tool-versions format
```

### Migration from Other Version Managers
```bash
#!/bin/bash
# scripts/migrate-to-asdf.sh

echo "Migrating from other version managers to ASDF..."

# Migrate from nvm (Node.js)
if [ -f .nvmrc ]; then
    node_version=$(cat .nvmrc)
    echo "nodejs $node_version" >> .tool-versions
    echo "Migrated Node.js version from .nvmrc"
fi

# Migrate from pyenv (Python)
if [ -f .python-version ]; then
    python_version=$(cat .python-version)
    echo "python $python_version" >> .tool-versions
    echo "Migrated Python version from .python-version"
fi

# Migrate from rbenv (Ruby)
if [ -f .ruby-version ]; then
    ruby_version=$(cat .ruby-version)
    echo "ruby $ruby_version" >> .tool-versions
    echo "Migrated Ruby version from .ruby-version"
fi

# Migrate from gvm (Go)
if [ -f .go-version ]; then
    go_version=$(cat .go-version)
    echo "golang $go_version" >> .tool-versions
    echo "Migrated Go version from .go-version"
fi

echo "Migration complete! Review .tool-versions file."
```

## Shell Integration and Automation

### Shell Hook Configuration
```bash
# ~/.bashrc or ~/.zshrc
. "$HOME/.asdf/asdf.sh"
. "$HOME/.asdf/completions/asdf.bash"

# Auto-activate environment when entering directory
cd() {
    builtin cd "$@"
    if [ -f .tool-versions ]; then
        echo "Found .tool-versions, checking environment..."
        asdf current
    fi
}

# Alias for quick environment setup
alias env-setup="bash scripts/setup-env.sh"
alias env-activate="bash scripts/activate-env.sh"
```

### VS Code Integration
```json
{
  "asdf.checkOnStartup": true,
  "asdf.autoInstall": true,
  "asdf.defaultPlugins": [
    "nodejs",
    "python", 
    "golang",
    "rust"
  ],
  "terminal.integrated.env.linux": {
    "PATH": "${env:HOME}/.asdf/shims:${env:PATH}"
  }
}
```

## Performance Optimization

### Plugin Management
```bash
# Update all plugins
asdf plugin update --all

# Update specific plugin
asdf plugin update nodejs

# List outdated versions
asdf list all nodejs | tail -5  # Show latest versions

# Cleanup old versions
asdf uninstall nodejs 16.20.2   # Remove old versions
```

### Shim Optimization
```bash
# Reshape shims (run after installing new tools)
asdf reshim

# List all shims
asdf shim-versions nodejs

# Direct execution (bypass shims for performance)
~/.asdf/installs/nodejs/20.9.0/bin/node --version
```

## Error Handling and Troubleshooting

### Common Issues and Solutions
```bash
# Plugin installation issues
asdf plugin update nodejs       # Update plugin
asdf plugin remove nodejs       # Remove and reinstall
asdf plugin add nodejs

# Version installation failures
asdf install nodejs 20.9.0     # Retry installation
ASDF_NODEJS_FORCE_COMPILE=1 asdf install nodejs 20.9.0  # Force compile

# Shim issues
asdf reshim                     # Rebuild shims
asdf which node                 # Check which version is active

# Environment debugging
asdf current                    # Show all current versions
asdf where nodejs 20.9.0       # Show installation path
echo $ASDF_DIR                  # Check ASDF directory
```

### Validation Scripts
```bash
#!/bin/bash
# scripts/validate-env.sh

echo "Validating ASDF environment..."

# Check ASDF installation
if ! command -v asdf &> /dev/null; then
    echo "❌ ASDF not installed"
    exit 1
fi
echo "✅ ASDF installed"

# Check .tool-versions exists
if [ ! -f .tool-versions ]; then
    echo "❌ No .tool-versions file found"
    exit 1
fi
echo "✅ .tool-versions found"

# Validate each tool in .tool-versions
while IFS= read -r line; do
    [[ -z "$line" || "$line" =~ ^[[:space:]]*# ]] && continue
    
    tool=$(echo "$line" | awk '{print $1}')
    version=$(echo "$line" | awk '{print $2}')
    
    if asdf list "$tool" | grep -q "$version"; then
        echo "✅ $tool $version installed"
    else
        echo "❌ $tool $version not installed"
        echo "   Run: asdf install $tool $version"
    fi
done < .tool-versions

echo "Environment validation complete!"
```

## Integration with Other Modules

### Environment Management Coordination
- Provides alternative to Flox for teams preferring traditional version managers
- Works alongside Docker environments when needed
- Supports gradual migration from other version managers

### Code Quality Integration
- Ensures consistent tool versions for linting and formatting
- Integrates with CI/CD systems using same version specifications
- Supports language-specific quality tool installation

### Universal Commands Compatibility
- All standard commands work with ASDF-managed tool versions
- Environment-aware execution detects ASDF automatically
- Consistent command interface across different tool versions

### Project Detection Enhancement
- Detects ASDF-managed projects through .tool-versions presence
- Automatically installs missing versions when entering projects
- Supports complex multi-language project configurations