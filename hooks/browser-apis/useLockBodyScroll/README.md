# useLockBodyScroll

A React, Vue, and Angular hook/composable that locks or unlocks body scroll. Use it when a modal or drawer is open so the background page does not scroll.

## Features

- Lock body overflow to prevent background scroll
- Restore previous overflow on unlock or unmount
- React: pass a boolean; Vue/Angular: pass a getter for reactive lock state
- SSR-safe; no-op when `document` is unavailable
- Identical behavior across React, Vue, and Angular

## Installation

```bash
npm install tri-hooks
```

## When to Use

- Modals and dialogs
- Full-screen drawers or side panels
- Any overlay that should trap focus and prevent scroll

## API Reference

### React

```typescript
useLockBodyScroll(lock: boolean): void
```

### Vue / Angular

```typescript
useLockBodyScroll(lock: () => boolean): void
```

**Parameters:**

- `lock`: When true, body overflow is set to hidden; when false, previous value is restored (Vue/Angular: getter for reactivity)

## Usage Examples

### React

```tsx
import { useLockBodyScroll } from 'tri-hooks/hooks/browser-apis/useLockBodyScroll/react';

function Modal({ open }: { open: boolean }) {
  useLockBodyScroll(open);
  return open ? <div className="modal">...</div> : null;
}
```

### Vue

```vue
<script setup>
import { useLockBodyScroll } from 'tri-hooks/hooks/browser-apis/useLockBodyScroll/vue';

const open = ref(false);
useLockBodyScroll(() => open.value);
</script>
```

### Angular

```typescript
import { useLockBodyScroll } from 'tri-hooks/hooks/browser-apis/useLockBodyScroll/angular';

@Component({ ... })
export class ModalComponent {
  open = signal(false);
  constructor() {
    useLockBodyScroll(() => this.open());
  }
}
```

## Cleanup Guarantees

Body overflow is restored on unmount or when lock becomes false.
