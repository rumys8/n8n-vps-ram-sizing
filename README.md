# n8n VPS hosting: how much RAM you actually need, whether the cheapest annual plan is enough, and who fixes what when a workflow breaks (RackNerd vs. mainstream VPS pricing)

Self-hosted n8n has no licence fee. The Community Edition is free to download and run, so the only recurring cost is the machine underneath it, and that cost is flat: a monthly or annual server price that stays the same whether your workflows fire ten times a day or ten thousand. The plans in circulation for exactly this job run from roughly **$1.83/month equivalent** ($21.99/year for a 1 GB KVM box) up to **$25.99/month** for a 4 GB VPS from a mainstream host.

Which end of that range you need depends on three things: how much memory n8n plus its database actually take, whether the renewal price matches the signup price, and who you can ask when an execution fails at 2 a.m. This article covers all three, plus the RAM figure RackNerd itself publishes for n8n and the point at which a bigger box stops being a waste of money.

> **Most first-time self-hosters should take the 2 GB tier rather than the 1 GB loss leader.** RackNerd lists n8n as a one-click app on its KVM VPS specials and puts n8n's comfort line at 2 GB of RAM. That leaves room for PostgreSQL on the same box instead of watching memory. The 1 GB tier will boot n8n, but you'll be fighting swap within a week.
> 👉 [Check RackNerd's current n8n-ready VPS plans and prices](https://bit.ly/RacKnerd)

## What n8n actually eats, and where the 2 GB figure comes from

n8n the application is light. The things around it aren't. Every workflow definition, every stored credential and every execution record lives in PostgreSQL on your server, and that database is what grows. A busy instance writes to disk constantly, which is why SSD storage matters more here than raw CPU.

The sizing rules one.com publishes for its own n8n VPS line are a reasonable public benchmark:

- **Light workflows:** 2 vCPU and 4 GB RAM
- **Moderate workflows:** 4 vCPU and 8 GB RAM
- **Heavy production:** 8 vCPU and 16 GB RAM or above

RackNerd's own documentation is more forgiving, and it's specific about the app: n8n is comfortable from **2 GB of RAM**, while its heavier one-click apps (AnythingLLM, Ollama, LibreChat, OpenClaw) want 4 GB or 8 GB respectively. Two gigabytes is the floor for n8n plus a database; four is where you stop thinking about it.

The other number worth knowing is throughput. Docker Compose can run n8n alongside PostgreSQL and Redis in queue mode, spreading work across multiple workers; Bluehost quotes a ceiling of **220 executions per second** for that setup. Almost nobody self-hosting a business's automations gets close to it. A single workflow firing every five minutes is 8,640 runs a month, which is a lot for a per-execution billing tier and almost nothing for a CPU.

That's the shape of the decision: your money buys database headroom and disk speed, not execution slots.

## What you actually get on a RackNerd KVM box

RackNerd's VPS specials are KVM virtual machines with full root access, and the useful part for this use case is that n8n is one of the apps in its **ctrl** control panel. After the VPS deploys (provisioning happens right after payment), you open One-Click Apps in ctrl, select n8n, and the credentials appear in the panel when it's ready. No SSH session required for the install itself.

Verified details that matter for an automation host:

- **1 Gbps port on every plan**, not gated behind a premium tier
- **Full root access**, so you can install Docker yourself, bring your own stack, or add services next to n8n
- **OS choice** of Ubuntu, Debian, AlmaLinux, Rocky Linux, Fedora or CentOS, with custom ISO installs available through a support ticket
- **ctrl panel** handles reboots, OS reinstalls, an HTML5 VNC console for recovery, the network firewall rules, root password resets and plan upgrades
- **Docker with Portainer** is also a one-click app if you'd rather build the container stack yourself
- **Up to 100 free IPv6 addresses** on request in the Los Angeles and France locations, useful if you run separate n8n instances per client
- **Multiple datacenter locations**, so you can put the box near the APIs your workflows hammer

On price, the entry-level annual specials have been listed at **$21.99/year for the 1 GB tier** and **$35.99/year for 2 GB**. Treat those as the commonly advertised floor and confirm the live plan list and current pricing on the order page, since promotional tiers rotate.

👉 [See RackNerd's 2 GB KVM plan and current annual pricing](https://bit.ly/RacKnerd)

## What the cheap plans cost, side by side

| Option | What you get | Listed price | How you're billed |
| --- | --- | --- | --- |
| RackNerd KVM VPS special | 1 GB or 2 GB RAM, 1 Gbps port, full root, n8n one-click in ctrl | $21.99/yr (1 GB), $35.99/yr (2 GB), confirm at checkout | Annual, flat |
| Bluehost n8n VPS Standard | 2 vCPU / 4 GB RAM / 80 GB SSD | $4.69–$25.99/mo across its n8n range | Monthly, flat, independent of execution volume |
| OVHcloud VPS-3 | 6 vCores / 12 GB RAM / 100 GB NVMe / 1 Gbps, anti-DDoS included | from $12.32/mo | Monthly |
| Hostinger KVM with n8n template | 2 vCPU / 8 GB / 100 GB NVMe on the KVM 2 spec | $6.49/mo now, renews at $11.99/mo | 24-month prepay, $155.76 up front |

Read the billing column before the price column. A $6.49 monthly figure that requires $155.76 paid today and renews at nearly double is a different product from a $21.99 annual charge. The RackNerd row and the OVHcloud row are the two that stay legible across years; the Hostinger row is the cheapest per month today and the most expensive by year three.

Full disclosure on the gaps: one.com's Cloud Server line (2 vCPU/4 GB up to 16 vCPU/32 GB in five tiers) and IONOS's VPS+ and Dedicated n8n offerings both publish configurations but no prices in the material reviewed here, so they're absent from the table rather than estimated.

## The three things hiding behind a low monthly price

**1. Prepayment length.** Hostinger's published structure is the clearest example: $6.49/mo means $155.76 for 24 months, renewing at $11.99/mo. Its middle tier runs $12.99/mo ($311.76 today) and renews at $28.99/mo; the largest runs $25.99/mo ($623.76) and renews at $49.99/mo. Over three years the difference between the signup price and the renewal price is the actual cost of the server.

**2. Flat pricing versus per-execution pricing.** Every host in this comparison prices by the server, not by the task. n8n Cloud is the contrast: per Bluehost's published comparison, the Starter tier allows 2,500 executions/month at $24/month and Pro allows 10,000 at $60/month, against the 8,640 executions a single five-minute workflow burns. Those figures come from a hosting provider's comparison page, not from n8n's own pricing, so treat them as an order of magnitude rather than a quote. The structural point holds either way: on a flat-rate VPS, doubling your workflow count costs nothing.

**3. "Unmanaged" is literal.** Root access is the feature and the obligation. Nobody patches your OS, tunes PostgreSQL, or updates n8n for you. one.com says it plainly for its own line (updates on your own schedule, following n8n's Docker documentation), and it's true of any KVM box with root.

## Who fixes what when a workflow breaks

This is the section the comparison articles skip, and it's the one that decides whether you keep the server.

- **Bluehost** states its scope as **Infrastructure Only**: hardware, network and virtualization layer. OS, configuration and applications are yours.
- **Hostinger** covers the VPS environment itself, including installation and keeping the n8n application running. It explicitly does **not** provide support or troubleshooting for internal n8n workflows, configuration or custom code.
- **OVHcloud** supports the infrastructure (hardware, network connectivity, virtualization stability, control panel). Workflow design, node configuration, n8n errors and software updates go to the n8n community forums, Discord, or n8n's official documentation.
- **one.com** is unmanaged: full root, self-scheduled updates, with the host stepping in only if something goes wrong during the initial installation.
- **RackNerd** handles the VM, the network and the ctrl panel, and installs n8n for you from One-Click Apps. Once it's running, the application layer is inside a box you have root on, so OS updates, n8n version bumps and credential hygiene are your job.

None of these are bad deals. They're just the same deal, and knowing it in advance beats discovering it during an outage. If you want someone else to debug a failing HTTP Request node, none of these plans include that; what you want instead is n8n's documentation and community, or a managed service at several times the price.

👉 [Open RackNerd's VPS specials and check what's included before you order](https://bit.ly/RacKnerd)

## From payment to a running instance, in five steps

1. **Order the VPS.** RackNerd provisions immediately after payment; you get the IP and root credentials.
2. **Pick your OS.** Ubuntu or Debian are the safe defaults if you plan to follow n8n's Docker instructions.
3. **Install n8n.** On RackNerd, that's One-Click Apps in ctrl; credentials appear in the panel. Elsewhere it's the provider's template (Hostinger ships "Ubuntu 24.04 with n8n" and a separate queue-mode template, IONOS preinstalls n8n, Docker and the web UI on both VPS+ and Dedicated).
4. **Open the port.** n8n listens on **port 5678** by default, reachable at your server's IP. Open 5678 in the firewall; on RackNerd that's the ctrl Network Firewall, on IONOS it's the Cloud Panel firewall.
5. **Put it behind a domain and HTTPS.** For anything beyond a test, point a domain at the IP and install an SSL certificate. OVHcloud's guide spells out the same sequence: provision, SSH in and update the OS, install n8n via Docker or npm, then configure domain, certificate and environment variables.

One quirk worth recognising: if a prebuilt image asks for a username and password before the n8n canvas loads, that's an extra HTTP Basic Auth layer, not n8n's own login. IONOS uses `admin` plus your server's root password, and it can be disabled over SSH.

## Where your data sits, and who can read it

Self-hosted n8n keeps workflows, credentials and execution history in your own PostgreSQL database; nothing is routed to a third party for storage. That's the whole reason people move off per-task SaaS in the first place, and it's why the host's jurisdiction matters.

- **one.com** runs its n8n VPS line in EU infrastructure, markets it as GDPR-ready and ISO 27001 certified.
- **IONOS** uses European datacenters and draws a line between API-based AI (data goes to OpenAI or similar over an encrypted connection) and local models such as Llama on a Dedicated Server, where data stays inside the organisation.
- **OVHcloud** spreads datacenters globally so you can deploy near users or integrated services, and includes anti-DDoS protection at no extra cost on all VPS plans.
- **RackNerd** offers multiple locations, which is useful when your workflows mostly talk to APIs in one region.

Security is a shared-responsibility model everywhere. The host secures hardware and network; you own OS updates, firewall rules, strong credentials, HTTPS and keeping n8n current. OVHcloud states this division explicitly, and it applies just as much to a $21.99 annual box as to a $25 monthly one.

## Questions people ask before buying

**Is n8n itself free?** Yes. The Community Edition is free to self-host with no licence fee. n8n sells an Enterprise plan separately, which adds audit logs, SSO/SAML, credential encryption at rest and team management for organisations with compliance requirements. You don't need it to run automations.

**How much RAM do I need?** 2 GB is the stated floor for n8n alongside a database. 4 GB is comfortable for moderate workflows. 8 GB and up is for heavy production, local LLMs, or queue mode with several workers.

**When does queue mode become worth it?** When a single worker can't keep up, or when several people share the instance and long-running jobs block short ones. Queue mode runs n8n with Redis and multiple workers via Docker Compose. Until you're pushing serious volume, the single-container setup is simpler and cheaper.

**How many integrations are there?** Published counts disagree: 400+, 500+, 1,000+, and roughly 1,900 connectors all appear on hosting providers' pages. Treat it as "hundreds, and growing". Anything missing can be reached with the HTTP Request node against any REST API.

**Can I run a local model?** Yes, with enough RAM. Ollama is a one-click app on RackNerd's VPS specials and wants at least 4 GB, and small models run acceptably on a modest VPS. Larger models need a dedicated server.

**What about refunds?** Hostinger publishes a 30-day money-back guarantee. RackNerd's refund terms weren't part of the material reviewed here, so confirm the billing terms on the order page before paying, particularly for annual plans.

## Which tier to buy

- **Testing n8n, or running two or three light webhooks:** the 1 GB annual special is enough to learn on, and at around $1.83/month equivalent there's little to lose.
- **Running real business automations with a database:** 2 GB, roughly $3/month equivalent annually. This is the tier where PostgreSQL stops being a memory problem, and it's the one most people should buy.
- **Queue mode, several workers, or a local model:** 4 GB and up, or a dedicated server if the model is large.
- **Any workflow touching customer data under GDPR:** pick the region deliberately, and remember that "self-hosted" only means your data stays put if you're not shipping it to an external AI API on every execution.

Two things to confirm before you pay: the price shown at checkout including the billing period, and what the renewal looks like after the promotional term. Then build the first workflow.

👉 [Start with RackNerd's 2 GB KVM plan and confirm today's price](https://bit.ly/RacKnerd)
