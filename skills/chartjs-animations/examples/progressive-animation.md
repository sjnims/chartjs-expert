# Progressive Line Animation Example

Reveal a line chart progressively from left to right.

## Basic Progressive Reveal

```javascript
import Chart from 'chart.js/auto';

const data = {
  labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul'],
  datasets: [{
    label: 'Revenue',
    data: [12, 19, 3, 5, 2, 3, 15],
    borderColor: 'rgb(75, 192, 192)',
    backgroundColor: 'rgba(75, 192, 192, 0.2)',
    fill: true,
    tension: 0.4
  }]
};

const config = {
  type: 'line',
  data: data,
  options: {
    animation: {
      duration: 2000,
      easing: 'easeInOutQuart',
      // Animate X from start
      x: {
        from: (ctx) => {
          const chart = ctx.chart;
          const {left} = chart.chartArea;
          return left;
        }
      }
    }
  }
};

const chart = new Chart(
  document.getElementById('myChart'),
  config
);
```

## Progressive with Delay Per Point

```javascript
options: {
  animation: {
    duration: 2000,
    delay: (context) => {
      let delay = 0;
      if (context.type === 'data' && context.mode === 'default') {
        // Stagger each point by 100ms
        delay = context.dataIndex * 100;
      }
      return delay;
    }
  }
}
```

## Drawing Progress Indicator

```javascript
const progressBar = document.getElementById('progress');
const percentageDisplay = document.getElementById('percentage');

const chart = new Chart(ctx, {
  type: 'line',
  data: data,
  options: {
    animation: {
      duration: 3000,
      easing: 'easeOutQuart',
      onProgress: (animation) => {
        const progress = (animation.currentStep / animation.numSteps) * 100;
        progressBar.style.width = progress + '%';
        percentageDisplay.textContent = Math.round(progress) + '%';
      },
      onComplete: () => {
        progressBar.style.width = '100%';
        percentageDisplay.textContent = '100%';
        setTimeout(() => {
          progressBar.parentElement.style.display = 'none';
        }, 500);
      }
    }
  }
});
```

```html
<!-- Progress bar HTML -->
<div class="progress-container">
  <div class="progress-bar" id="progress"></div>
  <span class="progress-text" id="percentage">0%</span>
</div>

<style>
.progress-container {
  width: 100%;
  height: 30px;
  background: #f0f0f0;
  border-radius: 15px;
  margin-bottom: 20px;
  position: relative;
}

.progress-bar {
  height: 100%;
  background: linear-gradient(90deg, #4CAF50, #45a049);
  border-radius: 15px;
  transition: width 0.3s ease;
  width: 0%;
}

.progress-text {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-weight: bold;
  color: #333;
}
</style>
```

## React Implementation

```jsx
import { Line } from 'react-chartjs-2';
import { useState, useRef } from 'react';

function ProgressiveLineChart({ chartData }) {
  const [progress, setProgress] = useState(0);
  const chartRef = useRef(null);

  const options = {
    animation: {
      duration: 2000,
      easing: 'easeOutQuart',
      onProgress: (animation) => {
        const pct = (animation.currentStep / animation.numSteps) * 100;
        setProgress(Math.round(pct));
      },
      onComplete: () => {
        setProgress(100);
      }
    }
  };

  return (
    <div>
      <div className="progress-bar-container">
        <div
          className="progress-bar"
          style={{width: `${progress}%`}}
        />
        <span className="progress-text">{progress}%</span>
      </div>
      <Line ref={chartRef} data={chartData} options={options} />
    </div>
  );
}
```

## Vue Implementation

```vue
<template>
  <div>
    <div class="progress-container">
      <div class="progress-bar" :style="{width: progress + '%'}"></div>
      <span class="progress-text">{{ progress }}%</span>
    </div>
    <Line :data="chartData" :options="chartOptions" />
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { Line } from 'vue-chartjs';

const progress = ref(0);

const chartOptions = {
  animation: {
    duration: 2000,
    easing: 'easeOutQuart',
    onProgress: (animation) => {
      progress.value = Math.round(
        (animation.currentStep / animation.numSteps) * 100
      );
    },
    onComplete: () => {
      progress.value = 100;
    }
  }
};
</script>
```

## Custom Drawing During Animation

Advanced technique for custom visual effects:

```javascript
let animationProgress = 0;

const chart = new Chart(ctx, {
  type: 'line',
  data: data,
  options: {
    animation: {
      duration: 2000,
      onProgress: (animation) => {
        animationProgress = animation.currentStep / animation.numSteps;
      }
    }
  },
  plugins: [{
    id: 'customDrawing',
    afterDraw: (chart) => {
      if (animationProgress < 1) {
        const {ctx, chartArea: {left, top, right, bottom}} = chart;
        const width = right - left;
        const revealWidth = left + (width * animationProgress);

        // Draw curtain effect
        ctx.save();
        ctx.fillStyle = 'rgba(255, 255, 255, 0.9)';
        ctx.fillRect(revealWidth, top, right - revealWidth, bottom - top);
        ctx.restore();
      }
    }
  }]
});
```

## Multiple Dataset Progressive Reveal

```javascript
const data = {
  labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May'],
  datasets: [
    {
      label: 'Revenue',
      data: [12, 19, 3, 5, 2],
      borderColor: 'rgb(75, 192, 192)',
      animation: {
        delay: 0  // Start immediately
      }
    },
    {
      label: 'Expenses',
      data: [8, 12, 7, 9, 4],
      borderColor: 'rgb(255, 99, 132)',
      animation: {
        delay: 500  // Start 0.5s after first dataset
      }
    },
    {
      label: 'Profit',
      data: [4, 7, -4, -4, -2],
      borderColor: 'rgb(255, 205, 86)',
      animation: {
        delay: 1000  // Start 1s after first dataset
      }
    }
  ]
};
```

## Looping Progressive Animation

```javascript
options: {
  animation: {
    duration: 3000,
    loop: true,
    easing: 'linear',
    onProgress: (animation) => {
      // Creates continuous wave effect
      const progress = (animation.currentStep / animation.numSteps);
      // Use progress for custom effects
    }
  }
}
```

## Combined with Easing Effects

```javascript
// Drop then reveal
options: {
  animations: {
    y: {
      duration: 1500,
      from: (ctx) => ctx.chart.scales.y.getPixelForValue(0),
      easing: 'easeOutBounce'
    },
    x: {
      duration: 2000,
      delay: 500,  // Start after Y animation
      easing: 'easeOutQuart'
    }
  }
}
```

## Accessibility Considerations

```javascript
const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

const chart = new Chart(ctx, {
  type: 'line',
  data: data,
  options: {
    animation: prefersReducedMotion ? false : {
      duration: 2000,
      onProgress: updateProgressBar
    }
  }
});
```
