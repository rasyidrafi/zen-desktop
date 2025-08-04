# Contributing to Zen Browser

Welcome to the Zen Browser project! This guide will help you understand how to contribute effectively to the project.

## Quick Start

If you're new to Zen development, start with our [Developer Getting Started Guide](./developer-guide/getting-started.md) for a complete setup walkthrough.

## Branch Structure

The repository is structured as follows:

```
dev (main branch)
 | |
 | \--->-- stable (release branch)
 |   ^
 ^   |
 |   \-<- Hotfix (hotfixes directly from stable)
 |
 \-<- (features branches)
```

### Branch Descriptions:

- **`dev`**: Main development branch - all new features and non-critical fixes
- **`stable`**: Release branch - production-ready code for stable releases
- **`twilight`**: Experimental branch - cutting-edge features and testing
- **Feature branches**: Individual feature development (branched from `dev`)
- **Hotfix branches**: Critical fixes (can branch from `stable` for urgent patches)

### Branch Workflow:

1. **Feature development**: Create feature branch from `dev`
2. **Testing**: Features merge to `twilight` for experimental testing
3. **Release preparation**: Stable features merge from `dev` to `stable`
4. **Hotfixes**: Critical fixes can go directly to `stable` then back to `dev`

## Development Setup

### Prerequisites

