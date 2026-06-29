Resource Migration Report

You generate a skimmable, executive-ready migration report across all Pulumi Insights accounts. A reader should get the picture in 2-3 minutes.

Deliver the report as a polished Markdown document/file (e.g. migration-report.md) so it renders cleanly and can be shared - not as a loose chat reply. Use proper headings, tables, and the inline bar charts below.

TOP PRIORITY: BE CONCISE AND VISUAL

- This is a summary, not an inventory. NEVER dump full resource lists or per-resource tables.
- For any category, show the count, the percentage, and at most 5 representative example resources, then aggregate the rest as "+N more" grouped by pattern. If you find yourself listing more than ~5 items, collapse them into a pattern with a count instead.
- Prefer one compact table per account over prose subsections. No multi-paragraph write-ups per account.
- Lead every section with the number, then a one-line takeaway. Cut hedging and repetition.

VISUALIZATIONS (render these inline)
Render every percentage as a 20-character bar followed by the number, using filled/empty blocks:
filled = round(percent / 5) blocks. Example -> 49% renders as: ██████████░░░░░░░░░░ 49%
Use these bars for: org-wide migration %, % migrated by account, % migrated by team, and coverage % per enrolled account.
For monthly momentum, draw one bar per month scaled to the busiest month, e.g.:
Apr 2026 ████████████ 146
Mar 2026 ████████ 98
If the surface renders Mermaid, you MAY add a Mermaid pie chart for the four buckets; otherwise rely on the text bars. Do not depend on Mermaid.

HOW TO GATHER DATA

1. Enumerate every Insights account; never sample or stop early. If one can't be read, list it explicitly.
2. Do a full, exact scan - accuracy matters more than speed. Enumerate every resource in each account (paginate through all results), classify each one, and report exact counts. Do NOT estimate from samples or extrapolate. Pull whatever properties/tags you need to classify and attribute correctly, even if the scan is slow. Only use "~" if a specific value is genuinely unavailable from the API, and say why.
3. Also pull stack/project growth data: total active stacks, projects, resources under management, and new stacks created over time (by month for the last ~12 months).
4. Work account-by-account, then roll up to org-wide and per-team totals. All bucket counts must sum to account totals; account totals must sum to the org total.

CLASSIFICATION (every resource lands in exactly one bucket)

1. Migrated - already Pulumi-managed (in a stack). Unambiguous; trust the managed signal.
2. Will remain unmanaged - AWS/cloud-platform or Control Tower infrastructure we intentionally don't bring into IaC. Match on these PATTERNS (counts are illustrative, not fixed):
   - Config rules tied to Security Hub / Control Tower compliance frameworks
   - Control Tower resources (name/type contains "controltower" or "control-tower")
   - GuardDuty org member resources
   - Security Hub product/standards subscriptions
   - AWS-managed CloudFront "Managed-\*" policies; AMS (AWS Managed Services) artifacts
   - Firewall Manager resources ("FMManaged\*")
   - AWS Service Linked Roles
   - AWS/GCP/Azure default & auto-created scaffolding: default VPCs/SGs/subnets/route tables, "do-not-delete"-tagged resources, QuickSetup, Config buckets/recorders, GCP default subnetworks / GCF & App Engine buckets / project service APIs, Azure NetworkWatchers / MC\_\* AKS node RGs / Cloud Shell storage / default RGs
     When unsure whether something is platform-managed, say so in one line rather than guessing silently.
3. Should be deleted - your judgement: obviously temporary/scratch resources, test/experiment leftovers, joke or placeholder names, orphaned resources with no tags and no owner, long-idle resources. Group by pattern with a count and a one-line reason each. These are recommendations for human review only.
4. Needs migration - everything else: active infra not yet under Pulumi management.

OWNERSHIP - WHO IS RESPONSIBLE FOR MIGRATING
For "needs migration" resources, attribute a responsible owner/team so work can be assigned (this is about who should migrate, not where it currently lives). Use, in order, and state which signal you used:

1. Resource tags (owner, team, cost-center, project, repo).
2. Project/resource naming conventions mapped to teams. If formal Pulumi Teams aren't set up, map projects to the org's team/POD structure using name prefixes - and STATE THE MAPPING you used so the reader can validate it (e.g. "client-_, facility-_ -> Client Connect"). Flag anything ambiguous.
3. GitHub repo / code search for the resource name, ID, or tags to find the managing team and CODEOWNERS.
4. Insights account owner as the fallback for anything unresolved.
   Group the migration queue by responsible team.

REPORT STRUCTURE (keep each section tight)

1. EXECUTIVE SUMMARY (~150 words max)
   Headline org-wide migration % as a bar, total discovered resources, total resources under Pulumi management, and the single biggest takeaway. Note context if relevant (e.g. demo vs production estate). Then a compact roll-up table: counts in each bucket; % migrated of all resources AND % of the migratable base (total minus "will remain unmanaged"), clearly labeled. Add a one-line data-quality caveat (e.g. accounts not enrolled in Insights, missing tags).

2. MIGRATION PROGRESS (visual)

- Org-wide four-bucket split (bars or Mermaid pie).
- % migrated by account (one bar each).
- % migrated by responsible team (one bar each).
  Keep commentary to one line under each.

3. MOMENTUM
   Adoption trend over time: total active stacks/projects/managed resources, new stacks per month for the last ~12 months (monthly bars), biggest month on record, and share of footprint built in the last 12 months. Call out which teams are accelerating vs stalled (e.g. no new stacks in 90 days).

4. COVERAGE BY ENROLLED ACCOUNT
   Important caveat first: Insights coverage reflects only enrolled accounts, not the full estate. Then a compact table - account, Pulumi-managed, total discovered, coverage % (as a bar), date enrolled if known. One line on which teams would gain visibility if more accounts were enrolled.

5. DATA ANALYSIS
   One compact row per account in a single table: cloud, total, migrated, will-remain-unmanaged, should-delete, needs-migration, % migrated (all), % migrated (migratable). Below it, at most 2-3 bullets total flagging the few noteworthy specifics (e.g. one account holding most of the orphaned artifacts). No per-resource enumeration.

6. ACTIONS NEEDED

- Migration queue by team: each team, count needing migration, and the top 3-5 highest-leverage items only. Largest teams first.
- Deletion candidates: grouped by pattern with counts and a one-line reason; show only the top ~10 groups, framed as "recommend human review before deleting."
- Unattributed: the handful of significant resources where ownership couldn't be resolved, for a human to assign.

RULES

- Cover all Insights accounts; never sample silently.
- Full, exact scan - report real counts, not estimates. Use "~" only when a value is genuinely unavailable, and explain why.
- Percentages must reconcile (buckets sum to account totals; accounts sum to org total).
- Concise and visual over exhaustive. Examples capped at 5 per category; collapse the rest into patterns + counts.
- State which ownership signal you used and any team-mapping assumptions.
- Deliver the final report as a clean Markdown document.
- Never delete, modify, or migrate anything. This command only reports and recommends.
