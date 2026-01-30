# Backend API Documentation

## Overview

This is a Node.js and Express.js backend application that provides RESTful API endpoints for the Software Design & Analysis Lab project.

## Table of Contents

- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Configuration](#configuration)
- [API Endpoints](#api-endpoints)
- [Development](#development)
- [Testing](#testing)
- [Deployment](#deployment)

## Getting Started

### Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (v14.x or higher)
- **npm** (v6.x or higher) or **yarn**
- **MongoDB** (if using MongoDB) or your preferred database
- **Git**

### Quick Start

1. Clone the repository:
```bash
git clone <repository-url>
cd Software_Design_Anylysis_Lab/Backend
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
npm run dev
```

The server will start on `http://localhost:3000` (or the port specified in your `.env` file).

## Project Structure

```
Backend/
├── src/
│   ├── config/          # Configuration files
│   │   ├── database.js  # Database connection
│   │   └── env.js       # Environment variables
│   ├── controllers/     # Route controllers
│   ├── models/          # Database models
│   ├── routes/          # API routes
│   ├── middleware/      # Custom middleware
│   ├── utils/           # Utility functions
│   ├── validators/      # Request validation
│   └── app.js           # Express app setup
├── tests/               # Test files
├── .env.example         # Environment variables template
├── .gitignore
├── package.json
└── server.js            # Entry point
```

## Installation

### Step 1: Install Dependencies

```bash
npm install
```

### Step 2: Environment Setup

Create a `.env` file in the root directory:

```env
# Server Configuration
PORT=3000
NODE_ENV=development

# Database Configuration
DB_HOST=localhost
DB_PORT=27017
DB_NAME=your_database_name
DB_USER=your_username
DB_PASSWORD=your_password

# JWT Configuration
JWT_SECRET=your_jwt_secret_key
JWT_EXPIRE=7d

# CORS Configuration
CORS_ORIGIN=http://localhost:3000

# Other Configuration
API_VERSION=v1
```

### Step 3: Database Setup

If using MongoDB:

```bash
# Start MongoDB service
mongod

# Or if using MongoDB Atlas, update your connection string in .env
```

### Step 4: Run the Application

**Development mode:**
```bash
npm run dev
```

**Production mode:**
```bash
npm start
```

## Configuration

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `PORT` | Server port number | `3000` |
| `NODE_ENV` | Environment (development/production) | `development` |
| `DB_HOST` | Database host | `localhost` |
| `DB_PORT` | Database port | `27017` |
| `DB_NAME` | Database name | - |
| `JWT_SECRET` | Secret key for JWT tokens | - |
| `JWT_EXPIRE` | JWT token expiration time | `7d` |
| `CORS_ORIGIN` | Allowed CORS origin | `*` |

### Database Configuration

The application supports multiple database options:
- MongoDB (Mongoose)
- PostgreSQL (Sequelize)
- MySQL (Sequelize)

Configure your database connection in `src/config/database.js`.

## API Endpoints

### Base URL

```
http://localhost:3000/api/v1
```

### Authentication

Most endpoints require authentication. Include the JWT token in the Authorization header:

```
Authorization: Bearer <your_token>
```

### Endpoint Categories

- **Authentication**: `/auth/*`
- **Users**: `/users/*`
- **Resources**: `/resources/*`

For detailed API documentation, see [API_DOCUMENTATION.md](./Documantation/API_DOCUMENTATION.md).

## Development

### Available Scripts

```bash
# Start development server with hot reload
npm run dev

# Start production server
npm start

# Run tests
npm test

# Run tests in watch mode
npm run test:watch

# Run linting
npm run lint

# Fix linting issues
npm run lint:fix
```

### Code Style

- Use ES6+ syntax
- Follow ESLint configuration
- Use async/await for asynchronous operations
- Follow RESTful API conventions

### Adding New Routes

1. Create a controller in `src/controllers/`
2. Create a route file in `src/routes/`
3. Register the route in `src/app.js`

Example:

```javascript
// src/routes/example.js
const express = require('express');
const router = express.Router();
const { getExample, createExample } = require('../controllers/exampleController');

router.get('/', getExample);
router.post('/', createExample);

module.exports = router;
```

## Testing

### Running Tests

```bash
# Run all tests
npm test

# Run tests with coverage
npm run test:coverage

# Run specific test file
npm test -- example.test.js
```

### Writing Tests

Tests are located in the `tests/` directory. Use Jest or Mocha for testing.

Example test:

```javascript
const request = require('supertest');
const app = require('../src/app');

describe('GET /api/v1/example', () => {
  it('should return 200 OK', async () => {
    const res = await request(app)
      .get('/api/v1/example')
      .expect(200);
    
    expect(res.body).toHaveProperty('data');
  });
});
```

## Deployment

### Production Checklist

- [ ] Set `NODE_ENV=production`
- [ ] Update database connection strings
- [ ] Set secure JWT secret
- [ ] Configure CORS properly
- [ ] Enable HTTPS
- [ ] Set up logging
- [ ] Configure error handling
- [ ] Set up monitoring

### Deployment Options

**Heroku:**
```bash
heroku create your-app-name
git push heroku main
```

**Docker:**
```bash
docker build -t backend-app .
docker run -p 3000:3000 backend-app
```

**AWS/Other Cloud Providers:**
Follow your cloud provider's Node.js deployment guide.

## Error Handling

The API uses standard HTTP status codes:

- `200` - Success
- `201` - Created
- `400` - Bad Request
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Not Found
- `500` - Internal Server Error

Error responses follow this format:

```json
{
  "success": false,
  "error": {
    "message": "Error message",
    "code": "ERROR_CODE"
  }
}
```

## Security

- All passwords are hashed using bcrypt
- JWT tokens are used for authentication
- CORS is configured to restrict origins
- Input validation is performed on all requests
- SQL injection and XSS protection is implemented

## Contributing

1. Create a feature branch
2. Make your changes
3. Write tests
4. Submit a pull request

## License

[Your License Here]

## Support

For issues and questions, please open an issue on GitHub or contact the development team.
