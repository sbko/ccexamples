# Nix Environment Management

## Purpose
Provides comprehensive Nix-based development environment management using Nix flakes, direnv integration, and declarative development shells for reproducible, deterministic development environments.

## Nix Development Strategy

### Primary Tools
- **Nix Flakes**: Modern, reproducible Nix configurations
- **direnv**: Automatic environment activation
- **nix-shell**: On-demand development environments
- **Home Manager**: User environment management

### Environment Detection and Setup
1. **Check for Nix files**: Look for `flake.nix`, `shell.nix`, `.envrc`
2. **Validate Nix installation**: Ensure Nix package manager is available
3. **Enable flakes**: Verify experimental flakes feature is enabled
4. **Configure direnv**: Set up automatic environment loading

## Nix Flakes Configuration

### Basic flake.nix Structure
```nix
{
  description = "Development environment for my project";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs = { self, nixpkgs, flake-utils }:
    flake-utils.lib.eachDefaultSystem (system:
      let
        pkgs = nixpkgs.legacyPackages.${system};
      in
      {
        devShells.default = pkgs.mkShell {
          buildInputs = with pkgs; [
            # Development tools
            git
            curl
            jq
            
            # Language-specific tools
            nodejs_20
            python311
            go_1_21
            rustc
            cargo
            
            # Database tools
            postgresql
            redis
            
            # Build tools
            gnumake
            gcc
          ];

          shellHook = ''
            echo "Development environment loaded!"
            echo "Node.js version: $(node --version)"
            echo "Python version: $(python --version)"
            echo "Go version: $(go version)"
          '';

          env = {
            NODE_ENV = "development";
            PYTHON_PATH = "./src";
            GOPATH = "$HOME/go";
          };
        };
      });
}
```

### Language-Specific Flakes

#### Node.js Development
```nix
{
  description = "Node.js development environment";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs = { self, nixpkgs, flake-utils }:
    flake-utils.lib.eachDefaultSystem (system:
      let
        pkgs = nixpkgs.legacyPackages.${system};
      in
      {
        devShells.default = pkgs.mkShell {
          buildInputs = with pkgs; [
            nodejs_20
            npm-check-updates
            nodePackages.typescript
            nodePackages.eslint
            nodePackages.prettier
            nodePackages.nodemon
          ];

          shellHook = ''
            echo "Node.js development environment"
            echo "Node version: $(node --version)"
            echo "NPM version: $(npm --version)"
            
            # Set up project
            if [ ! -d node_modules ]; then
              echo "Installing dependencies..."
              npm install
            fi
          '';
        };
      });
}
```

#### Python Development
```nix
{
  description = "Python development environment";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs = { self, nixpkgs, flake-utils }:
    flake-utils.lib.eachDefaultSystem (system:
      let
        pkgs = nixpkgs.legacyPackages.${system};
        python = pkgs.python311;
        pythonPackages = python.pkgs;
      in
      {
        devShells.default = pkgs.mkShell {
          buildInputs = with pkgs; [
            python
            pythonPackages.pip
            pythonPackages.virtualenv
            pythonPackages.poetry
            pythonPackages.black
            pythonPackages.ruff
            pythonPackages.mypy
            pythonPackages.pytest
          ];

          shellHook = ''
            echo "Python development environment"
            echo "Python version: $(python --version)"
            
            # Create virtual environment if it doesn't exist
            if [ ! -d venv ]; then
              echo "Creating virtual environment..."
              python -m venv venv
            fi
            
            # Activate virtual environment
            source venv/bin/activate
          '';

          env = {
            PYTHONPATH = "./src:$PYTHONPATH";
          };
        };
      });
}
```

