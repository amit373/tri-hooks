# useClickOutside

A React, Vue, and Angular hook/composable that detects clicks outside a given element. Attach the returned ref to your node and run a callback when a click happens outside it—useful for closing dropdowns, modals, and tooltips.

## Features

- Single callback when click occurs outside the target element
- Framework-specific ref/element return (React ref, Vue ref, Angular element)
- Automatic listener cleanup on unmount
- Same behavior across React, Vue, and Angular

## Installation

```bash
npm install tri-hooks
```

## Problem

Detecting clicks outside an element typically requires manual event listeners and complex bubbling logic. This hook provides a clean API for handling outside clicks.

## When to Use

- Closing dropdown menus
- Dismissing modals
- Hiding tooltips
- Closing context menus
- Collapsing navigation menus
- Closing popovers

## API Reference

### React

```typescript
const ref = useClickOutside<T extends HTMLElement>(callback)
```

### Vue

```typescript
const elementRef = useClickOutside<T extends HTMLElement>(callback)
```

### Angular

```typescript
const { element } = useClickOutside<T extends HTMLElement>(callback)
```

**Parameters:**

- `callback` (function): Function to call when click occurs outside the element

**Returns:**

- `ref`/`elementRef`/`element` (Ref/Element): Reference to attach to the element to watch

## Usage Examples

### React

```tsx
import { useRef } from 'react';
import { useClickOutside } from 'tri-hooks/hooks/dom-events/useClickOutside/react';

function Dropdown() {
  const [isOpen, setIsOpen] = useState(false);
  const dropdownRef = useClickOutside<HTMLDivElement>(() => {
    setIsOpen(false);
  });

  return (
    <div ref={dropdownRef} className="dropdown">
      <button onClick={() => setIsOpen(!isOpen)}>Toggle Dropdown</button>
      {isOpen && (
        <div className="dropdown-menu">
          <a href="#">Option 1</a>
          <a href="#">Option 2</a>
          <a href="#">Option 3</a>
        </div>
      )}
    </div>
  );
}
```

### Vue

```vue
<script setup>
import { ref } from 'vue';
import { useClickOutside } from 'tri-hooks/hooks/dom-events/useClickOutside/vue';

const isOpen = ref(false);
const dropdownRef = useClickOutside(() => {
  isOpen.value = false;
});
</script>

<template>
  <div ref="dropdownRef" class="dropdown">
    <button @click="isOpen = !isOpen">Toggle Dropdown</button>
    <div v-if="isOpen" class="dropdown-menu">
      <a href="#">Option 1</a>
      <a href="#">Option 2</a>
      <a href="#">Option 3</a>
    </div>
  </div>
</template>
```

### Angular

```typescript
import { Component, ElementRef, ViewChild, OnInit } from '@angular/core';
import { useClickOutside } from 'tri-hooks/hooks/dom-events/useClickOutside/angular';

@Component({
  selector: 'app-dropdown',
  template: `
    <div #dropdownRef class="dropdown">
      <button (click)="toggleDropdown()">Toggle Dropdown</button>
      <div *ngIf="isOpen" class="dropdown-menu">
        <a href="#">Option 1</a>
        <a href="#">Option 2</a>
        <a href="#">Option 3</a>
      </div>
    </div>
  `,
})
export class DropdownComponent implements OnInit {
  isOpen = false;
  private clickOutside = useClickOutside(() => {
    this.isOpen = false;
  });

  @ViewChild('dropdownRef', { static: true })
  dropdownRef!: ElementRef<HTMLDivElement>;

  ngOnInit() {
    // Attach the element reference
    this.clickOutside.element = this.dropdownRef.nativeElement;
  }

  toggleDropdown() {
    this.isOpen = !this.isOpen;
  }
}
```

## Browser Support

All modern browsers supporting ES2020+ and HTMLElement.contains()

## Edge Cases Handled

- Proper cleanup of event listeners on component unmount
- Prevents memory leaks from hanging event listeners
- Handles dynamic element changes
- SSR-safe implementation

## Cleanup Guarantees

- React: useEffect cleanup removes event listeners
- Vue: onUnmounted cleanup removes event listeners
- Angular: DestroyRef cleanup removes event listeners

## Angular Implementation Details

Uses getter/setter pattern to allow the element to be set after the hook is created. The destroyRef.onDestroy ensures proper cleanup when the component is destroyed.
