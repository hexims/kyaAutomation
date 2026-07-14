# Contributing to ZLocker

Thank you for your interest in contributing to ZLocker! We welcome contributions from developers, designers, and community members.

## Getting Started

### Prerequisites
- Node.js >= 18
- MySQL/MariaDB >= 5.7
- Redis >= 6.0
- Docker & Docker Compose (optional)
- Git

### Development Setup

1. **Fork and Clone**
   ```bash
   git clone https://github.com/yourusername/kyaAutomation.git
   cd kyaAutomation
   ```

2. **Install Dependencies**
   ```bash
   pnpm install
   # or npm install
   ```

3. **Setup Environment Variables**
   ```bash
   cp .env.example .env.local
   # Edit .env.local with your configuration
   ```

4. **Start Development Server**
   ```bash
   pnpm dev
   ```

## Development Workflow

### Creating a Feature Branch
```bash
git checkout -b feature/your-feature-name
```

### Code Standards
- **ESLint**: Run `pnpm lint` before committing
- **Prettier**: Format code with `pnpm format`
- **TypeScript**: Ensure type safety
- **Testing**: Write tests for new features

### Commit Messages
Follow conventional commits:
```
feat: add new feature
fix: fix a bug
docs: update documentation
style: code style changes
refactor: refactor code
test: add tests
```

### Testing
```bash
# Run unit tests
pnpm test

# Run integration tests
pnpm test:integration

# Run e2e tests
pnpm test:e2e

# Check coverage
pnpm test:coverage
```

### Pull Request Process

1. **Update your branch**: `git pull origin main`
2. **Push your changes**: `git push origin feature/your-feature-name`
3. **Create PR** with detailed description
4. **Link issues**: Use `closes #123`
5. **Request review** from maintainers
6. **Address feedback** and update PR

## Code Review Guidelines

When submitting a PR, ensure:
- [ ] Code follows project standards
- [ ] Tests are added/updated
- [ ] Documentation is updated
- [ ] No breaking changes
- [ ] Commit history is clean
- [ ] All checks pass

## Reporting Issues

### Bug Reports
Include:
- Description of the bug
- Steps to reproduce
- Expected vs actual behavior
- Environment details
- Screenshots/logs if applicable

### Feature Requests
Include:
- Clear description of the feature
- Use cases and benefits
- Potential implementation approach
- Relevant examples

## Community Guidelines

- Be respectful and inclusive
- Provide constructive feedback
- Help others when possible
- Follow the code of conduct

## Questions?

Join our discussions or open an issue with the label `question`.

Thank you for contributing to ZLocker!
