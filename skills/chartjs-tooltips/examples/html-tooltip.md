# HTML Tooltip Complete Example

Full implementation of custom HTML tooltips with styling.

## Complete Implementation

```javascript
import Chart from 'chart.js/auto';

// Create tooltip element
const getOrCreateTooltip = (chart) => {
  let tooltipEl = chart.canvas.parentNode.querySelector('div.chartjs-tooltip');

  if (!tooltipEl) {
    tooltipEl = document.createElement('div');
    tooltipEl.classList.add('chartjs-tooltip');

    // Styling
    Object.assign(tooltipEl.style, {
      background: 'rgba(0, 0, 0, 0.9)',
      borderRadius: '8px',
      color: 'white',
      opacity: '1',
      pointerEvents: 'none',
      position: 'absolute',
      transform: 'translate(-50%, -100%)',
      transition: 'all .3s ease',
      padding: '12px',
      boxShadow: '0 4px 6px rgba(0,0,0,0.3)',
      marginTop: '-10px'
    });

    chart.canvas.parentNode.appendChild(tooltipEl);
  }

  return tooltipEl;
};

// Tooltip handler
const externalTooltipHandler = (context) => {
  const {chart, tooltip} = context;
  const tooltipEl = getOrCreateTooltip(chart);

  if (tooltip.opacity === 0) {
    tooltipEl.style.opacity = 0;
    return;
  }

  // Build HTML content
  let innerHTML = '';

  if (tooltip.title.length) {
    innerHTML += `<div style="font-weight: bold; margin-bottom: 8px; font-size: 14px;">${tooltip.title[0]}</div>`;
  }

  if (tooltip.body) {
    tooltip.body.forEach((body, i) => {
      const colors = tooltip.labelColors[i];
      const indicator = `<span style="
        display: inline-block;
        width: 12px;
        height: 12px;
        background: ${colors.backgroundColor};
        border: 2px solid ${colors.borderColor};
        border-radius: 50%;
        margin-right: 8px;
      "></span>`;

      innerHTML += `<div style="margin: 4px 0;">${indicator}${body.lines[0]}</div>`;
    });
  }

  tooltipEl.innerHTML = innerHTML;

  // Position
  const {offsetLeft: positionX, offsetTop: positionY} = chart.canvas;
  tooltipEl.style.opacity = 1;
  tooltipEl.style.left = positionX + tooltip.caretX + 'px';
  tooltipEl.style.top = positionY + tooltip.caretY + 'px';
};

// Chart configuration
const chart = new Chart(ctx, {
  type: 'line',
  data: data,
  options: {
    plugins: {
      tooltip: {
        enabled: false,
        external: externalTooltipHandler
      }
    }
  }
});
```

## With Images

```javascript
const externalTooltipHandler = (context) => {
  const {chart, tooltip} = context;
  const tooltipEl = getOrCreateTooltip(chart);

  if (tooltip.opacity === 0) {
    tooltipEl.style.opacity = 0;
    return;
  }

  const dataPoint = tooltip.dataPoints[0];
  const imageUrl = chart.data.datasets[dataPoint.datasetIndex].images[dataPoint.dataIndex];

  let innerHTML = `
    <div style="display: flex; align-items: center; gap: 12px;">
      <img src="${imageUrl}" style="width: 40px; height: 40px; border-radius: 50%;" />
      <div>
        <div style="font-weight: bold;">${tooltip.title[0]}</div>
        <div>${tooltip.body[0].lines[0]}</div>
      </div>
    </div>
  `;

  tooltipEl.innerHTML = innerHTML;

  // Position tooltip
  const {offsetLeft, offsetTop} = chart.canvas;
  tooltipEl.style.opacity = 1;
  tooltipEl.style.left = offsetLeft + tooltip.caretX + 'px';
  tooltipEl.style.top = offsetTop + tooltip.caretY + 'px';
};
```

## React Component

```jsx
import { Line } from 'react-chartjs-2';
import { useRef, useEffect } from 'react';

