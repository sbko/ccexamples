# Flox Environment Management

## Environment Management Strategy

### Primary Tool: Flox.dev
- **Preference**: Always use Flox for environment management when available
- **Rationale**: Flox provides reproducible, cross-platform environments with simple CLI commands
- **Installation**: Install Flox via `curl -sSf https://install.flox.dev | bash` if not available

### Environment Detection and Initialization
1. **Check for existing environment**: Look for `.flox/` directory or `flox.toml` manifest
2. **Auto-initialize**: Create flox environment if project lacks one
3. **Activate environment**: Always activate flox environment before executing commands
4. **Fallback strategy**: Use alternative tools if flox is not available/appropriate

## Command Execution Protocol

### Before ANY build/test/dev command:
1. Check if flox environment exists: `flox list`
2. If no environment found, initialize: `flox init`
3. Activate environment: `flox activate` (or ensure commands run within activated environment)
4. Install missing dependencies as needed: `flox install <package>`

### Common Dependencies to Install:
- **Go projects**: `go`, `make`, `git`
- **Node.js projects**: `nodejs`, `npm`, `yarn`, `pnpm`
- **Python projects**: `python`, `pip`, `poetry`, `pipenv`
- **Rust projects**: `rust`, `cargo`
- **Docker projects**: `docker`, `docker-compose`
- **General tools**: `git`, `curl`, `wget`, `jq`, `make`

## Project-Specific Environment Setup

### Language-Specific Templates
When creating new environments, use these patterns:

#### Go Projects
```bash
flox init
flox install go make git
flox edit  # Add any specific Go version or tools
```

#### Node.js Projects
```bash
flox init
flox install nodejs npm git
flox edit  # Add specific Node version if needed
```

#### Python Projects
```bash
flox init
flox install python pip git
flox edit  # Add poetry, pipenv, or other Python tools
```

#### Multi-language Projects
```bash
flox init
flox install go nodejs python rust make git docker
```

## Environment Sharing and Version Control

### Best Practices:
- **Always commit** `.flox/` directory and `flox.toml` to version control
- **Document environment** in project README with `flox activate` instructions
- **Use specific versions** when possible for reproducibility
- **Layer environments** for shared dependencies across projects

### Manifest Structure (flox.toml example):
```toml
[install]
go = "1.21"
nodejs = "20"
python = "3.11"
git = "latest"
make = "latest"

[vars]
GOPATH = "$HOME/go"
NODE_ENV = "development"

[shell]
init = """
echo "Development environment activated"
export PATH="$PWD/bin:$PATH"
"""
```

## Alternative Tools (Fallback Strategy)

### If Flox is not available:
1. **Nix + direnv**: Look for `flake.nix` or `shell.nix`, use `nix develop`
2. **devcontainers**: Look for `.devcontainer/` directory, use VS Code dev containers
3. **Docker**: Look for `Dockerfile` or `docker-compose.yml`
4. **Language-specific**: Use `package.json`, `Cargo.toml`, `go.mod`, etc.

### Tool Priority:
1. Flox (preferred)
2. Nix + direnv
3. devcontainers
4. Docker
5. Native package managers

## Common Commands and Patterns

### Environment Lifecycle:
```bash
# Create environment
flox init

# Install dependencies
flox install <package1> <package2>

# Activate environment
flox activate

# Update environment
flox update

# Share environment
git add .flox/ flox.toml
git commit -m "Add flox environment"
```

### Troubleshooting:
- **Environment conflicts**: Use `flox list` to check active environments
- **Package not found**: Search with `flox search <package>`
- **Version issues**: Specify exact versions in `flox.toml`
- **Permission issues**: Ensure flox is properly installed and accessible

## Development Workflow Integration

### Before executing any command:
1. **Environment check**: Verify flox environment is active
2. **Dependency verification**: Ensure required tools are installed
3. **Version compatibility**: Check tool versions match project requirements
4. **Path configuration**: Ensure environment PATH is properly set

### Test and Build Commands:
- **Always prefix** test/build commands with environment activation
- **Example**: Instead of `make test`, use `flox activate -- make test`
- **Batch operations**: Use `flox activate` once, then run multiple commands

## Memory and Performance

### Optimization Tips:
- **Cache environments**: Reuse environments across similar projects
- **Layer efficiently**: Use base environments for common tools
- **Clean periodically**: Remove unused environments with `flox delete`
- **Share environments**: Use FloxHub for team environments

### Resource Management:
- **Disk space**: Monitor `.flox/` directory size
- **Memory usage**: Flox environments are lightweight compared to containers
- **Network**: Cache packages locally for faster activation

## Security Considerations

### Best Practices:
- **Pin versions**: Use specific versions for security-sensitive tools
- **Review manifests**: Check `flox.toml` for unexpected dependencies
- **Audit regularly**: Update environments and dependencies
- **Isolate environments**: Use separate environments for different projects

### Access Control:
- **Shared environments**: Use FloxHub permissions for team access
- **Secrets management**: Use environment variables, not embedded secrets
- **Network isolation**: Be aware that flox environments share network access

## Integration with Claude Code

### Automatic Environment Detection:
When Claude Code encounters build/test failures:
1. **Diagnose**: Check if environment is properly activated
2. **Repair**: Install missing dependencies via flox
3. **Retry**: Re-execute command within activated environment
4. **Document**: Update project documentation with environment requirements

### Error Handling:
- **Missing tools**: Automatically suggest `flox install <tool>`
- **Version mismatches**: Recommend specific versions in `flox.toml`
- **Environment conflicts**: Guide resolution with `flox list` and `flox activate`

## Integration with Other Modules

This module provides the foundation for all development workflows and integrates with:
- **Code Quality module**: Defines what quality checks to execute in the environment
- **Security Practices module**: Provides security scanning requirements  
- **Git Workflow module**: Ensures quality gates are met before commits
- **Universal Commands module**: Defines the standard commands executed in the environment
- **Project Detection module**: Determines which tools to install based on project type

This configuration ensures consistent, reproducible development environments while maintaining flexibility for different project types and team workflows.