# FuelFlux - Fuel Station Management System

## Project Overview
FuelFlux is a comprehensive fuel station management system designed to streamline operations, monitor transactions, manage employees, track safety records, and enhance customer experience through online booking and administration.

The project is built with HTML, CSS, and JavaScript, with Firebase integration for backend functionality including authentication, database, storage, and hosting.

## Pages and Features

### 1. Landing Page (index.html)
- **Description**: Main entry point for the application
- **Features**:
  - Modern, responsive design with the FuelFlux branding
  - Call-to-action buttons directing to login/register
  - Mobile-friendly layout
  - Interactive fuel nozzle animation
  - Fast & Reliable tagline with stylized typography

### 2. Login Page (Pages/login.html)
- **Description**: User authentication portal
- **Features**:
  - Login form with email/password authentication
  - Registration option for new users
  - Responsive design with modern UI
  - "Remember me" and "Forgot password" functionality
  - Firebase Authentication integration
  - Social login options (Google and Facebook)
  - Form validation for both login and registration
  - Mobile number validation with proper format checking

### 3. Admin Dashboard (Pages/Admin.html)
- **Description**: Central control panel for administrators
- **Features**:
  - Dashboard overview with key metrics
  - Sidebar navigation to other admin sections
  - Real-time statistics display
  - Crowd monitoring and management
  - Employee tracking
  - Transaction history and analytics
  - Responsive administrative interface
  - Advanced filtering system for transactions
  - Password toggle functionality for secure input
  - Mobile-responsive sidebar with toggle functionality

### 4. Add Employee (Pages/add-employee.html)
- **Description**: Form to add new employees to the system
- **Features**:
  - Employee information collection
  - Role assignment
  - Document upload capabilities
  - Form validation
  - Firebase database integration
  - Responsive design for all device sizes

### 5. Edit Employee (Pages/edit-employee.html)
- **Description**: Interface to modify existing employee information
- **Features**:
  - Pre-populated form with current employee data
  - Update functionality
  - Document management
  - Status changes (active/inactive)
  - Role modification capabilities

### 6. Booking Details (Pages/booking-details.html)
- **Description**: Dashboard for managing fuel booking orders
- **Features**:
  - Booking list with filtering options
  - Status management (pending, confirmed, completed)
  - Customer information
  - Payment tracking
  - Date range filtering
  - Export functionality for reports

### 7. Transaction Details (Pages/transaction-details.html)
- **Description**: Comprehensive view of all financial transactions
- **Features**:
  - Transaction history with search and filter
  - Financial reporting
  - Export options
  - Transaction analytics
  - Advanced filtering by fuel type, payment method, date range, and amount

### 8. Safety Record (Pages/safety-record.html)
- **Description**: Interface for tracking safety-related incidents and compliance
- **Features**:
  - Incident logging
  - Compliance reporting
  - Safety metric visualization
  - Historical safety data
  - Severity categorization
  - Status tracking for all incidents

### 9. Subscription (Pages/Subscription.html)
- **Description**: Subscription management for premium services
- **Features**:
  - Subscription plan options
  - Payment integration
  - Subscription status tracking
  - Plan comparison
  - Auto-renewal settings

### 10. Alert System (Pages/alert.html)
- **Description**: Emergency and notification alert system
- **Features**:
  - Real-time alerts for critical situations
  - Notification preferences
  - Alert history
  - Staff notification system
  - Priority-based alert categorization

## Installation and Setup Guide

### 1. Clone the Repository
```
git clone https://github.com/yourusername/FuelFlux.git
cd FuelFlux
```

