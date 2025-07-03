# Development Container Environment

## Purpose
Provides comprehensive VS Code development container configuration for consistent, portable development environments using the Development Containers specification and Remote-Containers extension.

## Dev Container Strategy

### Primary Tools
- **VS Code Dev Containers**: Integrated containerized development
- **Docker**: Container runtime for dev containers
- **devcontainer CLI**: Command-line interface for dev containers
- **Features**: Reusable development container components

### Environment Detection and Setup
1. **Check for dev container files**: Look for `.devcontainer/` directory or `.devcontainer.json`
2. **Validate Docker availability**: Ensure Docker is running and accessible
3. **VS Code extension**: Verify Remote-Containers extension is installed
4. **Container orchestration**: Support for docker-compose based setups

## Basic Dev Container Configuration

### Simple .devcontainer/devcontainer.json
```json
{
  "name": "Development Container",
  "image": "mcr.microsoft.com/devcontainers/typescript-node:20",
  
  "features": {
    "ghcr.io/devcontainers/features/git:1": {},
    "ghcr.io/devcontainers/features/github-cli:1": {},
    "ghcr.io/devcontainers/features/docker-in-docker:1": {}
  },
  
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-vscode.vscode-typescript-next",
        "esbenp.prettier-vscode",
        "bradlc.vscode-tailwindcss",
        "ms-vscode.vscode-json"
      ],
      "settings": {
        "typescript.preferences.quoteStyle": "single",
        "editor.formatOnSave": true,
        "editor.defaultFormatter": "esbenp.prettier-vscode"
      }
    }
  },
  
  "forwardPorts": [3000, 5432],
  "postCreateCommand": "npm install",
  "postStartCommand": "npm run dev",
  
  "remoteUser": "node"
}
```

### Custom Dockerfile Configuration
```json
{
  "name": "Custom Development Environment",
  "build": {
    "dockerfile": "Dockerfile",
    "context": ".."
  },
  
  "features": {
    "ghcr.io/devcontainers/features/common-utils:1": {
      "installZsh": true,
      "installOhMyZsh": true,
      "upgradePackages": true
    }
  },
  
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-python.python",
        "ms-python.pylint",
        "ms-python.black-formatter",
        "charliermarsh.ruff"
      ]
    }
  },
  
  "mounts": [
    "source=${localEnv:HOME}/.ssh,target=/home/vscode/.ssh,type=bind,consistency=cached"
  ],
  
  "postCreateCommand": ".devcontainer/setup.sh"
}
```

## Language-Specific Dev Containers

### Node.js Development
```json
{
  "name": "Node.js Development",
  "image": "mcr.microsoft.com/devcontainers/typescript-node:20",
  
  "features": {
    "ghcr.io/devcontainers/features/git:1": {},
    "ghcr.io/devcontainers/features/github-cli:1": {},
    "ghcr.io/devcontainers/features/node:1": {
      "version": "20",
      "nodeGypDependencies": true
    }
  },
  
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-vscode.vscode-typescript-next",
        "esbenp.prettier-vscode",
        "dbaeumer.vscode-eslint",
        "bradlc.vscode-tailwindcss",
        "ms-vscode.vscode-json",
        "ms-vscode.thunder-client"
      ],
      "settings": {
        "typescript.preferences.quoteStyle": "single",
        "javascript.preferences.quoteStyle": "single",
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
          "source.fixAll.eslint": true
        },
        "eslint.validate": ["javascript", "typescript", "javascriptreact", "typescriptreact"]
      }
    }
  },
  
  "forwardPorts": [3000, 3001, 8080],
  "portsAttributes": {
    "3000": {
      "label": "Application",
      "onAutoForward": "notify"
    }
  },
  
  "postCreateCommand": "npm install && npm run build",
  "postStartCommand": "npm run dev",
  
  "remoteUser": "node",
  "workspaceFolder": "/workspace"
}
```

### Python Development
```json
{
  "name": "Python Development",
  "image": "mcr.microsoft.com/devcontainers/python:3.11",
  
  "features": {
    "ghcr.io/devcontainers/features/git:1": {},
    "ghcr.io/devcontainers/features/github-cli:1": {},
    "ghcr.io/devcontainers/features/python:1": {
      "version": "3.11",
      "installJupyterlab": true
    }
  },
  
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-python.python",
        "ms-python.pylint",
        "ms-python.black-formatter",
        "charliermarsh.ruff",
        "ms-python.isort",
        "ms-toolsai.jupyter",
        "ms-python.mypy-type-checker"
      ],
      "settings": {
        "python.defaultInterpreterPath": "/usr/local/bin/python",
        "python.linting.enabled": true,
        "python.linting.pylintEnabled": true,
        "python.formatting.provider": "black",
        "python.sortImports.args": ["--profile", "black"],
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
          "source.organizeImports": true
        }
      }
    }
  },
  
  "forwardPorts": [8000, 8888],
  "portsAttributes": {
    "8000": {
      "label": "Django Server"
    },
    "8888": {
      "label": "Jupyter Lab"
    }
  },
  
  "postCreateCommand": "pip install -r requirements.txt",
  "remoteUser": "vscode"
}
```

