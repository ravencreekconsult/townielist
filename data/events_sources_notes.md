# Wider-region events source research (Nov 20)
## WORKING (feed/API)
- City of Florence: CivicPlus RSS https://www.ci.florence.or.us/RSSFeed.aspx?ModID=58&CID=All-0 (200 XML) - same adapter as Eugene/Veneta
- City of Lowell: CivicPlus RSS https://www.lowelloregon.gov/RSSFeed.aspx?ModID=58&CID=All-0 (200 XML, small)
- City of Albany: iCal https://albanyoregon.gov/_coa/calendar/subscribe.php (BEGIN:VCALENDAR, all public meetings+events; per-category ?cag= variants exist)
- Albany Visitors Assoc (DMO): server-rendered HTML list https://albanyvisitors.com/events/ paginated ?s=10&view=agenda, date headers "Friday, Nov 20, 2026", /eventdetail/ID/slug links. Needs html adapter.
## EXISTING CORE: Springfield tribe, Lane Events Center tribe, Farmers Market tribe, UO localist, City of Eugene RSS, City of Veneta RSS, Springfield Library ical
## GAPS / BLOCKED (reason)
- Cottage Grove city: client-rendered, RSS 404 (known dead)
- Coburg city: Cloudflare challenge 403 on RSS (coburgoregon.gov)
- Oakridge city (ci.oakridge.or.us): Cloudflare challenge 403
- Oakridge chamber (oakridgeoregon.org): WP but no tribe endpoint (rest_no_route); check events page HTML
- Junction City: no CivicPlus; old govoffice3 index.asp server-rendered calendar at /index.asp?Type=NONE&SEC={1447AA7B-62E8-4B5A-8747-7A7F323446F5}&month=M&year=Y (#cal{DBBBC137-B177-4F33-B93F-6C93B7E722F4}) - HTML parse possible, terms-gray
- Hult Center: WP, no tribe; find real events path
- The Shedd: no tribe (XHTML site); find events path
- Eugene Saturday Market: WP, no tribe
- Willamalane: not WP; find events platform
- Travel Lane County (eugenecascadescoast.org): 403 block
- Florence Chamber: 403 block
- Cottage Grove Chamber: /events/ 404; find real path
- LCC: calendar.lanecc.edu doesn't resolve; find real calendar host
## TODO probe: Shedd/Hult real paths, LCC, Eugene Public Library, school districts (4J/Bethel/Sps), JC chamber, Oregon Country Fair, River Road Park & Rec, McDonald Theatre, WOW Hall, Cottage Theatre, Albany carousel/civic theater
## DESIGN
- event_sources.json registry: [{key,name,town,scope(local|wider),category,adapter(tribe|localist|eugene_rss|ical|html_albany|...),url,feed,attribution_url,status}]
- fetch_events.py reads registry; per-event scope: local=Eugene/Springfield, wider=everything else
- dedupe key: normalize(title)+date+normalize(venue); merge keeps sources list
- events page: Local only / Wider region toggle (default Local); keep venue/type/town filters
## Round 2 probe results (11:01am)
- Eugene Ballet: WP with tribe_events CPT but REST empty ([]) - check season page HTML or flag
- McDonald Theatre: no events CPT (Avada); /events/ page has ld+json but @type None (check @graph); HTML list parse possible
- Hult Center: WP, no events CPT; homepage ld+json; need real events path
- WOW Hall: 406 block; Cottage Theatre: 200 plain HTML (parse possible); Eugene Ballet season TBD
- eugene-or.gov/538/Library: 403 via curl (library events maybe in City RSS already)
## Build round 1 SHIPPED (11:10am) - pending deploy
- event_sources.json registry: 24 sources (11 active, 11 gap w/ reasons, 2 planned)
- fetch_events.py registry-driven; new albany_visitors_html adapter; dedupe (norm title+date, sources merged with ' + '); scope local/wider; allow_empty flag (Florence)
- 419 events: 388 local (Eug/Spr), 31 wider (Veneta 4, Albany 25, Lowell 2); Florence valid-empty
- Albany city iCal dropped to gap: trailing 6-week window, all past meetings
- generate.py: Local only / Wider region toggle (default Local), sc field, dynamic source-count intro; JS checked with node
- New pages: events/albany/ (25), events/lowell/ (2, noindex)

## Cuthbert Amphitheater (Tier B, curated — added Nov 20, 2026)
- cuthbert.com unreachable from both the cloud browser (provider_unreachable) and direct fetch (WAF/bot block), so no automated feed and their terms page could not be read. Facts are hand-curated in cuthbert_curated.json from mammothnw.com event pages (Kesey Enterprises & Mammoth NW, the venue's concert producer). mammothnw.com robots.txt is fully permissive and no terms page exists at the standard paths.
- Each entry links out to its own mammothnw.com event page. Weekly refresh: open the Cuthbert venue page on mammothnw.com in the cloud browser and hand-update the curated file; never scrape cuthbert.com.
- Pink Martini page existed but was the Sep 4, 2026 show (past); excluded.