### 2. Firebase Configuration Setup
1. Create a new Firebase project at [Firebase Console](https://console.firebase.google.com/)
2. Register your app in the Firebase project
3. Enable required Firebase services:
   - Authentication (Email/Password and Social providers)
   - Firestore Database
   - Storage
   - Hosting
4. Copy the Firebase configuration to your project by creating a new file `assets/js/firebase-config.js`:

```javascript
// Firebase configuration
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "your-project-id.firebaseapp.com",
  projectId: "your-project-id",
  storageBucket: "your-project-id.appspot.com",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID",
  measurementId: "YOUR_MEASUREMENT_ID"
};

// Initialize Firebase
firebase.initializeApp(firebaseConfig);
```

### 3. Install Firebase CLI and Deploy
```
npm install -g firebase-tools
firebase login
firebase init
firebase deploy
```

## Firebase Integration Guide for Backend Developers

### 1. Project Setup
1. Create a new Firebase project at [Firebase Console](https://console.firebase.google.com/)
2. Register your app in the Firebase project
3. Copy the Firebase configuration to your project

```javascript
// Add this to your JavaScript file or in a script tag at the end of all HTML files
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "your-project-id.firebaseapp.com",
  projectId: "your-project-id",
  storageBucket: "your-project-id.appspot.com",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID",
  measurementId: "YOUR_MEASUREMENT_ID"
};

// Initialize Firebase
firebase.initializeApp(firebaseConfig);
```

### 2. Authentication Setup
FuelFlux uses Firebase Authentication for user management.

#### Implementation Steps:
1. Enable Email/Password authentication in Firebase Console
2. Enable Google and Facebook authentication providers
3. Implement sign-up functionality:

```javascript
// User registration
function registerUser(email, password, userData) {
  firebase.auth().createUserWithEmailAndPassword(email, password)
    .then((userCredential) => {
      // Save additional user data to Firestore
      return firebase.firestore().collection('users').doc(userCredential.user.uid).set({
        email: email,
        role: 'customer', // Default role
        name: userData.name,
        phone: userData.phone,
        created: firebase.firestore.FieldValue.serverTimestamp()
      });
    })
    .then(() => {
      // Success handling
    })
    .catch((error) => {
      // Error handling
    });
}
```

4. Implement login functionality with validation:

```javascript
// User login
function loginUser(email, password) {
  // Validate input
  if (!email || !password) {
    showError('Please enter both email and password');
    return;
  }
  
  firebase.auth().signInWithEmailAndPassword(email, password)
    .then((userCredential) => {
      // Redirect based on user role
      return firebase.firestore().collection('users').doc(userCredential.user.uid).get();
    })
    .then((doc) => {
      if (doc.exists && doc.data().role === 'admin') {
        window.location.href = 'Pages/Admin.html';
      } else {
        window.location.href = 'Pages/customer-dashboard.html';
      }
    })
    .catch((error) => {
      // Show appropriate error message
      showError('Login failed: ' + error.message);
    });
}
```

5. Implement social authentication:

```javascript
// Social authentication (Google)
function socialAuth(provider) {
  let authProvider;
  
  if (provider === 'google') {
    authProvider = new firebase.auth.GoogleAuthProvider();
  } else if (provider === 'facebook') {
    authProvider = new firebase.auth.FacebookAuthProvider();
  }
  
  firebase.auth().signInWithPopup(authProvider)
    .then((result) => {
      // Check if user exists in database
      const user = result.user;
      return firebase.firestore().collection('users').doc(user.uid).get()
        .then((doc) => {
          if (!doc.exists) {
            // Create new user record
            return firebase.firestore().collection('users').doc(user.uid).set({
              email: user.email,
              name: user.displayName,
              role: 'customer',
              created: firebase.firestore.FieldValue.serverTimestamp()
            });
          }
        })
        .then(() => {
          // Redirect based on role
          window.location.href = 'Pages/customer-dashboard.html';
        });
    })
    .catch((error) => {
      showError('Social login failed: ' + error.message);
    });
}
```

### 3. Firestore Database Structure
The application requires the following collections:

#### Collections:
1. **users**
   - Document ID: `{uid}`
   - Fields:
     - `email`: string
     - `name`: string
     - `phone`: string
     - `role`: string (admin, employee, customer)
     - `created`: timestamp
     - `lastLogin`: timestamp
     - `profileImage`: string (URL)

2. **employees**
   - Document ID: auto-generated
   - Fields:
     - `name`: string
     - `email`: string
     - `phone`: string
     - `position`: string
     - `salary`: number
     - `joiningDate`: timestamp
     - `documents`: array of strings (document URLs)
     - `status`: string (active/inactive)
     - `emergencyContact`: string
     - `address`: string

3. **transactions**
   - Document ID: auto-generated
   - Fields:
     - `customerId`: string (reference to user)
     - `amount`: number
     - `date`: timestamp
     - `paymentMethod`: string
     - `fuelType`: string
     - `quantity`: number
     - `status`: string (completed, pending, failed)
     - `stationId`: string (reference to station)
     - `invoiceNumber`: string

4. **bookings**
   - Document ID: auto-generated
   - Fields:
     - `customerId`: string (reference to user)
     - `fuelType`: string
     - `quantity`: number
     - `scheduledTime`: timestamp
     - `status`: string (pending, confirmed, completed, cancelled)
     - `paymentStatus`: string
     - `createdAt`: timestamp
     - `vehicleInfo`: object
       - `make`: string
       - `model`: string
       - `licensePlate`: string

5. **safety_records**
   - Document ID: auto-generated
   - Fields:
     - `incidentType`: string
     - `description`: string
     - `date`: timestamp
     - `reportedBy`: string (reference to user)
     - `severity`: string (low, medium, high)
     - `status`: string (open, investigating, resolved)
     - `location`: string
     - `affectedArea`: string
     - `resolutionDetails`: string
     - `resolutionDate`: timestamp

6. **subscriptions**
   - Document ID: auto-generated
   - Fields:
     - `userId`: string (reference to user)
     - `plan`: string
     - `startDate`: timestamp
     - `endDate`: timestamp
     - `status`: string (active, expired, cancelled)
     - `paymentMethod`: string
     - `autoRenew`: boolean
     - `features`: array of strings
     - `price`: number
     - `billingCycle`: string (monthly, quarterly, yearly)

7. **alerts**
   - Document ID: auto-generated
   - Fields:
     - `type`: string (emergency, notification, warning)
     - `title`: string
     - `message`: string
     - `createdAt`: timestamp
     - `createdBy`: string (reference to user)
     - `priority`: string (low, medium, high)
     - `targetUsers`: array of strings (user IDs or roles)
     - `status`: string (active, resolved, acknowledged)

### 4. Security Rules
Implement these Firestore security rules:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Allow users to read and write their own data
    match /users/{userId} {
      allow read: if request.auth != null && request.auth.uid == userId;
      allow write: if request.auth != null && request.auth.uid == userId;
    }
    
    // Admin access
    match /{document=**} {
      allow read, write: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
    
    // Employee records
    match /employees/{employeeId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
    
    // Transactions
    match /transactions/{transactionId} {
      allow read: if request.auth != null && (
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin' ||
        resource.data.customerId == request.auth.uid
      );
      allow create: if request.auth != null;
      allow update, delete: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
    
    // Bookings
    match /bookings/{bookingId} {
      allow read: if request.auth != null && (
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin' ||
        resource.data.customerId == request.auth.uid
      );
      allow create: if request.auth != null;
      allow update: if request.auth != null && (
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin' ||
        resource.data.customerId == request.auth.uid
      );
      allow delete: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
    
    // Safety Records
    match /safety_records/{recordId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null;
      allow update, delete: if request.auth != null && 
        (get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin' || 
         resource.data.reportedBy == request.auth.uid);
    }
    
    // Alerts
    match /alerts/{alertId} {
      allow read: if request.auth != null;
      allow create, update, delete: if request.auth != null && 
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
  }
}
```

### 5. Firebase Storage Integration
For document uploads and image storage:

```javascript
// Upload employee document
function uploadEmployeeDocument(employeeId, file) {
  const storageRef = firebase.storage().ref();
  const documentRef = storageRef.child(`employees/${employeeId}/documents/${file.name}`);
  
  return documentRef.put(file).then(snapshot => {
    return snapshot.ref.getDownloadURL();
  }).then(downloadURL => {
    // Save download URL to employee record
    return firebase.firestore().collection('employees').doc(employeeId).update({
      documents: firebase.firestore.FieldValue.arrayUnion(downloadURL)
    });
  });
}

// Upload profile image
function uploadProfileImage(userId, file) {
  // Check file size (max 2MB)
  if (file.size > 2 * 1024 * 1024) {
    throw new Error('File size exceeds 2MB limit');
  }
  
  // Check file type
  if (!['image/jpeg', 'image/png', 'image/gif'].includes(file.type)) {
    throw new Error('Only JPG, PNG, and GIF images are allowed');
  }
  
  const storageRef = firebase.storage().ref();
  const imageRef = storageRef.child(`users/${userId}/profile/${Date.now()}_${file.name}`);
  
  return imageRef.put(file).then(snapshot => {
    return snapshot.ref.getDownloadURL();
  }).then(downloadURL => {
    // Update user profile with new image URL
    return firebase.firestore().collection('users').doc(userId).update({
      profileImage: downloadURL
    });
  });
}
```

### 6. Real-time Updates
Implement listeners for real-time data changes:

```javascript
// Listen for real-time transaction updates
function listenForTransactions() {
  firebase.firestore().collection('transactions')
    .orderBy('date', 'desc')
    .onSnapshot((snapshot) => {
      snapshot.docChanges().forEach((change) => {
        if (change.type === 'added') {
          // Handle new transaction
          addTransactionToUI(change.doc.id, change.doc.data());
        }
        if (change.type === 'modified') {
          // Handle modified transaction
          updateTransactionInUI(change.doc.id, change.doc.data());
        }
        if (change.type === 'removed') {
          // Handle removed transaction
          removeTransactionFromUI(change.doc.id);
        }
      });
    });
}

// Listen for alerts in real-time
function listenForAlerts(userId) {
  firebase.firestore().collection('alerts')
    .where('status', '==', 'active')
    .onSnapshot((snapshot) => {
      snapshot.docChanges().forEach((change) => {
        if (change.type === 'added') {
          const alert = change.doc.data();
          // Check if alert is for all users or specifically this user
          if (alert.targetUsers.includes('all') || 
              alert.targetUsers.includes(userId)) {
            showAlertNotification(alert);
          }
        }
      });
    });
}
```

### 7. Cloud Functions (Advanced)
For implementing server-side logic like:
- Transaction validation
- Automatic email notifications
- Schedule processing
- Data aggregation for analytics
- Safety alert generation

Example Cloud Function:

```javascript
// In your Firebase Cloud Functions index.js
const functions = require('firebase-functions');
const admin = require('firebase-admin');
admin.initializeApp();

// Process booking and send confirmation
exports.processBooking = functions.firestore
  .document('bookings/{bookingId}')
  .onCreate((snap, context) => {
    const newBooking = snap.data();
    
    // Send confirmation email
    const userRef = admin.firestore().collection('users').doc(newBooking.customerId);
    
    return userRef.get().then(userDoc => {
      if (!userDoc.exists) {
        throw new Error('User not found');
      }
      
      const user = userDoc.data();
      
      // Here you would integrate with an email service
      console.log(`Sending confirmation email to ${user.email} for booking ${context.params.bookingId}`);
      
      // Update booking status
      return snap.ref.update({
        status: 'confirmed',
        lastUpdated: admin.firestore.FieldValue.serverTimestamp()
      });
    });
  });

// Generate safety alerts
exports.generateSafetyAlert = functions.firestore
  .document('safety_records/{recordId}')
  .onCreate((snap, context) => {
    const safetyRecord = snap.data();
    
    // Only create alerts for medium or high severity incidents
    if (safetyRecord.severity === 'low') {
      return null;
    }
    
    // Create alert in the alerts collection
    return admin.firestore().collection('alerts').add({
      type: 'safety',
      title: `Safety Incident: ${safetyRecord.incidentType}`,
      message: `A ${safetyRecord.severity} severity incident has been reported at ${safetyRecord.location}. ${safetyRecord.description}`,
      createdAt: admin.firestore.FieldValue.serverTimestamp(),
      createdBy: safetyRecord.reportedBy,
      priority: safetyRecord.severity === 'high' ? 'high' : 'medium',
      targetUsers: ['admin', 'employee'],
      status: 'active'
    });
  });

// Generate monthly reports
exports.generateMonthlyReport = functions.pubsub
  .schedule('0 0 1 * *')  // Run at midnight on the 1st of every month
  .timeZone('UTC')
  .onRun(async (context) => {
    const now = new Date();
    const lastMonth = new Date(now.getFullYear(), now.getMonth() - 1, 1);
    const endOfLastMonth = new Date(now.getFullYear(), now.getMonth(), 0);
    
    // Get transactions from last month
    const transactionsSnapshot = await admin.firestore()
      .collection('transactions')
      .where('date', '>=', lastMonth)
      .where('date', '<=', endOfLastMonth)
      .get();
    
    // Process transactions
    let totalRevenue = 0;
    const fuelTypeSummary = {};
    const paymentMethodSummary = {};
    
    transactionsSnapshot.forEach(doc => {
      const transaction = doc.data();
      totalRevenue += transaction.amount;
      
      // Summarize by fuel type
      if (fuelTypeSummary[transaction.fuelType]) {
        fuelTypeSummary[transaction.fuelType].count++;
        fuelTypeSummary[transaction.fuelType].revenue += transaction.amount;
      } else {
        fuelTypeSummary[transaction.fuelType] = {
          count: 1,
          revenue: transaction.amount
        };
      }
      
      // Summarize by payment method
      if (paymentMethodSummary[transaction.paymentMethod]) {
        paymentMethodSummary[transaction.paymentMethod]++;
      } else {
        paymentMethodSummary[transaction.paymentMethod] = 1;
      }
    });
    
    // Create monthly report document
    const monthName = lastMonth.toLocaleString('default', { month: 'long' });
    const year = lastMonth.getFullYear();
    
    await admin.firestore().collection('reports').add({
      type: 'monthly',
      month: monthName,
      year: year,
      totalRevenue: totalRevenue,
      transactionCount: transactionsSnapshot.size,
      fuelTypeSummary: fuelTypeSummary,
      paymentMethodSummary: paymentMethodSummary,
      generatedAt: admin.firestore.FieldValue.serverTimestamp()
    });
    
    return null;
  });
```

### 8. Deployment
1. Configure Firebase Hosting:
   ```
   firebase init hosting
   ```
   - Choose your Firebase project
   - Set public directory to the root folder
   - Configure as a single-page app: No
   - Set up automatic builds and deploys: No

2. Build and deploy:
   ```
   firebase deploy
   ```

3. Configure custom domain (optional):
   - Go to Firebase Console > Hosting
   - Click "Add custom domain"
   - Follow the instructions to verify and connect your domain

## Development Guidelines
1. Always use Firebase SDK version 9.0.0 or higher
2. Maintain separation of concerns between UI and data logic
3. Implement proper error handling for all Firebase operations
4. Use Firebase emulators for local development
5. Set up continuous integration for testing before deployment
6. Follow the existing project structure and naming conventions
7. Implement proper user authorization checks in both client and server code
8. Use responsive design patterns for all new pages
9. Follow the established color scheme and UI components
10. Test on multiple devices and browsers before deploying

## Testing
1. Use Firebase Auth Emulator for authentication testing
2. Set up Firestore emulator for database testing
3. Test security rules using the Firebase Rules Simulator
4. Test Cloud Functions locally using the Functions Emulator
5. Implement unit tests for critical business logic
6. Perform cross-browser testing on Chrome, Firefox, Safari, and Edge
7. Test the mobile responsiveness on iOS and Android devices

## Performance Considerations
1. Implement proper indexing for Firestore queries
2. Use batched writes for bulk operations
3. Optimize image uploads with compression before storage
4. Implement proper caching strategies for frequently accessed data
5. Lazy load non-critical resources
6. Minimize JavaScript bundle size
7. Use pagination for large data sets
8. Implement offline capabilities where appropriate

## Security Best Practices
1. Never expose Firebase API keys in client-side code
2. Always validate data on the server using Firebase Security Rules
3. Implement proper authentication checks for all operations
4. Use Firebase App Check to prevent abuse
5. Regularly audit and update security rules
6. Implement rate limiting for sensitive operations
7. Use content security policies (CSP)
8. Sanitize all user inputs to prevent XSS attacks
9. Implement MFA for admin accounts
10. Regularly back up critical data 