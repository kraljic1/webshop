# Luxury Textile Webshop

A modern e-commerce webshop built with Next.js Commerce, React, and Supabase, inspired by luxury textile brands like Joshua Ellis. The site features product categories (Scarves, Stoles, Homeware, Classic Collection), an "Our Story" page, and a secure checkout system powered by Stripe. This project can be hosted on Netlify or Vercel.

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Setup Instructions](#setup-instructions)
- [Running Locally](#running-locally)
- [Deployment](#deployment)
- [Environment Variables](#environment-variables)
- [Supabase Configuration](#supabase-configuration)
- [Payment Integration](#payment-integration)
- [Contributing](#contributing)
- [License](#license)

## Features

- **Product Categories**: Browse Scarves, Stoles, Homeware, and Classic Collection with dynamic product listings.
- **Our Story**: A narrative page highlighting the brand's 250-year textile heritage.
- **Cart & Checkout**: Add products to cart and pay securely via Stripe.
- **User Authentication**: Login/register with Supabase Auth (email or social login).
- **Responsive Design**: Mobile-first, elegant UI styled with Tailwind CSS.
- **SEO Optimized**: Server-side rendering (SSR) and static site generation (SSG) via Next.js.
- **Real-Time Updates**: Supabase real-time subscriptions for stock and order updates.
- **Deployment**: Supports hosting on Netlify or Vercel.

## Tech Stack

- **Frontend**: React, Next.js Commerce, Tailwind CSS
- **Backend**: Supabase (PostgreSQL, Authentication, Storage)
- **Payments**: Stripe
- **Deployment**: Netlify (or Vercel)
- **Dependencies**:
  - `@stripe/stripe-js`, `@stripe/react-stripe-js` for payments
  - `@supabase/supabase-js` for backend integration
  - `@netlify/plugin-nextjs` for Netlify hosting
  - `react-query` for data fetching and caching

## Project Structure

```
luxury-textile-webshop/
├── components/                # Reusable React components
│   ├── Header.jsx
│   ├── Footer.jsx
│   ├── ProductCard.jsx
│   ├── Cart.jsx
│   ├── Auth.jsx
├── pages/                     # Next.js pages
│   ├── index.jsx             # Home page
│   ├── scarves.jsx           # Scarves category
│   ├── stoles.jsx
│   ├── homeware.jsx
│   ├── classic-collection.jsx
│   ├── our-story.jsx
│   ├── cart.jsx
│   ├── account.jsx
│   ├── api/                  # API routes
│   │   ├── checkout.js
│   │   ├── webhooks.js
├── lib/                      # Utility functions
│   ├── supabase.js           # Supabase client
│   ├── stripe.js             # Stripe client
├── public/                   # Static assets
│   ├── images/
├── styles/                   # Tailwind CSS
│   ├── globals.css
├── netlify.toml              # Netlify configuration
├── next.config.js            # Next.js configuration
├── package.json
├── README.md
```

## Prerequisites

- **Node.js**: Version 18 or higher
- **Supabase Account**: Sign up at https://supabase.com
- **Stripe Account**: Sign up at https://stripe.com
- **Netlify CLI** (optional): For local testing and deployment (`npm install -g netlify-cli`)
- **Git**: For version control

## Setup Instructions

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/your-username/luxury-textile-webshop.git
   cd luxury-textile-webshop
   ```

2. **Install Dependencies**:
   ```bash
   npm install
   ```

3. **Set Up Supabase**:
   - Create a Supabase project at https://supabase.com.
   - Create tables for products, cart, and orders (see Supabase Configuration).
   - Enable Row-Level Security (RLS) for secure data access.
   - Note your Supabase URL and Anon Key.

4. **Set Up Stripe**:
   - Create a Stripe account and get your API keys (publishable and secret).
   - Configure webhooks for order updates (e.g., `https://your-site.netlify.app/api/webhooks`).

5. **Configure Environment Variables**:
   Create a `.env.local` file in the root directory:
   ```env
   NEXT_PUBLIC_SUPABASE_URL=https://your-supabase-project.supabase.co
   NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
   STRIPE_SECRET_KEY=sk_test_...
   NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_...
   STRIPE_WEBHOOK_SECRET=whsec_...
   ```

6. **Install Netlify Plugin** (for Netlify hosting):
   ```bash
   npm install @netlify/plugin-nextjs
   ```

## Running Locally

1. **Start Development Server**:
   ```bash
   npm run dev
   ```
   Open http://localhost:3000 in your browser.

2. **Test with Netlify CLI** (optional):
   ```bash
   netlify dev
   ```
   This simulates Netlify's environment locally.

## Deployment

### Netlify

1. **Push to Git Repository**:
   Commit changes and push to GitHub/GitLab/Bitbucket.

2. **Connect to Netlify**:
   - Log in to Netlify, create a new site, and link your repository.
   - Set build settings:
     - **Build Command**: `next build`
     - **Publish Directory**: `.next`

3. Add environment variables in Netlify's dashboard (see Environment Variables).

4. **Deploy**:
   ```bash
   netlify deploy --prod
   ```
   Your site will be live at `https://your-site.netlify.app`.

### Vercel (Alternative)

1. Push to a Git repository.
2. Import the repository into Vercel and deploy with zero configuration.
3. Add environment variables in Vercel's dashboard.

**Note**: For Netlify, disable Next.js image optimization in `next.config.js`:
```javascript
module.exports = {
  images: {
    unoptimized: true,
  },
};
```

## Environment Variables

| Variable | Description | Source |
|----------|-------------|--------|
| `NEXT_PUBLIC_SUPABASE_URL` | Supabase project URL | Supabase Dashboard |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase anonymous key | Supabase Dashboard |
| `STRIPE_SECRET_KEY` | Stripe secret key | Stripe Dashboard |
| `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY` | Stripe publishable key | Stripe Dashboard |
| `STRIPE_WEBHOOK_SECRET` | Stripe webhook secret | Stripe Dashboard |

## Supabase Configuration

Create the following tables in Supabase:

```sql
CREATE TABLE products (
  id UUID PRIMARY KEY,
  name TEXT,
  price DECIMAL,
  category TEXT, -- e.g., 'Scarves', 'Stoles', 'Homeware', 'Classic Collection'
  image TEXT,
  stock INTEGER
);

CREATE TABLE cart (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id),
  product_id UUID REFERENCES products(id),
  quantity INTEGER
);

CREATE TABLE orders (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id),
  stripe_payment_id TEXT,
  total DECIMAL,
  status TEXT, -- e.g., 'pending', 'completed'
  created_at TIMESTAMP
);
```

- Enable RLS policies to restrict access (e.g., users can only view their own cart/orders).
- Use Supabase Storage for product images.

## Payment Integration

Payments are handled via Stripe:

1. **Checkout Flow**:
   - Users add products to the cart (stored in Supabase or local storage).
   - The `/api/checkout` route creates a Stripe Checkout Session.
   - Users are redirected to Stripe's hosted checkout or pay in-place with Stripe Elements.

2. **Webhooks**: The `/api/webhooks` route updates order status in Supabase upon payment completion.
3. **Testing**: Use Stripe test cards (e.g., `4242 4242 4242 4242`) in test mode.

## Contributing

1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/your-feature`).
3. Commit changes (`git commit -m "Add your feature"`).
4. Push to the branch (`git push origin feature/your-feature`).
5. Open a pull request.

Please follow the coding standards:
- Create a new component for each new feature
- Maintain clean code (max 200 lines per file)
- Use meaningful variable and function names
- Add comments for complex logic
- Adhere to TypeScript types
- No inline styles - use CSS files