#### Go Development
```nix
{
  description = "Go development environment";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs = { self, nixpkgs, flake-utils }:
    flake-utils.lib.eachDefaultSystem (system:
      let
        pkgs = nixpkgs.legacyPackages.${system};
      in
      {
        devShells.default = pkgs.mkShell {
          buildInputs = with pkgs; [
            go_1_21
            gotools
            golangci-lint
            delve
            air
          ];

          shellHook = ''
            echo "Go development environment"
            echo "Go version: $(go version)"
            
            # Set up Go workspace
            export GOPATH="$HOME/go"
            export PATH="$GOPATH/bin:$PATH"
            
            # Download dependencies
            if [ -f go.mod ]; then
              go mod download
            fi
          '';

          env = {
            CGO_ENABLED = "1";
          };
        };
      });
}
```

## direnv Integration

### .envrc Configuration
```bash
# .envrc
if ! has nix_direnv_version || ! nix_direnv_version 2.3.0; then
  source_url "https://raw.githubusercontent.com/nix-community/nix-direnv/2.3.0/direnvrc" "sha256-Dmd+j63L84wuzgyjITIfSxSD57Tx7v51DMxVZOsiUD8="
fi

use flake

# Additional environment variables
export NODE_ENV=development
export DATABASE_URL="postgresql://localhost:5432/myapp_dev"
export REDIS_URL="redis://localhost:6379"

# Path additions
PATH_add ./scripts
PATH_add ./bin
```

### Advanced .envrc with Conditionals
```bash
# .envrc with environment detection
if ! has nix_direnv_version || ! nix_direnv_version 2.3.0; then
  source_url "https://raw.githubusercontent.com/nix-community/nix-direnv/2.3.0/direnvrc" "sha256-Dmd+j63L84wuzgyjITIfSxSD57Tx7v51DMxVZOsiUD8="
fi

# Use different flake outputs based on environment
if [[ "${CI:-}" == "true" ]]; then
  use flake .#ci
elif [[ "${NODE_ENV:-}" == "production" ]]; then
  use flake .#production
else
  use flake .#default
fi

# Load secrets from external file
if [[ -f .env.local ]]; then
  dotenv .env.local
fi

# Set up development databases
if command -v postgresql >/dev/null; then
  export DATABASE_URL="postgresql://localhost:5432/$(basename "$PWD")_dev"
fi
```

## Legacy shell.nix Support

### Basic shell.nix
```nix
{ pkgs ? import <nixpkgs> {} }:

pkgs.mkShell {
  buildInputs = with pkgs; [
    nodejs_20
    python311
    go_1_21
    postgresql
    redis
    git
    curl
    jq
  ];

  shellHook = ''
    echo "Legacy Nix shell environment loaded"
    export NODE_ENV=development
    export GOPATH=$HOME/go
  '';
}
```

### Advanced shell.nix with Overlays
```nix
{ pkgs ? import <nixpkgs> {
    overlays = [
      (self: super: {
        nodejs = super.nodejs_20;
        python = super.python311;
      })
    ];
  }
}:

let
  pythonPackages = pkgs.python.pkgs;
in
pkgs.mkShell {
  buildInputs = with pkgs; [
    nodejs
    python
    pythonPackages.pip
    pythonPackages.virtualenv
    go_1_21
    rustc
    cargo
    
    # Development tools
    git
    gh
    curl
    jq
    
    # Database tools
    postgresql
    redis
    sqlite
  ];

  shellHook = ''
    echo "Advanced Nix development environment"
    
    # Set up Python virtual environment
    if [ ! -d venv ]; then
      python -m venv venv
    fi
    source venv/bin/activate
    
    # Set up Go workspace
    export GOPATH="$HOME/go"
    export PATH="$GOPATH/bin:$PATH"
    
    # Install npm dependencies
    if [ -f package.json ] && [ ! -d node_modules ]; then
      npm install
    fi
  '';
}
```

## Multiple Environment Configurations

