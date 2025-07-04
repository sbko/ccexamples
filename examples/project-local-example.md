# Project-Local Modules Example

This example shows how to use Claude Code modules by copying them directly into your project.

## Quick Setup

```bash
# 1. Clone the repository
git clone https://github.com/sbko/ccexamples.git

# 2. Copy modules to your project
mkdir -p .claude/modules/global .claude/modules/project-specific
cp -r ccexamples/modules/global/* .claude/modules/global/
cp -r ccexamples/modules/project-specific/* .claude/modules/project-specific/

# 3. Create project configuration
cat > ./CLAUDE.md << 'EOF'
# Project Claude Code Configuration

## Core Development Standards
@.claude/modules/global/git-workflow.md
@.claude/modules/global/code-quality.md
@.claude/modules/global/security-practices.md

## Environment Management
@.claude/modules/global/flox-environment.md

## Project-Specific Standards
@.claude/modules/project-specific/testing-strategies.md

## Project Instructions
This project uses locally stored Claude Code modules.
EOF
```

## Customizing Modules

Since modules are local, you can customize them for your project:

```bash
# Edit a module for project-specific needs
nano .claude/modules/global/code-quality.md
```

## Git Integration

Add modules to version control:

```bash
# Add to git
git add .claude/
git commit -m "Add Claude Code modules configuration"

# Add to .gitignore if you don't want to version control them
echo ".claude/modules/" >> .gitignore
```

## Benefits of Project-Local Modules

✅ **Self-contained**: No external dependencies  
✅ **Customizable**: Modify modules for project needs  
✅ **Version controlled**: Modules evolve with your project  
✅ **Offline friendly**: Works without internet connection  
✅ **Team consistency**: Everyone gets exact same module versions  

## Updating Modules

To update modules, fetch the latest versions:

```bash
# Update from remote repository
git clone https://github.com/sbko/ccexamples.git /tmp/ccexamples
cp -r /tmp/ccexamples/modules/global/* .claude/modules/global/
cp -r /tmp/ccexamples/modules/project-specific/* .claude/modules/project-specific/
rm -rf /tmp/ccexamples
```

## When to Use Project-Local Modules

Choose this approach when:
- You need project-specific customizations
- You want modules version-controlled with your code
- You're working offline frequently
- You have specific compliance requirements
- You want complete control over module versions