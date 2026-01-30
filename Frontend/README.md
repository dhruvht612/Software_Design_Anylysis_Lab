# Frontend Application Documentation

## Overview

This is a modern frontend application built with React (or your chosen framework) that provides a user interface for the Software Design & Analysis Lab project. It communicates with the Node.js/Express.js backend API.

## Table of Contents

- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Configuration](#configuration)
- [Available Scripts](#available-scripts)
- [Development](#development)
- [Building for Production](#building-for-production)
- [Testing](#testing)
- [Deployment](#deployment)
- [API Integration](#api-integration)

## Getting Started

### Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (v14.x or higher)
- **npm** (v6.x or higher) or **yarn**
- **Git**

### Quick Start

1. Clone the repository:
```bash
git clone <repository-url>
cd Software_Design_Anylysis_Lab/Frontend
```

2. Install dependencies:
```bash
npm install
```

3. Set up environment variables:
```bash
cp .env.example .env
# Edit .env with your configuration
```

4. Start the development server:
```bash
npm start
```

The application will open in your browser at `http://localhost:3000` (or the port specified in your configuration).

## Project Structure

```
Frontend/
├── public/                 # Static files
│   ├── index.html
│   ├── favicon.ico
│   └── assets/
│
├── src/
│   ├── components/         # Reusable UI components
│   │   ├── common/        # Common components (Button, Input, etc.)
│   │   ├── layout/        # Layout components (Header, Footer, etc.)
│   │   └── features/      # Feature-specific components
│   │
│   ├── pages/             # Page components
│   │   ├── Home/
│   │   ├── Login/
│   │   ├── Register/
│   │   └── Dashboard/
│   │
│   ├── services/          # API services
│   │   ├── api.js         # API client configuration
│   │   ├── authService.js
│   │   └── userService.js
│   │
│   ├── hooks/             # Custom React hooks
│   │   ├── useAuth.js
│   │   └── useApi.js
│   │
│   ├── context/           # React Context providers
│   │   ├── AuthContext.js
│   │   └── AppContext.js
│   │
│   ├── utils/             # Utility functions
│   │   ├── helpers.js
│   │   ├── validators.js
│   │   └── constants.js
│   │
│   ├── styles/            # Global styles
│   │   ├── index.css
│   │   └── variables.css
│   │
│   ├── assets/            # Images, fonts, etc.
│   │   ├── images/
│   │   └── icons/
│   │
│   ├── routes/            # Route configuration
│   │   └── index.js
│   │
│   ├── App.js             # Main App component
│   ├── index.js           # Application entry point
│   └── setupTests.js      # Test setup
│
├── tests/                 # Test files
│   ├── components/
│   ├── pages/
│   └── utils/
│
├── .env.example           # Environment variables template
├── .gitignore
├── package.json
└── README.md
```

## Installation

### Step 1: Install Dependencies

```bash
npm install
```

or with yarn:

```bash
yarn install
```

### Step 2: Environment Setup

Create a `.env` file in the root directory:

```env
# API Configuration
REACT_APP_API_URL=http://localhost:3000/api/v1
REACT_APP_API_TIMEOUT=10000

# Application Configuration
REACT_APP_NAME=Software Design Lab
REACT_APP_VERSION=1.0.0

# Feature Flags
REACT_APP_ENABLE_ANALYTICS=false
REACT_APP_ENABLE_DEBUG=true

# OAuth (if applicable)
REACT_APP_GOOGLE_CLIENT_ID=your_google_client_id
REACT_APP_FACEBOOK_APP_ID=your_facebook_app_id
```

### Step 3: Start Development Server

```bash
npm start
```

The application will automatically open in your default browser. The page will reload when you make changes to the code.

## Configuration

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `REACT_APP_API_URL` | Backend API base URL | `http://localhost:3000/api/v1` |
| `REACT_APP_API_TIMEOUT` | API request timeout (ms) | `10000` |
| `REACT_APP_NAME` | Application name | - |
| `REACT_APP_ENABLE_DEBUG` | Enable debug mode | `false` |

**Note:** All environment variables must be prefixed with `REACT_APP_` to be accessible in the React application.

### API Configuration

The API client is configured in `src/services/api.js`. Update the base URL and default headers as needed.

## Available Scripts

### Development

```bash
# Start development server
npm start

# Start with custom port
PORT=3001 npm start
```

### Building

```bash
# Build for production
npm run build

# Analyze bundle size
npm run analyze
```

### Testing

```bash
# Run tests
npm test

# Run tests in watch mode
npm test -- --watch

# Run tests with coverage
npm test -- --coverage

# Run tests once
npm test -- --watchAll=false
```

### Code Quality

```bash
# Run ESLint
npm run lint

# Fix ESLint issues
npm run lint:fix

# Format code with Prettier
npm run format

# Type checking (if using TypeScript)
npm run type-check
```

## Development

### Adding New Components

1. Create component file:
```javascript
// src/components/common/Button.js
import React from 'react';
import './Button.css';

const Button = ({ children, onClick, variant = 'primary', ...props }) => {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      {...props}
    >
      {children}
    </button>
  );
};

export default Button;
```

2. Create component styles:
```css
/* src/components/common/Button.css */
.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.btn-primary {
  background-color: #007bff;
  color: white;
}
```

3. Export from index (optional):
```javascript
// src/components/common/index.js
export { default as Button } from './Button';
export { default as Input } from './Input';
```

### Adding New Pages

1. Create page component:
```javascript
// src/pages/About/About.js
import React from 'react';
import './About.css';

const About = () => {
  return (
    <div className="about-page">
      <h1>About Us</h1>
      <p>About page content...</p>
    </div>
  );
};

export default About;
```

2. Add route:
```javascript
// src/routes/index.js
import { Route } from 'react-router-dom';
import About from '../pages/About/About';

<Route path="/about" component={About} />
```

### State Management

#### Using Context API

```javascript
// src/context/AuthContext.js
import React, { createContext, useState, useContext } from 'react';

const AuthContext = createContext();

export const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);
  const [isAuthenticated, setIsAuthenticated] = useState(false);

  const login = async (credentials) => {
    // Login logic
  };

  const logout = () => {
    // Logout logic
  };

  return (
    <AuthContext.Provider value={{ user, isAuthenticated, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => useContext(AuthContext);
```

#### Using Custom Hooks

```javascript
// src/hooks/useAuth.js
import { useState, useEffect } from 'react';
import { useAuth as useAuthContext } from '../context/AuthContext';

export const useAuth = () => {
  const { user, isAuthenticated, login, logout } = useAuthContext();
  
  // Additional logic
  
  return {
    user,
    isAuthenticated,
    login,
    logout,
  };
};
```

## Building for Production

### Create Production Build

```bash
npm run build
```

This creates an optimized production build in the `build/` directory.

### Build Output

The build folder contains:
- Optimized and minified JavaScript
- Optimized CSS
- Static assets
- `index.html` entry point

### Serving the Build

**Using serve:**
```bash
npm install -g serve
serve -s build
```

**Using Express:**
```javascript
const express = require('express');
const path = require('path');
const app = express();

app.use(express.static(path.join(__dirname, 'build')));

app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, 'build', 'index.html'));
});

app.listen(3000);
```

## Testing

### Writing Tests

Tests are written using Jest and React Testing Library:

```javascript
// src/components/common/Button.test.js
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import Button from './Button';

describe('Button', () => {
  it('renders correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('calls onClick when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

### Running Tests

```bash
# Run all tests
npm test

# Run with coverage
npm test -- --coverage

# Run specific test file
npm test -- Button.test.js
```

## Deployment

### Deployment Checklist

- [ ] Update environment variables for production
- [ ] Build the application (`npm run build`)
- [ ] Test the production build locally
- [ ] Configure CORS on backend
- [ ] Set up error tracking (Sentry, etc.)
- [ ] Configure analytics
- [ ] Set up CI/CD pipeline
- [ ] Configure CDN (if applicable)

### Deployment Options

**Netlify:**
```bash
npm install -g netlify-cli
netlify deploy --prod --dir=build
```

**Vercel:**
```bash
npm install -g vercel
vercel --prod
```

**AWS S3 + CloudFront:**
1. Upload build folder to S3 bucket
2. Configure CloudFront distribution
3. Set up custom domain

**Docker:**
```dockerfile
FROM node:14-alpine as build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## API Integration

### Making API Calls

```javascript
// src/services/api.js
import axios from 'axios';

const api = axios.create({
  baseURL: process.env.REACT_APP_API_URL,
  timeout: process.env.REACT_APP_API_TIMEOUT || 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// Response interceptor
api.interceptors.response.use(
  (response) => response.data,
  (error) => {
    if (error.response?.status === 401) {
      // Handle unauthorized
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

### Using API Services

```javascript
// src/services/userService.js
import api from './api';

export const userService = {
  getUsers: async (params) => {
    const response = await api.get('/users', { params });
    return response.data;
  },

  getUserById: async (id) => {
    const response = await api.get(`/users/${id}`);
    return response.data;
  },

  createUser: async (userData) => {
    const response = await api.post('/users', userData);
    return response.data;
  },
};
```

For detailed API integration guide, see [API_INTEGRATION.md](./Documantation/API_INTEGRATION.md).

## Styling

### CSS Modules

```javascript
// Component.js
import styles from './Component.module.css';

const Component = () => {
  return <div className={styles.container}>Content</div>;
};
```

### Styled Components

```javascript
import styled from 'styled-components';

const Button = styled.button`
  background-color: #007bff;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
`;
```

### CSS-in-JS

```javascript
const styles = {
  container: {
    padding: '20px',
    backgroundColor: '#f5f5f5',
  },
};

<div style={styles.container}>Content</div>
```

## Performance Optimization

### Code Splitting

```javascript
import { lazy, Suspense } from 'react';

const LazyComponent = lazy(() => import('./LazyComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </Suspense>
  );
}
```

### Route-based Code Splitting

```javascript
import { lazy } from 'react';
import { Route } from 'react-router-dom';

const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));

<Route path="/" component={Home} />
<Route path="/about" component={About} />
```

### Memoization

```javascript
import { memo, useMemo, useCallback } from 'react';

const ExpensiveComponent = memo(({ data, onUpdate }) => {
  const processedData = useMemo(() => {
    return data.map(item => processItem(item));
  }, [data]);

  const handleClick = useCallback(() => {
    onUpdate();
  }, [onUpdate]);

  return <div>{/* Render */}</div>;
});
```

## Browser Support

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Edge (latest)

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
- Check Node.js version compatibility
- Clear build cache: `rm -rf build`
- Check for syntax errors in code

## Contributing

1. Create a feature branch
2. Make your changes
3. Write tests
4. Ensure all tests pass
5. Submit a pull request

## License

[Your License Here]

## Support

For issues and questions:
- Check [Development Guide](./Documantation/DEVELOPMENT_GUIDE.md)
- Review [Architecture Documentation](./Documantation/ARCHITECTURE.md)
- Open an issue on GitHub
