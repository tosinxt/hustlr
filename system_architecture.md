# Task Management App System Architecture

## 1. Overall Architecture

A modern, scalable architecture for the task management application:

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Client Side   │     │   Server Side   │     │   Data Layer    │
│   (Frontend)    │◄────┤   (Backend)     │◄────┤   (Database)    │
└─────────────────┘     └─────────────────┘     └─────────────────┘
                              │   │
                              │   │
                        ┌─────┘   └─────┐
                        ▼               ▼
              ┌─────────────────┐ ┌─────────────────┐
              │  External APIs  │ │  File Storage   │
              │  (SMS, Email)   │ │  (Images, Docs) │
              └─────────────────┘ └─────────────────┘
```

## 2. Backend Architecture (Node.js & Express)

### Core Components

1. **API Layer**
   - Express.js server
   - RESTful API endpoints
   - API versioning (e.g., /api/v1/)
   - Rate limiting & security middleware

2. **Service Layer**
   - Business logic
   - Data validation
   - Transaction management

3. **Data Access Layer**
   - MongoDB models/schemas
   - Mongoose ODM
   - Data mappers

4. **Authentication & Authorization**
   - Firebase Authentication
   - JWT-based authentication (alternative method)
   - Role-based access control (taskOwner, tasker, admin)
   - KYC verification system (NIN/BVN for Nigerian users)

5. **External Service Integration**
   - Email service (Mailtrap for development)
   - SMS notifications (io)
   - File storage (Firebase Storage)

### Directory Structure

```
/
├── config/                 # Configuration files
│   ├── database.js         # MongoDB configuration
│   ├── firebase.js         # Firebase configuration
│   ├── auth.js             # Authentication configuration
│   └── email.js            # Email service configuration
├── controllers/            # Request handlers
│   ├── authController.js   # Authentication controller
│   ├── taskController.js   # Task management controller
│   ├── userController.js   # User profile controller
│   └── kycController.js    # KYC verification controller
├── models/                 # Database models
│   ├── User.js             # User model
│   ├── Task.js             # Task model
│   ├── Bid.js              # Bid model
│   └── KYC.js              # KYC verification model
├── middleware/             # Custom middleware
│   ├── auth.js             # Authentication middleware
│   ├── validation.js       # Request validation middleware
│   └── errorHandler.js     # Error handling middleware
├── routes/                 # API routes
│   ├── authRoutes.js       # Authentication routes
│   ├── taskRoutes.js       # Task management routes
│   ├── userRoutes.js       # User profile routes
│   └── kycRoutes.js        # KYC verification routes
├── services/               # Business logic
│   ├── authService.js      # Authentication service
│   ├── taskService.js      # Task management service
│   ├── notificationService.js # Notification service
│   └── kycService.js       # KYC verification service
├── utils/                  # Utility functions
│   ├── logger.js           # Logging utility
│   ├── validators.js       # Data validation utilities
│   └── helpers.js          # Helper functions
├── uploads/                # File uploads directory
│   └── kyc/                # KYC document uploads
├── app.js                  # Express application setup
├── server.js               # Server entry point
└── package.json            # Project dependencies
```

## 3. Database Design (MongoDB)

### Key Collections

1. **users**
   ```javascript
   {
     _id: ObjectId,
     uid: String,          // Firebase UID
     username: String,
     email: String,
     role: String,         // 'taskOwner', 'tasker', 'admin'
     profile_image: String,
     phone_number: String,
     created_at: Date,
     updated_at: Date
   }
   ```

2. **kyc_verifications**
   ```javascript
   {
     _id: ObjectId,
     user_id: ObjectId,    // Reference to users collection
     nin: String,          // National ID Number
     bvn: String,          // Bank Verification Number
     document_path: String,
     status: String,       // 'pending', 'approved', 'rejected'
     remarks: String,
     submitted_at: Date,
     verified_at: Date,
     verified_by: ObjectId  // Reference to admin user
   }
   ```

3. **tasks**
   ```javascript
   {
     _id: ObjectId,
     owner_id: ObjectId,    // Reference to users collection
     title: String,
     description: String,
     category: String,
     location: String,
     budget: Number,
     status: String,        // 'open', 'assigned', 'in_progress', 'completed', 'cancelled'
     deadline: Date,
     images: [String],      // Array of image paths
     created_at: Date,
     updated_at: Date
   }
   ```

4. **bids**
   ```javascript
   {
     _id: ObjectId,
     task_id: ObjectId,     // Reference to tasks collection
     tasker_id: ObjectId,   // Reference to users collection
     amount: Number,
     proposal: String,
     status: String,        // 'pending', 'accepted', 'rejected'
     created_at: Date,
     updated_at: Date
   }
   ```

## 4. API Endpoints

### Authentication
- POST /api/auth/register - User registration
- POST /api/auth/login - User login
- GET /api/auth/me - Get current user
- POST /api/auth/refresh-token - Refresh JWT token

### User Profile
- GET /api/users/:id - Get user profile
- PUT /api/users/:id - Update user profile
- GET /api/users/:id/tasks - Get user's tasks
- GET /api/users/:id/bids - Get user's bids

### KYC Verification
- POST /api/kyc/submit-nin - Submit NIN for verification
- POST /api/kyc/submit-bvn - Submit BVN for verification
- POST /api/kyc/upload-document - Upload verification document
- GET /api/kyc/status - Check verification status
- POST /api/kyc/update-status - Admin endpoint to update status

### Tasks
- GET /api/tasks - List all tasks (with filters)
- POST /api/tasks - Create a new task
- GET /api/tasks/:id - Get task details
- PUT /api/tasks/:id - Update task
- DELETE /api/tasks/:id - Delete task
- POST /api/tasks/:id/images - Upload task images

### Bids
- GET /api/tasks/:id/bids - Get bids for a task
- POST /api/tasks/:id/bids - Submit a bid
- PUT /api/bids/:id - Update bid
- DELETE /api/bids/:id - Delete bid
- PUT /api/bids/:id/accept - Accept a bid
- PUT /api/bids/:id/reject - Reject a bid

## 5. Authentication & Security

- Firebase Authentication
- JWT-based authentication (alternative method)
- CORS configuration
- Rate limiting
- Input validation and sanitization
- XSS protection
- NoSQL injection prevention
- CSRF protection

## 6. External Services Integration

### Email Service (Mailtrap for development)
- User registration confirmation
- Task assignment notifications
- Bid notifications
- Status updates

### SMS Notifications
- Task assignment notifications
- Bid acceptance notifications
- Task completion reminders

### File Storage
- Firebase Storage for both development and production
- Support for image uploads (JPG, PNG)
- Document uploads for KYC (JPG, PNG, PDF)
- File size limits (5MB per file)

## 7. Deployment Strategy

### Development
- Local Node.js server
- Local MongoDB database
- Firebase Authentication
- Mailtrap for email testing
- SMS notification service

### Production
- Node.js deployment on cloud provider (AWS, Azure, or Heroku)
- MongoDB Atlas for database
- Firebase Authentication
- Production email service (SendGrid or Mailgun)
- Production SMS service
- Firebase Storage for file storage
- HTTPS with proper SSL certificates
- Environment-specific configuration
