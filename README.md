# How to Scrape The Muse Job Postings (Public API, No Login)

> **Run it now, no code: [themuse-jobs-scraper on the Apify Store](https://apify.com/bibian/themuse-jobs-scraper)** &nbsp;|&nbsp; $2.00 per 1,000 jobs &nbsp;|&nbsp; no login, no browser

The Muse lists thousands of jobs from companies that care about culture and career growth, searchable by category, experience level, location and company. It runs on an official public JSON API, so this guide shows how to pull it as clean structured data with a free Apify Actor, from the console or from code — no scraping, no HTML parsing.

Searches this page answers: the muse scraper, themuse jobs API, the muse job postings data, scrape themuse.com.

## What you get from the The Muse scraper

- Every job matching your filters: title, hiring company, locations, categories and experience levels.
- The full job description as plain text, optional per run.
- Filter by category, level, location and company exactly as The Muse's own site accepts them — narrow the filters to reach past the API's own ~2,000-result pagination cap.
- Publication date and the canonical `url` back to the job on themuse.com.

A real sample (five results, long descriptions truncated) is in [`sample-output.json`](sample-output.json).

## Output fields

| Field | What it is |
|---|---|
| `id`, `url` | The Muse job id and public link |
| `title` | Job title |
| `companyName` | Hiring company as displayed on The Muse |
| `locations` | Array of locations the job is open to (a job can list several) |
| `categories` | The Muse job categories, for example `Software Engineering` or `Data and Analytics` |
| `levels` | Experience levels, for example `Entry Level`, `Mid Level`, `Senior Level` |
| `publishedAt` | ISO timestamp the posting went live |
| `description` | Full job description as plain text, only when `includeDescription` is enabled |

## Use cases

- **Watch a category across companies.** Filter `category` to `Software Engineering` or `Design` and track every new posting without checking the site.
- **Compare levels and locations at once.** `levels` and `locations` are arrays, so one job already tells you every level/location combination it is open to.
- **Build a niche job board or newsletter.** Use the structured fields and the canonical `url` to power a filtered board for a category or region The Muse's own search doesn't expose as a feed.
- **Track a specific employer.** Filter `company` to watch when a given employer posts new roles.

## Input

| Field | What it does |
|---|---|
| `category` | Keep only jobs matching one of these categories, exactly as The Muse's own filter accepts (e.g. `Software Engineering`). Empty means every category. |
| `level` | Keep only jobs matching one of these experience levels (e.g. `Senior Level`). Empty means every level. |
| `location` | Keep only jobs matching one of these locations, exactly as shown on The Muse (e.g. `New York, NY` or `Flexible / Remote`). Empty means every location. |
| `company` | Keep only jobs posted by one of these companies, exactly as named on The Muse. Empty means every company. |
| `maxItems` | The run stops once this many jobs are saved (default 50). The API paginates about 2,000 results per filter combination, so narrow the filters to reach beyond that. |
| `includeDescription` | Adds the job's full description as plain text (default on). |

Example input:

```json
{
  "category": ["Software Engineering"],
  "level": [],
  "location": [],
  "company": [],
  "maxItems": 100,
  "includeDescription": false
}
```

## Three ways to use it

### 1. Apify console, no code

1. Open [the Actor page](https://apify.com/bibian/themuse-jobs-scraper) and click **Try for free** (a free Apify account is enough).
2. Fill in the filters you want (category, level, location, company) and the maximum number of results, then click **Start**.
3. When the run finishes, open the **Output** tab, check the table, then **Export** as CSV, Excel or JSON.
4. For ongoing monitoring, open **Schedules**, create a daily or weekly schedule for the Actor and, optionally, add an integration to send new results to Slack, e-mail or Google Sheets.

### 2. Apify API with JavaScript

```bash
npm install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
node examples/run.js
```

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });

const run = await client.actor('bibian/themuse-jobs-scraper').call({
    "category": ["Software Engineering"],
    "level": [],
    "location": [],
    "company": [],
    "maxItems": 100,
    "includeDescription": false
});

const { items } = await client.dataset(run.defaultDatasetId).listItems();
for (const item of items) {
    console.log(`${item.title} | ${item.companyName} | ${item.locations.join(', ')}`);
}
```

The complete file is [`examples/run.js`](examples/run.js) (ES module, Node 18 or later).

### 3. Apify API with Python

```bash
pip install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
python examples/run.py
```

```python
import os
from apify_client import ApifyClient

client = ApifyClient(os.environ["APIFY_TOKEN"])

run = client.actor("bibian/themuse-jobs-scraper").call(run_input={
    "category": ["Software Engineering"],
    "level": [],
    "location": [],
    "company": [],
    "maxItems": 100,
    "includeDescription": False,
})

for item in client.dataset(run.default_dataset_id).iterate_items():
    print(f"{item['title']} | {item.get('companyName')} | {', '.join(item.get('locations', []))}")
```

The complete file is [`examples/run.py`](examples/run.py). Never put your token in the code: read it from the `APIFY_TOKEN` environment variable as above.

## Pricing

Pay per result: **$2.00 per 1,000 jobs** (that is $0.002 per job), plus a tiny start fee of $0.00005 per run. You pay only for the results saved to the dataset. Apify's free plan includes monthly credits, enough to try it on a few hundred results. Prices are those shown on the [Store page](https://apify.com/bibian/themuse-jobs-scraper) when this guide was written; the Store page is the reference.

## Limits and honest notes

- The API itself paginates roughly 2,000 results (100 pages) per filter combination. To go beyond that, narrow with `category`, `level`, `location` or `company` and run several combinations.
- Only what The Muse publishes is collected; the dataset never carries a reachable recruiter contact.
- This is an unofficial tool, not affiliated with The Muse. It reads the same public JSON API (`themuse.com/api/public/jobs`) that powers the site's own search.

## FAQ

**Does it need a browser or a login?**
No. The Actor reads The Muse's own public JSON API directly.

**Does it work without any filter?**
Yes. With no input at all it saves up to 50 of the newest jobs across every category.

**Can I combine several filters?**
Yes. `category`, `level`, `location` and `company` all combine, and each field accepts several values at once.

**Can I get the data in Excel or Google Sheets?**
Yes. Export the dataset as CSV, Excel or JSON from the Apify console, or connect the Apify Google Sheets integration.

## Try it

**[Open themuse-jobs-scraper on the Apify Store](https://apify.com/bibian/themuse-jobs-scraper)**

The code in `examples/` is released under the MIT license (see [LICENSE](LICENSE)). The data belongs to its original publishers.
