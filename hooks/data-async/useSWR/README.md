# useSWR

A React, Vue, and Angular hook/composable/service for data fetching with a stale-while-revalidate strategy. Fetch by key, get cached data while revalidating, and optionally refresh on an interval.

## Features

- Stale-while-revalidate: show cached data while fetching fresh data
- Configurable refresh interval for auto-revalidation
- Error and loading state (`error`, `isValidating`)
- Manual revalidation or optimistic update via `mutate(newData?)`
- Same API across React, Vue, and Angular
- SSR-safe (no fetch during SSR if key is null)

## Installation

```bash
npm install tri-hooks
```

## Usage

### React

```tsx
import { useSWR } from 'tri-hooks/hooks/data-async/useSWR/react';

const UserProfile = ({ userId }: { userId: string }) => {
  const { data, error, isValidating, mutate } = useSWR(
    `/api/users/${userId}`,
    async url => {
      const res = await fetch(url);
      if (!res.ok) throw new Error('Failed to fetch');
      return res.json();
    },
    { refreshInterval: 30000 }
  );

  if (error) return <div>Error loading user</div>;
  if (!data) return <div>Loading...</div>;

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.email}</p>
      {isValidating && <span>Updating...</span>}
      <button onClick={() => mutate()}>Refresh</button>
    </div>
  );
};
```

### Vue

```vue
<template>
  <div v-if="error">Error loading user</div>
  <div v-else-if="!data">Loading...</div>
  <div v-else>
    <h1>{{ data.name }}</h1>
    <p>{{ data.email }}</p>
    <span v-if="isValidating">Updating...</span>
    <button @click="mutate()">Refresh</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { useSWR } from 'tri-hooks/hooks/data-async/useSWR/vue';

const userId = ref('1');
const { data, error, isValidating, mutate } = useSWR(
  `/api/users/${userId.value}`,
  async url => {
    const res = await fetch(url);
    if (!res.ok) throw new Error('Failed to fetch');
    return res.json();
  },
  { refreshInterval: 30000 }
);
</script>
```

### Angular

```typescript
import { Component } from '@angular/core';
import { useSWR } from 'tri-hooks/hooks/data-async/useSWR/angular';

@Component({
  selector: 'app-user-profile',
  template: `
    <div *ngIf="swr.error()">Error loading user</div>
    <div *ngIf="!swr.data()">Loading...</div>
    <div *ngIf="swr.data()">
      <h1>{{ swr.data()!.name }}</h1>
      <p>{{ swr.data()!.email }}</p>
      <span *ngIf="swr.isValidating()">Updating...</span>
      <button (click)="swr.mutate()">Refresh</button>
    </div>
  `,
})
export class UserProfileComponent {
  swr = useSWR(
    '/api/users/1',
    async url => {
      const res = await fetch(url);
      if (!res.ok) throw new Error('Failed to fetch');
      return res.json();
    },
    { refreshInterval: 30000 }
  );
}
```

In Angular, the returned object exposes `data`, `error`, and `isValidating` as getters (backed by signals), so in the template use `swr.data()`, `swr.error()`, `swr.isValidating()`.

## API

### Arguments

- **`key`** (string | null | getter): Cache key. When null, no fetch runs. In Vue/Angular you can pass a reactive getter.
- **`fetcher`** (function): `(key: string) => Promise<T>`. Called to fetch data for the given key.
- **`options`** (object, optional):
  - **`refreshInterval`** (number): Auto-refresh interval in milliseconds.
  - **`revalidateOnFocus`** (boolean): Revalidate when window gains focus (if implemented).
  - **`revalidateOnReconnect`** (boolean): Revalidate when network reconnects (if implemented).

### Return Values

- **`data`**: Fetched data or `undefined` while loading.
- **`error`**: `Error | null` if the last fetch failed.
- **`isValidating`**: `true` while a request is in flight.
- **`mutate(newData?)`**: Call with no args to revalidate; call with data to set cache optimistically then optionally revalidate.

## License

MIT
