# CaribCart Server

[![License: ISC](https://img.shields.io/badge/License-ISC-blue.svg)](https://opensource.org/licenses/ISC)
[![Node.js](https://img.shields.io/badge/Node.js-18.x-green.svg)](https://nodejs.org/)
[![Express.js](https://img.shields.io/badge/Express.js-5.1.0-lightgrey.svg)](https://expressjs.com/)
[![Prisma](https://img.shields.io/badge/Prisma-6.17.0-2D3748.svg)](https://www.prisma.io/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Neon-316192.svg)](https://neon.tech/)

Backend API server for CaribCart - a full-stack social media profile marketplace application where users can buy and sell verified social media accounts.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Environment Variables](#environment-variables)
- [API Documentation](#api-documentation)
- [Database Schema](#database-schema)
- [Background Jobs](#background-jobs)
- [Deployment](#deployment)
- [Related Repositories](#related-repositories)


## Overview

CaribCart Server is a RESTful API backend built with Node.js and Express.js that powers a marketplace for buying and selling social media profiles. The platform enables users to list their social media accounts (YouTube, Instagram, TikTok, etc.) for sale, manage transactions, and communicate with potential buyers through an integrated chat system.

## Features

- **User Authentication & Authorization**: Clerk-based authentication with JWT tokens
- **Listing Management**: Create, read, update, and delete social media profile listings
- **Real-time Chat**: WebSocket-based messaging system between buyers and sellers
- **Admin Panel**: Administrative controls for credential verification and listing management
- **Payment Processing**: Stripe integration for secure transactions
- **Background Jobs**: Inngest-powered asynchronous task processing for emails and webhooks
- **Image Management**: ImageKit integration for optimized image storage and delivery
- **Withdrawal System**: Secure withdrawal processing for seller earnings
- **Transaction History**: Comprehensive order tracking and management

## Tech Stack

- **Runtime**: Node.js
- **Framework**: Express.js 5.1.0
- **Database**: PostgreSQL (Neon Serverless)
- **ORM**: Prisma 6.17.0
- **Authentication**: Clerk Express
- **File Upload**: Multer 2.0.2
- **Image Storage**: ImageKit
- **Background Jobs**: Inngest 3.44.2
- **Payment Gateway**: Stripe 19.3.0
- **Email**: Nodemailer 7.0.9
- **WebSockets**: ws 8.18.3

## Project Structure
```
caribcart-server/
├── configs/
│   ├── imagekit.js          # ImageKit configuration
│   ├── multer.js            # File upload middleware configuration
│   ├── nodemailer.js        # Email service configuration
│   └── prisma.js            # Prisma client initialization
├── controllers/
│   ├── adminController.js   # Admin operations (verification, moderation)
│   ├── chatController.js    # Chat and messaging logic
│   ├── listingController.js # Listing CRUD operations
│   └── stripeWebhook.js     # Stripe payment webhook handlers
├── inngest/
│   └── index.js             # Background job definitions
├── middlewares/
│   └── authMiddleware.js    # Authentication middleware
├── prisma/
│   └── schema.prisma        # Database schema definitions
├── routes/
│   ├── adminRoutes.js       # Admin API endpoints
│   ├── chatRoutes.js        # Chat API endpoints
│   └── listingRoutes.js     # Listing API endpoints
├── .gitignore
├── package.json
├── server.js                # Application entry point
└── vercel.json              # Vercel deployment configuration
```

## Getting Started

### Prerequisites

- Node.js 18.x or higher
- npm or yarn
- PostgreSQL database (Neon recommended)
- Clerk account for authentication
- ImageKit account for image storage
- Stripe account for payments
- Inngest account for background jobs

### Installation

1. Clone the repository:
```bash
git clone https://github.com/JOSIAHTHEPROGRAMMER/caribcart-server.git
cd caribcart-server
```

2. Install dependencies:
```bash
npm install
```

3. Generate Prisma client:
```bash
npx prisma generate
```

4. Run database migrations:
```bash
npx prisma migrate deploy
```

5. Start the development server:
```bash
npm run server
```

The server will start on `http://localhost:5000` (or your configured PORT).

### Environment Variables

Create a `.env` file in the root directory with the following variables:
```env
# Database
DATABASE_URL="postgresql://..."
DIRECT_URL="postgresql://..."

# Clerk Authentication
CLERK_PUBLISHABLE_KEY="pk_..."
CLERK_SECRET_KEY="sk_..."

# ImageKit
IMAGEKIT_PRIVATE_KEY="private_..."


# Stripe
STRIPE_SECRET_KEY="sk_..."
STRIPE_WEBHOOK_SECRET="whsec_..."

# Inngest
INNGEST_EVENT_KEY="..."
INNGEST_SIGNING_KEY="..."

# Email (Nodemailer)
ADMIN_EMAILS = "your-email@gmail.com,"your-email2@gmail.com""
SENDER_EMAIL= your-email@gmail.com
SMTP_USER="@smtp-brevo.com"
SMTP_PASS="your-app-password"

# Application
PORT=5000
NODE_ENV=production
CLIENT_URL="https://your-frontend-url.com"
```

## API Documentation

### Listing Routes

- `POST /api/listing` - Create a new listing (authenticated, with image upload)
- `PUT /api/listing` - Update an existing listing (authenticated, with image upload)
- `GET /api/listing/public` - Get all public active listings
- `GET /api/listing/user` - Get user's listings (authenticated)
- `PUT /api/listing/:id/status` - Toggle listing status (authenticated)
- `DELETE /api/listing/:listingId` - Delete a listing (authenticated)
- `POST /api/listing/add-credential` - Submit listing credentials (authenticated)
- `GET /api/listing/purchase-account/:listingId` - Purchase an account (authenticated)
- `PUT /api/listing/featured/:id` - Mark listing as featured (authenticated)
- `GET /api/listing/user-orders` - Get user's purchase orders (authenticated)
- `POST /api/listing/withdraw` - Request withdrawal (authenticated)

### Chat Routes

- `POST /api/chat` - Create or get existing chat (authenticated)
- `GET /api/chat/user` - Get all user chats (authenticated)
- `POST /api/chat/send-message` - Send a message (authenticated)

### Admin Routes

- `GET /api/admin/isAdmin` - Verify admin status (authenticated, admin only)
- `GET /api/admin/dashboard` - Get admin dashboard statistics (authenticated, admin only)
- `GET /api/admin/all-listings` - Get all listings across the platform (authenticated, admin only)
- `PUT /api/admin/change-status/:listingId` - Change listing status (active/inactive/banned) (authenticated, admin only)
- `GET /api/admin/unverified-listings` - Get all listings with unverified credentials (authenticated, admin only)
- `GET /api/admin/credential/:listingId` - Get credential details for a specific listing (authenticated, admin only)
- `PUT /api/admin/verify-credential/:listingId` - Mark listing credentials as verified (authenticated, admin only)
- `GET /api/admin/unchanged-listings` - Get all listings with unchanged credentials (authenticated, admin only)
- `PUT /api/admin/change-credential/:listingId` - Update listing credentials (authenticated, admin only)
- `GET /api/admin/transactions` - Get all platform transactions (authenticated, admin only)
- `GET /api/admin/withdraw-requests` - Get all withdrawal requests (authenticated, admin only)
- `PUT /api/admin/withdrawal-mark/:id` - Mark withdrawal as paid/processed (authenticated, admin only)

## Database Schema

The application uses PostgreSQL with Prisma ORM. Key models include:

- **User**: User accounts with earnings and withdrawal tracking
- **Listing**: Social media profile listings with platform, metrics, and credentials
- **Chat**: Chat sessions between buyers and sellers
- **Message**: Individual chat messages
- **Credential**: Secure storage of account credentials
- **Transaction**: Purchase transactions and order history
- **Withdrawal**: Seller withdrawal requests and processing

Supported platforms: YouTube, Instagram, TikTok, Facebook, Twitter, LinkedIn, Pinterest, Snapchat, Twitch, Discord

Supported niches: Lifestyle, Fitness, Food, Travel, Tech, Gaming, Fashion, Beauty, Business, Education, and more

See `prisma/schema.prisma` for the complete schema definition.

## Background Jobs

Background jobs are handled by Inngest and include:

- **Email Notifications**: Send transactional emails for new orders, listings, and withdrawals
- **Webhook Processing**: Handle Clerk user events and Stripe payment webhooks
- **Credential Management**: Process credential verification and updates
- **Order Fulfillment**: Deliver account credentials to buyers after successful payment

## Deployment

The application is configured for deployment on Vercel:
```bash
vercel --prod
```

Ensure all environment variables are configured in your Vercel project settings.

### Production Considerations

- Database connection pooling is handled via Neon's serverless adapter
- Prisma generates the client during the build process (`postinstall` script)
- WebSocket connections may require additional configuration depending on your hosting platform

## Related Repositories

- **Frontend**: [caribcart-client](https://github.com/JOSIAHTHEPROGRAMMER/caribcart-client) - React.js client application


---

**Note**: This is a marketplace platform for educational and demonstration purposes. Ensure compliance with platform terms of service when buying or selling social media accounts.
