# Townie List source data

Snapshot of the exact inputs used for the current build. Public data, mirrored for reproducibility.

- Overture Maps places, release 2026-11-18.0 (theme=places), Eugene-Springfield metro bbox -123.45..-122.75, 43.75..44.35. License CDLA-Permissive. Pulled 2026-11-18.
- Oregon CCB license records: https://data.oregon.gov/api/views/g77e-6bhs/rows.csv (public). Pulled 2026-11-19; matched rows are merged into listings.json / bulk_listings.json (full 14 MB dump not mirrored; re-pull from source).
- listings.json: original curated trades (plumbing, HVAC, tree services).
- bulk_listings.json + bulk_candidates_clean.json: 7 new trades from the Overture+CCB bulk build approved 2026-11-19.
- auto_repair.json: independent auto repair, body, glass, tire and detailing shops from a full Lane County Overture pull (release 2026-11-18.0, bbox -124.2..-122.5, 43.55..44.30, 21,423 places, pulled 2026-11-20). Chains excluded; same-address former-name dupes resolved to the stronger record.
- category_counts.tsv: full per-category place counts from that county pull - the supply side of category research.
- restaurants.json: independent restaurants by cuisine from the same county pull (669 spots, 11 towns). Coffee/cafe and food-truck categories excluded (own directories); bars, wineries and dessert excluded (future categories). Chains dropped; same-address former-name dupes resolved; names already on the coffee page removed.
- hair_salons.json, barbershops.json, massage.json, chiropractors.json, tattoo.json, dentists.json, vets.json, locksmiths.json, towing.json, accountants.json, gyms.json, yoga.json: independent personal-services directories from the same county pull (build_services.py). Chains dropped (including Advantage Dental and Motto Clear Aligners as regional/national dental brands; CrossFit affiliates and YMCA out of gyms under the national-brand rule); same-address dupes resolved.
