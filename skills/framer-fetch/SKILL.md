---
name: framer-fetch
description: Working with dynamic data via Framer's Fetch API and custom backends
---

# Framer Fetch API

Fetch allows you to integrate dynamic, real-time data from external APIs directly into your Framer site.

## 1. Core Principles
- **Dynamic vs Static:** Use Fetch for data that changes frequently (stock counts, weather, user location). Use the Framer CMS for static content to ensure better SEO and performance.
- **Data Types:** Framer supports JSON objects containing Strings, Booleans, Numbers, Image URLs, and Colors.
- **Arrays:** While you cannot iterate over arrays directly in the Framer UI, you can access specific indices (e.g., `data.items[0].name`) using the Path selector.

## 2. When to Use Fetch
- **Personalization:** User location, login state, or custom greetings.
- **Real-time Inventory:** Product availability, stock prices, or seat counts.
- **External Integration:** Pulling data from platforms like Airtable, Google Sheets, or custom databases.

## 3. Backend Coordination (The Middleman)
Most production APIs require authentication (API Keys) or have CORS restrictions that prevent direct access from a browser-based tool like Framer.
- **The Proxy Pattern:** Build a small backend (Cloudflare Workers, Val Town, or Vercel Functions) to handle authentication and return clean JSON to Framer.
- **CORS Configuration:** Ensure your backend includes appropriate `Access-Control-Allow-Origin` headers to allow the Framer preview and live site to fetch data.

## 4. Implementation Checklist
- [ ] **JSON Wrapper:** The API must return a JSON object. Returning a single value directly (like a raw string) will not be picked up by the Path selector.
- [ ] **Path Selection:** Use the Framer UI Path selector to map specific JSON fields to component variables.
- [ ] **SEO Caution:** Fetch data is loaded on the client-side. Important SEO content should remain in the CMS or static layers.
- [ ] **Fallbacks:** Always provide a default/placeholder value in Framer in case the API call fails or is slow.
