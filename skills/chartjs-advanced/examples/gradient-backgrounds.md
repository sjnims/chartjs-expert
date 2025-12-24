# Gradient Background Patterns

Collection of common gradient patterns for Chart.js.

## Vertical Gradient (Top to Bottom)

```javascript
function createVerticalGradient(ctx, chartArea, colorTop, colorBottom) {
  const gradient = ctx.createLinearGradient(0, chartArea.top, 0, chartArea.bottom);
  gradient.addColorStop(0, colorTop);
  gradient.addColorStop(1, colorBottom);
  return gradient;
}

// Usage
backgroundColor: function(context) {
  const {ctx, chartArea} = context.chart;
  if (!chartArea) return null;
  return createVerticalGradient(ctx, chartArea,
    'rgba(75, 192, 192, 1)',
    'rgba(75, 192, 192, 0.1)'
  );
}
```

## Horizontal Gradient (Left to Right)

```javascript
function createHorizontalGradient(ctx, chartArea, colorLeft, colorRight) {
  const gradient = ctx.createLinearGradient(chartArea.left, 0, chartArea.right, 0);
  gradient.addColorStop(0, colorLeft);
  gradient.addColorStop(1, colorRight);
  return gradient;
}
```

## Three-Color Gradient

```javascript
function createThreeColorGradient(ctx, chartArea) {
  const gradient = ctx.createLinearGradient(0, chartArea.bottom, 0, chartArea.top);
  gradient.addColorStop(0, 'rgba(255, 99, 132, 0)');
  gradient.addColorStop(0.5, 'rgba(255, 206, 86, 0.5)');
  gradient.addColorStop(1, 'rgba(75, 192, 192, 1)');
  return gradient;
}
```

## Radial Gradient (Center Out)

```javascript
function createCenterRadialGradient(ctx, chartArea) {
  const centerX = (chartArea.left + chartArea.right) / 2;
  const centerY = (chartArea.top + chartArea.bottom) / 2;
  const radius = Math.min(
    chartArea.right - chartArea.left,
    chartArea.bottom - chartArea.top
  ) / 2;

  const gradient = ctx.createRadialGradient(
    centerX, centerY, 0,
    centerX, centerY, radius
  );

  gradient.addColorStop(0, 'rgba(255, 99, 132, 1)');
  gradient.addColorStop(1, 'rgba(255, 99, 132, 0)');

  return gradient;
}
```

## Multi-Dataset Unique Gradients

```javascript
const colorSchemes = [
  { top: 'rgba(255, 99, 132, 1)', bottom: 'rgba(255, 99, 132, 0.1)' },
  { top: 'rgba(54, 162, 235, 1)', bottom: 'rgba(54, 162, 235, 0.1)' },
  { top: 'rgba(255, 206, 86, 1)', bottom: 'rgba(255, 206, 86, 0.1)' },
];

datasets: colorSchemes.map((scheme, i) => ({
  label: `Dataset ${i + 1}`,
  data: data[i],
  backgroundColor: function(context) {
    const {ctx, chartArea} = context.chart;
    if (!chartArea) return null;

    const gradient = ctx.createLinearGradient(0, chartArea.bottom, 0, chartArea.top);
    gradient.addColorStop(0, scheme.bottom);
    gradient.addColorStop(1, scheme.top);
    return gradient;
  },
  borderColor: scheme.top,
  fill: true
}))
```

## Conditional Gradient (Positive/Negative)

```javascript
backgroundColor: function(context) {
  const {ctx, chartArea} = context.chart;
  const value = context.parsed.y;

  if (!chartArea) return null;

  const gradient = ctx.createLinearGradient(0, chartArea.bottom, 0, chartArea.top);

  if (value >= 0) {
    // Positive values: green gradient
    gradient.addColorStop(0, 'rgba(75, 192, 192, 0.1)');
    gradient.addColorStop(1, 'rgba(75, 192, 192, 1)');
  } else {
    // Negative values: red gradient
    gradient.addColorStop(0, 'rgba(255, 99, 132, 0.1)');
    gradient.addColorStop(1, 'rgba(255, 99, 132, 1)');
  }

  return gradient;
}
```

## Diagonal Gradient

```javascript
function createDiagonalGradient(ctx, chartArea) {
  const gradient = ctx.createLinearGradient(
    chartArea.left, chartArea.top,
    chartArea.right, chartArea.bottom
  );

  gradient.addColorStop(0, 'rgba(255, 99, 132, 1)');
  gradient.addColorStop(1, 'rgba(54, 162, 235, 1)');

  return gradient;
}
```

## React Hook for Gradients

```jsx
import { useMemo } from 'react';

function useChartGradient(chartRef, topColor, bottomColor) {
  return useMemo(() => {
    return function(context) {
      const chart = context.chart;
      const {ctx, chartArea} = chart;

      if (!chartArea) {
        return null;
      }

      const gradient = ctx.createLinearGradient(0, chartArea.bottom, 0, chartArea.top);
      gradient.addColorStop(0, bottomColor);
      gradient.addColorStop(1, topColor);
      return gradient;
    };
  }, [topColor, bottomColor]);
}

// Usage in component
function MyChart() {
  const chartRef = useRef();
  const gradientBg = useChartGradient(
    chartRef,
    'rgba(75, 192, 192, 1)',
    'rgba(75, 192, 192, 0.1)'
  );

  const data = {
    datasets: [{
      data: [10, 20, 30],
      backgroundColor: gradientBg
    }]
  };

  return <Line ref={chartRef} data={data} />;
}
```
