# ParcelFlow – Parcel Delivery Management System

**ParcelFlow** is a modern, production-grade, full-stack courier and parcel management platform engineered with React.js, Node.js, Express.js, and MongoDB. It provides enterprise logistics capabilities including automated parcel booking, real-time checkpoint timeline tracking, role-based access control (Admin & Customer), courier fleet assignment, customer management, and data-driven executive analytics.

---

## 1. Features Overview

### 🔐 Secure Authentication & Role Authorization
- **JWT-Based Authentication**: Secure authentication stored in local storage and sent via Bearer tokens.
- **Password Security**: Strong hashing with `bcryptjs` with salts before MongoDB persistence.
- **Role-Based Access Control**:
  - **Customer**: Book shipments, view/edit their own active parcels, track live parcel statuses, calculate estimated rates, inspect booking history, and manage personal profile credentials.
  - **Admin**: System-wide dashboard, global parcel management, real-time status progression (`Booked` ➔ `Picked Up` ➔ `In Transit` ➔ `Out for Delivery` ➔ `Delivered` / `Cancelled`), delivery courier assignment, user management (promote/demote roles, delete accounts), delivery personnel fleet CRUD, and executive analytics reports.

### 📦 Complete Parcel Lifecycle & CRUD
- **Unique Tracking Code Generation**: Generates collision-free IDs formatted as `PF<YYYY><MM><RANDOM 5 DIGITS>` (e.g. `PF202610001`).
- **Dynamic Pricing Calculator**: Instant delivery fee estimation based on package weight and priority service tier (*Standard*, *Express*, *Same Day*).
- **Search, Filtering & Pagination**: Server-side filtering by tracking code, receiver name, phone number, parcel status, delivery tier, and creation date ranges.
- **Audit History & Visual Timeline**: Automatic recording of every checkpoint in `ParcelStatusHistory` collection, displaying a visual progress stepper with checkpoint locations and remarks.
- **Public & Authenticated Tracking**: Direct tracking portal at `/track` accessible to guests and customers alike.

### 🚚 Delivery Personnel Fleet Management
- Comprehensive courier database tracking vehicle registration, vehicle type (*Bike*, *Van*, *Truck*, *Scooter*), and availability status (*Available*, *Busy*, *Offline*).
- Real-time courier assignment to parcels with automated audit log remarks.

### 📊 Real-Time Analytics & Reports
- **Dashboard Overview**: Role-customized statistics cards, SVG parcel status distribution chart, and monthly volume trend graph.
- **Executive Reports Module**:
  - Parcel status volume, weights, and revenue breakdown.
  - Delivery service tier distribution.
  - Annual monthly volume and revenue trends.
  - Delivery success rate percentage.
  - Date filtering (*Today*, *Last 7 Days*, *Last 30 Days*, *This Month*, *Custom Date Range*).

---

## 2. Technology Stack

### Frontend
- **Framework**: React 18 (Vite)
- **Routing**: React Router DOM v6
- **HTTP Client**: Axios with centralized request/response interceptors
- **Icons**: Lucide React
- **Design System**: Responsive custom CSS with modern design tokens, interactive SVG charts, modal dialogs, status badges, and toast notifications.

### Backend
- **Runtime**: Node.js (v24+)
- **Server Framework**: Express.js
- **Database**: MongoDB with Mongoose ODM
- **Authentication**: JSON Web Tokens (`jsonwebtoken`) & `bcryptjs`
- **Logging & Utilities**: Morgan HTTP logger, CORS, Dotenv

---

## 3. Project Structure

