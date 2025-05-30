# Assets Directory

This directory contains static assets for the Canva platform.

## Structure

```
assets/
├── images/          # Image files (screenshots, icons, etc.)
├── thumbnails/      # Simulation thumbnails
├── icons/           # UI icons and favicons
├── fonts/           # Custom fonts (if any)
└── videos/          # Demo videos or animations
```

## Usage

### Image Assets
- Simulation screenshots
- Platform previews
- Documentation images
- Social media images

### Thumbnails
- Auto-generated or custom thumbnails for simulations
- Recommended size: 300x200px
- Format: JPG or WebP for better compression

### Icons
- Platform favicon
- UI icons for buttons and navigation
- Social media icons

## Guidelines

- Use WebP format for better compression when possible
- Optimize images for web (compress before adding)
- Follow consistent naming conventions
- Include alt text descriptions for accessibility

## Examples

```html
<!-- Simulation thumbnail -->
<img src="assets/thumbnails/ripple.jpg" alt="Water ripple simulation preview" />

<!-- Platform icon -->
<link rel="icon" href="assets/icons/favicon.ico" />

<!-- Documentation image -->
![Platform Screenshot](assets/images/platform-preview.jpg)
```

## Future Additions

- Animated GIFs for simulation previews
- Video demos of complex simulations
- Custom icon set for better branding
- Loading animations and transitions 