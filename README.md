# Chart.js Expert Plugin for Claude Code

A comprehensive Claude Code plugin providing expert-level assistance for Chart.js v4.5.1.

## Features

- **6 Specialized Skills**: Deep knowledge across all Chart.js domains
- **Interactive Command**: `/chartjs:component` - Generate chart code for any framework
- **Proactive Agent**: Automatically assists when working with charts or data visualization

## Skills

| Skill | Coverage |
|-------|----------|
| `chartjs-overview` | Installation, setup, tree-shaking, module loaders |
| `chartjs-chart-types` | Line, bar, pie, radar, bubble, scatter, polar, doughnut, mixed charts |
| `chartjs-configuration` | Options, animations, interactions, legends, tooltips, responsive design |
| `chartjs-axes` | Cartesian/radial axes, time scales, logarithmic scales, styling |
| `chartjs-developers` | Custom plugins, chart types, axes, API reference |
| `chartjs-integrations` | React, Vue, Angular, Rails 8, vanilla JS patterns |

## Commands

### `/chartjs:component`

Interactive chart code generator. Prompts for:
- Chart type (line, bar, pie, etc.)
- Target framework (React, Vue, Angular, Rails 8, vanilla JS)
- Generates production-ready code with proper imports

## Agent

### `chartjs-expert`

Triggers proactively when:
- Working with Chart.js code or configuration
- Implementing data visualization features
- Troubleshooting chart rendering issues
- Asking about charting best practices

## Installation

```bash
claude /install-plugin /path/to/chartjs-expert
```

Or add to your Claude Code settings.

## Chart.js Version

This plugin targets **Chart.js v4.5.1** specifically.

## License

MIT
