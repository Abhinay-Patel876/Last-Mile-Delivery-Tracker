LastMileUS
Enterprise-grade delivery management platform: customers place orders with live quotes, admins configure zones and pricing, agents update delivery status, and every status change is recorded in an immutable tracking timeline.

Hosted app: Deploy frontend to Vercel and backend to Render — see Deployment. Replace this line with your live URL after deploying.

Tech Stack
Layer	Technology
Frontend	React 19, Vite, React Router, Vanilla CSS (Glassmorphism)
Backend	Node.js, Express.js
Database	PostgreSQL
ORM	Prisma
Auth	JWT + OTP-based Email Auth + Role-based access (Customer, Admin, Agent)
Architecture	Modular Service-Controller pattern with distinct Core Engines
Quick Start
1. Clone and configure
git clone https://github.com/Abhirajverma12/LastMileUS.git
cd LastMileUS
cp backend/.env.example backend/.env
cp frontend/.env.example frontend/.env
2. Database Setup
Create a PostgreSQL database named delivery_tracker and update the DATABASE_URL in backend/.env.

3. Backend
cd backend
npm install
npx prisma migrate dev --name init
npm run prisma:seed
npm run dev
Backend runs at http://localhost:3000.

4. Frontend
cd frontend
npm install
npm run dev
Frontend runs at http://localhost:5173.

5. Run tests
cd backend
npx tsx src/tests/integration.ts
Demo Credentials
Role	Email	Password
Admin	admin@delivery.com	admin123
Customer	customer@delivery.com	customer123
Agent 1	agent1@delivery.com	agent123
Agent 2	agent2@delivery.com	agent123
Seed addresses for orders:

Pickup: Connaught Place, pincode 110001 (North Zone)
Drop: Koramangala, pincode 560034 (South Zone)
Environment Variables
Variable	Location	Description
DATABASE_URL	backend/.env	PostgreSQL connection string
JWT_SECRET	backend/.env	Secret for signing JWT tokens
PORT	backend/.env	API port (default 3000)
FRONTEND_URL	backend/.env	CORS origin for frontend
VITE_API_URL	frontend/.env	Backend API base URL (http://localhost:3000/api)
API Documentation
Base URL: http://localhost:3000/api All protected routes require header: Authorization: Bearer <token>

Auth
POST /auth/register (Public) - Register with OTP
POST /auth/verify-otp (Public) - Verify registration
POST /auth/login (Public) - Login and receive JWT
GET /auth/me (Any) - Current user profile
Orders
POST /orders/calculate (Customer, Admin) - Calculate delivery charge breakdown
POST /orders (Customer, Admin) - Create order atomically
GET /orders (Scoped) - List orders (Admin sees all, Customer sees theirs, Agent sees assigned)
GET /orders/:id (Scoped) - Order details & timeline
PATCH /orders/:id/status (Admin, Agent) - Update delivery state
POST /orders/:id/reschedule (Customer) - Reschedule failed delivery
Zones & Rate Cards
GET/POST /zones (Admin) - Zone CRUD
GET/POST /areas (Admin) - Area/pincode mapping CRUD
GET/POST /rates (Admin) - B2B/B2C rate configuration
Rate Calculation Logic
volumetricWeight = (length × breadth × height) / 5000
billableWeight   = max(actualWeight, volumetricWeight)
baseCharge       = rateCard.baseCharge
weightCharge     = billableWeight × rateCard.perKgCharge
codCharge        = Payment is COD ? rateCard.codSurcharge : 0
finalCharge      = baseCharge + weightCharge + codCharge
Zones detected by matching pincode + area name using a partial text search algorithm.
Zone Type (INTRA_ZONE vs INTER_ZONE) detected automatically to apply correct rate cards.
System Design
See system_design.md in the root folder for a comprehensive architectural breakdown of the Core Engines (Rate, Assignment, Zone).

Deployment
Backend & Database (Render)
Go to Render.com and connect your GitHub repo.
Select Blueprint and point it to the render.yaml file in this repository.
Render will automatically provision PostgreSQL and the Node.js web service.
Frontend (Vercel)
Import repo to Vercel, set root directory to frontend.
Set VITE_API_URL to your Render API URL + /api.
Deploy!
Project Structure
├── backend/          # Express API + Prisma + Core Engines
├── frontend/         # React + Vite UI (Glassmorphism)
├── scripts/          # Helper bash scripts
├── docker-compose.yml# Local DB provisioning
├── render.yaml       # Render deployment blueprint
└── system_design.md  # Full architectural specification
Notifications
Order state transitions trigger the notification.service.ts. Currently configured to log mock emails to the console, but structured to accept Resend/SendGrid adapters.

Submission Checklist
 Complete source (backend + frontend)
 README with setup, API docs, schema, rate logic
 OTP-based secure authentication
 .gitignore excludes .env, node_modules
 Integration testing suite
 Demo seed data included
 Auto-deployment blueprints provided
