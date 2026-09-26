# Appointment / Contact Page — Product Specification

---

## 1. Overview

Create a **dedicated appointment booking page** (`appointment.html`) separate from the main landing page. This page serves as the primary conversion funnel — every CTA across the site should drive users here or to WhatsApp.

---

## 2. Architecture Decision

### Why a Separate Page (Not a Section)?

| Factor | In-Page Section | Separate Page ✅ |
| -------- | ---------------- | ----------------- |
| SEO value | Shares with main page | Gets its own title, meta, JSON-LD — ranks independently for "book dentist appointment Jagatpura" |
| Page speed | Adds form weight to main page load | Main page stays fast; form loads only when needed |
| Analytics | Hard to track conversions separately | Dedicated page = clear GA4 conversion tracking |
| User focus | Distractions from other sections | Clean, focused — higher form completion rate |
| Shareability | Can't share direct link to form | Direct URL: `amratadentalclinic.com/appointment.html` |

### File Structure After This Change

```
dentist-client-main/
├── index.html              ← Main landing page (existing)
├── appointment.html         ← NEW: Appointment booking page
├── assets/css/style.css     ← Shared styles (add form styles here)
├── assets/js/main.js        ← Shared scripts (add form handler here)
├── ...existing files
```

---

## 3. Form Fields Specification

| # | Field | Type | Required | Validation | Notes |
| --- | ------- | ------ | ---------- | ------------ | ------- |
| 1 | **Full Name** | Text input | ✅ Yes | Min 2 chars, letters + spaces only | Placeholder: "Enter your full name" |
| 2 | **Contact Number / Email ID** | Text input | ✅ Yes | Accept either valid 10-digit Indian phone OR valid email | Label should say "Phone Number or Email". Placeholder: "+91 XXXXX XXXXX or <email@example.com>" |
| 3 | **Date of Visit** | Date picker | ❌ Optional | Must be today or future date, no past dates | Use native HTML `<input type="date">` with `min` attribute set to today's date via JS. Placeholder: "Select preferred date" |
| 4 | **Reason for Visit / Enquiry** | Dropdown select | ❌ Optional | — | First option is empty placeholder: "Select reason (optional)" |
| 5 | **Description** | Textarea | ✅ Yes | Max 500 chars | Placeholder: "Tell us more about your concern or query..." Show character count below |

### Dropdown Options for "Reason for Visit" (Exact Order)

1. *(empty — placeholder text: "Select reason (optional)")*
2. General Checkup
3. Root Canal Treatment
4. Dental Implants
5. Crown & Bridges
6. Dentures
7. Teeth Whitening
8. Others (Mention in description)

> When "Others (Mention in description)" is selected, visually highlight the Description textarea below to draw attention — add a subtle border color change or a small helper text like "Please describe your concern below".

---

## 4. UX Flow & Behavior

### Form Submission Flow

```
User fills form → Clicks "Book Appointment" button
    ↓
Show loading spinner on button (disable button, text changes to "Sending...")
    ↓
POST to Web3Forms API (https://api.web3forms.com/submit)
    ↓
┌─ SUCCESS ──────────────────────────────────────────────┐
│ Hide the form                                          │
│ Show success message card:                              │
│   ✅ "Thank you! Your appointment request has been     │
│       submitted successfully."                          │
│   "We'll contact you within 2 hours during clinic      │
│    hours (10 AM – 1 PM & 5 PM – 8 PM)."               │
│                                                         │
│   Two buttons below:                                   │
│   [WhatsApp for Faster Response]  [← Back to Home]    │
└────────────────────────────────────────────────────────┘
    
┌─ ERROR ────────────────────────────────────────────────┐
│ Show inline error message below button:                │
│   "Something went wrong. Please try again or           │
│    contact us on WhatsApp."                            │
│ Re-enable the submit button                            │
└────────────────────────────────────────────────────────┘
```

### Form Validation

- Use native HTML5 validation attributes (`required`, `type`, `min`, `maxlength`)
- Show validation errors using browser-native validation UI (no custom JS validation needed)
- The "Contact Number / Email" field: use `type="text"` with a custom pattern or keep it simple — just require non-empty input. Web3Forms will receive whatever the user types.

### Spam Protection

- Web3Forms built-in honeypot: add a hidden checkbox field with `name="botcheck"`
- No CAPTCHA needed (Web3Forms handles this on free tier)

---

## 5. Page Layout & Design Direction

### Visual Structure (Top to Bottom)

