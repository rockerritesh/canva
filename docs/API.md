# Canva Platform API Documentation

This document outlines the API and JavaScript interfaces available in the Canva platform.

## Content Management API

### Markdown Rendering

```javascript
// Render markdown content to HTML
function renderMarkdown(markdownText) {
  return marked.parse(markdownText);
}

// Example usage
const markdown = `# Hello World\nThis is **bold** text.`;
const html = renderMarkdown(markdown);
```

### HTML Content Management

```javascript
// Render HTML content with safety checks
function renderHTML(htmlContent) {
  // Basic sanitization (implement proper sanitization in production)
  const cleanHTML = htmlContent.replace(/<script[^>]*>.*?<\/script>/gi, '');
  return cleanHTML;
}
```

### Content Type Management

```javascript
// Set content editing mode
function setContentType(type) {
  // type: 'markdown' | 'html'
  currentContentType = type;
  updateEditorInterface(type);
}

// Get current content type
function getCurrentContentType() {
  return currentContentType;
}
```

## Simulation Management API

### Simulation Registry

```javascript
// Get all available simulations
function getSimulations() {
  return simulations; // Array of simulation objects
}

// Get simulation by name
function getSimulationByName(name) {
  return simulations.find(sim => sim.name === name);
}

// Add new simulation
function addSimulation(simulationData) {
  simulations.push(simulationData);
  refreshSimulationGrid();
}
```

### Simulation Object Structure

```javascript
const simulationExample = {
  name: 'Water Ripples',
  file: 'simulations/ripple.html',
  description: 'Interactive water ripple effects with realistic physics',
  category: 'nature', // 'nature', 'physics', 'art', 'tools'
  tags: ['water', 'physics', 'interactive'],
  thumbnail: 'assets/thumbnails/ripple.jpg', // optional
  featured: false // optional
};
```

## UI Management API

### Theme Management

```javascript
// Available CSS custom properties
const themeVariables = {
  '--primary-bg': '#0a0a0a',
  '--secondary-bg': '#1a1a2e',
  '--accent-color': '#4ecdc4',
  '--accent-secondary': '#ff6b6b',
  '--text-primary': '#ffffff',
  '--text-secondary': '#b8bcc8'
};

// Update theme colors
function updateTheme(newTheme) {
  Object.entries(newTheme).forEach(([property, value]) => {
    document.documentElement.style.setProperty(property, value);
  });
}
```

### Grid Layout Management

```javascript
// Refresh simulation grid
function refreshSimulationGrid() {
  populateSimulations();
}

// Filter simulations by category
function filterSimulations(category) {
  const filtered = simulations.filter(sim => 
    sim.category === category || !category
  );
  displaySimulations(filtered);
}

// Search simulations
function searchSimulations(query) {
  const results = simulations.filter(sim =>
    sim.name.toLowerCase().includes(query.toLowerCase()) ||
    sim.description.toLowerCase().includes(query.toLowerCase()) ||
    (sim.tags && sim.tags.some(tag => 
      tag.toLowerCase().includes(query.toLowerCase())
    ))
  );
  displaySimulations(results);
}
```

## Blog Management API

### Blog Content Loading

```javascript
// Load blog content from markdown file
async function loadBlogContent(filePath = 'content/blog.md') {
  try {
    const response = await fetch(filePath);
    const markdown = await response.text();
    return renderMarkdown(markdown);
  } catch (error) {
    console.error('Failed to load blog content:', error);
    return '<p>Failed to load blog content.</p>';
  }
}

// Update blog section
function updateBlogSection(content) {
  const blogDiv = document.getElementById('blogContent');
  blogDiv.innerHTML = content;
}
```

### Dynamic Content Updates

```javascript
// Auto-update content with debouncing
let updateTimeout;
function scheduleContentUpdate(delay = 500) {
  clearTimeout(updateTimeout);
  updateTimeout = setTimeout(() => {
    renderContent();
    hljs.highlightAll(); // Re-highlight code blocks
  }, delay);
}
```

## Canvas Simulation API

### Basic Canvas Setup

```javascript
// Create optimized canvas context
function createCanvas(containerId, width = 800, height = 600) {
  const container = document.getElementById(containerId);
  const canvas = document.createElement('canvas');
  const ctx = canvas.getContext('2d');
  
  // Set canvas size
  canvas.width = width;
  canvas.height = height;
  
  // Optimize for animations
  ctx.imageSmoothingEnabled = true;
  ctx.imageSmoothingQuality = 'high';
  
  container.appendChild(canvas);
  return { canvas, ctx };
}
```

### Animation Framework

```javascript
// Animation loop management
class AnimationLoop {
  constructor(updateFunction) {
    this.update = updateFunction;
    this.running = false;
    this.lastTime = 0;
  }
  
  start() {
    this.running = true;
    this.animate();
  }
  
  stop() {
    this.running = false;
  }
  
  animate(currentTime = 0) {
    if (!this.running) return;
    
    const deltaTime = currentTime - this.lastTime;
    this.lastTime = currentTime;
    
    this.update(deltaTime);
    requestAnimationFrame((time) => this.animate(time));
  }
}

