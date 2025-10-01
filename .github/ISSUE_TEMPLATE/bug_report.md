---
name: Bug report
about: Create a report to help us improve
title: '[BUG] '
labels: bug
assignees: ''

---

## Describe the bug
A clear and concise description of what the bug is.

## To Reproduce
Steps to reproduce the behavior:
1. Go to '...'
2. Configure '...'
3. See error

## Expected behavior
A clear and concise description of what you expected to happen.

## Configuration
```yaml
# Your nordpool configuration (remove any sensitive information)
sensor:
  - platform: nordpool
    region: "NO1"
    # ... rest of config
```

## Environment
- **Home Assistant version**: [e.g. 2024.10.1]
- **Nord Pool integration version**: [e.g. 0.0.18]
- **Region**: [e.g. NO1, SE3, DK1]
- **Currency**: [e.g. NOK, EUR]
- **Period type**: [e.g. 15min, hourly]

## Logs
```
Add relevant log entries here (enable debug logging)
```

<details>
<summary>Debug logging configuration</summary>

Add this to your `configuration.yaml` and restart Home Assistant:

```yaml
logger:
  logs:
    custom_components.nordpool: debug
```
</details>

## Screenshots
If applicable, add screenshots to help explain your problem.

## Additional context
Add any other context about the problem here.