```
┌──────────────────────────────────────────────────────────┐
│  NAVBAR (same as index.html — shared component)          │
│  Logo | Home About Services Reviews Gallery Contact      │
│                                        [📞 Call Now]     │
├──────────────────────────────────────────────────────────┤
│                                                          │
│              📅 BOOK AN APPOINTMENT                      │
│                                                          │
│  ┌─────────────────────┐  ┌────────────────────────────┐ │
│  │                     │  │                            │ │
│  │   THE FORM          │  │   CLINIC INFO SIDEBAR      │ │
│  │                     │  │                            │ │
│  │   Full Name         │  │   📍 Address               │ │
│  │   Contact / Email   │  │   📞 Phone numbers         │ │
│  │   Date of Visit     │  │   📧 Email                 │ │
│  │   Reason (dropdown) │  │   🕐 Working Hours         │ │
│  │   Description       │  │   💬 WhatsApp link         │ │
│  │                     │  │                            │ │
│  │   [Book Appointment]│  │   📍 Google Maps embed     │ │
│  │                     │  │      (small, optional)     │ │
│  └─────────────────────┘  └────────────────────────────┘ │
│                                                          │
├──────────────────────────────────────────────────────────┤
│  FOOTER (same as index.html — shared component)          │
└──────────────────────────────────────────────────────────┘
```

### Design Rules

- **Two-column layout** on desktop (`md:grid-cols-5` — form gets 3 cols, sidebar gets 2 cols)
- **Single-column stacked** on mobile (form first, then sidebar info)
- Use the **same color scheme, fonts, and styling** as index.html (teal-600 primary, Inter font, Playfair Display for headings)
- Form card: white background, rounded-2xl, subtle border and shadow (same as service cards)
- Submit button: full-width, teal-600 bg, same style as other CTAs
- Page background: same gradient as hero or plain stone-50

---

## 6. Tech Stack & Integration

### Form Backend: Web3Forms (Free Tier)

- **Endpoint**: `https://api.web3forms.com/submit`
- **Method**: POST (standard HTML form submission, enhanced with JS `fetch` for AJAX)
- **Hidden fields to include**:
  - `access_key` — the Web3Forms API key (placeholder: `YOUR_WEB3FORMS_ACCESS_KEY`)
  - `subject` — "New Appointment Request — Dr. Amrata Dental Clinic"
  - `from_name` — "Dr. Amrata Dental Clinic Website"
  - `redirect` — leave empty (we handle success in JS)
  - `botcheck` — honeypot checkbox, hidden with `style="display:none"`

### Form Submission: Use JavaScript `fetch` (AJAX)

- Do NOT use standard HTML form POST (it redirects the page)
- Use `fetch()` with `FormData` to submit asynchronously
- Handle the response in JS to show success/error states
- Add the form handler logic in `assets/js/main.js` — wrap it so it only runs on the appointment page (check if the form element exists before attaching the listener)

### Date Picker: Native HTML

- Use `<input type="date">` — works on all modern browsers
- Set `min` attribute to today's date using JavaScript on page load
- No external date picker library needed

---

## 7. SEO Requirements for the New Page

### Head Section — Must Include All of These

| Tag | Value |
| ----- | ------- |
| `<title>` | "Book Appointment — Dr. Amrata Dental Clinic \| Jagatpura, Jaipur" |
| `<meta name="description">` | "Book your dental appointment with Dr. Amrata Srivastava at our Jagatpura, Jaipur clinic. Specializing in Root Canal, Implants, Crown & Bridges. Call +91 85918 91766 or fill the form." |
| `<meta name="keywords">` | "book dentist appointment Jagatpura, dental appointment Jaipur, Dr Amrata appointment, dentist near me booking" |
| `<link rel="canonical">` | `https://www.amratadentalclinic.com/appointment.html` |
| OG title | "Book Appointment — Dr. Amrata Dental Clinic" |
| OG description | Same as meta description |
| OG image | `/images/og-image.png` (same as main page) |
| OG url | `https://www.amratadentalclinic.com/appointment.html` |
| `<meta name="theme-color">` | `#0D9488` |
| Favicon links | Same set as index.html |
| Google Fonts | Same `<link>` tags as index.html |
| Tailwind CDN | Same `<script>` tag as index.html |
| Lucide Icons | Same `<script>` tag as index.html |
| Custom CSS | `<link rel="stylesheet" href="assets/css/style.css">` |

### JSON-LD Schema for Appointment Page

Add a `ContactPage` schema:

