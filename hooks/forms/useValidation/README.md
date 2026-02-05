# useValidation

A React, Vue, and Angular hook/composable/service for form-level validation. Define initial values and validation rules per field; get reactive values, errors, and helpers like `handleChange`, `validate`, and `reset`.

## Features

- Form-level validation with multiple fields
- Custom validation rules per field (return `string` error or `undefined` if valid)
- Reactive values and errors; clear errors on change
- `handleChange(name, value)` and `validate()` / `reset()`
- TypeScript-friendly with generic form shape
- Identical API across React, Vue, and Angular

## Installation

```bash
npm install tri-hooks
```

## Usage

### React

```tsx
import { useValidation } from 'tri-hooks/hooks/forms/useValidation/react';

const LoginForm = () => {
  const { values, errors, isValid, handleChange, validate, reset } =
    useValidation(
      { email: '', password: '' },
      {
        email: v => (v.trim() ? undefined : 'Email is required'),
        password: v =>
          v.length >= 8 ? undefined : 'Password must be at least 8 characters',
      }
    );

  const onSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (validate().isValid) {
      console.log('Submit', values);
    }
  };

  return (
    <form onSubmit={onSubmit}>
      <input
        value={values.email}
        onChange={e => handleChange('email', e.target.value)}
        placeholder="Email"
      />
      {errors.email && <span>{errors.email}</span>}
      <input
        type="password"
        value={values.password}
        onChange={e => handleChange('password', e.target.value)}
        placeholder="Password"
      />
      {errors.password && <span>{errors.password}</span>}
      <button type="submit" disabled={!isValid}>
        Submit
      </button>
      <button type="button" onClick={reset}>
        Reset
      </button>
    </form>
  );
};
```

### Vue

```vue
<template>
  <form @submit.prevent="onSubmit">
    <input
      v-model="values.email"
      @input="handleChange('email', ($event.target as HTMLInputElement).value)"
      placeholder="Email"
    />
    <span v-if="errors.email">{{ errors.email }}</span>
    <input
      type="password"
      v-model="values.password"
      @input="
        handleChange('password', ($event.target as HTMLInputElement).value)
      "
      placeholder="Password"
    />
    <span v-if="errors.password">{{ errors.password }}</span>
    <button type="submit" :disabled="!isValid">Submit</button>
    <button type="button" @click="reset">Reset</button>
  </form>
</template>

<script setup lang="ts">
import { useValidation } from 'tri-hooks/hooks/forms/useValidation/vue';

const { values, errors, isValid, handleChange, validate, reset } =
  useValidation(
    { email: '', password: '' },
    {
      email: v => (v.trim() ? undefined : 'Email is required'),
      password: v =>
        v.length >= 8 ? undefined : 'Password must be at least 8 characters',
    }
  );

const onSubmit = () => {
  if (validate().isValid) {
    console.log('Submit', values);
  }
};
</script>
```

### Angular

```typescript
import { Component } from '@angular/core';
import { useValidation } from 'tri-hooks/hooks/forms/useValidation/angular';

@Component({
  selector: 'app-login-form',
  template: `
    <form (ngSubmit)="onSubmit()">
      <input
        [ngModel]="v.values.email"
        name="email"
        (ngModelChange)="v.handleChange('email', $event)"
        placeholder="Email"
      />
      <span *ngIf="v.errors.email">{{ v.errors.email }}</span>
      <input
        type="password"
        [ngModel]="v.values.password"
        name="password"
        (ngModelChange)="v.handleChange('password', $event)"
        placeholder="Password"
      />
      <span *ngIf="v.errors.password">{{ v.errors.password }}</span>
      <button type="submit" [disabled]="!v.isValid">Submit</button>
      <button type="button" (click)="v.reset()">Reset</button>
    </form>
  `,
})
export class LoginFormComponent {
  v = useValidation(
    { email: '', password: '' },
    {
      email: val => (val?.trim() ? undefined : 'Email is required'),
      password: val =>
        val && val.length >= 8
          ? undefined
          : 'Password must be at least 8 characters',
    }
  );

  onSubmit() {
    if (this.v.validate().isValid) {
      console.log('Submit', this.v.values);
    }
  }
}
```

## API

### Arguments

- **`initialValues`** (object): Initial field values (e.g. `{ email: '', password: '' }`).
- **`validationRules`** (object): One validator per field. Keys match `initialValues`. Each value is a function `(value) => string | undefined` — return `undefined` when valid, or an error string when invalid.

### Return Values

- **`values`**: Current form values (React: plain object; Vue: reactive object; Angular: use `values()` signal).
- **`errors`**: Current errors by field name (React: plain object; Vue: reactive; Angular: use `errors()`).
- **`isValid`**: `true` when there are no errors.
- **`handleChange(name, value)`**: Update one field and clear its error.
- **`validate()`**: Run all validators, update `errors`, and return `{ isValid, errors }`.
- **`reset()`**: Reset values and errors to `initialValues` and empty errors.

## Validation Rules

Each rule is a function that receives the field value and returns:

- `undefined` (or no return) when valid
- A `string` error message when invalid

## Common Patterns

- Required: `(v) => (v?.trim() ? undefined : 'Field is required')`
- Min length: `(v) => (v && v.length >= 8 ? undefined : 'At least 8 characters')`
- Email: `(v) => (/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(v || '') ? undefined : 'Invalid email')`
- Match other: `(v) => (v === otherValue ? undefined : 'Values do not match')`

## License

MIT