### Go Development
```json
{
  "name": "Go Development",
  "image": "mcr.microsoft.com/devcontainers/go:1.21",
  
  "features": {
    "ghcr.io/devcontainers/features/git:1": {},
    "ghcr.io/devcontainers/features/github-cli:1": {},
    "ghcr.io/devcontainers/features/docker-in-docker:1": {}
  },
  
  "customizations": {
    "vscode": {
      "extensions": [
        "golang.go",
        "ms-vscode.makefile-tools",
        "humao.rest-client"
      ],
      "settings": {
        "go.useLanguageServer": true,
        "go.alternateTools": {
          "go-langserver": "gopls"
        },
        "go.lintOnSave": "package",
        "go.formatTool": "goimports",
        "go.vetOnSave": "package"
      }
    }
  },
  
  "forwardPorts": [8080, 9090],
  "postCreateCommand": "go mod download",
  "remoteUser": "vscode",
  
  "mounts": [
    "source=${localWorkspaceFolder}/.devcontainer/go-cache,target=/go/pkg,type=bind"
  ]
}
```

## Docker Compose Integration

### Multi-Service Development
```json
{
  "name": "Full-Stack Development",
  "dockerComposeFile": "docker-compose.yml",
  "service": "app",
  "workspaceFolder": "/workspace",
  
  "features": {
    "ghcr.io/devcontainers/features/git:1": {},
    "ghcr.io/devcontainers/features/github-cli:1": {}
  },
  
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-vscode.vscode-typescript-next",
        "ms-python.python",
        "ms-vscode.vscode-json",
        "cweijan.vscode-postgresql-client2"
      ]
    }
  },
  
  "forwardPorts": [3000, 5432, 6379],
  "postCreateCommand": ".devcontainer/setup.sh",
  "shutdownAction": "stopCompose"
}
```

### docker-compose.yml for Dev Containers
```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: .devcontainer/Dockerfile
    volumes:
      - ../..:/workspace:cached
    command: sleep infinity
    networks:
      - development
    depends_on:
      - db
      - redis
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://postgres:postgres@db:5432/myapp_dev
      - REDIS_URL=redis://redis:6379
  
  db:
    image: postgres:15
    restart: unless-stopped
    volumes:
      - postgres-data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: myapp_dev
    networks:
      - development
  
  redis:
    image: redis:7-alpine
    restart: unless-stopped
    volumes:
      - redis-data:/data
    networks:
      - development

volumes:
  postgres-data:
  redis-data:

networks:
  development:
```

## Features and Pre-built Components

### Using Dev Container Features
```json
{
  "name": "Feature-Rich Development",
  "image": "mcr.microsoft.com/devcontainers/base:ubuntu",
  
  "features": {
    "ghcr.io/devcontainers/features/common-utils:1": {
      "installZsh": true,
      "installOhMyZsh": true,
      "upgradePackages": true,
      "username": "vscode",
      "userUid": 1000,
      "userGid": 1000
    },
    "ghcr.io/devcontainers/features/git:1": {
      "version": "latest",
      "ppa": true
    },
    "ghcr.io/devcontainers/features/github-cli:1": {
      "version": "latest"
    },
    "ghcr.io/devcontainers/features/node:1": {
      "version": "20",
      "nodeGypDependencies": true,
      "nvmVersion": "latest"
    },
    "ghcr.io/devcontainers/features/python:1": {
      "version": "3.11",
      "installJupyterlab": true,
      "configureGitCredentialHelper": true
    },
    "ghcr.io/devcontainers/features/go:1": {
      "version": "1.21"
    },
    "ghcr.io/devcontainers/features/docker-in-docker:1": {
      "version": "latest",
      "enableNonRootDocker": true,
      "moby": true
    },
    "ghcr.io/devcontainers/features/kubectl-helm-minikube:1": {
      "version": "latest",
      "helm": "latest",
      "minikube": "latest"
    }
  }
}
```

### Custom Feature Development
```json
{
  "id": "my-custom-feature",
  "version": "1.0.0",
  "name": "My Custom Development Tools",
  "description": "Custom development tools and configuration",
  "options": {
    "version": {
      "type": "string",
      "default": "latest",
      "description": "Version to install"
    }
  },
  "installsAfter": [
    "ghcr.io/devcontainers/features/common-utils"
  ]
}
```

## Advanced Configuration

### Lifecycle Scripts
```json
{
  "name": "Advanced Development Container",
  "image": "mcr.microsoft.com/devcontainers/typescript-node:20",
  
  "onCreateCommand": {
    "install-deps": "npm install",
    "setup-git": "git config --global init.defaultBranch main"
  },
  
  "updateContentCommand": {
    "update-deps": "npm update",
    "clean-cache": "npm run clean"
  },
  
  "postCreateCommand": [
    "npm run build",
    "npm run test"
  ],
  
  "postStartCommand": {
    "start-services": "npm run services:start",
    "background-task": "npm run dev &"
  },
  
  "postAttachCommand": {
    "welcome": "echo 'Welcome to the development environment!'",
    "status": "npm run status"
  }
}
```

