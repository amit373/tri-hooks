# useFullscreen

A React, Vue, and Angular hook/composable/service for controlling fullscreen mode. Enter, exit, and toggle fullscreen for any element with a consistent API and browser support detection.

## Features

- Enter fullscreen mode for any element (or document)
- Exit fullscreen and toggle fullscreen
- Track fullscreen state reactively
- Browser support detection (`isEnabled`)
- Cross-browser compatibility (including webkit prefixes)
- SSR-safe implementation
- Identical API across React, Vue, and Angular

## Installation

```bash
npm install tri-hooks
```

## Usage

### React

```tsx
import { useRef } from 'react';
import { useFullscreen } from 'tri-hooks/hooks/browser-apis/useFullscreen/react';

const VideoPlayer = () => {
  const videoRef = useRef<HTMLVideoElement>(null);
  const {
    isFullscreen,
    enterFullscreen,
    exitFullscreen,
    toggleFullscreen,
    isEnabled,
  } = useFullscreen();

  if (!isEnabled) {
    return <div>Fullscreen is not supported in your browser</div>;
  }

  return (
    <div>
      <video
        ref={videoRef}
        controls
        style={{ width: '100%', maxWidth: '800px' }}
      >
        <source src="sample-video.mp4" type="video/mp4" />
      </video>
      <div style={{ marginTop: '10px' }}>
        <button onClick={() => toggleFullscreen(videoRef.current)}>
          {isFullscreen ? 'Exit Fullscreen' : 'Enter Fullscreen'}
        </button>
        <button
          onClick={() => enterFullscreen(videoRef.current)}
          disabled={isFullscreen}
        >
          Enter Fullscreen
        </button>
        <button onClick={exitFullscreen} disabled={!isFullscreen}>
          Exit Fullscreen
        </button>
      </div>
      {isFullscreen && <p>Currently in fullscreen mode</p>}
    </div>
  );
};
```

### Vue

```vue
<template>
  <div>
    <div v-if="!isEnabled">Fullscreen is not supported in your browser</div>
    <template v-else>
      <video ref="videoRef" controls style="width: 100%; max-width: 800px">
        <source src="sample-video.mp4" type="video/mp4" />
      </video>
      <div style="margin-top: 10px">
        <button @click="toggleFullscreen(videoRef)">
          {{ isFullscreen ? 'Exit' : 'Enter' }} Fullscreen
        </button>
        <button @click="enterFullscreen(videoRef)" :disabled="isFullscreen">
          Enter Fullscreen
        </button>
        <button @click="exitFullscreen()" :disabled="!isFullscreen">
          Exit Fullscreen
        </button>
      </div>
      <p v-if="isFullscreen">Currently in fullscreen mode</p>
    </template>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { useFullscreen } from 'tri-hooks/hooks/browser-apis/useFullscreen/vue';

const videoRef = ref<HTMLVideoElement | null>(null);
const {
  isFullscreen,
  enterFullscreen,
  exitFullscreen,
  toggleFullscreen,
  isEnabled,
} = useFullscreen();
</script>
```

### Angular

```typescript
import { Component, ViewChild, ElementRef } from '@angular/core';
import { useFullscreen } from 'tri-hooks/hooks/browser-apis/useFullscreen/angular';

@Component({
  selector: 'app-video-player',
  template: `
    <div *ngIf="!fs.isEnabled()">
      Fullscreen is not supported in your browser
    </div>
    <template *ngIf="fs.isEnabled()">
      <video #videoRef controls style="width: 100%; max-width: 800px">
        <source src="sample-video.mp4" type="video/mp4" />
      </video>
      <div style="margin-top: 10px">
        <button (click)="fs.toggleFullscreen(videoRef?.nativeElement)">
          {{ fs.isFullscreen() ? 'Exit' : 'Enter' }} Fullscreen
        </button>
        <button
          (click)="fs.enterFullscreen(videoRef?.nativeElement)"
          [disabled]="fs.isFullscreen()"
        >
          Enter Fullscreen
        </button>
        <button (click)="fs.exitFullscreen()" [disabled]="!fs.isFullscreen()">
          Exit Fullscreen
        </button>
      </div>
      <p *ngIf="fs.isFullscreen()">Currently in fullscreen mode</p>
    </template>
  `,
})
export class VideoPlayerComponent {
  @ViewChild('videoRef') videoRef!: ElementRef<HTMLVideoElement>;
  fs = useFullscreen();
}
```

## API

### Arguments

None

### Return Values

- `isFullscreen` (boolean): True if currently in fullscreen mode
- `enterFullscreen` (function): Function to enter fullscreen mode for an element
  - `element` (HTMLElement, optional): Element to enter fullscreen, defaults to document body
- `exitFullscreen` (function): Function to exit fullscreen mode
- `toggleFullscreen` (function): Function to toggle fullscreen mode
  - `element` (HTMLElement, optional): Element to toggle fullscreen for
- `isEnabled` (boolean): True if fullscreen API is supported in the browser

## Browser Compatibility

- Chrome 15+
- Firefox 10+
- Safari 5.1+
- Edge 11+
- Opera 12.1+

## Security Considerations

Fullscreen can only be initiated by explicit user action (e.g., click event). Programmatic entry to fullscreen will be blocked by browsers for security reasons.

## Use Cases

- Video players
- Image galleries
- Presentations
- Games
- Immersive experiences
- Data visualization dashboards

## License

MIT