```text
fsmpi/
├── backend/
│   ├── config/
│   │   └── db.js                        # MongoDB Mongoose connection
│   ├── controllers/
│   │   ├── authController.js            # Register, Login, Me, Profile
│   │   ├── parcelController.js          # Parcel CRUD, Track, Status, Assign
│   │   ├── userController.js            # Admin user directory & role management
│   │   ├── deliveryPersonnelController.js # Fleet CRUD & active workload
│   │   ├── dashboardController.js       # Real-time MongoDB aggregations
│   │   └── reportController.js          # Status, monthly & performance reports
│   ├── middleware/
│   │   ├── authMiddleware.js            # JWT verification & role authorization
│   │   ├── errorMiddleware.js           # Centralized 404 & error handlers
│   │   └── validationMiddleware.js      # Input validators
│   ├── models/
│   │   ├── User.js                      # User schema with bcrypt hooks
│   │   ├── Parcel.js                    # Parcel schema with trackingNumber index
│   │   ├── DeliveryPersonnel.js         # Fleet personnel schema
│   │   └── ParcelStatusHistory.js       # Timeline audit history schema
│   ├── routes/
│   │   ├── authRoutes.js
│   │   ├── parcelRoutes.js
│   │   ├── userRoutes.js
│   │   ├── deliveryPersonnelRoutes.js
│   │   ├── dashboardRoutes.js
│   │   └── reportRoutes.js
│   ├── seed/
│   │   └── seeder.js                    # Database seed script
│   ├── utils/
│   │   ├── generateTrackingNumber.js    # Unique tracking code generator
│   │   └── responseHandler.js          # Standardized API response format
│   ├── .env                             # Environment variables
│   ├── .env.example
│   ├── package.json
│   └── server.js                        # Main Express entry point
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   │   ├── common/                  # Navbar, Sidebar, Modal, Toast, Button, Input, Select, Pagination, etc.
│   │   │   ├── dashboard/               # StatCard, StatusChart (SVG), MonthlyVolumeChart
│   │   │   ├── parcel/                  # TrackingTimeline, StatusUpdateModal, AssignPersonnelModal
│   │   │   ├── ProtectedRoute.jsx       # Route guard for authenticated users
│   │   │   └── AdminRoute.jsx           # Route guard for administrators
│   │   ├── context/
│   │   │   ├── AuthContext.jsx          # User state, JWT storage, login/logout
│   │   │   └── ToastContext.jsx         # Floating notification queue
│   │   ├── layouts/
│   │   │   ├── MainLayout.jsx           # Authenticated layout with Navbar & Sidebar
│   │   │   └── AuthLayout.jsx           # Guest layout for Login, Register & Public Track
│   │   ├── pages/
│   │   │   ├── LoginPage.jsx            # Sign in with demo quick-fill buttons
│   │   │   ├── RegisterPage.jsx         # Account registration with validation
│   │   │   ├── DashboardPage.jsx        # Role-based KPI dashboard
│   │   │   ├── ParcelsPage.jsx          # Shipments table with search, filter, pagination
│   │   │   ├── CreateParcelPage.jsx     # Shipment booking form with auto-rate calculation
│   │   │   ├── ParcelDetailsPage.jsx    # Complete specs, route, courier & visual timeline
│   │   │   ├── EditParcelPage.jsx       # Parcel update page
│   │   │   ├── TrackParcelPage.jsx      # Public & authenticated tracking portal
│   │   │   ├── ProfilePage.jsx          # Account settings & password update
│   │   │   ├── NotFoundPage.jsx         # 404 view
│   │   │   └── admin/
│   │   │       ├── UserManagementPage.jsx       # Admin user directory
│   │   │       ├── DeliveryPersonnelPage.jsx   # Courier fleet management
│   │   │       └── ReportsPage.jsx              # Executive audit reports
│   │   ├── services/
│   │   │   ├── api.js                   # Centralized Axios with interceptors
│   │   │   ├── authService.js
│   │   │   ├── parcelService.js
│   │   │   ├── userService.js
│   │   │   ├── deliveryPersonnelService.js
│   │   │   ├── dashboardService.js
│   │   │   └── reportService.js
│   │   ├── styles/
│   │   │   └── index.css                # Complete responsive design stylesheet
│   │   ├── utils/
│   │   │   ├── constants.js             # Status colors, rates, vehicle types
│   │   │   └── formatters.js            # Date, currency, weight formatters
│   │   ├── App.jsx                      # Route definitions
│   │   └── main.jsx                     # React root mount
│   ├── .env
│   ├── .env.example
│   ├── index.html
│   ├── package.json
│   └── vite.config.js                   # Vite config with dev proxy to port 5000
│
├── README.md
└── .gitignore
```

---

## 4. Prerequisites

Before running the application, ensure the following are installed on your machine:
- **Node.js**: v18 or later (tested on v24.16.0)
- **npm**: v9 or later (tested on v11.13.0)
- **MongoDB**: Local MongoDB community service running on port 27017 or a MongoDB Atlas connection string.

---

## 5. Installation & Execution

### Step 1: Clone or Navigate to the Workspace
```bash
cd fsmpi
```

### Step 2: Configure Environment Variables

**Backend (`backend/.env`):**
```env
PORT=5000
MONGO_URI=mongodb://127.0.0.1:27017/parcelflow
JWT_SECRET=parcelflow_super_secret_jwt_key_2026_secure!
JWT_EXPIRE=30d
NODE_ENV=development
```

**Frontend (`frontend/.env`):**
```env
VITE_API_URL=http://localhost:5000/api
```

### Step 3: Backend Setup & Database Seeding

Open a terminal:
```bash
cd backend
npm install
npm run seed     # Populates demo admin, customer, fleet, parcels & history
npm run dev      # Starts the Express API server on http://localhost:5000
```

### Step 4: Frontend Setup

