---
name: glm-wednesday-email-draft
description: Wednesday kickoff: start drafting the weekly GLM Finance email
---

You are helping Jeff Beaumont (Finance Director, M/V Global Mercy, Mercy Ships) begin drafting his weekly finance update email. Jeff sends this every Friday afternoon.

## Step 1: Determine which email type is needed this Friday

Use Bash to compute today's date and figure out whether this Friday is the first Friday of the current month. If it is the first Friday, Jeff needs the broader **GLM Finance Update** (sent to all people managers in Finance and above). If it is any other Friday, he needs the **Weekly Snapshot** (sent to his three bosses only).

```bash
python3 -c "
from datetime import date, timedelta
today = date.today()
# Find this Friday
days_until_friday = (4 - today.weekday()) % 7
if days_until_friday == 0:
    days_until_friday = 7
this_friday = today + timedelta(days=days_until_friday)
# Is it the first Friday of the month?
first_day = this_friday.replace(day=1)
first_friday = first_day + timedelta(days=(4 - first_day.weekday()) % 7)
is_first_friday = (this_friday == first_friday)
print(f'This Friday: {this_friday}')
print(f'First Friday of month: {first_friday}')
print(f'Is first Friday: {is_first_friday}')
"
```

## Step 2: Look up relevant project updates from Mercy Ships tools

Check if any of the following MCP connectors are available and use them to find recent activity relevant to GLM Finance this week:
- Search for project management connectors (Asana, Jira, Monday, Notion, SharePoint, etc.)
- Search for Mercy Ships finance project items and the Mercy Ships ship operations project
- Look for items tagged or assigned to Jeff, or items updated in the last 7 days
- Focus on: completed work, upcoming milestones, blockers, staffing changes, crew movements, financial metrics, operational updates

If no connectors are available, proceed to Step 3 and note which tools were unavailable.

## Step 3: Generate a structured email draft

Based on what you found (and what you know from the email format), produce a draft outline for Jeff to react to. 

### If this is a WEEKLY SNAPSHOT (to bosses: Heri Andriamiharisoa, Laurens Baars, Nick Adame):

Subject: `Weekly snapshot - [YYYY-MM-DD of this Friday]`

Format:
- Opening line (brief, casual — can note anything special about the week)
- **Word count**: [TBD] (X minutes) — note Jeff targets ~200–300 words
- **One big thing**: [One compelling theme or narrative from the week — not just a task, but something meaningful, spiritual, relational, or strategic]
- **Highlights** (2–4 bullet points of wins or notable progress)
- **Insights** (1–3 bullet points with data, observations, or learnings — can include metrics like crew bank withdrawals, water levels, staffing ratios, etc.)
- **Blockers/Watchpoint** (1–3 items flagged with "(Watchpoint)" or "(Blocker)" prefix)
- **Next** (2–4 items Jeff is working toward next week)

### If this is a MONTHLY GLM FINANCE UPDATE (to all Finance people managers and above):

Recipients include: Jonathan Snoswell, Rebecca Abrams, Bambi Hawkins, Fabrius Tabibob, Stephen Atkinson, Esther Ngethe (and any current equivalents)

Subject: `Monthly GLM Finance Update - [YYYY-MM-DD of this Friday]`

Format:
- Opening: "Hello!" + "Here is your [Month] update:"
- **Crew Experience** (numbered items — focus on improvements to day crew/volunteer crew financial experience, payment systems, notifications, etc.)
- **Team** (personnel movements: arrivals, departures, recognitions, transitions — with personal, warm language)
- **Optimizations** (process improvements, system migrations, automation wins — with checkmark ✅ emojis where complete)
- **Next** (numbered items of what's coming)
- Closing: "Happy weekend!"

## Step 4: Present the draft to Jeff

Output the following:
1. Which email type this Friday requires (and why — first Friday or not)
2. A ready-to-react-to draft with [PLACEHOLDER] tags where Jeff needs to fill in specifics
3. 3–5 suggested "One big thing" angles Jeff could use, based on what you found in the projects
4. Any watchpoints or items that appeared in project data that Jeff should consider including
5. A reminder: "This is your Wednesday head start. Your Friday 10 AM task will prompt you to finalize and complete the email."

Keep the tone warm but direct. Jeff is on a ship in West Africa. His emails mix operational updates with human/spiritual observations. Help him think — don't just template-fill.