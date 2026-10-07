# Assets

Screenshots and diagrams for the journal live here. Current images: `control-centre-overview.png` (security panel blanked), `docker-maintenance.png` (cropped) and `invoicing-pipeline.png`. Originals are kept outside the repo.

**Being in `assets/` does not make an image safe to publish.** Review every image by hand before committing it, and look for:

- IP addresses and internal hostnames
- usernames and email addresses
- customer information and serial numbers
- tokens, API keys and certificates
- browser tabs, bookmarks and URL bars
- terminal history and prompts
- notifications and QR codes
- hidden metadata (EXIF, document properties). Strip it, or re-export the image.

Prefer a redrawn diagram over a screenshot. Crop to the minimum needed. If in doubt, leave it out.