Open a second terminal:
```bash
cd frontend
npm install
npm run dev      # Starts the Vite development server on http://localhost:3000
```

Open your browser and navigate to:
```text
http://localhost:3000
```

---

## 6. Demo Credentials

The database seeder automatically initializes the following accounts:

| Role | Email | Password | Permissions |
| :--- | :--- | :--- | :--- |
| **Administrator** | `admin@parcelflow.com` | `Admin@123` | System-wide visibility, parcel status update, courier assignment, user directory, reports |
| **Customer** | `customer@parcelflow.com` | `Customer@123` | Shipment booking, own parcel tracking, rate calculation, profile management |

> **Note**: The login screen includes a **"Demo Quick-Fill"** button to populate either set of credentials in one click.

---

## 7. REST API Endpoints

All responses adhere to the standard JSON format:
```json
{
  "success": true,
  "message": "Descriptive status message",
  "data": { ... }
}
```

### Authentication (`/api/auth`)
| Method | Endpoint | Access | Description |
| :--- | :--- | :--- | :--- |
| `POST` | `/api/auth/register` | Public | Register a new user |
| `POST` | `/api/auth/login` | Public | Authenticate user & return JWT |
| `GET` | `/api/auth/me` | Private | Get authenticated user profile |
| `PUT` | `/api/auth/profile` | Private | Update user profile / password |

### Parcels (`/api/parcels`)
| Method | Endpoint | Access | Description |
| :--- | :--- | :--- | :--- |
| `POST` | `/api/parcels` | Private | Book a new parcel shipment |
| `GET` | `/api/parcels` | Private | List parcels (scoped to customer, global for admin; supports search & filters) |
| `GET` | `/api/parcels/:id` | Private | Get single parcel details with status history |
| `GET` | `/api/parcels/track/:trackingNumber` | Public | Public parcel tracking with checkpoint history |
| `PUT` | `/api/parcels/:id` | Private | Update parcel information |
| `DELETE`| `/api/parcels/:id` | Private | Delete eligible parcel |
| `PATCH`| `/api/parcels/:id/status` | Admin | Update parcel status, location & remarks |
| `PATCH`| `/api/parcels/:id/assign` | Admin | Assign delivery personnel to parcel |

### Users (`/api/users`)
| Method | Endpoint | Access | Description |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/users` | Admin | List all registered users with parcel count |
| `GET` | `/api/users/:id` | Admin | Get single user details |
| `PUT` | `/api/users/:id` | Admin | Update user information / role |
| `DELETE`| `/api/users/:id` | Admin | Delete user account |

### Delivery Personnel (`/api/delivery-personnel`)
| Method | Endpoint | Access | Description |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/delivery-personnel` | Private | List delivery personnel fleet |
| `POST` | `/api/delivery-personnel` | Admin | Add new courier driver |
| `GET` | `/api/delivery-personnel/:id`| Private | Get courier profile & active assignments |
| `PUT` | `/api/delivery-personnel/:id`| Admin | Update courier driver details |
| `DELETE`| `/api/delivery-personnel/:id`| Admin | Remove courier driver |

### Dashboard & Analytics (`/api/dashboard` & `/api/reports`)
| Method | Endpoint | Access | Description |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/dashboard/stats` | Private | Get KPI summary, status counts, monthly trend & recent parcels |
| `GET` | `/api/reports/parcel-status` | Admin | Status distribution volume & revenue |
| `GET` | `/api/reports/monthly` | Admin | Annual monthly volume & delivered counts |
| `GET` | `/api/reports/delivery-performance` | Admin | Delivery success rate & service tier distribution |

---

## 8. Application Screenshots

*(Screenshots can be placed in this section to showcase the application during presentations)*

- **Dashboard**: Real-time KPI summary cards, SVG status distribution donut chart, and shipment volume bars.
- **Shipments Directory**: Server-side filtered responsive table with status badges and quick action dropdowns.
- **Booking Form**: Multi-step sender/receiver input with dynamic rate calculation.
- **Live Tracking View**: Interactive visual progress stepper highlighting current parcel checkpoints.
- **Admin Fleet Management**: Courier availability statuses and active delivery counts.
- **Executive Reports**: Filterable date ranges with success rates and service tier distribution.

---

## 9. Security & Production Standards Implemented
- No plaintext password storage; enforced `bcryptjs` salts on pre-save middleware.
- Request validation on both frontend and backend controllers.
- Protected API routes verified via JWT authorization headers.
- Role-based authorization guard checking permissions before executing actions.
- Centralized error handling eliminating stack trace exposure in responses.
- Environment variable separation for secrets and MongoDB URIs.
- MongoDB indexes configured on unique and frequently queried fields (`trackingNumber`, `email`, `status`, `customerId`).