function ChartWithHTMLTooltip({ chartData }) {
  const chartRef = useRef(null);

  const getOrCreateTooltip = (chart) => {
    let tooltipEl = chart.canvas.parentNode.querySelector('div.custom-tooltip');

    if (!tooltipEl) {
      tooltipEl = document.createElement('div');
      tooltipEl.className = 'custom-tooltip';
      chart.canvas.parentNode.appendChild(tooltipEl);
    }

    return tooltipEl;
  };

  const externalTooltipHandler = (context) => {
    const {chart, tooltip} = context;
    const tooltipEl = getOrCreateTooltip(chart);

    if (tooltip.opacity === 0) {
      tooltipEl.style.opacity = 0;
      return;
    }

    let html = '<div class="tooltip-content">';

    if (tooltip.title?.length) {
      html += `<div class="tooltip-title">${tooltip.title[0]}</div>`;
    }

    tooltip.body?.forEach((body, i) => {
      const colors = tooltip.labelColors[i];
      html += `
        <div class="tooltip-item">
          <span class="color-box" style="background: ${colors.backgroundColor}"></span>
          <span>${body.lines[0]}</span>
        </div>
      `;
    });

    html += '</div>';
    tooltipEl.innerHTML = html;

    const {offsetLeft, offsetTop} = chart.canvas;
    tooltipEl.style.opacity = 1;
    tooltipEl.style.left = offsetLeft + tooltip.caretX + 'px';
    tooltipEl.style.top = offsetTop + tooltip.caretY + 'px';
  };

  const options = {
    plugins: {
      tooltip: {
        enabled: false,
        external: externalTooltipHandler
      }
    }
  };

  return (
    <>
      <style>{`
        .custom-tooltip {
          position: absolute;
          background: rgba(0, 0, 0, 0.9);
          color: white;
          border-radius: 8px;
          padding: 12px;
          pointer-events: none;
          transform: translate(-50%, -120%);
          transition: all 0.3s ease;
          box-shadow: 0 4px 6px rgba(0,0,0,0.3);
        }

        .tooltip-title {
          font-weight: bold;
          margin-bottom: 8px;
          font-size: 14px;
        }

        .tooltip-item {
          margin: 4px 0;
          display: flex;
          align-items: center;
          gap: 8px;
        }

        .color-box {
          width: 12px;
          height: 12px;
          border-radius: 50%;
          display: inline-block;
        }
      `}</style>
      <Line ref={chartRef} data={chartData} options={options} />
    </>
  );
}
```

## Vue Component

```vue
<template>
  <div class="chart-wrapper">
    <Line :data="chartData" :options="chartOptions" />
  </div>
</template>

<script setup>
import { Line } from 'vue-chartjs';

const chartOptions = {
  plugins: {
    tooltip: {
      enabled: false,
      external: (context) => {
        const {chart, tooltip} = context;
        let tooltipEl = chart.canvas.parentNode.querySelector('.custom-tooltip');

        if (!tooltipEl) {
          tooltipEl = document.createElement('div');
          tooltipEl.className = 'custom-tooltip';
          chart.canvas.parentNode.appendChild(tooltipEl);
        }

        if (tooltip.opacity === 0) {
          tooltipEl.style.opacity = 0;
          return;
        }

        // Build HTML
        let html = '';
        if (tooltip.title?.length) {
          html += `<div class="title">${tooltip.title[0]}</div>`;
        }
        tooltip.body?.forEach(b => {
          html += `<div>${b.lines[0]}</div>`;
        });

        tooltipEl.innerHTML = html;

        const {offsetLeft, offsetTop} = chart.canvas;
        tooltipEl.style.opacity = 1;
        tooltipEl.style.left = offsetLeft + tooltip.caretX + 'px';
        tooltipEl.style.top = offsetTop + tooltip.caretY + 'px';
      }
    }
  }
};
</script>

<style scoped>
.chart-wrapper {
  position: relative;
}

:deep(.custom-tooltip) {
  position: absolute;
  background: rgba(0, 0, 0, 0.9);
  color: white;
  border-radius: 8px;
  padding: 12px;
  pointer-events: none;
  transform: translate(-50%, -120%);
  transition: all 0.3s ease;
}

:deep(.custom-tooltip .title) {
  font-weight: bold;
  margin-bottom: 8px;
}
</style>
```