// Usage example
const animationLoop = new AnimationLoop((deltaTime) => {
  // Your animation update code here
  clearCanvas();
  updateSimulation(deltaTime);
  renderSimulation();
});
```

### Particle System API

```javascript
// Basic particle class
class Particle {
  constructor(x, y, vx = 0, vy = 0) {
    this.x = x;
    this.y = y;
    this.vx = vx;
    this.vy = vy;
    this.life = 1.0;
    this.decay = 0.01;
  }
  
  update(deltaTime) {
    this.x += this.vx * deltaTime;
    this.y += this.vy * deltaTime;
    this.life -= this.decay * deltaTime;
    return this.life > 0;
  }
  
  render(ctx) {
    ctx.globalAlpha = this.life;
    ctx.fillStyle = '#ffffff';
    ctx.beginPath();
    ctx.arc(this.x, this.y, 2, 0, Math.PI * 2);
    ctx.fill();
    ctx.globalAlpha = 1.0;
  }
}

// Particle system manager
class ParticleSystem {
  constructor() {
    this.particles = [];
  }
  
  addParticle(particle) {
    this.particles.push(particle);
  }
  
  update(deltaTime) {
    this.particles = this.particles.filter(particle => 
      particle.update(deltaTime)
    );
  }
  
  render(ctx) {
    this.particles.forEach(particle => particle.render(ctx));
  }
}
```

## Event Handling API

### Input Management

```javascript
// Unified input handler for mouse and touch
function setupInputHandlers(canvas, callbacks) {
  const getEventPos = (event) => {
    const rect = canvas.getBoundingClientRect();
    const clientX = event.clientX || (event.touches && event.touches[0].clientX);
    const clientY = event.clientY || (event.touches && event.touches[0].clientY);
    
    return {
      x: clientX - rect.left,
      y: clientY - rect.top
    };
  };
  
  // Mouse events
  canvas.addEventListener('mousedown', (e) => {
    if (callbacks.onDown) callbacks.onDown(getEventPos(e));
  });
  
  canvas.addEventListener('mousemove', (e) => {
    if (callbacks.onMove) callbacks.onMove(getEventPos(e));
  });
  
  canvas.addEventListener('mouseup', (e) => {
    if (callbacks.onUp) callbacks.onUp(getEventPos(e));
  });
  
  // Touch events
  canvas.addEventListener('touchstart', (e) => {
    e.preventDefault();
    if (callbacks.onDown) callbacks.onDown(getEventPos(e));
  });
  
  canvas.addEventListener('touchmove', (e) => {
    e.preventDefault();
    if (callbacks.onMove) callbacks.onMove(getEventPos(e));
  });
  
  canvas.addEventListener('touchend', (e) => {
    e.preventDefault();
    if (callbacks.onUp) callbacks.onUp(getEventPos(e));
  });
}
```

## Utility Functions

### Math Utilities

```javascript
// Common math functions for simulations
const MathUtils = {
  lerp: (a, b, t) => a + (b - a) * t,
  clamp: (value, min, max) => Math.min(Math.max(value, min), max),
  map: (value, inMin, inMax, outMin, outMax) => {
    return (value - inMin) * (outMax - outMin) / (inMax - inMin) + outMin;
  },
  distance: (x1, y1, x2, y2) => {
    return Math.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2);
  },
  randomBetween: (min, max) => Math.random() * (max - min) + min
};
```

### Color Utilities

```javascript
// Color manipulation functions
const ColorUtils = {
  hslToRgb: (h, s, l) => {
    // HSL to RGB conversion
    const c = (1 - Math.abs(2 * l - 1)) * s;
    const x = c * (1 - Math.abs((h / 60) % 2 - 1));
    const m = l - c / 2;
    
    let r, g, b;
    if (h < 60) [r, g, b] = [c, x, 0];
    else if (h < 120) [r, g, b] = [x, c, 0];
    else if (h < 180) [r, g, b] = [0, c, x];
    else if (h < 240) [r, g, b] = [0, x, c];
    else if (h < 300) [r, g, b] = [x, 0, c];
    else [r, g, b] = [c, 0, x];
    
    return [
      Math.round((r + m) * 255),
      Math.round((g + m) * 255),
      Math.round((b + m) * 255)
    ];
  },
  
  rgbToHex: (r, g, b) => {
    return "#" + ((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1);
  }
};
```

## Configuration

### Platform Settings

```javascript
// Global platform configuration
const PlatformConfig = {
  version: '2.0.0',
  features: {
    contentEditor: true,
    syntaxHighlighting: true,
    autoSave: false, // Coming soon
    collaboration: false // Coming soon
  },
  performance: {
    maxParticles: 1000,
    targetFPS: 60,
    enableDebugMode: false
  },
  ui: {
    animatedBackground: true,
    responsiveDesign: true,
    darkMode: true
  }
};
```

## Error Handling

### Error Management

```javascript
// Global error handler
function handleError(error, context = 'general') {
  console.error(`[${context}] Error:`, error);
  
  // Show user-friendly error message
  const errorDiv = document.createElement('div');
  errorDiv.className = 'error-message';
  errorDiv.textContent = `Error in ${context}: ${error.message}`;
  
  // Auto-remove after 5 seconds
  setTimeout(() => errorDiv.remove(), 5000);
  
  document.body.appendChild(errorDiv);
}

// Wrap async functions with error handling
async function safeAsync(asyncFunction, context) {
  try {
    return await asyncFunction();
  } catch (error) {
    handleError(error, context);
    return null;
  }
}
```

---

*API Documentation v2.0.0 - Last updated: December 2024* 