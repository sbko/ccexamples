# Global Installation Example

This example shows how to use Claude Code modules when installed globally at `~/.claude/ccexamples/`.

## Quick Setup

```bash
# 1. Clone modules globally
git clone https://github.com/sbko/ccexamples.git ~/.claude/ccexamples

# 2. Create global configuration
cat > ~/.claude/CLAUDE.md << 'EOF'
# Global Claude Code Configuration

## Core Development Standards
@~/.claude/ccexamples/modules/global/git-workflow.md
@~/.claude/ccexamples/modules/global/code-quality.md
@~/.claude/ccexamples/modules/global/security-practices.md

## Environment Management
@~/.claude/ccexamples/modules/global/flox-environment.md

## Workflow Integration
@~/.claude/ccexamples/modules/global/project-detection.md
@~/.claude/ccexamples/modules/global/universal-commands.md
EOF

# 3. Test it works
claude "What environment management approach should I use?"
```

## Environment Management Options

You can choose different environment management approaches by editing your global configuration:

```bash
# For Docker users
@~/.claude/ccexamples/modules/global/docker-environment.md

# For Nix users  
@~/.claude/ccexamples/modules/global/nix-environment.md

# For DevContainer users
@~/.claude/ccexamples/modules/global/devcontainer-environment.md

# For ASDF users
@~/.claude/ccexamples/modules/global/asdf-environment.md
```

## Use in Your Projects

Create a simple `./CLAUDE.md` in any project:

```markdown
# My Project Configuration

## Project-Specific Modules
@~/.claude/ccexamples/modules/templates/go-project.md

## Project Instructions
- This is a Go REST API project
- Use PostgreSQL for persistence
- Follow microservice patterns
- Include comprehensive logging

Note: Global standards from ~/.claude/CLAUDE.md are loaded automatically
```

## Benefits of Global Installation

✅ **Consistency**: Same standards across all projects  
✅ **Easy updates**: `git pull` in `~/.claude/ccexamples` updates all projects  
✅ **Team alignment**: Everyone references the same module versions  
✅ **Minimal setup**: Each project just needs a simple CLAUDE.md  

## Alternative Project Setup

For projects that only need global standards:

```bash
# Create minimal project config
echo "# Project uses global standards only" > ./CLAUDE.md
```

Or add additional project-specific modules:

```bash
# Add testing strategies for this project
echo "@~/.claude/ccexamples/modules/project-specific/testing-strategies.md" >> ./CLAUDE.md
```