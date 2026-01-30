# Development Guide

## Table of Contents

- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Coding Standards](#coding-standards)
- [Git Workflow](#git-workflow)
- [Testing Guidelines](#testing-guidelines)
- [Debugging](#debugging)
- [Common Tasks](#common-tasks)
- [Troubleshooting](#troubleshooting)

---

## Getting Started

### Prerequisites

Ensure you have the following installed:

- Node.js (v14.x or higher)
- npm or yarn
- Git
- Code editor (VS Code recommended)
- MongoDB/PostgreSQL (depending on your database choice)

### Initial Setup

1. **Clone the repository:**
```bash
git clone <repository-url>
cd Software_Design_Anylysis_Lab/Backend
```

2. **Install dependencies:**
```bash
npm install
```

3. **Set up environment variables:**
```bash
cp .env.example .env
# Edit .env with your local configuration
```

4. **Start the development server:**
```bash
npm run dev
```

---

## Development Workflow

### Daily Workflow

1. **Pull latest changes:**
```bash
git pull origin main
```

2. **Create a feature branch:**
```bash
git checkout -b feature/your-feature-name
```

3. **Make your changes:**
   - Write code
   - Write tests
   - Update documentation

4. **Test your changes:**
```bash
npm test
npm run lint
```

5. **Commit your changes:**
```bash
git add .
git commit -m "feat: add new feature"
```

6. **Push and create pull request:**
```bash
git push origin feature/your-feature-name
```

### Development Server

The development server runs with hot reload using Nodemon:

```bash
npm run dev
```

The server will automatically restart when you make changes to:
- `.js` files
- `.json` files
- Configuration files

---

## Coding Standards

### JavaScript Style Guide

#### Naming Conventions

- **Variables and Functions**: Use camelCase
```javascript
const userName = 'john';
function getUserData() { }
```

- **Constants**: Use UPPER_SNAKE_CASE
```javascript
const MAX_RETRIES = 3;
const API_BASE_URL = 'https://api.example.com';
```

- **Classes**: Use PascalCase
```javascript
class UserController { }
```

- **Files**: Use camelCase for JavaScript files
```javascript
userController.js
authService.js
```

#### Code Formatting

- Use 2 spaces for indentation
- Use single quotes for strings
- Always use semicolons
- Maximum line length: 100 characters
- Use trailing commas in objects and arrays

**Example:**
```javascript
const user = {
  id: 1,
  name: 'John Doe',
  email: 'john@example.com',
};
```

#### Async/Await

Always use async/await instead of callbacks or promises:

**Good:**
```javascript
async function getUser(id) {
  try {
    const user = await User.findById(id);
    return user;
  } catch (error) {
    throw error;
  }
}
```

**Bad:**
```javascript
function getUser(id, callback) {
  User.findById(id, (err, user) => {
    if (err) return callback(err);
    callback(null, user);
  });
}
```

#### Error Handling

Always handle errors properly:

```javascript
async function createUser(userData) {
  try {
    const user = await User.create(userData);
    return user;
  } catch (error) {
    if (error.name === 'ValidationError') {
      throw new Error('Invalid user data');
    }
    throw error;
  }
}
```

#### Comments

- Write self-documenting code
- Add comments for complex logic
- Use JSDoc for function documentation

**Example:**
```javascript
/**
 * Authenticates a user and returns a JWT token
 * @param {string} email - User's email address
 * @param {string} password - User's password
 * @returns {Promise<Object>} User object and JWT token
 * @throws {Error} If authentication fails
 */
async function login(email, password) {
  // Implementation
}
```

### File Organization

#### Controller Structure

```javascript
// userController.js
const userService = require('../services/userService');
const { validateUser } = require('../validators/userValidator');

/**
 * Get all users
 */
async function getUsers(req, res, next) {
  try {
    const { page = 1, limit = 10 } = req.query;
    const users = await userService.getAllUsers(page, limit);
    res.status(200).json({
      success: true,
      data: users,
    });
  } catch (error) {
    next(error);
  }
}

/**
 * Get user by ID
 */
async function getUserById(req, res, next) {
  try {
    const { id } = req.params;
    const user = await userService.getUserById(id);
    res.status(200).json({
      success: true,
      data: { user },
    });
  } catch (error) {
    next(error);
  }
}

module.exports = {
  getUsers,
  getUserById,
};
```

#### Service Structure

```javascript
// userService.js
const User = require('../models/User');
const { NotFoundError } = require('../utils/errors');

class UserService {
  async getAllUsers(page = 1, limit = 10) {
    const skip = (page - 1) * limit;
    const users = await User.find()
      .skip(skip)
      .limit(limit)
      .select('-password');
    
    const total = await User.countDocuments();
    
    return {
      users,
      pagination: {
        page,
        limit,
        total,
        pages: Math.ceil(total / limit),
      },
    };
  }

  async getUserById(id) {
    const user = await User.findById(id).select('-password');
    if (!user) {
      throw new NotFoundError('User not found');
    }
    return user;
  }
}

module.exports = new UserService();
```

### Best Practices

1. **Keep functions small and focused**
2. **Use meaningful variable names**
3. **Avoid deep nesting (max 3 levels)**
4. **Extract magic numbers to constants**
5. **Use early returns**
6. **Handle errors at the appropriate level**
7. **Don't commit sensitive data**
8. **Write tests for new features**

---

## Git Workflow

### Branch Naming

- `feature/` - New features
- `bugfix/` - Bug fixes
- `hotfix/` - Critical production fixes
- `refactor/` - Code refactoring
- `docs/` - Documentation updates

**Examples:**
```
feature/user-authentication
bugfix/login-error-handling
hotfix/security-patch
refactor/user-service
docs/api-documentation
```

### Commit Messages

Follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

**Format:**
```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

**Examples:**
```
feat(auth): add JWT token refresh endpoint

fix(user): resolve password validation issue

docs(api): update authentication documentation

refactor(services): improve error handling in user service
```

### Pull Request Process

1. **Create a descriptive PR title**
2. **Fill out the PR template**
3. **Link related issues**
4. **Request review from team members**
5. **Address review comments**
6. **Ensure all tests pass**
7. **Get approval before merging**

---

## Testing Guidelines

### Test Structure

Organize tests by type:

```
tests/
├── unit/           # Unit tests for individual functions
├── integration/    # Integration tests for API endpoints
└── e2e/            # End-to-end tests
```

### Writing Tests

**Unit Test Example:**
```javascript
// tests/unit/services/userService.test.js
const userService = require('../../../src/services/userService');
const User = require('../../../src/models/User');

describe('UserService', () => {
  describe('getUserById', () => {
    it('should return user when found', async () => {
      const mockUser = { id: '1', name: 'John' };
      User.findById = jest.fn().mockResolvedValue(mockUser);

      const result = await userService.getUserById('1');

      expect(result).toEqual(mockUser);
      expect(User.findById).toHaveBeenCalledWith('1');
    });

    it('should throw error when user not found', async () => {
      User.findById = jest.fn().mockResolvedValue(null);

      await expect(userService.getUserById('1'))
        .rejects
        .toThrow('User not found');
    });
  });
});
```

**Integration Test Example:**
```javascript
// tests/integration/routes/userRoutes.test.js
const request = require('supertest');
const app = require('../../../src/app');

describe('GET /api/v1/users', () => {
  it('should return list of users', async () => {
    const response = await request(app)
      .get('/api/v1/users')
      .set('Authorization', `Bearer ${token}`)
      .expect(200);

    expect(response.body.success).toBe(true);
    expect(response.body.data).toHaveProperty('users');
  });
});
```

### Test Coverage

Aim for at least 80% code coverage:

```bash
npm run test:coverage
```

### Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Run specific test file
npm test -- userService.test.js

# Run tests with coverage
npm run test:coverage
```

---

## Debugging

### VS Code Debugging

Create `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug Server",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "skipFiles": ["<node_internals>/**"]
    }
  ]
}
```

### Console Logging

Use appropriate log levels:

```javascript
const logger = require('./utils/logger');

logger.info('User created successfully');
logger.error('Database connection failed', error);
logger.debug('Processing request', { userId, action });
```

### Debugging Tips

1. Use `console.log` sparingly (remove before committing)
2. Use debugger breakpoints in VS Code
3. Check server logs for errors
4. Use Postman/Insomnia for API testing
5. Check database directly for data issues

---

## Common Tasks

### Adding a New Route

1. **Create controller:**
```javascript
// src/controllers/exampleController.js
async function getExample(req, res, next) {
  // Implementation
}

module.exports = { getExample };
```

2. **Create route file:**
```javascript
// src/routes/exampleRoutes.js
const express = require('express');
const router = express.Router();
const { getExample } = require('../controllers/exampleController');
const { authenticate } = require('../middleware/auth');

router.get('/', authenticate, getExample);

module.exports = router;
```

3. **Register route in app.js:**
```javascript
const exampleRoutes = require('./routes/exampleRoutes');
app.use('/api/v1/examples', exampleRoutes);
```

### Adding a New Model

```javascript
// src/models/Example.js
const mongoose = require('mongoose');

const exampleSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
  },
  description: String,
}, {
  timestamps: true,
});

module.exports = mongoose.model('Example', exampleSchema);
```

### Adding Validation

```javascript
// src/validators/exampleValidator.js
const { body, validationResult } = require('express-validator');

const validateExample = [
  body('name')
    .trim()
    .notEmpty()
    .withMessage('Name is required')
    .isLength({ min: 3, max: 50 })
    .withMessage('Name must be between 3 and 50 characters'),
  
  (req, res, next) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({
        success: false,
        error: { message: errors.array() },
      });
    }
    next();
  },
];

module.exports = { validateExample };
```

---

## Troubleshooting

### Common Issues

#### Port Already in Use

```bash
# Find process using port 3000
lsof -i :3000  # macOS/Linux
netstat -ano | findstr :3000  # Windows

# Kill the process or change PORT in .env
```

#### Database Connection Issues

- Check database is running
- Verify connection string in `.env`
- Check firewall settings
- Verify database credentials

#### Module Not Found

```bash
# Clear node_modules and reinstall
rm -rf node_modules package-lock.json
npm install
```

#### Authentication Errors

- Verify JWT_SECRET in `.env`
- Check token expiration
- Verify token format in request header

### Getting Help

1. Check existing documentation
2. Search for similar issues in GitHub
3. Ask team members
4. Create an issue with:
   - Error message
   - Steps to reproduce
   - Expected vs actual behavior
   - Environment details

---

## Additional Resources

- [Express.js Documentation](https://expressjs.com/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [JavaScript Style Guide](https://github.com/airbnb/javascript)
- [Conventional Commits](https://www.conventionalcommits.org/)

---

## Code Review Checklist

Before submitting a PR, ensure:

- [ ] Code follows style guidelines
- [ ] All tests pass
- [ ] New features have tests
- [ ] Documentation is updated
- [ ] No console.log statements
- [ ] No commented-out code
- [ ] Error handling is implemented
- [ ] Security considerations addressed
- [ ] Performance implications considered
