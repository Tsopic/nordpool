# Contributing to Nord Pool Integration

Thank you for your interest in contributing to the Nord Pool Home Assistant integration! This document provides guidelines and information for contributors.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Development Setup](#development-setup)
- [Coding Standards](#coding-standards)
- [Pull Request Process](#pull-request-process)
- [Reporting Bugs](#reporting-bugs)
- [Suggesting Enhancements](#suggesting-enhancements)

## Code of Conduct

This project follows the principles of respect, collaboration, and constructive feedback. Please:

- Be respectful and considerate in all interactions
- Welcome newcomers and help them get started
- Focus on what is best for the community
- Show empathy towards other community members

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check existing issues to avoid duplicates. When creating a bug report, include:

- **Clear title and description**
- **Steps to reproduce** the issue
- **Expected behavior** vs actual behavior
- **Home Assistant version** and integration version
- **Region/currency** configuration
- **Relevant logs** (enable debug logging)
- **Screenshots** if applicable

### Suggesting Enhancements

Enhancement suggestions are welcome! Please provide:

- **Clear use case** and motivation
- **Detailed description** of the proposed feature
- **Alternative solutions** you've considered
- **Impact on existing users** (breaking changes?)

### Code Contributions

Contributions that improve the integration are always welcome:

- Bug fixes
- New features
- Documentation improvements
- Test coverage improvements
- Performance optimizations

## Development Setup

### Prerequisites

- Python 3.11 or higher
- Home Assistant development environment
- Git

### Local Development

1. **Fork and clone the repository**

```bash
git clone https://github.com/YOUR-USERNAME/nordpool.git
cd nordpool
```

2. **Create a development branch**

```bash
git checkout -b feature/your-feature-name
```

3. **Install in Home Assistant**

```bash
# Link to your Home Assistant custom_components directory
ln -s $(pwd)/custom_components/nordpool ~/.homeassistant/custom_components/nordpool
```

4. **Enable debug logging**

Add to your `configuration.yaml`:

```yaml
logger:
  logs:
    custom_components.nordpool: debug
```

5. **Restart Home Assistant and test your changes**

## Coding Standards

### Python Style Guide

- Follow [PEP 8](https://www.python.org/dev/peps/pep-0008/) style guide
- Use meaningful variable and function names
- Add docstrings for functions and classes
- Keep functions focused and concise
- Use type hints where appropriate

### Code Quality

- **Format your code**: Use `black` for formatting
- **Lint your code**: Use `pylint` or `flake8`
- **Test your changes**: Ensure existing functionality still works
- **Add comments**: Explain complex logic

### Example Code Style

```python
async def calculate_price(
    self,
    base_price: float,
    vat: float,
    additional_costs: float
) -> float:
    """
    Calculate the final price including VAT and additional costs.

    Args:
        base_price: Base electricity price from Nord Pool
        vat: VAT rate (e.g., 0.25 for 25%)
        additional_costs: Additional costs per kWh

    Returns:
        Final calculated price
    """
    return (base_price + additional_costs) * (1 + vat)
```

## Pull Request Process

### Before Submitting

1. **Test thoroughly** in your Home Assistant instance
2. **Update documentation** if needed
3. **Add/update tests** if applicable
4. **Update CHANGELOG.md** following [Keep a Changelog](https://keepachangelog.com/) format
5. **Ensure no breaking changes** unless discussed first

### PR Guidelines

1. **Title**: Use clear, descriptive titles
   - ✅ "Add support for hourly period detection"
   - ❌ "Fix bug"

2. **Description**: Provide context and details
   - What problem does this solve?
   - How does it solve it?
   - Any breaking changes?
   - Screenshots/examples if applicable

3. **Scope**: Keep PRs focused
   - One feature/fix per PR
   - Separate refactoring from feature additions

4. **Commits**: Write clear commit messages
   - Use conventional commit format when possible
   - Example: `feat: add 15-minute period support`

### PR Template

```markdown
## Description
Brief description of changes

## Motivation
Why is this change needed?

## Changes
- List of changes made

## Testing
- [ ] Tested in Home Assistant
- [ ] Works with hourly periods
- [ ] Works with 15-minute periods
- [ ] No errors in logs

## Breaking Changes
List any breaking changes (or write "None")

## Checklist
- [ ] Code follows style guidelines
- [ ] Documentation updated
- [ ] CHANGELOG.md updated
- [ ] No new warnings or errors
```

### Review Process

1. Maintainers will review your PR
2. Address any requested changes
3. Once approved, your PR will be merged
4. Changes will be included in the next release

## Testing

### Manual Testing

Test your changes with:

- Different regions (NO, SE, DK, FI, etc.)
- Different currencies
- Both hourly and 15-minute periods
- Various configuration options
- Tomorrow's prices (wait until ~13:00 CET)

### Test Checklist

- [ ] Integration loads without errors
- [ ] Sensor shows current price correctly
- [ ] Today's prices are populated
- [ ] Tomorrow's prices appear after publication
- [ ] Attributes calculate correctly (avg, peak, off-peak)
- [ ] Additional costs template works
- [ ] Period type detection works
- [ ] No errors in Home Assistant logs

## Documentation

### Update Documentation

If your changes affect user-facing functionality:

- Update **README.md** with new features/options
- Update **CHANGELOG.md** with changes
- Add examples for new configuration options
- Update code comments and docstrings

### Documentation Style

- Use clear, concise language
- Provide examples
- Explain why, not just what
- Keep formatting consistent

## Questions?

If you have questions about contributing:

- Open a discussion on GitHub
- Check existing issues and PRs
- Read the code and comments
- Look at previous similar contributions

## Recognition

Contributors will be:

- Listed in release notes
- Credited in commit history
- Appreciated by the community!

Thank you for contributing to make this integration better! 🎉