### Multi-Output Flake
```nix
{
  description = "Multi-environment development setup";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs = { self, nixpkgs, flake-utils }:
    flake-utils.lib.eachDefaultSystem (system:
      let
        pkgs = nixpkgs.legacyPackages.${system};
        
        # Common development tools
        commonTools = with pkgs; [
          git
          curl
          jq
          gnumake
        ];
        
        # Language-specific tool sets
        nodeTools = with pkgs; [
          nodejs_20
          nodePackages.typescript
          nodePackages.eslint
          nodePackages.prettier
        ];
        
        pythonTools = with pkgs; [
          python311
          python311Packages.pip
          python311Packages.black
          python311Packages.ruff
        ];
        
        goTools = with pkgs; [
          go_1_21
          golangci-lint
          delve
        ];
      in
      {
        devShells = {
          # Default development environment
          default = pkgs.mkShell {
            buildInputs = commonTools ++ nodeTools ++ pythonTools ++ goTools;
            shellHook = ''
              echo "Full-stack development environment"
            '';
          };
          
          # Node.js only environment
          node = pkgs.mkShell {
            buildInputs = commonTools ++ nodeTools;
            shellHook = ''
              echo "Node.js development environment"
            '';
          };
          
          # Python only environment
          python = pkgs.mkShell {
            buildInputs = commonTools ++ pythonTools;
            shellHook = ''
              echo "Python development environment"
            '';
          };
          
          # Go only environment
          go = pkgs.mkShell {
            buildInputs = commonTools ++ goTools;
            shellHook = ''
              echo "Go development environment"
            '';
          };
          
          # CI/CD environment
          ci = pkgs.mkShell {
            buildInputs = commonTools ++ nodeTools ++ [
              pkgs.docker
              pkgs.docker-compose
            ];
            shellHook = ''
              echo "CI/CD environment"
            '';
          };
        };
      });
}
```

## Nix Development Commands

### Standard Nix Workflow
```bash
# Environment activation
nix develop                    # Enter development shell
nix develop --command bash     # Enter specific shell
nix develop .#python          # Enter specific flake output

# With direnv
direnv allow                   # Allow .envrc execution
direnv reload                  # Reload environment

# Development commands
nix run .#build               # Run build command
nix run .#test                # Run test command
nix run .#lint                # Run linting

# Flake management
nix flake update              # Update flake inputs
nix flake check               # Check flake validity
nix flake show                # Show flake outputs
nix flake metadata            # Show flake metadata
```

### Nix Shell Utilities
```bash
# Package search and installation
nix search nixpkgs nodejs     # Search for packages
nix shell nixpkgs#nodejs      # Temporary package shell
nix profile install nixpkgs#nodejs  # Install to profile

# Garbage collection
nix-collect-garbage           # Clean up unused packages
nix-collect-garbage -d        # Delete old generations

# Store management
nix store gc                  # Garbage collect Nix store
nix store verify              # Verify store integrity
```

## Home Manager Integration

### home.nix Configuration
```nix
{ config, pkgs, ... }:

{
  home.username = "developer";
  home.homeDirectory = "/home/developer";
  home.stateVersion = "23.11";

  home.packages = with pkgs; [
    git
    curl
    jq
    nodejs_20
    python311
    go_1_21
    docker
    docker-compose
  ];

  programs.git = {
    enable = true;
    userName = "Developer";
    userEmail = "developer@example.com";
    extraConfig = {
      init.defaultBranch = "main";
      push.default = "current";
    };
  };

  programs.direnv = {
    enable = true;
    nix-direnv.enable = true;
  };

  programs.bash = {
    enable = true;
    shellAliases = {
      ll = "ls -l";
      la = "ls -la";
      ndev = "nix develop";
      nshell = "nix-shell";
    };
  };

  home.file.".config/nix/nix.conf".text = ''
    experimental-features = nix-command flakes
    auto-optimise-store = true
  '';
}
```

## Cross-Platform Compatibility

