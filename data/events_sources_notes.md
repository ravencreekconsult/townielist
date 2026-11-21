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

## Adapter handoff notes (Nov 20, 2026, 1:10pm) - for the weekly/durable agent
- Actors Cabaret of Eugene (actorscabaret.org/shows): Wix. The /shows HTML fetches fine with a browser UA (610KB) but contains no plain-text event list and no ld+json; event data lives in the Wix bootstrap JSON embedded in the page (look for window.__INITIAL_STATE__ / "events" arrays) or via Wix Data /_api/ collections. Parse that, or fall back to Tier-B hand-curation from their shows page via cloud browser (per Matthew's secondary-source rule).
- Cottage Theatre (cottagetheatre.org, Cottage Grove - registry town fixed from Eugene): static server-rendered. Homepage shows productions with date ranges ("December 4 - 20", "Tickets go on sale November 4"); per-production pages (e.g. /come-from-away.html) carry the performance dates/times. Adapter sketch: fetch homepage, extract production page links, fetch each, parse show datetimes (watch for matinee/evening time lists), venue "Cottage Theatre".
- McDonald Theatre adapter (mcdonald_html in fetch_events.py): WORKING since Nov 20. Honors robots Crawl-delay: 10 and caches show times in mcdonald_cache.json - do not delete that file; without it every run re-fetches every event page at 10s each.
- Cuthbert Amphitheater (cuthbert-curated): weekly, open mammothnw.com/venue/cuthbert-amphitheater/ in the cloud browser and hand-update cuthbert_curated.json. cuthbert.com is bot-blocked on every route tried.


## 2026-11-20 (afternoon): Oregon Contemporary Theatre + Eugene Weekly added

### Oregon Contemporary Theatre (key: octheatre, adapter: curated -> oct_curated.json)
- Site 403s automated fetches; facts gathered via a normal browser session (Matthew-approved blocked-source workaround).
- Current season: 3 productions, 9 performances Nov 20 - Dec 19, 2026. Showtimes from their ticketing pages
  (app.arts-people.com/index.php?show=<id>) which DO respond to curl: See Jane Run (show 351376),
  Oy Vey In A Manger (351377), A Charlie Brown Christmas (351378). OCT pages give date ranges only.
- Refresh: browse https://www.octheatre.org/ for current productions, then curl each arts-people show page for
  exact datetimes; rewrite oct_curated.json. allow_empty is set (between seasons).

### Eugene Weekly (key: eugene-weekly, adapter: cityspark)
- eugeneweekly.com/calendar is a CitySpark widget; robots.txt permissive; public JSON endpoint the page itself calls:
  POST https://portal.cityspark.com/api/events/GetEventsByDay/EugeneWeekly
  body {"ppid":10212,"lat":44.05,"lng":-123.09,"distance":30,"start":"YYYY-MM-DD","daysToLoad":1,"eventsPerDay":50}
  ppid 10212 is the EugeneWeekly portal id (from cSparkLocals in the widget bundle). No auth.
- Adapter loops day-by-day (eventsPerDay caps at 50; days with >50 events lose the tail - acceptable), 0.3s between
  calls, keeps Lane County towns only, drops csVirtual/csRemote. Event url = PrimaryUrl (official organizer page);
  falls back to the EW calendar URL. ~580 local events over 90 days (542 after cross-source dedupe).
- Full fetch_events.py now runs ~2.5-3 min because of the day loop. ONLY_SOURCES=key1,key2 env var fetches a subset
  and MERGES into events.json (keeps unfetched sources' events) - use for quick single-source refreshes.
- The widget endpoint GetEvents/EugeneWeekly (no ByDay) returns national promoted filler unless ppid+lat/lng are sent;
  even then it paginates oddly. GetEventsByDay is the reliable one.

### Shared fix: dedupe key now (normalized title, start to the minute) instead of (title, date) -
  was collapsing same-day double shows (e.g. OCT's Dec 19 2pm + 7:30pm Charlie Brown).
