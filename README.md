# heroku alternatives: what Render, Railway and Fly.io really cost — and the self-hosted Coolify route on Vultr

Heroku made deployment feel like one command, and fifteen years later every platform on the market is still measured against it. What has changed is the bill and the roadmap: the free tier disappeared in November 2022, dynos are priced per process, and in February 2026 Salesforce confirmed Heroku is moving to a sustaining engineering model — it keeps your app running and patched, and stops shipping new features. That leaves three honest ways to replace it: managed platforms like Render and Railway that copy the Heroku shape, edge platforms like Fly.io, and the self-hosted route — Coolify or Dokku on a cheap VPS — which is where the math gets genuinely interesting.

This article covers all three with current prices, then does something most roundups skip: it costs out the self-hosted option line by line, using Vultr as the VPS layer.

> **Quick pick:** if you never want to see a server again, Render or Railway will feel the most familiar — expect a bill shaped like Heroku's, just smaller. If you're comfortable with one Linux box, a **$10/month Vultr instance** running Coolify or Dokku replaces your web dyno, worker, Postgres and Redis in a single line item. 👉 [Check Vultr's current plans and the new-user credit](https://www.vultr.com/pricing/?ref=7268283)

## Why everyone is re-checking their Heroku bill right now

The trigger was a February 6, 2026 post from Nitin T Bhat, the Salesforce SVP and GM responsible for Heroku: the platform is transitioning to a sustaining engineering model focused on stability, security, reliability and support. Existing customers keep their pricing and service, and current enterprise contracts will be honored — but no new enterprise contracts are being sold. Read the list of what Heroku will keep doing and one word is missing: features.

The pricing does the rest of the pushing. Eco dynos cost $5/month for a shared pool of 1,000 dyno hours and sleep after 30 minutes without traffic. Always-on Basic dynos run $7 each, per app, per process — a web process plus a background worker is already $14 before anything else. Standard-1X is $25/month for 0.5 GB of RAM, Standard-2X is $50 for 1 GB, and then the ladder jumps straight to Performance-M at $250. There is nothing in between. Add Postgres (Essential-0 at $5/month for 1 GB) and Redis (from about $15/month as an add-on) and a modest production app lands somewhere between $50 and $100/month.

That gap — $50 to $250 in one step — is what usually starts the migration conversation, more than the February announcement itself.

## The managed PaaS field at a glance

Four platforms cover most Heroku migrations, and each keeps a different part of the Heroku experience while changing the billing model.

| Platform | What it replaces on Heroku | Starts at | Free tier | The catch |
| --- | --- | --- | --- | --- |
| Render | Web services, workers, cron, managed Postgres | $7/mo per service (512 MB) | Yes — sleeps after 15 min | Costs stack per service, like Heroku |
| Railway | Dynos become metered compute | ~$20/vCPU-month + $10/GB-month | $5 one-time trial credit | The bill moves with traffic |
| Fly.io | Dynos become microVMs in 30+ regions | ~$2/mo (256 MB) | Limited | Most hands-on of the four |
| DigitalOcean App Platform | App Platform | $5/mo per container | Static sites (3 free apps) | Databases are a separate product |

**Render** is the closest mental match: connect a repo, get builds on every push, with web services, background workers, cron jobs and managed Postgres (from $6/month, Redis from $10) under one roof. It's actively courting Heroku users, with migration credits of up to $10,000 and a flat $25/month Pro workspace for teams. The trade-off is familiar: every service and database is its own line item, so the bill grows the way Heroku's does, just from a lower base. The free tier sleeps after 15 minutes of inactivity and takes about a minute to wake — fine for demos, wrong for anything a customer touches.

**Railway** swaps fixed dyno tiers for a meter: roughly $20 per vCPU-month and $10 per GB-month, billed per second, plus $0.05/GB of egress. Hobby is $5/month including $5 of usage; Pro is $20 including $20. For bursty workloads the meter works in your favor; a 24/7 web app with Postgres lands around $17/month. The trade-off is predictability — an idle-but-running service still costs money every second, and a traffic spike shows up on the invoice.

**Fly.io** runs your app as Firecracker microVMs in 30+ regions, with per-second billing and genuinely cheap compute: $2.02/month for a 256 MB shared-CPU machine, $5.92 for 1 GB. Two things temper it. Managed Postgres starts at $38/month for the Basic cluster, so the database can cost six times the app it serves unless you self-manage. And it's the most hands-on option here — you'll edit `fly.toml`, think about machines and regions, and work from a terminal. Fly has also repositioned its messaging around AI agent workloads, which is a reasonable bet but changes the product focus you'd be migrating toward.

**DigitalOcean App Platform** is the plain-value option: static sites free, containers from $5/month, managed databases sold separately. Predictable pricing, no drama, less Heroku-style polish around previews and add-ons.

## The route most roundups skip: Coolify or Dokku on a VPS

The cheapest Heroku alternative isn't a managed platform — it's a $5–10/month VPS running open-source tooling, and most comparisons mention this in exactly one sentence before moving on. Here's that sentence expanded.

Two tools dominate this route. **Dokku** is a minimal, single-server Heroku clone: you add a git remote, push, and buildpacks do the rest — it literally restores the `git push` workflow people miss. **Coolify** is the friendlier option: a web UI over Docker, Git-connected builds on push, preview environments for pull requests, and one-click databases (Postgres, Redis, MySQL and friends). Both are free; you pay only for the box they run on.

This is where Vultr fits the picture. Vultr is an infrastructure provider — it sells compute and managed services rather than a finished PaaS — and both Coolify and Dokku ship as one-click apps in its Marketplace. You pick the image at deploy time and have a working server with either installed in a few minutes. Billing is hourly, so a throwaway test box costs cents and gets deleted the same day.

## What a Vultr server costs: all current Cloud Compute plans

The regular Cloud Compute line starts at $2.50/month for 512 MB, and the full tier list looks like this. Prices are Vultr's listed monthly rates; tiers occasionally shift, so treat the live pricing page as the source of truth.

| Instance | vCPUs | SSD | Included transfer | Price/mo | Deploy |
| --- | --- | --- | --- | --- | --- |
| 512 MB | 1 | 10 GB | 0.5 TB | $2.50* | [ Deploy this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 1 GB | 1 | 25 GB | 1 TB | $5 | [ Deploy this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 2 GB | 1 | 55 GB | 2 TB | $10 | [ Deploy this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 4 GB | 2 | 80 GB | 3 TB | $20 | [ Deploy this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 8 GB | 4 | 160 GB | 4 TB | $40 | [ Deploy this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 16 GB | 6 | 320 GB | 5 TB | $80 | [ Deploy this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 32 GB | 8 | 640 GB | 6 TB | $160 | [ Deploy this plan](https://www.vultr.com/pricing/?ref=7268283) |

*The $2.50 tier is only offered in a limited set of locations.

The line continues above that with $320 (64 GB) and $640 (128 GB) tiers. Vultr also sells High Performance and High Frequency variants — same RAM, faster CPUs and NVMe storage, a few dollars more per tier — plus dedicated-core, cloud GPU and bare metal lines. None of those are needed to replace Heroku; the regular line is the one that maps to dynos.

Around the compute, a few other prices matter for a Heroku-style stack:

| Product | What it's for | Starting price |
| --- | --- | --- |
| Managed PostgreSQL / MySQL / Valkey / Kafka | Databases without the sysadmin work | ~$15/mo |
| Block Storage | Extra disk for an instance | $1/mo (10 GB) |
| Object Storage | Files, backups, static assets | $5/mo |
| Load Balancer | Spreading traffic across instances | $10/mo |

Billing details worth knowing before you pick a tier:

- Instances are billed by the hour up to the monthly cap. Destroy one and billing stops — useful for preview and staging boxes.
- Transfer is included per plan (0.5–6 TB on the tiers above); overage is $0.01/GB.
- Automatic backups cost 20% on top of the instance price.
- There's no free tier. Instead, new users typically get a credit — $250 valid for 30 days in recent offers — which covers a lot of first-month experimenting. Check the current amount at signup.

On the database question: Heroku Postgres Essential-0 is $5/month for 1 GB, and Vultr's managed PostgreSQL starts around $15/month for 1 vCPU, 1 GB and 25 GB with automated backups and point-and-click config. That's more than Heroku's entry tier, but it's far below what a managed database costs at most PaaS competitors, and it keeps Postgres off your app server entirely. 👉 [Check current Managed PostgreSQL pricing](https://www.vultr.com/pricing/?ref=7268283)

## The actual math: one Heroku stack vs. the same stack on Vultr

A standard small production stack costs about $52/month on Heroku and $10–20/month on Vultr. Here's the breakdown using current list prices.

Heroku side:

- Standard-1X web dyno: $25 (0.5 GB RAM)
- Basic worker dyno: $7
- Postgres Essential-0: $5
- Redis add-on: from ~$15
- **Total: ~$52/month** — and the web dyno has half a gigabyte of RAM

On Vultr, option A is one $10/month 2 GB instance running Coolify, with the app, worker, Postgres and Redis as containers on that box. You get four times the RAM of the Standard-1X dyno and a single line item. Option B separates things: a $5/month 1 GB box for app and worker, plus a $15/month managed Postgres — $20 total, with the database backed up and maintained for you.

The gap widens as you add apps. Three small apps on Heroku triple every line item — you're past $150/month. On Vultr, one $20/month 4 GB box hosts all three under Coolify, so each additional app costs effectively nothing; even giving each app its own $5 box comes to $15 total. Egress tells a similar story: Railway charges $0.05/GB, while Vultr includes 1–6 TB and charges $0.01/GB after. Half a terabyte of monthly traffic costs $25 on Railway's meter and $0 on any Vultr plan above the smallest tier.

The honest costs on the Vultr side: you patch the OS, you own the backup plan (or pay the 20% for automated backups), and there's no autoscaler. For a small team running a handful of steady apps, that trade is usually worth several hundred dollars a year. 👉 [See the full Vultr Cloud Compute pricing table](https://www.vultr.com/pricing/?ref=7268283)

## When Vultr is the wrong choice

If nobody on your team wants to own a server, stay on a managed platform — that's what Render and Railway are for, and their price premium buys exactly that. A raw VPS also can't match Fly.io's 30+ region edge placement; a Vultr instance lives in one region, and global low latency would mean running several.

Two Heroku behaviors don't carry over automatically. There's no built-in `git push` until you install Dokku (one-click from the Marketplace, so this one's easily solved). And there's no dyno-style sleep/wake model — apps run or they don't. Preview environments, which Heroku charged for as Review Apps, come back via Coolify's PR previews instead. Last thing to check before committing: if your stack leans on Heroku add-ons beyond a database — search, queues-as-a-service, monitoring — make sure you know what replaces each one. Some of that becomes a container under Coolify; some of it doesn't move at all.

## Moving over in an afternoon

The migration is a repo re-wire plus a database dump, and it doesn't need to be a big-bang move — run production on Heroku while you validate the new box on staging for a week, then swap DNS.

1. Create a Vultr account and grab the new-user credit; it covers a lot of first-month experimenting.
2. Deploy an instance close to your users — Vultr runs 30+ locations — on Ubuntu LTS, sized from the table above (1–2 GB handles most single-app starts).
3. Pick Coolify or Dokku from the Marketplace at deploy time, so the tooling is installed before you first log in.
4. Wire the code: add a Dokku git remote, or connect your repo in Coolify and let it build on push. Move Heroku config vars into environment variables on the new box.
5. Dump Postgres from Heroku, restore it into the new database, point a staging domain at the instance, and cut over DNS when error rates and latency match. 👉 [Browse Vultr's Cloud Compute plans and regions](https://www.vultr.com/products/cloud-compute/?ref=7268283)

## FAQ

### Is Heroku shutting down?

No. It's on a sustaining engineering model since February 2026: existing apps keep running, pricing and support are unchanged, and current enterprise subscriptions renew as usual. What stopped is new feature development and new enterprise contracts. Running there while you plan a move is a legitimate choice.

### What's the cheapest Heroku alternative?

Self-hosting on a VPS: $2.50–10/month gets you a box that runs Coolify or Dokku and replaces several Heroku line items. Among managed options, Fly.io has the cheapest entry compute ($2.02 for 256 MB), while Railway's Hobby plan and Render's free tier are the cheapest managed starting points — with the usual caveats that free tiers sleep and meters run.

### Does Vultr have a free tier?

It doesn't. The compensations are a new-user credit (typically $250, valid 30 days) and hourly billing, so you can deploy a test instance, try Coolify for an afternoon, and pay a few cents.

### Can I keep git push deploys without Heroku?

Yes. Dokku reproduces the workflow almost exactly — push to a dokku remote and buildpacks build the app server-side. Coolify takes the Git-connected approach instead: it watches your repo and rebuilds on push, with preview environments for pull requests.

## Picking one and getting started

Want zero operations and a small bill today? Render or Railway, and budget for the per-service stacking. Need low latency across continents and like the terminal? Fly.io. Running more than one app, leaning on Postgres and Redis, or just tired of per-dyno math? The Vultr route wins on cost and only asks that someone on your team enjoys a terminal session now and then.

Before you deploy: confirm the current new-user credit, start on the $5–10 tier rather than over-provisioning, and remember instances resize upward later without a rebuild. Hourly billing means the cost of trying this for a day rounds to zero. 👉 [Create a Vultr account and claim the new-user credit](https://bit.ly/VulTR)