Before you set up your local development environment, **read our [Building Guidelines](https://docs.zen-browser.app/guides/building)**. Skipping them can lead to avoidable build errors.

For detailed setup instructions, see our [Developer Getting Started Guide](./developer-guide/getting-started.md).

### Quick Setup

```bash
# Clone the repository
git clone https://github.com/zen-browser/desktop.git zen-desktop
cd zen-desktop

# Install dependencies
npm install

# Download Firefox source
npm run download

# Import Zen modifications
npm run import

# Bootstrap build environment
npm run bootstrap

# Build Zen
npm run build

# Run Zen
npm start
```

## Contribution Workflow

### 1. Planning Your Contribution

Before starting work:

1. **Check existing issues**: Look for existing [GitHub Issues](https://github.com/zen-browser/desktop/issues)
2. **Create an issue**: For new features or bugs, create an issue first to discuss
3. **Get assignment**: Comment on issues you'd like to work on to avoid duplicate work
4. **Understand scope**: Start with small, focused changes

### 2. Setting Up Your Development Environment

1. **Fork the repository** on GitHub
2. **Clone your fork** locally
3. **Add upstream remote**:
   ```bash
   git remote add upstream https://github.com/zen-browser/desktop.git
   ```
4. **Follow the setup guide**: Complete development environment setup

### 3. Creating a Feature Branch

```bash
# Ensure you're on the latest dev branch
git checkout dev
git pull upstream dev

# Create your feature branch
git checkout -b feature/your-feature-name

# Or for bug fixes
git checkout -b fix/issue-number-description
```

### 4. Making Changes

#### Code Style Guidelines

- **JavaScript/TypeScript**: Follow existing code patterns and use ESLint
- **CSS**: Use existing class naming conventions and organize by component
- **File naming**: Use kebab-case for files, camelCase for JavaScript variables
- **Comments**: Add comments for complex logic, prefer self-documenting code

#### Code Organization

- **Zen features**: Add new features to `src/zen/[feature-name]/`
- **UI changes**: Modify browser themes in `src/browser/themes/`
- **Tests**: Add tests in `src/zen/tests/[feature-name]/`
- **Documentation**: Update relevant documentation

#### Making Incremental Changes

```bash
# Make your changes
# Test frequently during development
npm run build:ui  # For UI-only changes (faster)
npm start        # Test your changes

# Lint and format
npm run lint:fix
npm run pretty

# Run tests
npm test
```

### 5. Testing Your Changes

Always test your changes thoroughly:

1. **Build and run**: Ensure Zen builds and runs with your changes
2. **Manual testing**: Test the feature/fix manually in the browser
3. **Automated tests**: Run existing tests and add new ones if needed
4. **Edge cases**: Test error conditions and edge cases
5. **Performance**: Ensure changes don't degrade performance

See our [Testing Guide](./developer-guide/testing.md) for detailed testing information.

### 6. Committing Changes

#### Commit Message Format

Use conventional commit format:

```
type(scope): brief description

Optional longer description explaining the change.

Fixes #issue-number
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

Examples:
```bash
git commit -m "feat(workspaces): add workspace switching animations"
git commit -m "fix(tabs): resolve pinned tab persistence issue

Fixes issue where pinned tabs weren't restored after browser restart.
Added proper session storage integration.

Fixes #1234"
```

### 7. Submitting a Pull Request

#### Before Submitting

1. **Ensure all tests pass**: `npm test`
2. **Lint your code**: `npm run lint:fix`
3. **Update documentation**: If you changed functionality
4. **Rebase on latest dev**: Ensure your branch is up-to-date

```bash
# Update your branch
git checkout dev
git pull upstream dev
git checkout your-feature-branch
git rebase dev
```

#### Pull Request Template

When creating a PR, include:

1. **Clear title**: Summarize the change
2. **Description**: Explain what and why
3. **Testing**: How you tested the changes
4. **Screenshots**: For UI changes
5. **Breaking changes**: Note any breaking changes
6. **Related issues**: Link to related issues

#### PR Checklist

- [ ] Tests pass (`npm test`)
- [ ] Code is linted (`npm run lint`)
- [ ] Documentation updated (if applicable)
- [ ] Manual testing completed
- [ ] Screenshots included (for UI changes)
- [ ] Commit messages follow conventions
- [ ] Branch is up-to-date with dev

### 8. Code Review Process

1. **Automated checks**: CI/CD runs automated tests and checks
2. **Maintainer review**: Core maintainers review code and provide feedback
3. **Address feedback**: Make requested changes and push updates
4. **Final approval**: Once approved, maintainers will merge the PR

## Types of Contributions

### 🐛 Bug Fixes

- Look for issues labeled `bug` or `good first issue`
- Reproduce the bug before fixing
- Add tests to prevent regression
- Keep fixes minimal and focused

### ✨ New Features

- Discuss in issues before implementing
- Consider impact on performance and usability
- Follow Zen's design principles
- Include comprehensive tests and documentation

### 📚 Documentation

- Fix typos and improve clarity
- Add missing documentation
- Update outdated information
- Add examples and tutorials

### 🎨 UI/UX Improvements

- Follow Zen's design language
- Consider accessibility
- Test on different platforms
- Include screenshots in PRs

### 🧪 Testing

- Add missing test coverage
- Improve test reliability
- Add performance tests
- Fix flaky tests

## Development Guidelines

### Code Quality

1. **Follow existing patterns**: Look at similar code for style guidance
2. **Keep it simple**: Prefer readable code over clever code
3. **Test thoroughly**: Add tests for new functionality
4. **Document complex logic**: Add comments for non-obvious code
5. **Consider performance**: Avoid changes that degrade performance

### Zen-Specific Guidelines

1. **Firefox compatibility**: Ensure changes work with Firefox updates
2. **Minimal patching**: Prefer extending Firefox over modifying core code
3. **Component isolation**: Keep features modular and independent
4. **Preference integration**: Use Firefox's preference system properly
5. **Cross-platform support**: Test on multiple operating systems

### Architecture Principles

1. **Manager pattern**: Use `gZen[Feature]Manager` for component management
2. **Storage pattern**: Use `Zen[Feature]Storage` for data persistence
3. **Event-driven**: Use events for component communication
4. **Progressive enhancement**: Features should degrade gracefully

## Getting Help

### Resources

- **Documentation**: Start with [Developer Guides](./developer-guide/)
- **Architecture**: Read [Architecture Overview](./developer-guide/architecture.md)
- **Codebase**: See [Codebase Structure Guide](./developer-guide/codebase-structure.md)
- **Testing**: Follow [Testing Guide](./developer-guide/testing.md)

### Community Support

- **GitHub Issues**: Ask questions in [GitHub Issues](https://github.com/zen-browser/desktop/issues)
- **GitHub Discussions**: Use [GitHub Discussions](https://github.com/zen-browser/desktop/discussions) for general questions
- **Discord**: Join our [Discord community](https://discord.gg/zen-browser)

### Reporting Issues

When reporting bugs:

1. **Search existing issues** first
2. **Use issue templates** provided
3. **Include reproduction steps**
4. **Provide system information**
5. **Include logs/screenshots** if relevant

## Recognition

Contributors are recognized through:

- **Contributor list**: Listed in project contributors
- **Release notes**: Contributions mentioned in release notes
- **Community highlights**: Featured in community updates

## Code of Conduct

Please read our [Code of Conduct](../CODE_OF_CONDUCT.md) before contributing. We're committed to providing a welcoming and inclusive environment for all contributors.

## License

By contributing to Zen Browser, you agree that your contributions will be licensed under the [MPL-2.0 License](../LICENSE).

---

Thank you for contributing to Zen Browser! Your contributions help make browsing more productive and enjoyable for users worldwide. 🚀
