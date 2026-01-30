# Component Documentation

## Overview

This document provides documentation for reusable components in the frontend application. Each component includes usage examples, props, and styling guidelines.

## Table of Contents

- [Common Components](#common-components)
- [Layout Components](#layout-components)
- [Form Components](#form-components)
- [Feedback Components](#feedback-components)
- [Component Patterns](#component-patterns)

---

## Common Components

### Button

A reusable button component with multiple variants.

**Location:** `src/components/common/Button/Button.js`

**Props:**
- `children` (node, required) - Button content
- `variant` (string) - Button style: 'primary', 'secondary', 'danger'
- `size` (string) - Button size: 'small', 'medium', 'large'
- `onClick` (function) - Click handler
- `disabled` (boolean) - Disabled state
- `type` (string) - Button type: 'button', 'submit', 'reset'
- `loading` (boolean) - Loading state

**Usage:**
```javascript
import Button from '../components/common/Button';

// Basic usage
<Button onClick={handleClick}>Click me</Button>

// With variant
<Button variant="secondary">Secondary</Button>
<Button variant="danger">Delete</Button>

// With size
<Button size="small">Small</Button>
<Button size="large">Large</Button>

// Disabled state
<Button disabled>Disabled</Button>

// Loading state
<Button loading>Loading...</Button>
```

**Example:**
```javascript
const ActionButtons = () => {
  const [loading, setLoading] = useState(false);

  const handleSubmit = async () => {
    setLoading(true);
    try {
      await submitForm();
    } finally {
      setLoading(false);
    }
  };

  return (
    <div>
      <Button onClick={handleSubmit} loading={loading}>
        Submit
      </Button>
      <Button variant="secondary" onClick={handleCancel}>
        Cancel
      </Button>
    </div>
  );
};
```

---

### Input

A reusable input component with validation support.

**Location:** `src/components/common/Input/Input.js`

**Props:**
- `label` (string) - Input label
- `name` (string, required) - Input name
- `type` (string) - Input type: 'text', 'email', 'password', etc.
- `value` (string) - Input value
- `onChange` (function) - Change handler
- `placeholder` (string) - Placeholder text
- `error` (string) - Error message
- `required` (boolean) - Required field
- `disabled` (boolean) - Disabled state

**Usage:**
```javascript
import Input from '../components/common/Input';

// Basic usage
<Input
  name="email"
  label="Email"
  type="email"
  value={email}
  onChange={(e) => setEmail(e.target.value)}
/>

// With error
<Input
  name="password"
  label="Password"
  type="password"
  value={password}
  onChange={(e) => setPassword(e.target.value)}
  error="Password is required"
/>

// Required field
<Input
  name="username"
  label="Username"
  required
  value={username}
  onChange={(e) => setUsername(e.target.value)}
/>
```

**Example:**
```javascript
const LoginForm = () => {
  const [formData, setFormData] = useState({
    email: '',
    password: '',
  });
  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData({ ...formData, [name]: value });
    // Clear error when user types
    if (errors[name]) {
      setErrors({ ...errors, [name]: '' });
    }
  };

  return (
    <form>
      <Input
        name="email"
        label="Email"
        type="email"
        value={formData.email}
        onChange={handleChange}
        error={errors.email}
        required
      />
      <Input
        name="password"
        label="Password"
        type="password"
        value={formData.password}
        onChange={handleChange}
        error={errors.password}
        required
      />
    </form>
  );
};
```

---

### Modal

A reusable modal/dialog component.

**Location:** `src/components/common/Modal/Modal.js`

**Props:**
- `isOpen` (boolean) - Modal visibility
- `onClose` (function) - Close handler
- `title` (string) - Modal title
- `children` (node) - Modal content
- `size` (string) - Modal size: 'small', 'medium', 'large'
- `showCloseButton` (boolean) - Show close button

**Usage:**
```javascript
import Modal from '../components/common/Modal';

const MyComponent = () => {
  const [isModalOpen, setIsModalOpen] = useState(false);

  return (
    <>
      <Button onClick={() => setIsModalOpen(true)}>Open Modal</Button>
      
      <Modal
        isOpen={isModalOpen}
        onClose={() => setIsModalOpen(false)}
        title="Confirm Action"
      >
        <p>Are you sure you want to proceed?</p>
        <Button onClick={handleConfirm}>Confirm</Button>
        <Button variant="secondary" onClick={() => setIsModalOpen(false)}>
          Cancel
        </Button>
      </Modal>
    </>
  );
};
```

---

### Card

A container component for displaying content in a card format.

**Location:** `src/components/common/Card/Card.js`

**Props:**
- `children` (node) - Card content
- `title` (string) - Card title
- `footer` (node) - Card footer content
- `onClick` (function) - Click handler (makes card clickable)
- `hoverable` (boolean) - Add hover effect

**Usage:**
```javascript
import Card from '../components/common/Card';

// Basic card
<Card title="User Information">
  <p>Name: John Doe</p>
  <p>Email: john@example.com</p>
</Card>

// Card with footer
<Card
  title="Resource"
  footer={<Button>View Details</Button>}
>
  <p>Resource description...</p>
</Card>

// Clickable card
<Card
  title="Clickable Card"
  onClick={() => navigate('/details')}
  hoverable
>
  <p>Click to view details</p>
</Card>
```

---

## Layout Components

### Header

Application header with navigation.

**Location:** `src/components/layout/Header/Header.js`

**Props:**
- `user` (object) - Current user object
- `onLogout` (function) - Logout handler

**Usage:**
```javascript
import Header from '../components/layout/Header';
import { useAuth } from '../hooks/useAuth';

const App = () => {
  const { user, logout } = useAuth();

  return (
    <div>
      <Header user={user} onLogout={logout} />
      {/* Rest of app */}
    </div>
  );
};
```

---

### Footer

Application footer.

**Location:** `src/components/layout/Footer/Footer.js`

**Usage:**
```javascript
import Footer from '../components/layout/Footer';

<Footer />
```

---

### Layout

Main layout wrapper component.

**Location:** `src/components/layout/Layout/Layout.js`

**Props:**
- `children` (node) - Page content

**Usage:**
```javascript
import Layout from '../components/layout/Layout';

const App = () => {
  return (
    <Layout>
      <Routes>
        {/* Your routes */}
      </Routes>
    </Layout>
  );
};
```

---

## Form Components

### Form

A form wrapper with validation support.

**Location:** `src/components/forms/Form/Form.js`

**Props:**
- `onSubmit` (function) - Submit handler
- `children` (node) - Form content
- `initialValues` (object) - Initial form values
- `validationSchema` (object) - Validation rules

**Usage:**
```javascript
import Form from '../components/forms/Form';

const validationSchema = {
  email: (value) => {
    if (!value) return 'Email is required';
    if (!/\S+@\S+\.\S+/.test(value)) return 'Email is invalid';
    return null;
  },
  password: (value) => {
    if (!value) return 'Password is required';
    if (value.length < 8) return 'Password must be at least 8 characters';
    return null;
  },
};

const LoginForm = () => {
  const handleSubmit = async (values) => {
    await authService.login(values);
  };

  return (
    <Form
      onSubmit={handleSubmit}
      initialValues={{ email: '', password: '' }}
      validationSchema={validationSchema}
    >
      <Input name="email" label="Email" type="email" />
      <Input name="password" label="Password" type="password" />
      <Button type="submit">Login</Button>
    </Form>
  );
};
```

---

### Select

A dropdown select component.

**Location:** `src/components/forms/Select/Select.js`

**Props:**
- `name` (string, required) - Select name
- `label` (string) - Select label
- `options` (array) - Options array: `[{ value, label }]`
- `value` (string) - Selected value
- `onChange` (function) - Change handler
- `placeholder` (string) - Placeholder text
- `error` (string) - Error message

**Usage:**
```javascript
import Select from '../components/forms/Select';

const options = [
  { value: 'option1', label: 'Option 1' },
  { value: 'option2', label: 'Option 2' },
  { value: 'option3', label: 'Option 3' },
];

<Select
  name="category"
  label="Category"
  options={options}
  value={category}
  onChange={(e) => setCategory(e.target.value)}
  placeholder="Select a category"
/>
```

---

### Textarea

A textarea component.

**Location:** `src/components/forms/Textarea/Textarea.js`

**Props:**
- `name` (string, required) - Textarea name
- `label` (string) - Textarea label
- `value` (string) - Textarea value
- `onChange` (function) - Change handler
- `rows` (number) - Number of rows
- `placeholder` (string) - Placeholder text
- `error` (string) - Error message

**Usage:**
```javascript
import Textarea from '../components/forms/Textarea';

<Textarea
  name="description"
  label="Description"
  value={description}
  onChange={(e) => setDescription(e.target.value)}
  rows={5}
  placeholder="Enter description..."
/>
```

---

## Feedback Components

### Loading Spinner

A loading spinner component.

**Location:** `src/components/common/Spinner/Spinner.js`

**Props:**
- `size` (string) - Size: 'small', 'medium', 'large'
- `color` (string) - Spinner color

**Usage:**
```javascript
import Spinner from '../components/common/Spinner';

// Basic usage
<Spinner />

// With size
<Spinner size="large" />

// In button
<Button disabled>
  <Spinner size="small" /> Loading...
</Button>
```

---

### Alert

An alert/notification component.

**Location:** `src/components/common/Alert/Alert.js`

**Props:**
- `type` (string) - Alert type: 'success', 'error', 'warning', 'info'
- `message` (string) - Alert message
- `onClose` (function) - Close handler
- `dismissible` (boolean) - Show close button

**Usage:**
```javascript
import Alert from '../components/common/Alert';

// Success alert
<Alert type="success" message="Operation completed successfully!" />

// Error alert
<Alert type="error" message="An error occurred" dismissible onClose={handleClose} />

// Warning alert
<Alert type="warning" message="Please review your input" />
```

---

### Toast

A toast notification component (for temporary messages).

**Location:** `src/components/common/Toast/Toast.js`

**Usage:**
```javascript
import { useToast } from '../hooks/useToast';

const MyComponent = () => {
  const { showToast } = useToast();

  const handleSuccess = () => {
    showToast('Operation successful!', 'success');
  };

  const handleError = () => {
    showToast('An error occurred', 'error');
  };

  return (
    <div>
      <Button onClick={handleSuccess}>Success</Button>
      <Button onClick={handleError}>Error</Button>
    </div>
  );
};
```

---

## Component Patterns

### Compound Components

Components that work together:

```javascript
// Card compound component
const Card = ({ children }) => <div className="card">{children}</div>;
Card.Header = ({ children }) => <div className="card-header">{children}</div>;
Card.Body = ({ children }) => <div className="card-body">{children}</div>;
Card.Footer = ({ children }) => <div className="card-footer">{children}</div>;

// Usage
<Card>
  <Card.Header>Title</Card.Header>
  <Card.Body>Content</Card.Body>
  <Card.Footer>Footer</Card.Footer>
</Card>
```

### Render Props

Components that accept render functions:

```javascript
const DataFetcher = ({ url, children }) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(data => {
        setData(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err);
        setLoading(false);
      });
  }, [url]);

  return children({ data, loading, error });
};

// Usage
<DataFetcher url="/api/users">
  {({ data, loading, error }) => {
    if (loading) return <Spinner />;
    if (error) return <Alert type="error" message={error.message} />;
    return <UserList users={data} />;
  }}
</DataFetcher>
```

### Higher-Order Components

Components that enhance other components:

```javascript
const withLoading = (Component) => {
  return ({ loading, ...props }) => {
    if (loading) return <Spinner />;
    return <Component {...props} />;
  };
};

// Usage
const UserListWithLoading = withLoading(UserList);

<UserListWithLoading loading={isLoading} users={users} />
```

---

## Styling Guidelines

### CSS Modules

Each component should have its own CSS module:

```css
/* Button.module.css */
.button {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.buttonPrimary {
  background-color: var(--primary-color);
  color: white;
}

.buttonDisabled {
  opacity: 0.6;
  cursor: not-allowed;
}
```

### CSS Variables

Use CSS variables for theming:

```css
:root {
  --primary-color: #007bff;
  --secondary-color: #6c757d;
  --success-color: #28a745;
  --danger-color: #dc3545;
  --border-radius: 4px;
  --spacing-unit: 8px;
}
```

---

## Best Practices

1. **Keep components focused** - Each component should have a single responsibility
2. **Use PropTypes** - Always define PropTypes for component props
3. **Provide default props** - Set sensible defaults for optional props
4. **Handle edge cases** - Consider null, undefined, and empty states
5. **Make components accessible** - Use semantic HTML and ARIA attributes
6. **Document components** - Add JSDoc comments for complex components
7. **Test components** - Write tests for component behavior
8. **Optimize performance** - Use React.memo, useMemo, useCallback when needed

---

## Component Checklist

When creating a new component:

- [ ] Component is in the correct directory
- [ ] Component has PropTypes defined
- [ ] Component has default props (if applicable)
- [ ] Component is accessible (ARIA labels, keyboard navigation)
- [ ] Component has error handling
- [ ] Component has loading states (if needed)
- [ ] Component has tests
- [ ] Component is documented
- [ ] Component follows naming conventions
- [ ] Component styles are scoped (CSS Modules)

---

## Examples

### Complete Form Component

```javascript
import React, { useState } from 'react';
import Input from '../components/common/Input';
import Button from '../components/common/Button';
import Alert from '../components/common/Alert';

const ContactForm = () => {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: '',
  });
  const [errors, setErrors] = useState({});
  const [loading, setLoading] = useState(false);
  const [success, setSuccess] = useState(false);

  const validate = () => {
    const newErrors = {};
    if (!formData.name) newErrors.name = 'Name is required';
    if (!formData.email) newErrors.email = 'Email is required';
    if (!formData.message) newErrors.message = 'Message is required';
    return newErrors;
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    const newErrors = validate();
    if (Object.keys(newErrors).length > 0) {
      setErrors(newErrors);
      return;
    }

    setLoading(true);
    try {
      await submitForm(formData);
      setSuccess(true);
      setFormData({ name: '', email: '', message: '' });
    } catch (error) {
      setErrors({ submit: error.message });
    } finally {
      setLoading(false);
    }
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData({ ...formData, [name]: value });
    if (errors[name]) {
      setErrors({ ...errors, [name]: '' });
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      {success && (
        <Alert type="success" message="Message sent successfully!" />
      )}
      {errors.submit && (
        <Alert type="error" message={errors.submit} />
      )}
      
      <Input
        name="name"
        label="Name"
        value={formData.name}
        onChange={handleChange}
        error={errors.name}
        required
      />
      
      <Input
        name="email"
        label="Email"
        type="email"
        value={formData.email}
        onChange={handleChange}
        error={errors.email}
        required
      />
      
      <Textarea
        name="message"
        label="Message"
        value={formData.message}
        onChange={handleChange}
        error={errors.message}
        required
      />
      
      <Button type="submit" loading={loading}>
        Send Message
      </Button>
    </form>
  );
};

export default ContactForm;
```

---

For more information, see:
- [Development Guide](./DEVELOPMENT_GUIDE.md)
- [Architecture Documentation](./ARCHITECTURE.md)
- [API Integration Guide](./API_INTEGRATION.md)
