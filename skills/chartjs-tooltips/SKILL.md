---
name: chartjs-tooltips
description: This skill should be used when the user asks "Chart.js tooltips", "Chart.js tooltip callbacks", "custom Chart.js tooltip", "HTML tooltip Chart.js", "Chart.js tooltip formatting", "Chart.js tooltip position", "Chart.js tooltip label", "external tooltip", "Chart.js tooltip styling", or needs help configuring tooltips in Chart.js v4.5.1.
---

# Chart.js Tooltips (v4.5.1)

Complete guide to configuring and customizing tooltips in Chart.js.

## Tooltip Basics

Tooltips display when users hover over chart elements. Chart.js provides extensive customization options.

### Basic Configuration

Namespace: `options.plugins.tooltip`

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `enabled` | boolean | true | Enable/disable canvas tooltips |
| `mode` | string | 'nearest' | Interaction mode (point, nearest, index, dataset) |
| `intersect` | boolean | true | Require mouse directly over element |
| `position` | string | 'average' | Tooltip position (average, nearest) |
| `backgroundColor` | Color | 'rgba(0,0,0,0.8)' | Background color |
| `titleColor` | Color | '#fff' | Title text color |
| `bodyColor` | Color | '#fff' | Body text color |
| `borderColor` | Color | 'rgba(0,0,0,0)' | Border color |
| `borderWidth` | number | 0 | Border width |

### Basic Example

```javascript
const chart = new Chart(ctx, {
  type: 'bar',
  data: data,
  options: {
    plugins: {
      tooltip: {
        enabled: true,
        mode: 'index',
        intersect: false,
        backgroundColor: 'rgba(0, 0, 0, 0.9)',
        titleColor: '#fff',
        bodyColor: '#fff',
        borderColor: '#666',
        borderWidth: 1
      }
    }
  }
});
```

## Tooltip Callbacks

Customize tooltip content using callbacks.

Namespace: `options.plugins.tooltip.callbacks`

| Callback | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `title` | TooltipItem[] | string\|string[] | Tooltip title |
| `label` | TooltipItem | string\|string[] | Individual item label |
| `footer` | TooltipItem[] | string\|string[] | Tooltip footer |
| `labelColor` | TooltipItem | object | Color box styling |
| `labelTextColor` | TooltipItem | Color | Label text color |

### Format Label with Currency

```javascript
options: {
  plugins: {
    tooltip: {
      callbacks: {
        label: function(context) {
          let label = context.dataset.label || '';
          if (label) {
            label += ': ';
          }
          if (context.parsed.y !== null) {
            label += new Intl.NumberFormat('en-US', {
              style: 'currency',
              currency: 'USD'
            }).format(context.parsed.y);
          }
          return label;
        }
      }
    }
  }
}
```

### Add Percentage to Tooltip

```javascript
options: {
  plugins: {
    tooltip: {
      callbacks: {
        label: function(context) {
          const dataset = context.dataset;
          const total = dataset.data.reduce((sum, val) => sum + val, 0);
          const value = context.parsed;
          const percentage = ((value / total) * 100).toFixed(2);
          return `${context.label}: ${value} (${percentage}%)`;
        }
      }
    }
  }
}
```

### Multi-Line Labels

```javascript
options: {
  plugins: {
    tooltip: {
      callbacks: {
        label: function(context) {
          return [
            `Sales: $${context.parsed.y}`,
            `Date: ${context.label}`,
            `Region: North America`
          ];
        }
      }
    }
  }
}
```

### Custom Title

```javascript
options: {
  plugins: {
    tooltip: {
      callbacks: {
        title: function(tooltipItems) {
          const item = tooltipItems[0];
          return `Week of ${item.label}`;
        }
      }
    }
  }
}
```

### Custom Footer

```javascript
options: {
  plugins: {
    tooltip: {
      callbacks: {
        footer: function(tooltipItems) {
          let sum = 0;
          tooltipItems.forEach(item => {
            sum += item.parsed.y;
          });
          return 'Total: ' + sum;
        }
      }
    }
  }
}
```

## Interaction Modes

Control which elements trigger tooltips.

| Mode | Behavior |
|------|----------|
| `point` | Only element directly under cursor |
| `nearest` | Nearest element to cursor |
| `index` | All elements at same index |
| `dataset` | All elements in dataset |
| `x` | All elements with same X value |
| `y` | All elements with same Y value |

### Index Mode (Vertical Line)

```javascript
options: {
  plugins: {
    tooltip: {
      mode: 'index',
      intersect: false  // Show even if not directly over point
    }
  },
  interaction: {
    mode: 'index',
    intersect: false
  }
}
```

### Single Point Mode

```javascript
options: {
  plugins: {
    tooltip: {
      mode: 'point',
      intersect: true  // Require hover directly over point
    }
  }
}
```

## HTML Tooltips (External)

Create fully custom HTML tooltips.

### Basic HTML Tooltip

