# Frontend Development Guide

## Table of Contents

- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Coding Standards](#coding-standards)
- [Component Development](#component-development)
- [State Management](#state-management)
- [Styling Guidelines](#styling-guidelines)
- [Testing Guidelines](#testing-guidelines)
- [Git Workflow](#git-workflow)
- [Debugging](#debugging)
- [Common Tasks](#common-tasks)
- [Troubleshooting](#troubleshooting)

---

## Getting Started

### Prerequisites

- Node.js (v14.x or higher)
- npm or yarn
- Git
- Code editor (VS Code recommended)

### Initial Setup

1. **Clone and install:**
```bash
git clone <repository-url>
cd Software_Design_Anylysis_Lab/Frontend
npm install
```

2. **Configure environment:**
```bash
cp .env.example .env
# Edit .env with your configuration
```

3. **Start development server:**
```bash
npm start
```

---

## Development Workflow

### Daily Workflow

1. **Pull latest changes:**
```bash
git pull origin main
```

2. **Create feature branch:**
```bash
git checkout -b feature/your-feature-name
```

3. **Make changes:**
   - Write code
   - Write tests
   - Update documentation

4. **Test your changes:**
```bash
npm test
npm run lint
```

5. **Commit and push:**
```bash
git add .
git commit -m "feat: add new feature"
git push origin feature/your-feature-name
```

### Hot Reload

The development server automatically reloads when you save files. Changes to:
- `.js`/`.jsx` files
- `.css` files
- Component files

Will trigger a hot reload without losing application state.

---

## Coding Standards

### JavaScript/React Style Guide

#### Naming Conventions

- **Components**: PascalCase
```javascript
const UserCard = () => { };
const NavigationMenu = () => { };
```

- **Functions/Variables**: camelCase
```javascript
const getUserData = () => { };
const userName = 'john';
```

- **Constants**: UPPER_SNAKE_CASE
```javascript
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRIES = 3;
```

- **Files**: Match component/function name
```javascript
UserCard.js
getUserData.js
```

#### Code Formatting

- Use 2 spaces for indentation
- Use single quotes for strings
- Always use semicolons
- Maximum line length: 100 characters
- Use trailing commas

**Example:**
```javascript
const user = {
  id: 1,
  name: 'John Doe',
  email: 'john@example.com',
};
```

#### Component Structure

```javascript
// 1. Imports
import React, { useState, useEffect } from 'react';
import PropTypes from 'prop-types';
import './Component.css';

// 2. Component
const Component = ({ prop1, prop2, onAction }) => {
  // 3. Hooks (state, effects, etc.)
  const [state, setState] = useState(null);

  useEffect(() => {
    // Effect logic
  }, []);

  // 4. Event handlers
  const handleClick = () => {
    onAction();
  };

  // 5. Render helpers
  const renderContent = () => {
    return <div>Content</div>;
  };

  // 6. Return JSX
  return (
    <div className="component">
      {renderContent()}
    </div>
  );
};

// 7. PropTypes
Component.propTypes = {
  prop1: PropTypes.string.isRequired,
  prop2: PropTypes.number,
  onAction: PropTypes.func,
};

// 8. Default props
Component.defaultProps = {
  prop2: 0,
  onAction: () => {},
};

// 9. Export
export default Component;
```

#### Best Practices

1. **Use functional components and hooks**
2. **Keep components small and focused**
3. **Extract reusable logic to custom hooks**
4. **Use meaningful variable names**
5. **Avoid deep nesting (max 3 levels)**
6. **Use early returns**
7. **Destructure props**
8. **Use optional chaining**

**Good:**
```javascript
const UserCard = ({ user, onEdit }) => {
  if (!user) return null;

  const { name, email } = user;

  return (
    <div>
      <h3>{name}</h3>
      <p>{email}</p>
      <button onClick={onEdit}>Edit</button>
    </div>
  );
};
```

**Bad:**
```javascript
const UserCard = (props) => {
  return (
    <div>
      <h3>{props.user && props.user.name}</h3>
      <p>{props.user && props.user.email}</p>
      <button onClick={props.onEdit}>Edit</button>
    </div>
  );
};
```

---

## Component Development

### Creating a New Component

1. **Create component file:**
```javascript
// src/components/common/Button/Button.js
import React from 'react';
import PropTypes from 'prop-types';
import './Button.css';

const Button = ({ children, variant, onClick, disabled, ...props }) => {
  return (
    <button
      className={`btn btn-${variant} ${disabled ? 'btn-disabled' : ''}`}
      onClick={onClick}
      disabled={disabled}
      {...props}
    >
      {children}
    </button>
  );
};

Button.propTypes = {
  children: PropTypes.node.isRequired,
  variant: PropTypes.oneOf(['primary', 'secondary', 'danger']),
  onClick: PropTypes.func,
  disabled: PropTypes.bool,
};

Button.defaultProps = {
  variant: 'primary',
  disabled: false,
};

export default Button;
```

2. **Create styles:**
```css
/* src/components/common/Button/Button.css */
.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: all 0.3s ease;
}

.btn-primary {
  background-color: #007bff;
  color: white;
}

.btn-primary:hover {
  background-color: #0056b3;
}

.btn-disabled {
  opacity: 0.6;
  cursor: not-allowed;
}
```

3. **Create index file (optional):**
```javascript
// src/components/common/Button/index.js
export { default } from './Button';
```

4. **Export from parent index:**
```javascript
// src/components/common/index.js
export { default as Button } from './Button';
export { default as Input } from './Input';
```

### Component Patterns

#### Controlled Components

```javascript
const ControlledInput = ({ value, onChange }) => {
  return (
    <input
      type="text"
      value={value}
      onChange={(e) => onChange(e.target.value)}
    />
  );
};
```

#### Uncontrolled Components

```javascript
const UncontrolledInput = ({ defaultValue, onBlur }) => {
  const inputRef = useRef(null);

  const handleBlur = () => {
    onBlur(inputRef.current.value);
  };

  return (
    <input
      ref={inputRef}
      type="text"
      defaultValue={defaultValue}
      onBlur={handleBlur}
    />
  );
};
```

#### Compound Components

```javascript
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

---

## State Management

### Local State

Use `useState` for component-specific state:

```javascript
const Counter = () => {
  const [count, setCount] = useState(0);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);

  return (
    <div>
      <button onClick={decrement}>-</button>
      <span>{count}</span>
      <button onClick={increment}>+</button>
    </div>
  );
};
```

### Context API

For global state:

```javascript
// Create context
const UserContext = createContext();

// Provider
export const UserProvider = ({ children }) => {
  const [user, setUser] = useState(null);

  const login = async (credentials) => {
    const userData = await authService.login(credentials);
    setUser(userData);
  };

  const logout = () => {
    authService.logout();
    setUser(null);
  };

  return (
    <UserContext.Provider value={{ user, login, logout }}>
      {children}
    </UserContext.Provider>
  );
};

// Custom hook
export const useUser = () => {
  const context = useContext(UserContext);
  if (!context) {
    throw new Error('useUser must be used within UserProvider');
  }
  return context;
};
```

### Custom Hooks

Extract reusable state logic:

```javascript
// src/hooks/useLocalStorage.js
const useLocalStorage = (key, initialValue) => {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });

  const setValue = (value) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
};

// Usage
const [theme, setTheme] = useLocalStorage('theme', 'light');
```

---

## Styling Guidelines

### CSS Modules

Use CSS Modules for component-scoped styles:

```javascript
// Component.js
import styles from './Component.module.css';

<div className={styles.container}>
  <h1 className={styles.title}>Title</h1>
</div>
```

```css
/* Component.module.css */
.container {
  padding: 20px;
}

.title {
  font-size: 24px;
  color: var(--primary-color);
}
```

### BEM Naming (Alternative)

```css
.block {}
.block__element {}
.block--modifier {}
.block__element--modifier {}
```

### CSS Variables

Use CSS variables for theming:

```css
/* styles/variables.css */
:root {
  --primary-color: #007bff;
  --secondary-color: #6c757d;
  --spacing-sm: 8px;
  --spacing-md: 16px;
  --spacing-lg: 24px;
  --border-radius: 4px;
}
```

### Responsive Design

Use mobile-first approach:

```css
.container {
  padding: 16px;
}

@media (min-width: 768px) {
  .container {
    padding: 24px;
  }
}

@media (min-width: 1024px) {
  .container {
    padding: 32px;
  }
}
```

---

## Testing Guidelines

### Component Testing

```javascript
// Button.test.js
import { render, screen, fireEvent } from '@testing-library/react';
import Button from './Button';

describe('Button', () => {
  it('renders with text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('calls onClick when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click me</Button>);
    expect(screen.getByText('Click me')).toBeDisabled();
  });
});
```

### Hook Testing

```javascript
// useCounter.test.js
import { renderHook, act } from '@testing-library/react';
import { useCounter } from './useCounter';

describe('useCounter', () => {
  it('initializes with default value', () => {
    const { result } = renderHook(() => useCounter());
    expect(result.current.count).toBe(0);
  });

  it('increments count', () => {
    const { result } = renderHook(() => useCounter());
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });
});
```

### Running Tests

```bash
# Run all tests
npm test

# Run in watch mode
npm test -- --watch

# Run with coverage
npm test -- --coverage

# Run specific test file
npm test -- Button.test.js
```

---

## Git Workflow

### Branch Naming

- `feature/` - New features
- `bugfix/` - Bug fixes
- `hotfix/` - Critical fixes
- `refactor/` - Code refactoring
- `docs/` - Documentation

**Examples:**
```
feature/user-dashboard
bugfix/login-error
hotfix/security-patch
refactor/component-structure
```

### Commit Messages

Follow Conventional Commits:

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Code style
- `refactor`: Refactoring
- `test`: Tests
- `chore`: Maintenance

**Examples:**
```
feat(auth): add login functionality

fix(button): resolve disabled state styling

docs(readme): update installation instructions

refactor(components): extract common logic to hooks
```

---

## Debugging

### React DevTools

Install React DevTools browser extension:
- Chrome: [React Developer Tools](https://chrome.google.com/webstore)
- Firefox: [React Developer Tools](https://addons.mozilla.org)

### Console Debugging

```javascript
// Use console.log strategically
console.log('Component rendered', { props, state });

// Use console.group for organized logs
console.group('User Action');
console.log('Action:', action);
console.log('Payload:', payload);
console.groupEnd();
```

### Breakpoints

Use browser DevTools or VS Code debugger:

```javascript
// Add debugger statement
const handleClick = () => {
  debugger; // Execution will pause here
  // Your code
};
```

### VS Code Debugging

Create `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "Launch Chrome",
      "url": "http://localhost:3000",
      "webRoot": "${workspaceFolder}/src"
    }
  ]
}
```

---

## Common Tasks

### Adding a New Page

1. Create page component:
```javascript
// src/pages/About/About.js
import React from 'react';
import './About.css';

const About = () => {
  return (
    <div className="about-page">
      <h1>About</h1>
      <p>About content...</p>
    </div>
  );
};

export default About;
```

2. Add route:
```javascript
// src/routes/index.js
import About from '../pages/About/About';

<Route path="/about" component={About} />
```

### Adding Form Validation

```javascript
import { useState } from 'react';

const useForm = (initialValues, validate) => {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues({ ...values, [name]: value });
    
    if (touched[name]) {
      setErrors({ ...errors, [name]: validate[name](value) });
    }
  };

  const handleBlur = (e) => {
    const { name } = e.target;
    setTouched({ ...touched, [name]: true });
    setErrors({ ...errors, [name]: validate[name](values[name]) });
  };

  return { values, errors, handleChange, handleBlur };
};
```

### Handling API Calls

```javascript
const useApi = (apiCall) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        setError(null);
        const result = await apiCall();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, []);

  return { data, loading, error };
};
```

---

## Troubleshooting

### Common Issues

**Port already in use:**
```bash
# Kill process on port 3000
npx kill-port 3000
# Or use different port
PORT=3001 npm start
```

**Module not found:**
```bash
# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm install
```

**Build fails:**
- Check Node.js version
- Clear build cache: `rm -rf build`
- Check for syntax errors

**Hot reload not working:**
- Restart development server
- Clear browser cache
- Check file watcher limits

**Styling issues:**
- Check CSS import paths
- Verify CSS Modules syntax
- Check for conflicting styles

### Performance Issues

**Slow renders:**
- Use React.memo for expensive components
- Implement useMemo for expensive calculations
- Use useCallback for event handlers

**Large bundle size:**
- Implement code splitting
- Lazy load routes
- Remove unused dependencies

---

## Best Practices Summary

1. ✅ Keep components small and focused
2. ✅ Use functional components and hooks
3. ✅ Extract reusable logic to custom hooks
4. ✅ Write tests for components
5. ✅ Use meaningful variable names
6. ✅ Follow consistent code style
7. ✅ Document complex logic
8. ✅ Handle errors gracefully
9. ✅ Optimize performance
10. ✅ Ensure accessibility

---

## Additional Resources

- [React Documentation](https://react.dev/)
- [React Hooks Documentation](https://react.dev/reference/react)
- [Testing Library](https://testing-library.com/)
- [ESLint Rules](https://eslint.org/docs/rules/)
- [Prettier Configuration](https://prettier.io/docs/en/configuration.html)

---

## Code Review Checklist

Before submitting a PR:

- [ ] Code follows style guidelines
- [ ] All tests pass
- [ ] New features have tests
- [ ] No console.log statements
- [ ] No commented-out code
- [ ] Components are accessible
- [ ] Performance considerations addressed
- [ ] Documentation updated
- [ ] No sensitive data committed
