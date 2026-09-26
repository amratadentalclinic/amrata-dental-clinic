# Services Section — Replace Icons with Treatment Images

## Prompt for IDE

---

Redesign the **Services section** (`#services`) in `index.html` (around lines 389-447) to replace the current Lucide SVG icons with actual treatment images. Do NOT touch any other section.

### What to Change

Each of the 6 service cards currently has a small Lucide icon inside a `<div class="service-icon ...">` container. Replace that icon container with an `<img>` tag using the treatment images from the `treatment-icons/` folder.

### Exact Image-to-Service Mapping

| Service Card | Current Lucide Icon | Replace With Image | Alt Text |
|---|---|---|---|
| Root Canal Treatment | `heart-pulse` | `treatment-icons/root-canal-treatment.png` | "Root Canal Treatment illustration" |
| Dental Implants | `pin` | `treatment-icons/dental-implants.png` | "Dental Implants procedure illustration" |
| Dentures | `smile` | `treatment-icons/dentures.png` | "Dentures illustration" |
| Crown & Bridges | `crown` | `treatment-icons/crown-and-bridges.png` | "Crown and Bridges dental illustration" |
| Teeth Whitening | `sun` | `treatment-icons/teeth-whitening.png` | "Teeth Whitening illustration" |
| General Dentistry | `stethoscope` | `treatment-icons/general-dentistry.png` | "General Dentistry illustration" |

### Design Direction

- **Image size in card**: Display each image at `w-20 h-20` (80×80px) or `w-24 h-24` (96×96px) — choose whichever looks better proportionally inside the card. The images are 200×200 source so they'll be crisp at this display size.
- **Image styling**: Apply `rounded-xl` (rounded corners) and `object-cover` to each image. Add a very subtle `border border-stone-100` around the image for definition.
- **Remove the old icon container**: Delete the entire `<div class="service-icon w-14 h-14 ...">` wrapper and its `<i data-lucide="...">` child. Replace with the `<img>` tag directly.
- **Add `loading="lazy"`** to all 6 images since they're below the fold.
- **Add `width="96" height="96"`** attributes on each `<img>` for CLS prevention.
- **Keep all existing**: card hover effects, card classes, service titles (`<h3>`), service descriptions (`<p>`), the section heading, the section divider, the grid layout, and the `fade-up` animation classes.
- **Keep the card layout structure** the same — image at top, title below it, description below title.

### CSS Changes

In `assets/css/style.css`, the existing `.service-icon` and `.service-card:hover .service-icon` rules (around lines 106-113) that change the icon background gradient on hover are no longer needed for the SVG icon. Update the hover behavior:

- On card hover, add a subtle `transform: scale(1.05)` to the treatment image for a micro-interaction.
- Add a new CSS rule: `.service-card img { transition: transform 0.3s ease; }` and `.service-card:hover img { transform: scale(1.05); }`
- Keep the existing `.service-card:hover` translateY and shadow rules as-is.
- You can remove `.service-card:hover .service-icon svg { stroke: white; }` since there's no SVG icon anymore.

### Constraints

- Only modify the Services section in `index.html` (the `<section id="services">` block).
- Only add/modify CSS rules related to `.service-card img` in `style.css`.
- Do NOT change the grid layout (`sm:grid-cols-2 lg:grid-cols-3`).
- Do NOT change section headings, descriptions, or `transition-delay` values on cards.
- Do NOT remove the `fade-up` class from any card.
- All 6 images must use `loading="lazy"` since this section is below the fold.
- Keep the images as `<img>` tags, NOT as CSS `background-image`.

---
