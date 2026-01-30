# API Integration Guide

## Overview

This guide explains how to integrate the frontend application with the backend API. It covers authentication, making API calls, error handling, and best practices.

## Table of Contents

- [API Client Setup](#api-client-setup)
- [Authentication](#authentication)
- [Making API Calls](#making-api-calls)
- [Error Handling](#error-handling)
- [Request/Response Interceptors](#requestresponse-interceptors)
- [Service Layer](#service-layer)
- [Examples](#examples)
- [Best Practices](#best-practices)

---

## API Client Setup

### Basic Configuration

Create an API client using Axios (or fetch):

```javascript
// src/services/api.js
import axios from 'axios';

const api = axios.create({
  baseURL: process.env.REACT_APP_API_URL || 'http://localhost:3000/api/v1',
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

export default api;
```

### Environment Configuration

Set the API URL in your `.env` file:

```env
REACT_APP_API_URL=http://localhost:3000/api/v1
```

---

## Authentication

### Storing Tokens

Store JWT tokens securely:

```javascript
// src/utils/auth.js
const TOKEN_KEY = 'auth_token';

export const auth = {
  getToken: () => {
    return localStorage.getItem(TOKEN_KEY);
  },

  setToken: (token) => {
    localStorage.setItem(TOKEN_KEY, token);
  },

  removeToken: () => {
    localStorage.removeItem(TOKEN_KEY);
  },

  isAuthenticated: () => {
    return !!auth.getToken();
  },
};
```

### Adding Token to Requests

Use an interceptor to automatically add the token:

```javascript
// src/services/api.js
import auth from '../utils/auth';

api.interceptors.request.use(
  (config) => {
    const token = auth.getToken();
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);
```

### Login Flow

```javascript
// src/services/authService.js
import api from './api';
import auth from '../utils/auth';

export const authService = {
  login: async (email, password) => {
    try {
      const response = await api.post('/auth/login', {
        email,
        password,
      });

      if (response.data.success && response.data.data.token) {
        auth.setToken(response.data.data.token);
        return response.data.data;
      }
      throw new Error('Login failed');
    } catch (error) {
      throw error.response?.data?.error || error;
    }
  },

  register: async (userData) => {
    try {
      const response = await api.post('/auth/register', userData);
      
      if (response.data.success && response.data.data.token) {
        auth.setToken(response.data.data.token);
        return response.data.data;
      }
      throw new Error('Registration failed');
    } catch (error) {
      throw error.response?.data?.error || error;
    }
  },

  logout: async () => {
    try {
      await api.post('/auth/logout');
    } finally {
      auth.removeToken();
    }
  },

  getCurrentUser: async () => {
    const response = await api.get('/auth/me');
    return response.data.data.user;
  },
};
```

---

## Making API Calls

### GET Request

```javascript
// Fetch all users
const getUsers = async (params = {}) => {
  try {
    const response = await api.get('/users', { params });
    return response.data;
  } catch (error) {
    throw error.response?.data || error;
  }
};

// Usage
const users = await getUsers({ page: 1, limit: 10 });
```

### POST Request

```javascript
// Create a new resource
const createResource = async (resourceData) => {
  try {
    const response = await api.post('/resources', resourceData);
    return response.data;
  } catch (error) {
    throw error.response?.data || error;
  }
};

// Usage
const newResource = await createResource({
  title: 'New Resource',
  description: 'Description',
});
```

### PUT Request

```javascript
// Update a resource
const updateResource = async (id, resourceData) => {
  try {
    const response = await api.put(`/resources/${id}`, resourceData);
    return response.data;
  } catch (error) {
    throw error.response?.data || error;
  }
};
```

### DELETE Request

```javascript
// Delete a resource
const deleteResource = async (id) => {
  try {
    const response = await api.delete(`/resources/${id}`);
    return response.data;
  } catch (error) {
    throw error.response?.data || error;
  }
};
```

### Uploading Files

```javascript
// Upload file with FormData
const uploadFile = async (file) => {
  const formData = new FormData();
  formData.append('file', file);

  const response = await api.post('/upload', formData, {
    headers: {
      'Content-Type': 'multipart/form-data',
    },
    onUploadProgress: (progressEvent) => {
      const percentCompleted = Math.round(
        (progressEvent.loaded * 100) / progressEvent.total
      );
      console.log(`Upload progress: ${percentCompleted}%`);
    },
  });

  return response.data;
};
```

---

## Error Handling

### Response Interceptor

Handle errors globally:

```javascript
// src/services/api.js
api.interceptors.response.use(
  (response) => {
    // Return data directly if using standard response format
    return response.data;
  },
  (error) => {
    // Handle different error scenarios
    if (error.response) {
      // Server responded with error status
      const { status, data } = error.response;

      switch (status) {
        case 401:
          // Unauthorized - redirect to login
          auth.removeToken();
          window.location.href = '/login';
          break;
        case 403:
          // Forbidden
          console.error('Access forbidden');
          break;
        case 404:
          // Not found
          console.error('Resource not found');
          break;
        case 500:
          // Server error
          console.error('Server error');
          break;
        default:
          console.error('API Error:', data?.error?.message || 'Unknown error');
      }

      return Promise.reject({
        message: data?.error?.message || 'An error occurred',
        status,
        data: data?.error,
      });
    } else if (error.request) {
      // Request made but no response
      return Promise.reject({
        message: 'No response from server',
        status: 0,
      });
    } else {
      // Error setting up request
      return Promise.reject({
        message: error.message || 'Request failed',
      });
    }
  }
);
```

### Error Handling in Components

```javascript
// src/components/UserList.js
import { useState, useEffect } from 'react';
import { userService } from '../services/userService';

const UserList = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        setLoading(true);
        setError(null);
        const response = await userService.getUsers();
        setUsers(response.data.users);
      } catch (err) {
        setError(err.message || 'Failed to fetch users');
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      {users.map(user => (
        <div key={user.id}>{user.name}</div>
      ))}
    </div>
  );
};
```

---

## Request/Response Interceptors

### Request Interceptor

Add common headers, modify requests:

```javascript
api.interceptors.request.use(
  (config) => {
    // Add timestamp
    config.metadata = { startTime: new Date() };

    // Add token
    const token = auth.getToken();
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }

    // Add request ID for tracking
    config.headers['X-Request-ID'] = generateRequestId();

    return config;
  },
  (error) => Promise.reject(error)
);
```

### Response Interceptor

Transform responses, handle errors:

```javascript
api.interceptors.response.use(
  (response) => {
    // Calculate request duration
    const duration = new Date() - response.config.metadata.startTime;
    console.log(`Request took ${duration}ms`);

    // Transform response if needed
    if (response.data.success) {
      return response.data.data;
    }
    return response.data;
  },
  (error) => {
    // Log error
    console.error('API Error:', error);

    // Handle specific errors
    if (error.response?.status === 401) {
      // Handle unauthorized
    }

    return Promise.reject(error);
  }
);
```

---

## Service Layer

### Organizing Services

Create service files for each resource:

```javascript
// src/services/userService.js
import api from './api';

export const userService = {
  // Get all users
  getUsers: async (params = {}) => {
    const response = await api.get('/users', { params });
    return response;
  },

  // Get user by ID
  getUserById: async (id) => {
    const response = await api.get(`/users/${id}`);
    return response;
  },

  // Create user
  createUser: async (userData) => {
    const response = await api.post('/users', userData);
    return response;
  },

  // Update user
  updateUser: async (id, userData) => {
    const response = await api.put(`/users/${id}`, userData);
    return response;
  },

  // Delete user
  deleteUser: async (id) => {
    const response = await api.delete(`/users/${id}`);
    return response;
  },
};
```

### Using Services in Components

```javascript
// src/pages/Users/Users.js
import { useState, useEffect } from 'react';
import { userService } from '../../services/userService';

const Users = () => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    const loadUsers = async () => {
      try {
        const data = await userService.getUsers();
        setUsers(data.users);
      } catch (error) {
        console.error('Failed to load users:', error);
      }
    };

    loadUsers();
  }, []);

  return (
    <div>
      {users.map(user => (
        <div key={user.id}>{user.name}</div>
      ))}
    </div>
  );
};
```

---

## Examples

### Complete Authentication Flow

```javascript
// src/hooks/useAuth.js
import { useState, useEffect, createContext, useContext } from 'react';
import { authService } from '../services/authService';
import auth from '../utils/auth';

const AuthContext = createContext();

export const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const initAuth = async () => {
      if (auth.isAuthenticated()) {
        try {
          const userData = await authService.getCurrentUser();
          setUser(userData);
        } catch (error) {
          auth.removeToken();
        }
      }
      setLoading(false);
    };

    initAuth();
  }, []);

  const login = async (email, password) => {
    const data = await authService.login(email, password);
    setUser(data.user);
    return data;
  };

  const logout = async () => {
    await authService.logout();
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, login, logout, loading }}>
      {children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => useContext(AuthContext);
```

### CRUD Operations Example

```javascript
// src/pages/Resources/Resources.js
import { useState, useEffect } from 'react';
import { resourceService } from '../../services/resourceService';

const Resources = () => {
  const [resources, setResources] = useState([]);
  const [loading, setLoading] = useState(false);

  // Fetch resources
  const fetchResources = async () => {
    setLoading(true);
    try {
      const data = await resourceService.getResources();
      setResources(data.resources);
    } catch (error) {
      console.error('Error fetching resources:', error);
    } finally {
      setLoading(false);
    }
  };

  // Create resource
  const handleCreate = async (resourceData) => {
    try {
      const newResource = await resourceService.createResource(resourceData);
      setResources([...resources, newResource.resource]);
    } catch (error) {
      console.error('Error creating resource:', error);
    }
  };

  // Update resource
  const handleUpdate = async (id, resourceData) => {
    try {
      const updated = await resourceService.updateResource(id, resourceData);
      setResources(
        resources.map(r => r.id === id ? updated.resource : r)
      );
    } catch (error) {
      console.error('Error updating resource:', error);
    }
  };

  // Delete resource
  const handleDelete = async (id) => {
    try {
      await resourceService.deleteResource(id);
      setResources(resources.filter(r => r.id !== id));
    } catch (error) {
      console.error('Error deleting resource:', error);
    }
  };

  useEffect(() => {
    fetchResources();
  }, []);

  return (
    <div>
      {/* Render resources */}
    </div>
  );
};
```

---

## Best Practices

### 1. Centralize API Configuration

Keep all API configuration in one place:

```javascript
// src/config/api.js
export const API_CONFIG = {
  baseURL: process.env.REACT_APP_API_URL,
  timeout: 10000,
  retries: 3,
  retryDelay: 1000,
};
```

### 2. Use TypeScript (if applicable)

```typescript
// src/types/api.ts
export interface ApiResponse<T> {
  success: boolean;
  data: T;
  message?: string;
  error?: {
    message: string;
    code: string;
  };
}

export interface User {
  id: string;
  username: string;
  email: string;
}
```

### 3. Implement Request Cancellation

```javascript
import { useEffect, useRef } from 'react';
import axios from 'axios';

const useApiCall = () => {
  const cancelTokenSource = useRef(null);

  useEffect(() => {
    cancelTokenSource.current = axios.CancelToken.source();
    return () => {
      if (cancelTokenSource.current) {
        cancelTokenSource.current.cancel('Component unmounted');
      }
    };
  }, []);

  const makeRequest = async (url) => {
    try {
      const response = await api.get(url, {
        cancelToken: cancelTokenSource.current.token,
      });
      return response;
    } catch (error) {
      if (axios.isCancel(error)) {
        console.log('Request cancelled');
      } else {
        throw error;
      }
    }
  };

  return { makeRequest };
};
```

### 4. Implement Retry Logic

```javascript
const retryRequest = async (fn, retries = 3) => {
  try {
    return await fn();
  } catch (error) {
    if (retries > 0 && error.response?.status >= 500) {
      await new Promise(resolve => setTimeout(resolve, 1000));
      return retryRequest(fn, retries - 1);
    }
    throw error;
  }
};
```

### 5. Cache Responses

```javascript
// src/utils/cache.js
const cache = new Map();

export const cachedRequest = async (key, requestFn, ttl = 5 * 60 * 1000) => {
  const cached = cache.get(key);
  if (cached && Date.now() - cached.timestamp < ttl) {
    return cached.data;
  }

  const data = await requestFn();
  cache.set(key, { data, timestamp: Date.now() });
  return data;
};
```

### 6. Handle Loading States

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
        setError(err);
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

## Testing API Calls

### Mocking API in Tests

```javascript
// src/services/__mocks__/api.js
export default {
  get: jest.fn(() => Promise.resolve({ data: {} })),
  post: jest.fn(() => Promise.resolve({ data: {} })),
  put: jest.fn(() => Promise.resolve({ data: {} })),
  delete: jest.fn(() => Promise.resolve({ data: {} })),
};
```

### Testing Service Functions

```javascript
// src/services/userService.test.js
import { userService } from './userService';
import api from './api';

jest.mock('./api');

describe('userService', () => {
  it('should fetch users', async () => {
    const mockUsers = { users: [{ id: 1, name: 'John' }] };
    api.get.mockResolvedValue({ data: mockUsers });

    const result = await userService.getUsers();

    expect(api.get).toHaveBeenCalledWith('/users', { params: {} });
    expect(result).toEqual(mockUsers);
  });
});
```

---

## Summary

- Use a centralized API client with interceptors
- Handle authentication tokens automatically
- Implement proper error handling
- Organize API calls in service layer
- Use custom hooks for reusable API logic
- Test API integrations with mocks
- Implement caching and retry logic where appropriate

For more information, refer to:
- [Backend API Documentation](../Backend/Documantation/API_DOCUMENTATION.md)
- [Development Guide](./DEVELOPMENT_GUIDE.md)
