# ResQ India — Surplus Rescue Marketplace (v2)

A full-stack Node/Express + vanilla JS MVP for a surplus-food rescue marketplace, built for the Indian market. No database or build step required — data lives in a local JSON file.

## Run it

```bash
npm install
npm run seed      # creates demo users/shops/listings
npm start
```

Open **http://localhost:4000**.

Demo login: `demo@resq.in` / `password123`
Demo sellers: `seller1@resq.in` … `seller5@resq.in` / `password123`

## What's implemented (tested end-to-end)

**Core platform**
- Consumers browse and order from restaurants, supermarkets, bakeries, sweet shops, tiffin services, and event halls.
- Sellers must register with a **valid 14-digit FSSAI license** — enforced server-side (`server/routes/auth.js`), not just in the UI.
- **50% minimum discount is hard-enforced** in `server/routes/listings.js`, on every listing including fixed-price boxes.
- Every order returns a real savings receipt: kg food rescued, kg CO₂ avoided, litres water saved, ₹ saved (`server/lib/engine.js`).
- Points + badges (Food Saver, Eco Hero, Century Club, Club Organizer…) and a global leaderboard.

**Deal types (all live in the seed data)**
- **Chef Rescue Box** — fixed ₹99/₹199/₹299 pricing, contents revealed after purchase.
- **Mystery Gourmet Box** — premium restaurants, contents hidden until pickup.
- **Family Pack** — bundles for 4–6 people.
- **Bakery Auto-discount** — `/api/listings/:id/bakery-tick` ages items into deeper discounts as they near closing.
- **Live Auction** — price drops automatically every N minutes toward a floor price (`engine.auctionPrice`).
- **Grocery Rescue**, **Tiffin Rescue**, **Event Leftover / Wedding Rescue**, **Student-only deals**, and stubs for Flash Sale, Festival Rescue, Train Deals, Office Lunch, and Buffet Deals (same listing model, different `dealType` — trivial to extend).

**Neighborhood Rescue Clubs** — group-buy: once enough neighbours commit, the underlying listing's discount is automatically bumped (`server/routes/clubs.js`).

**AI (rule-based, transparent)** — every suggestion returns *why* it made that call:
- Smart pricing (`suggestDiscount`) — factors in time to closing, category, weather, demand.
- Demand forecasting (`forecastDemand`) — moving-average on a shop's order history.
- Recipe suggestions for surplus produce/bread/dairy.
- "Near me in 15 minutes" distance/time filtering (`haversineKm`).

**Revenue model, implemented as real code paths**
- Commission on every order (15% default, `server/routes/orders.js`).
- Delivery fee (₹39 flat) when `fulfillment: 'delivery'`.
- Business subscriptions (Free / Growth ₹999 / Chain ₹4999) with featured-listing perks (`server/routes/shops.js`).
- Consumer premium membership (Saver+, ₹99/mo) (`server/routes/dashboard.js`).
- Corporate sustainability dashboard for chains — food waste reduced, money recovered, CO₂ savings, popular products, peak hours.
- Donation option — sponsor meals to NGOs/temples/gurudwaras/mosques at checkout.

## What's intentionally scoped out

Documented honestly rather than faked:
- Real FSSAI government database verification (currently format-validated only — 14-digit check).
- WhatsApp Business API integration for alerts (the "Apartment Society Flash Sale" and "Neighborhood WhatsApp Ordering" ideas need real WhatsApp Business credentials).
- Live payments (Razorpay/UPI) and real delivery-partner dispatch.
- The broader "Everything Rescue Marketplace" expansion (florists, pharmacies, cosmetics) — the data model already supports arbitrary `category`/`dealType` values, so this is additive, not a rewrite.

The "AI" throughout is honest rule-based heuristics, not a trained model — this is documented rather than oversold, and every suggestion exposes its reasoning so it's easy to swap in a real model later.

## Project structure

```
server/
  index.js          Express app entry
  seed.js            Demo data generator
  lib/
    store.js         JSON-file "database"
    engine.js         AI/impact/pricing heuristics
    gamify.js          Points + badges
  routes/
    auth.js, shops.js, listings.js, orders.js, clubs.js, dashboard.js
public/
  index.html, css/style.css, js/api.js, js/app.js
```

## Deploying

Works well on Render, Railway, or Fly.io free tiers — see prior conversation notes. Remember the JSON-file store resets on redeploy on most free tiers; swap in Supabase/Neon Postgres for persistence if needed.
