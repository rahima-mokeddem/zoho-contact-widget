# Zoho CRM Contact Quick Editor Widget

A lightweight widget for the Contact record detail page in Zoho CRM. It displays and edits core contact fields, and includes a postal-code-based address auto-fill.

## Features
- Displays First Name, Last Name, Email, and Phone, read live from the CRM record
- Edit mode with Save/Cancel — changes persist directly to the Contact record via the Zoho CRM API
- Mailing Address section with postal code lookup that auto-fills City and State/Province
- Visible error message when a postal code can't be resolved

## Setup / Installation (fresh Zoho org)

1. Sign up for a free Zoho CRM developer account at zoho.com
2. Create 2-3 sample Contacts in the Contacts module
3. Install Node.js, then install the Zoho Extension Toolkit:
npm install -g zoho-extension-toolkit

4. Clone this repo and run:
cd zoho_widget
npm install
zet run

5. Host the `app` folder as a static site (e.g. Netlify Drop) to get a public HTTPS URL, this avoids local browser restrictions (see note below)
6. In Zoho CRM: Setup > Widgets > Create New Widget
   - Widget Type: Related List
   - Hosting: External
   - Base URL: your hosted URL + `/widget.html`
7. Open a Contact record, click "Add Related List" in the side panel, select your widget

**Note on local dev**: Chrome's Private Network Access policy blocks the CRM (a public site) from reaching a widget hosted on `127.0.0.1`. Deploying to a free static host (Netlify, Vercel, etc.) avoids this entirely and is closer to a real production setup anyway.

## Address API: Zippopotam.us

I chose Zippopotam.us because it's completely free, requires no API key or signup, and needs no server-side proxy, all of which matter for a take-home exercise someone else will run without setting up credentials.

**Limitations** (documented, not hidden):
- **Brazil**: only "major" postal codes are supported (codes ending in `-000`), this is a restriction of the underlying GeoNames dataset, not something fixable client-side. The widget normalizes the postal code format automatically but can't resolve non-major CEPs.
- **Canada**: only the first 3 characters (the Forward Sortation Area) are supported, so lookups return the general area, not a precise match to the full 6-character postal code.
- Street address is never returned by this API, by task design, it stays a manual field.
- Coverage and accuracy are best for the US, UK, and Canada; other countries may need manual entry, which the widget signals with a visible error message.

If this were going to production, I'd consider a paid tier (e.g. Google Geocoding API) or a country-specific API for full Canadian/Brazilian postal code coverage.

## What I'd improve with more time
- Auto-detect the country from the postal code format itself, so the user doesn't have to manually select a country before typing (this needs care, since postal code formats overlap between countries — e.g. 5 digits could be US or several others — so it would need a priority order or a "did you mean X?" confirmation)
- Expand the country dropdown beyond the 5 currently hardcoded
- Add more inline guidance next to other fields (similar to the postal code info icon), so the interface is self-explanatory without needing a README
- Add loading states/spinners during API calls, not just after
- Debounce the postal code lookup instead of firing only on blur
- Customize colors, typography, and branding to match the client's visual identity, rather than the current neutral default styling.

## Assumptions
- Used Zoho CRM's built-in Mailing Address fields (Mailing_Street, Mailing_City, Mailing_State, Mailing_Zip, Mailing_Country) rather than creating custom fields, since Contacts already support them natively
- Assumed a single combined Edit/Save/Cancel control for both Contact Details and Mailing Address sections, rather than separate edit states per section