### Environment Variables and Secrets
```json
{
  "name": "Secure Development Container",
  "image": "mcr.microsoft.com/devcontainers/typescript-node:20",
  
  "containerEnv": {
    "NODE_ENV": "development",
    "API_BASE_URL": "http://localhost:3000",
    "LOG_LEVEL": "debug"
  },
  
  "remoteEnv": {
    "PATH": "${containerEnv:PATH}:/workspace/scripts",
    "WORKSPACE_FOLDER": "${containerWorkspaceFolder}"
  },
  
  "secrets": {
    "API_KEY": {
      "description": "API key for external services",
      "documentationUrl": "https://docs.example.com/api-keys"
    }
  },
  
  "mounts": [
    "source=${localEnv:HOME}/.aws,target=/home/vscode/.aws,type=bind,consistency=cached",
    "source=${localEnv:HOME}/.ssh,target=/home/vscode/.ssh,type=bind,consistency=cached"
  ]
}
```

## Multi-Root Workspace Support

### Multi-Project Configuration
```json
{
  "name": "Multi-Project Development",
  "dockerComposeFile": "docker-compose.yml",
  "service": "workspace",
  "workspaceFolder": "/workspace",
  
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-vscode.vscode-typescript-next",
        "ms-python.python",
        "golang.go"
      ],
      "settings": {
        "extensions.autoUpdate": false
      }
    }
  },
  
  "workspaceMount": "source=${localWorkspaceFolder},target=/workspace,type=bind,consistency=cached",
  "additionalProperties": {
    "workspaceFolders": [
      {
        "path": "/workspace/frontend"
      },
      {
        "path": "/workspace/backend" 
      },
      {
        "path": "/workspace/shared"
      }
    ]
  }
}
```

## Performance Optimization

### Volume and Mount Optimization
```json
{
  "name": "Optimized Development Container",
  "image": "mcr.microsoft.com/devcontainers/typescript-node:20",
  
  "workspaceMount": "source=${localWorkspaceFolder},target=/workspace,type=bind,consistency=cached",
  
  "mounts": [
    "source=project-node-modules,target=/workspace/node_modules,type=volume",
    "source=npm-cache,target=/home/node/.npm,type=volume",
    "source=vscode-extensions,target=/home/node/.vscode-server/extensions,type=volume"
  ],
  
  "customizations": {
    "vscode": {
      "settings": {
        "files.watcherExclude": {
          "**/node_modules/**": true,
          "**/.git/**": true,
          "**/dist/**": true
        }
      }
    }
  }
}
```

### Build Optimization
```dockerfile
# .devcontainer/Dockerfile
FROM mcr.microsoft.com/devcontainers/typescript-node:20

# Install additional tools
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
        build-essential \
        curl \
        git \
    && apt-get autoremove -y && apt-get clean -y

# Install global npm packages
RUN npm install -g \
    typescript \
    @types/node \
    nodemon \
    eslint \
    prettier

# Set up development user
USER node
WORKDIR /workspace

# Pre-create common directories
RUN mkdir -p /home/node/.npm /home/node/.vscode-server
```

## DevContainer CLI Integration

### Command Line Operations
```bash
# Build and run dev container
devcontainer build .
devcontainer up .
devcontainer exec . bash

# Feature management
devcontainer features list
devcontainer features info ghcr.io/devcontainers/features/node:1

# Template operations
devcontainer templates list
devcontainer templates apply ghcr.io/devcontainers/templates/typescript-node

# CI/CD integration
devcontainer build --workspace-folder . --image-name myapp:dev
devcontainer run-user-commands --workspace-folder .
```

### Automation Scripts
```bash
#!/bin/bash
# .devcontainer/setup.sh

set -e

echo "Setting up development environment..."

# Install project dependencies
if [ -f package.json ]; then
    echo "Installing Node.js dependencies..."
    npm ci
fi

if [ -f requirements.txt ]; then
    echo "Installing Python dependencies..."
    pip install -r requirements.txt
fi

if [ -f go.mod ]; then
    echo "Downloading Go dependencies..."
    go mod download
fi

# Set up git hooks
if [ -d .git ]; then
    echo "Setting up git hooks..."
    git config core.hooksPath .githooks
fi

# Create necessary directories
mkdir -p logs tmp cache

echo "Development environment setup complete!"
```

## Integration with Other Modules

### Environment Management Coordination
- Provides containerized alternative to local environment management
- Works with Docker-based workflows and CI/CD systems
- Supports consistent development environments across team members

### Code Quality Integration
- Includes development tools and extensions in container image
- Ensures consistent linting, formatting, and type checking tools
- Integrates with VS Code for real-time quality feedback

### Universal Commands Compatibility
- All standard commands work within dev container environment
- Container includes necessary build tools and language runtimes
- Supports both local and containerized command execution

### Project Detection Enhancement
- Automatically detects dev container configuration files
- Integrates with VS Code Remote-Containers extension
- Supports multiple container configurations for different project types