### Platform-Specific Configurations
```nix
{
  description = "Cross-platform development environment";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs = { self, nixpkgs, flake-utils }:
    flake-utils.lib.eachSystem ["x86_64-linux" "aarch64-linux" "x86_64-darwin" "aarch64-darwin"] (system:
      let
        pkgs = nixpkgs.legacyPackages.${system};
        
        # Platform-specific packages
        platformPackages = if pkgs.stdenv.isDarwin then [
          pkgs.darwin.apple_sdk.frameworks.Security
          pkgs.libiconv
        ] else [
          pkgs.glibc
          pkgs.gcc
        ];
      in
      {
        devShells.default = pkgs.mkShell {
          buildInputs = with pkgs; [
            nodejs_20
            python311
            go_1_21
            git
            curl
          ] ++ platformPackages;

          shellHook = ''
            echo "Cross-platform development environment"
            echo "Platform: ${system}"
            echo "OS: ${if pkgs.stdenv.isDarwin then "macOS" else "Linux"}"
          '';
        };
      });
}
```

## Performance Optimization

### Binary Cache Configuration
```nix
# ~/.config/nix/nix.conf
experimental-features = nix-command flakes
auto-optimise-store = true
builders-use-substitutes = true
substituters = https://cache.nixos.org/ https://nix-community.cachix.org
trusted-public-keys = cache.nixos.org-1:6NCHdD59X431o0gWypbMrAURkbJ16ZPMQFGspcDShjY= nix-community.cachix.org-1:mB9FSh9qf2dCimDSUo8Zy7bkq5CX+/rkCWyvRCYg3Fs=
```

### Flake Lock Management
```bash
# Pin dependencies for reproducibility
nix flake lock               # Create/update flake.lock
nix flake lock --update-input nixpkgs  # Update specific input
nix flake lock --recreate-lock-file     # Recreate lock file

# Use locked versions
nix develop --offline        # Use only cached/locked versions
```

## Error Handling and Debugging

### Common Nix Issues
```bash
# Permission issues
sudo chown -R $USER /nix/var/nix/profiles/per-user/$USER

# Flake evaluation errors
nix flake check --verbose    # Detailed error checking
nix eval .#devShells.x86_64-linux.default  # Evaluate specific output

# direnv issues
direnv status                # Check direnv status
direnv exec . bash           # Force environment loading
```

### Debugging Configurations
```nix
{
  description = "Debug-enabled development environment";

  outputs = { self, nixpkgs, flake-utils }:
    flake-utils.lib.eachDefaultSystem (system:
      let
        pkgs = nixpkgs.legacyPackages.${system};
      in
      {
        devShells.default = pkgs.mkShell {
          buildInputs = with pkgs; [
            nodejs_20
            # Add debug tools
            gdb
            valgrind
            strace
          ];

          shellHook = ''
            echo "Debug environment loaded"
            set -x  # Enable debug output
            
            # Verify all tools are available
            command -v node >/dev/null && echo "✓ Node.js available"
            command -v gdb >/dev/null && echo "✓ GDB available"
            
            set +x  # Disable debug output
          '';

          # Enable debug symbols
          env = {
            NIX_DEBUG = "1";
            NIXPKGS_ALLOW_UNFREE = "1";
          };
        };
      });
}
```

## Integration with Other Modules

### Environment Management Coordination
- Provides alternative to Flox for teams preferring Nix
- Works alongside Docker environments when containers are needed
- Supports hybrid setups with both Nix and native package managers

### Code Quality Integration
- Declaratively includes linting and formatting tools
- Ensures consistent tool versions across all developers
- Integrates with CI/CD systems using the same Nix configuration

### Universal Commands Compatibility
- All standard commands work within Nix shells
- Environment-aware execution detects Nix environment automatically
- Supports both legacy shell.nix and modern flake.nix configurations

### Project Detection Enhancement
- Detects Nix-based projects through flake.nix or shell.nix presence
- Automatically enters development environment with direnv
- Supports complex multi-environment project configurations