```javascript
const getOrCreateTooltip = (chart) => {
  let tooltipEl = chart.canvas.parentNode.querySelector('div.chartjs-tooltip');

  if (!tooltipEl) {
    tooltipEl = document.createElement('div');
    tooltipEl.classList.add('chartjs-tooltip');
    tooltipEl.style.background = 'rgba(0, 0, 0, 0.7)';
    tooltipEl.style.borderRadius = '3px';
    tooltipEl.style.color = 'white';
    tooltipEl.style.opacity = 1;
    tooltipEl.style.pointerEvents = 'none';
    tooltipEl.style.position = 'absolute';
    tooltipEl.style.transform = 'translate(-50%, 0)';
    tooltipEl.style.transition = 'all .1s ease';

    const table = document.createElement('table');
    table.style.margin = '0px';

    tooltipEl.appendChild(table);
    chart.canvas.parentNode.appendChild(tooltipEl);
  }

  return tooltipEl;
};

const externalTooltipHandler = (context) => {
  const {chart, tooltip} = context;
  const tooltipEl = getOrCreateTooltip(chart);

  // Hide if no tooltip
  if (tooltip.opacity === 0) {
    tooltipEl.style.opacity = 0;
    return;
  }

  // Set Text
  if (tooltip.body) {
    const titleLines = tooltip.title || [];
    const bodyLines = tooltip.body.map(b => b.lines);

    const tableHead = document.createElement('thead');
    titleLines.forEach(title => {
      const tr = document.createElement('tr');
      tr.style.borderWidth = 0;
      const th = document.createElement('th');
      th.style.borderWidth = 0;
      const text = document.createTextNode(title);
      th.appendChild(text);
      tr.appendChild(th);
      tableHead.appendChild(tr);
    });

    const tableBody = document.createElement('tbody');
    bodyLines.forEach((body, i) => {
      const colors = tooltip.labelColors[i];
      const span = document.createElement('span');
      span.style.background = colors.backgroundColor;
      span.style.borderColor = colors.borderColor;
      span.style.borderWidth = '2px';
      span.style.marginRight = '10px';
      span.style.height = '10px';
      span.style.width = '10px';
      span.style.display = 'inline-block';

      const tr = document.createElement('tr');
      tr.style.backgroundColor = 'inherit';
      tr.style.borderWidth = 0;

      const td = document.createElement('td');
      td.style.borderWidth = 0;
      const text = document.createTextNode(body);
      td.appendChild(span);
      td.appendChild(text);
      tr.appendChild(td);
      tableBody.appendChild(tr);
    });

    const tableRoot = tooltipEl.querySelector('table');
    while (tableRoot.firstChild) {
      tableRoot.firstChild.remove();
    }
    tableRoot.appendChild(tableHead);
    tableRoot.appendChild(tableBody);
  }

  const {offsetLeft: positionX, offsetTop: positionY} = chart.canvas;

  // Display, position, and set styles
  tooltipEl.style.opacity = 1;
  tooltipEl.style.left = positionX + tooltip.caretX + 'px';
  tooltipEl.style.top = positionY + tooltip.caretY + 'px';
  tooltipEl.style.font = tooltip.options.bodyFont.string;
  tooltipEl.style.padding = tooltip.options.padding + 'px';
};

// Use external tooltip
const chart = new Chart(ctx, {
  type: 'line',
  data: data,
  options: {
    plugins: {
      tooltip: {
        enabled: false,  // Disable canvas tooltip
        external: externalTooltipHandler
      }
    }
  }
});
```

## Tooltip Styling

### Custom Colors

```javascript
options: {
  plugins: {
    tooltip: {
      backgroundColor: 'rgba(255, 99, 132, 0.9)',
      titleColor: '#fff',
      bodyColor: '#fff',
      borderColor: 'rgb(255, 99, 132)',
      borderWidth: 2,
      cornerRadius: 10,
      displayColors: true,
      padding: 15
    }
  }
}
```

### Custom Fonts

```javascript
options: {
  plugins: {
    tooltip: {
      titleFont: {
        size: 16,
        weight: 'bold',
        family: 'Arial'
      },
      bodyFont: {
        size: 14,
        weight: 'normal',
        family: 'Arial'
      }
    }
  }
}
```

## Disabling Tooltips

```javascript
// Disable all tooltips
options: {
  plugins: {
    tooltip: {
      enabled: false
    }
  }
}

// Disable for specific dataset
datasets: [{
  label: 'Sales',
  data: [10, 20, 30],
  tooltip: {
    enabled: false
  }
}]
```

## Framework Integration

### React

```jsx
import { Bar } from 'react-chartjs-2';

function ChartWithTooltip() {
  const options = {
    plugins: {
      tooltip: {
        callbacks: {
          label: (context) => {
            return `$${context.parsed.y.toLocaleString()}`;
          }
        }
      }
    }
  };

  return <Bar data={data} options={options} />;
}
```

### Vue

```vue
<script setup>
import { Bar } from 'vue-chartjs';

const chartOptions = {
  plugins: {
    tooltip: {
      callbacks: {
        label: (context) => {
          return `€${context.parsed.y}`;
        }
      }
    }
  }
};
</script>

<template>
  <Bar :data="chartData" :options="chartOptions" />
</template>
```

## Additional Resources

- See `examples/html-tooltip.md` for complete HTML tooltip implementation
- See `examples/conditional-tooltips.md` for showing tooltips conditionally
