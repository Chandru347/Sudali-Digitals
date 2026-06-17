# Sudali Digitals

E-commerce website for **Sudali Digitals** — customizable gift frames with UPI and Cash on Delivery payments.

## Features

- Responsive storefront (mobile, tablet, desktop)
- Scroll animations and elegant UI
- Product customization (photo upload, text, size, color)
- Shopping cart with localStorage persistence
- UPI payment (manual — QR code + transaction ID)
- Cash on Delivery (COD)
- Admin panel for products and orders
- Order notification emails to admin
- Neon PostgreSQL + Vercel deployment ready

## Tech Stack

- Next.js 15+ (App Router)
- TypeScript, Tailwind CSS, Framer Motion
- Prisma ORM + Neon PostgreSQL
- NextAuth.js (admin authentication)
- Vercel Blob (image uploads)
- Resend (order emails)

## Getting Started

### 1. Install dependencies

```bash
npm install
```

### 2. Environment variables

Copy `.env.example` to `.env` and fill in your values:

```bash
cp .env.example .env
```

| Variable | Description |
|----------|-------------|
| `DATABASE_URL` | Neon PostgreSQL connection string |
| `NEXTAUTH_SECRET` | Random secret (e.g. `openssl rand -base64 32`) |
| `NEXTAUTH_URL` | App URL (`http://localhost:3000` locally) |
| `ADMIN_EMAIL` | Admin login email |
| `ADMIN_PASSWORD` | Admin login password |
| `RESEND_API_KEY` | Resend API key for order emails |
| `ORDER_NOTIFY_EMAIL` | Email to receive order notifications |
| `BLOB_READ_WRITE_TOKEN` | Vercel Blob token (optional locally — falls back to base64) |
| `NEXT_PUBLIC_UPI_ID` | Your UPI ID |

### 3. Start the local database

The app needs PostgreSQL. For **local development**, use Prisma's built-in local Postgres (no Neon account required):

**Terminal 1** — keep this running:

```bash
npm run dev:db
```

After it starts, note the port (usually `51218`). Your `.env` should use:

```
DATABASE_URL="postgres://postgres:postgres@localhost:51218/template1?sslmode=disable"
```

If the port differs, run `npx prisma dev ls` and copy the TCP connection string.

**First-time setup** (Terminal 2):

```bash
npm run dev:setup
```

This creates tables and seeds sample products + admin user.

### 4. Run the website

**Terminal 2**:

```bash
npm run dev
```

Open **http://localhost:3000**

Admin panel: **http://localhost:3000/admin/login**

**Admin login:** `admin@sudalidigitals.com` / `SudaliAdmin@2025` (from `.env`)

For Vercel/production, set `DATABASE_URL` to a fresh Neon connection string from the [Neon dashboard](https://console.neon.tech).

## Deploy to Vercel

1. Push this repo to GitHub
2. Import the project in [Vercel](https://vercel.com)
3. Add all environment variables from `.env.example`
4. Enable **Vercel Blob** storage in your project (Storage tab)
5. Deploy — the build runs `prisma generate && prisma migrate deploy && next build`
6. After first deploy, seed the database locally pointing at Neon:

```bash
npm run db:seed
```

### Resend email setup

1. Create a free account at [resend.com](https://resend.com)
2. Add your API key as `RESEND_API_KEY`
3. For production, verify your domain in Resend and update the `from` address in `src/lib/email.ts`

## Payment Flow

### UPI
1. Customer selects UPI at checkout
2. Order is created with `PENDING` payment status
3. Customer sees QR code and UPI ID (`suthakarmurugan4134@okaxis`)
4. Customer pays and enters transaction ID
5. Admin receives email with full order details

### COD
1. Customer selects Cash on Delivery
2. Order is created with `COD_PENDING` status
3. Admin receives email immediately

## Project Structure

```
src/
├── app/           # Pages and API routes
├── components/    # UI components
├── lib/           # Utilities, auth, email, prisma
└── generated/     # Prisma client (auto-generated)
prisma/
├── schema.prisma
└── seed.ts
```

## Admin Routes

| Route | Purpose |
|-------|---------|
| `/admin/login` | Admin sign in |
| `/admin` | Dashboard |
| `/admin/products` | Manage products |
| `/admin/orders` | View and update orders |

## License

Private — Sudali Digitals
