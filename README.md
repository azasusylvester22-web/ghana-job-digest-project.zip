# Ghana IT/Tech Jobs — Daily Email Digest

An automated n8n workflow that scrapes daily IT/Software/Tech job listings from
multiple Ghanaian job boards, filters and deduplicates them, and emails a clean
digest every morning — so you don't have to manually check job sites every day.

## How it works

```
Schedule Trigger (7am daily)
        │
        ├──> Fetch Jobberman (Software & Data category)  ──> Parse listings
        │
        └──> Fetch JobWebGhana (IT/Telecom category)      ──> Parse listings
                                │
                                ▼
                        Merge both sources
                                │
                                ▼
              Filter for IT/tech keywords + remove
              duplicates already sent in past runs
                                │
                                ▼
                 Only continue if new jobs found
                                │
                                ▼
                     Format HTML digest email
                                │
                                ▼
                          Send via SMTP
```

## Sources

- **[Jobberman Ghana](https://www.jobberman.com.gh/jobs/software-data)** — Software & Data category
- **[JobWebGhana](https://jobwebghana.com/job-category/it-telecom-jobs-in-ghana/)** — IT/Telecom category

Both are parsed using pattern-matching on their job-listing URL structure rather
than fragile CSS class selectors, which makes the scraper more resilient to
minor site redesigns.

## Key features

- **Deduplication across runs** — uses n8n's workflow static data to remember
  which job links have already been sent, so you never get the same listing twice
- **Keyword filtering** — easily customizable regex in the "Filter IT Jobs & Remove
  Duplicates" node to match your target role/industry
- **Fails gracefully** — if no new jobs are found on a given day, no email is sent
  (rather than sending an empty digest)

## Setup

1. Import `workflow.json` into your n8n instance (Cloud or self-hosted)
2. Add your own SMTP credentials to the **Send Email** node (Gmail App Password,
   or any SMTP provider)
3. Set `fromEmail` / `toEmail` in the **Send Email** node to your own address
4. Adjust the keyword filter in **Filter IT Jobs & Remove Duplicates** to match
   your target role/industry
5. Test with **Execute Workflow**, then publish/activate to run on schedule

## Customizing for other roles or locations

- Swap the source URLs for different job categories on the same sites
- Add more sources by duplicating a Fetch + Parse node pair and feeding into
  the same Merge node
- Change the Schedule Trigger's `triggerAtHour` to adjust delivery time
- Swap the Send Email node for a Telegram/Slack node for different delivery channels

## Tech stack

- [n8n](https://n8n.io) — workflow automation
- JavaScript (Code nodes) — HTML parsing, filtering, deduplication logic
- SMTP — email delivery

---

Built to solve a real daily job-search need: automatically surfacing new IT/tech
listings in Ghana without manually re-checking multiple job boards.