```json
{
  "@context": "https://schema.org",
  "@type": "ContactPage",
  "name": "Book Appointment — Dr. Amrata Dental Clinic",
  "description": "Online appointment booking form for Dr. Amrata Dental Clinic, Jagatpura, Jaipur.",
  "url": "https://www.amratadentalclinic.com/appointment.html",
  "mainEntity": {
    "@type": "Dentist",
    "name": "Dr. Amrata Dental Clinic",
    "telephone": ["+918591891766", "+919057064789"],
    "address": {
      "@type": "PostalAddress",
      "streetAddress": "O2, Buttercup Apartment, Vinayak Enclave, Way to VIT Campus",
      "addressLocality": "Jagatpura",
      "addressRegion": "Rajasthan",
      "postalCode": "302017",
      "addressCountry": "IN"
    }
  }
}
```

---

## 8. Navigation Updates (Both Pages)

### Changes Needed in `index.html`

1. **Hero "Appointment Form" button** — should link to the new page:
   - Change `href="#contact"` to `href="appointment.html"`

2. **Navbar "Contact" link** — keep pointing to `#contact` section on index.html (the contact info section stays on the main page)

3. **Add "Book Appointment" to footer quick links** — link to `appointment.html`

### Navigation in `appointment.html`

- Navbar links should point BACK to the main page sections:
  - Home → `index.html#home`
  - About → `index.html#about`
  - Services → `index.html#services`
  - Reviews → `index.html#reviews`
  - Gallery → `index.html#gallery`
  - Contact → `index.html#contact`
  - Call Now → `tel:+918591891766`

---

## 9. Sitemap Update

After creating the page, update `sitemap.xml` to include:

```xml
<url>
  <loc>https://www.amratadentalclinic.com/appointment.html</loc>
  <lastmod>2026-09-26</lastmod>
  <changefreq>monthly</changefreq>
  <priority>0.9</priority>
</url>
```

---

## 10. Implementation Steps (In Order)

| Step | Task | Files Affected |
| ------ | ------ | --------------- |
| **1** | Create `appointment.html` with full `<head>` section (copy from index.html, update title/meta/canonical/OG tags) | `appointment.html` [NEW] |
| **2** | Build the navbar in appointment.html (same markup as index.html but update nav links to point to `index.html#section`) | `appointment.html` |
| **3** | Build the page heading section ("Book an Appointment" with a subtitle) | `appointment.html` |
| **4** | Build the form (left column) with all 5 fields, honeypot, hidden Web3Forms fields, and submit button | `appointment.html` |
| **5** | Build the sidebar (right column) with clinic address, phones, email, working hours table, and optional small Google Maps embed | `appointment.html` |
| **6** | Build the footer (same as index.html) | `appointment.html` |
| **7** | Add the mobile menu (same as index.html, but with updated links) | `appointment.html` |
| **8** | Add form submission handler in `main.js` — AJAX fetch to Web3Forms, loading state, success/error UI, date picker min date | `assets/js/main.js` |
| **9** | Add form-specific styles in `style.css` — focus states, success card, error message styling, "Others" highlight behavior | `assets/css/style.css` |
| **10** | Update hero CTA in `index.html` — change "Appointment Form" button href from `#contact` to `appointment.html` | `index.html` |
| **11** | Add "Book Appointment" link in footer quick links on both pages | `index.html`, `appointment.html` |
| **12** | Update `sitemap.xml` with the new page URL | `sitemap.xml` |
| **13** | Call `lucide.createIcons()` at the bottom of `appointment.html` body (same as index.html) | `appointment.html` |

---

## 11. Acceptance Criteria

- [ ] Page loads at `/appointment.html` without errors
- [ ] All 5 form fields render correctly with proper labels and placeholders
- [ ] "Full Name" and "Contact Number / Email" are required; form won't submit without them
- [ ] Date picker doesn't allow past dates
- [ ] Dropdown shows all 7 service options + empty placeholder
- [ ] Selecting "Others" highlights the description field
- [ ] Submit button shows loading state during submission
- [ ] Success message appears after submission with WhatsApp + Home buttons
- [ ] Error message shows if submission fails
- [ ] Navbar links navigate back to correct sections on index.html
- [ ] Page is fully responsive (form stacks above sidebar on mobile)
- [ ] All SEO tags are present and valid (run through meta tag validator)
- [ ] Form submits to Web3Forms endpoint (test with placeholder key first)
- [ ] No console errors on page load or form submission
- [ ] Lucide icons render correctly
- [ ] Google Maps embed loads (if included in sidebar)
- [ ] Page shares correctly on WhatsApp/social (OG tags working)
