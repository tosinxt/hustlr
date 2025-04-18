# Hustlr - Task Management App Backend

A robust Node.js and Express backend for a task management application with KYC verification for Nigerian users.

## System Architecture

For a detailed overview of the system architecture, please refer to the [system_architecture.md](./system_architecture.md) file.

## Getting Started

### Prerequisites

- Node.js (v14.x or higher)
- npm (v6.x or higher)
- MongoDB (local or MongoDB Atlas)
- Firebase account
- Git

### Installation

1. Clone the repository
   ```bash
   git clone https://github.com/yourusername/hustlr.git
   cd hustlr
   ```

2. Install dependencies
   ```bash
   npm install
   ```

3. Set up environment variables
   ```bash
   cp .env.example .env
   ```
   Edit the `.env` file with your configuration details:
   - MongoDB connection string
   - Firebase configuration
   - JWT secret
   - Mailtrap credentials (for email testing)
   - SMS service credentials
   - File upload paths

4. Set up MongoDB collections
   ```bash
   npm run db:setup
   ```

5. Start the development server
   ```bash
   npm run dev
   ```

### Database Configuration

The application uses MongoDB with the following configuration options:

#### Local Development
```
MONGODB_URI=mongodb://localhost:27017/hustlr
```

#### Production (MongoDB Atlas)
```
MONGODB_URI=mongodb+srv://<username>:<password>@<cluster>.mongodb.net/hustlr
```

### Firebase Configuration

The application uses Firebase for authentication. You'll need to set up a Firebase project and add your configuration to the `.env` file:

```
FIREBASE_API_KEY=your_api_key
FIREBASE_AUTH_DOMAIN=your_project.firebaseapp.com
FIREBASE_PROJECT_ID=your_project_id
FIREBASE_STORAGE_BUCKET=your_project.appspot.com
FIREBASE_MESSAGING_SENDER_ID=your_sender_id
FIREBASE_APP_ID=your_app_id
```

## API Documentation

### Authentication

- **POST /api/auth/register** - User registration
  ```json
  {
    "username": "johndoe",
    "email": "john@example.com",
    "password": "securepassword",
    "role": "taskOwner",
    "phone_number": "+234XXXXXXXXXX"
  }
  ```

- **POST /api/auth/login** - User login
  ```json
  {
    "email": "john@example.com",
    "password": "securepassword"
  }
  ```

- **GET /api/auth/me** - Get current user (requires authentication)

### KYC Verification

- **POST /api/kyc/submit-nin** - Submit NIN for verification
  ```json
  {
    "nin": "12345678901"
  }
  ```

- **POST /api/kyc/submit-bvn** - Submit BVN for verification
  ```json
  {
    "bvn": "12345678901"
  }
  ```

- **POST /api/kyc/upload-document** - Upload verification document (multipart/form-data)

### Tasks

- **GET /api/tasks** - List all tasks (with filters)
  - Query parameters: `category`, `location`, `status`, `min_budget`, `max_budget`

- **POST /api/tasks** - Create a new task
  ```json
  {
    "title": "Website Development",
    "description": "Build a responsive website for my business",
    "category": "web_development",
    "location": "Lagos",
    "budget": 50000,
    "deadline": "2025-05-30T00:00:00Z"
  }
  ```

- **GET /api/tasks/:id** - Get task details

### Bids

- **POST /api/tasks/:id/bids** - Submit a bid
  ```json
  {
    "amount": 45000,
    "proposal": "I can deliver this project in 2 weeks with the following features..."
  }
  ```

- **PUT /api/bids/:id/accept** - Accept a bid (task owner only)

## Testing

Run the test suite with:

```bash
npm test
```

For development, you can use:

```bash
npm run test:watch
```

## External Services

### Email (Mailtrap)

The application uses Mailtrap for email testing in the development environment:

- Host: sandbox.smtp.mailtrap.io
- Port: 2525

You'll need to sign up for a Mailtrap account and add your credentials to the `.env` file.

### SMS Service

The application uses an SMS service for notifications:

- During development, you can only send SMS to verified phone numbers
- Add your SMS service credentials to the `.env` file

## Deployment

### Development

- Local Node.js server
- Local MongoDB database
- Firebase authentication
- Mailtrap for email testing
- SMS service trial account

### Production

- Node.js deployment on cloud provider (AWS, Azure, or Heroku)
- MongoDB Atlas for database
- Firebase Authentication
- Production email service (SendGrid or Mailgun)
- Production SMS service
- Firebase Storage for file storage
- HTTPS with proper SSL certificates
- Environment-specific configuration

## License

This project is licensed under the MIT License - see the LICENSE file for details.
