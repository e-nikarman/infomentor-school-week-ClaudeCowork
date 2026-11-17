---
name: infomentor-school-week
description: Weekly InfoMentor routine for parents. Copies the children's school schedules into their calendars, summarises InfoMentor news, checks homework tasks, development goals and fritidsschema, emails a parent version and a kids' version, and adds calendar reminders for important events and deadlines. Works with Outlook or Google (Gmail and Google Calendar).
---

# InfoMentor school week routine (shareable version)

Run this when the parent asks to "do the school week", "redo", or similar.

**Important: use visual browser checks.** For every step that reads data from InfoMentor, always verify results visually using `get_page_text` and screenshots — do not rely on API calls alone. After switching a child or navigating to a section, take a screenshot or read the page text to confirm the right content loaded. If a page appears blank in a screenshot, use `get_page_text` or `read_page` to check the DOM — content often renders in the DOM even when the visual viewport appears empty.

**Limiting the scope:** The parent can choose to run only part of this routine. If they say something like "only check Information", "just bedömningar", "skip calendars and emails, just give me the news", or any other narrowing instruction, **only run the steps they asked for** and skip everything else. Produce a shorter report covering just the requested sections. The parent does not need to run the full routine every time.

## 0. First-run setup – ask the parent

On the first run, or whenever the settings below are still placeholders, interview the parent before doing anything else.
- Use the AskUserQuestion tool for choices; ask for names and addresses in plain text.
- Ask in a few short rounds, not one long form.
- Write the answers back to the parent as a summary, ask them to confirm, and use them for every later run.
- If the parent wants to keep the settings permanently, they can save this skill again with the filled-in settings.

**Round 1 – the parent**
- The parent's own name, and how the kids should see them sign off (for example Pappa, Mamma, or a name).
- The parent's own email address: the account Claude will work in.
- Should another adult get the parents' summary and reminder invites (partner, co-parent)? If yes: their name and email address.
- Should reminders also be sent to a work email, so they show in a work calendar? If yes: that address.

**Round 2 – the children**
- Each child's name, exactly as it appears in InfoMentor's user menu (for example "Efternamn, Förnamn").
- Each child's email address, if they have one. If none, that child gets no email.
- The name of each child's calendar. If the calendars don't exist yet, explain how to create one shared calendar per child and share it with the child, then ask for the names.

**Round 3 – tools and preferences** (multiple choice)
- Preferred tool: **Outlook** (Outlook.com email and calendar) or **Gmail** (Gmail with Google Calendar).
- If Gmail, how the schedule should get into Google Calendar:
  - The Google Calendar connector (automatic, recommended if available)
  - A calendar file to import (about 1 minute of manual work per child)
  - The browser, with repeating weekly events (slow the first time)
- Include short slots (breaks, 5-minute transitions) in the kids' calendars? Yes or no.
- Language for the kids' email: English, Swedish, or another.

**Municipality:** this version is set up for **Stockholms stad**. Don't ask; just confirm in the summary that the parent logs in as Vårdnadshavare in Stockholms stad. If the parent says another municipality, choose that one in the login list instead.

```
MUNICIPALITY_LOGIN:   Stockholms Stad, Vårdnadshavare
PARENT_NAME:          <ask>
PARENT_SIGNOFF_KIDS:  <ask>
ACCOUNT:              <ask: the parent's own Outlook.com or Gmail address>
PARTNER:              <ask: name + email, or none>
EXTRA_INVITE:         <ask: work email, or none>

PROVIDER:             <ask: outlook | google>
GOOGLE_CALENDAR_MODE: <ask if google: connector | ics | browser>
INCLUDE_SHORT_SLOTS:  <ask: yes | no>
KIDS_EMAIL_LANGUAGE:  <ask>

CHILDREN:
  - name: <ask>    infomentor_name: <ask>    calendar: <ask>    email: <ask or none>
  - (one line per child)
```

## Period

The two school weeks starting **next Monday** (Mon–Fri of each week). Work out the dates from today.

## Step 1 – Log in to InfoMentor

Use the built-in browser pane. Load its tools in one ToolSearch call (`Claude_Browser`).

1. Open https://hub.infomentor.se/#/timetable. If the hub shows, the parent is already logged in.
2. Click "Logga in med e-legitimation" and choose MUNICIPALITY_LOGIN.
3. Choose "BankID och Mobilt BankID", then "BankID med QR-kod".
4. Tell the parent the QR code is ready to scan. Wait in 10-second steps until the tab reaches hub.infomentor.se.

Never type passwords or personal ID numbers. The parent always signs in with BankID themselves.

## Step 2 – Read each child's schedule

The top-right user menu ("Användarmeny – …") switches between children. Find it, click it, choose the child, wait about 4 seconds.

For each child, run this in the page (UTCOffset `-120` in summer time, `-60` in winter time):

```js
const who=document.body.innerText.match(/\(([^)]*)\)/);
const r = await fetch('/timetable/timetable/gettimetablelist',{method:'POST',headers:{'Content-Type':'application/x-www-form-urlencoded; charset=UTF-8','X-Requested-With':'XMLHttpRequest'},body:'UTCOffset=-60&start=YYYY-MM-DD&end=YYYY-MM-DD'}).then(r=>r.json());
(who&&who[1])+' n='+r.length+'\n'+r.map(e=>[e.start.slice(5,16),e.endTime,e.title,e.notes&&e.notes.roomInfo||'',e.allDay?'ALLDAY':''].join('|')).join('\n')
```

- Check that the name in the output is the child you selected.
- **Visual check:** after fetching the schedule data, also navigate to the Schema page and use `get_page_text` to visually confirm the schedule matches the API data. If there's a discrepancy, trust the visual page.
- The end date is the Saturday after week 2.
- Missing days usually mean a staff planning day (studiedag) or a school break. Mention them in the summary.
- Compare the children's schedules. Differences (other days for music, sloyd, language classes) go into the kids' email as personal notes.

## Step 3 – Write the schedules into each child's calendar

Each child has their own calendar (CHILDREN.calendar) in the parent's account. It is shared with the child, who sees it in their own calendar app. Every entry goes in, including short ones (breaks, transitions, lunch), unless INCLUDE_SHORT_SLOTS = no. Each event gets the title and time only, no location and no reminder.

Before adding, look for existing events in that calendar for the period. If some exist, don't add more; tell the parent instead. Afterwards, check that the number of events per day matches InfoMentor and that there are no duplicates.

### If PROVIDER = outlook (Outlook.com)

1. Open https://outlook.live.com/calendar/view/workweek in a new tab. If Microsoft sign-in appears, the parent signs in themselves.
2. While Outlook loads, capture the session headers with a fetch hook:
   ```js
   window.__cap=[];const of2=window.fetch;window.fetch=async function(i,o){try{const u=typeof i==='string'?i:(i&&i.url);let h={};const hh=(o&&o.headers)||(i&&i.headers);if(hh){if(hh.forEach)hh.forEach((v,k)=>h[k.toLowerCase()]=v);else for(const k in hh)h[k.toLowerCase()]=hh[k];}if(h.authorization&&/service\.svc/.test(u))window.__cap.push({u,h});}catch(e){}return of2.apply(this,arguments)};
   ```
   If nothing is captured, open the folder pane and toggle a calendar.
3. Build `svc(action, body)` from the last captured headers:
   - Remove x-owa-urlpostdata, ms-cv, traceparent, tracestate, x-owa-correlationid, action and content-length.
   - Add `action` and `content-type: application/json; charset=utf-8`.
   - POST to `/owa/service.svc?action=<Action>&app=Calendar` (`app=Mail` for email), with time zone `W. Europe Standard Time`.
4. Find each child's calendar folder ID with `GetCalendarFolders`, matching by calendar name.
5. Use `FindItem` (CalendarPageView) to check for existing events, then `CreateItem` one event per entry:
   - `SendMeetingInvitations:'SendToNone'`
   - `SavedItemFolderId` set to the child's folder
   - Subject, Start, End, `ReminderIsSet:false`, `LegacyFreeBusyStatus:'Busy'`
   - Do not send StartTimeZoneId or EndTimeZoneId; they cause an error.

### If PROVIDER = google (Google Calendar)

Follow GOOGLE_CALENDAR_MODE:

- **connector:** use the connected Google Calendar connector. Create the events in the child's calendar by name, with no reminders, and check the result. If the connector isn't available, tell the parent and offer the ics or browser mode.
- **ics:** build one `.ics` file per child with every event (time zone Europe/Stockholm, no alarms). Give the files to the parent. The parent imports each file into the right child's calendar in Google Calendar on a computer: Settings → Import & export → Import → choose the file and that child's calendar.
- **browser:** use calendar.google.com in the browser pane. The parent signs in to Google themselves.
  - **First run:** create each lesson once as a **weekly repeating event** until the end of the term. Choose the child's calendar in each event, with no notification. Work through one child at a time and tell the parent how far you have got.
  - **Later runs:** compare InfoMentor with the calendar and change only what differs. For a studiedag or a break, delete those single occurrences. For a changed lesson, edit that one occurrence. Don't recreate the whole schedule.
  - Before starting, warn the parent that the first run can take a long time.

INCLUDE_SHORT_SLOTS = no: skip breaks (Rast) and 5-minute transitions (Förflyttning). Keep lessons and lunch.

## Step 4 – Read InfoMentor news

Go to https://hub.infomentor.se/#/communication/news. Open every post since the last run: school newsletter, fritids newsletter, monthly letter, registrations (anmälan), parent events.

- Open a post by clicking its title, then read the open dialog. Close each dialog before opening the next.
- **Posts that are only images:** take screenshots of the dialog and scroll through it.
- **PDF attachments:** fetch the file in the page and extract the text. The browser pane can't display PDFs.
- **Pull out:** actions and deadlines, important events, fritids activities, homework, what's happening in class, upcoming dates, staff changes, and anything that applies to only one child.
- **Visual check:** always read each post visually with `get_page_text` and screenshots. Do not rely on API responses alone — some posts contain only images or PDFs that need visual inspection.

## Step 5 – Check Uppgifter (homework tasks)

For each child, navigate to `https://hub.infomentor.se/#/task` and visually check the page.

1. Switch to the child using the user menu.
2. Navigate to the Uppgifter section.
3. Use `get_page_text` to read the task list. Look for overdue, due, and completed tasks.
4. Note each task's subject, teacher, due date, and status.

If a child has overdue or upcoming tasks, include them in both the parents' and kids' emails. If all children have zero tasks, mention briefly that no homework tasks are posted.

**Note:** Uppgifter may show zero tasks for some classes — that's normal and depends on whether teachers use this feature.

## Step 6 – Review Dokumentation (development goals)

For each child, navigate to `https://hub.infomentor.se/#/documentation/conference` and read the latest development conference (Samtal).

1. Switch to the child using the user menu, then navigate to `#/documentation/conference`.
2. Use `get_page_text` to confirm the page loaded. The page may appear blank in screenshots but have content in the DOM.
3. Read the three sub-tabs by clicking each button:
   - **Hur gick det?** — evaluation of previous goals
   - **Var är vi?** — current status and school's efforts
   - **Vart ska vi? Hur gör vi?** — the development plan with prioritised goals per subject and responsibilities for school, student, and parents
4. Note the prioritised development areas per subject (SV, MA, ENG, etc.) and what the parents can help with at home.

Then check Extra anpassningar:

5. Click the "Extra anpassningar" tab.
6. Check both "Pågående" (ongoing) and "Avslutade" (completed) sub-tabs.
7. Note any active adaptations (small-group work, special teacher support, etc.).

**How to use this in the emails:**
- In the parents' email FYI section, include a brief note about each child's current development goals and what the school asks parents to support at home.
- If a child has an active extra adaptation, mention it so the parents stay aware.
- In the kids' email "Challenge for the week" section, tie the challenge to the child's actual development goals when possible (for example, if a goal is "raise your hand more", the challenge could be about that).
- Do not include the full conference text — just the actionable parts.

Then check Bedömningar (assessments):

Bedömningar is a **separate section** in the InfoMentor Hub (its own tile on the dashboard), NOT a sub-section of Dokumentation. The correct URL is `#/assessmentv2/summaryAssessment` (Översikt tab) and `#/assessmentv2` (Bedömning tab).

8. Navigate to `https://hub.infomentor.se/#/assessmentv2/summaryAssessment` or click the Bedömningar tile from the hub dashboard.
9. The **Översikt** tab shows "Ämnesomdömen" — a table of all subjects with assessment levels ("Är i behov av stödinsatser", "Når en godtagbar kunskapsnivå", "Når mer än en godtagbar kunskapsnivå"). Use `get_page_text` and screenshots to read this.
10. Click the **Bedömning** tab (`#/assessmentv2`). This shows detailed assessments per subject. Open the **ÄMNEN** (subjects) sidebar on the left — it lists all subjects, and subjects marked "NY" (new) have new assessments. **Click through every subject in the list**, not just the one that loads by default. For each subject, read the "Sammanfattande bedömning" (summary assessment) which shows the assessment level, date, and a **teacher comment** with recommendations. Use `get_page_text` to read each subject's teacher comments before moving to the next one.
11. The **Betyg** tab (`#/assessmentv2/grades`) shows letter grades when published (may be empty for younger students in åk 1-3).

Then check Planeringar (lesson plans):

Planeringar is also a **separate section** in the InfoMentor Hub (its own tile), NOT part of Dokumentation. The correct URL is `#/uolv2`.

12. Navigate to `https://hub.infomentor.se/#/uolv2` or click the Planeringar tile from the hub dashboard.
13. The page shows plan cards grouped by **Aktiva** (active), **Ej påbörjade** (not started), and **Slutförda** (completed). There are filters for time period, subjects, and status. Use `get_page_text` to read the list.
14. Click on each active plan card to open it. Each plan has two expandable sections:
    - **Översikt**: description, subject, dates, year, teachers
    - **Pedagogisk planering**: "Vad ska vi arbeta med?" (what), "Hur ska vi arbeta?" (how), "Vad ska du lära dig?" (learning goals), and assessment criteria
15. Use `get_page_text` after expanding each section to read the full content.

**How to use assessments and plans in the report:**
- In the parents' email, include a brief note on any new or recent assessments/grades per child. Mention the assessment level and key points from teacher comments (especially recommendations for home support).
- Lesson plan highlights (what's being taught, upcoming topics) can go in the FYI section or the kids' "In class" section.
- Do not dump the full text — summarise the key points.

## Step 7 – Check Fritidsschema (after-school care times)

For each child, check the registered fritids (after-school care) times for the coming weeks.

**Browse the page visually.** Navigate to `https://hub.infomentor.se/#/leisuretimeschedule` and use `get_page_text` and screenshots to read the schedule. The page shows a week view with registered arrival/departure times per day, days off, school closures (e.g. "Studiedag fritids stängt"), and staff comments. Use the week navigation arrows to check both coming weeks.

**If the page is blank or broken** (the frontend JS for this page can sometimes fail to load — you'll see a blank area or console errors), try `read_page` to check if content is in the DOM even though it doesn't render visually. If the page still appears empty, try refreshing or navigating away and back. Do not fall back to API calls — always use the visual page.

For each child:

1. Switch to the child using the user menu.
2. Navigate to `#/leisuretimeschedule`. Use `get_page_text` and take a screenshot to read the schedule.
3. Use the week navigation to check both coming weeks. Take a screenshot of each week.
4. Note:
   - Registered arrival and departure times per day
   - Any days marked as **Ledig** (not attending)
   - Any days where the **school is closed** (and the reason, e.g. "Studiedag fritids stängt")
   - Any days where times are **locked** by the school
   - Whether there are **comments** from staff
   - **Days with no registered hours** — a day that shows no times, or where the school is closed, means the child has no fritids that day. This is important: it may mean the parent needs to arrange alternative care. Always flag these prominently.

**How to use this in the emails and report:**
- **School closures and days without fritids are the most important thing to surface.** If any day is marked as closed (e.g. "Studiedag fritids stängt", "Lov", "Stängt"), or a day has no registered hours, or a day is marked Ledig — put it in the **To do** section at the top of the report and email, not buried in the Fritidsschema section. The parent may need to arrange alternative care on short notice. Use ⚠️ to make it stand out. Example: "⚠️ Fri 23 Oct – no fritids (Studiedag fritids stängt) — arrange alternative care."
- Also cross-check with the news posts (Step 4) and calendar — if a closure or studiedag was announced there, make sure the Fritidsschema matches and flag any discrepancy.
- In the Fritidsschema section of the email, include each child's registered times. If all children have the same standard registration and no closures, keep it brief. But any day without hours must stand out.
- In the kids' email "At fritids" section, mention if a day is different from the usual pattern (a day off, a closure, or different pickup time).
- If the school has locked times or left comments, flag that for the parent's attention.

## Step 8 – Show the report and (optionally) send emails

**The report is always shown.** After gathering all the information, present a full summary to the parent in the conversation. This is the main deliverable and is never skipped. It should contain everything the emails would contain (see the sections below).

**Sending emails is optional.** After showing the report, ask the parent whether they want the emails sent. Use AskUserQuestion with options like "Send both emails", "Send parents' email only", "Send kids' email only", "Don't send, just the report". Only send emails if the parent says yes. Never send emails without the parent's explicit approval.

**Parents' version**, to PARTNER. Subject: "School highlights, week of <dates>". Sections:
- To do: deadlines first, in bold
- This week
- Homework: include any tasks from Uppgifter, with due dates
- Fritidsschema: registered times per child, any days off or closures, any staff comments
- Assessments: any recent grades, subject assessments, or teacher feedback per child (from Bedömningar)
- Coming up
- FYI: include lesson plan highlights (from Planeringar) and a brief note per child on their current development goals (from Dokumentation > Samtal > Vart ska vi?) and what parents can help with at home; mention any active extra anpassningar

Short and factual.

**Kids' version**, one email to all children who have an email address, written in KIDS_EMAIL_LANGUAGE. Subject: "Your school week, <dates> 🌟". Warm, simple language for their age, a few emojis, signed PARENT_SIGNOFF_KIDS. Sections:
- At fritids: day by day
- Just for you: tag each child in bold (**@Name**) for anything that only applies to them
- Homework
- In class
- Get ready for: fun upcoming events
- Challenge for the week: tie it to the child's actual development goals from Dokumentation when possible

Leave out admin and staff details.

### Sending (only after the parent confirms)

- **outlook:** `CreateItem` with `MessageDisposition:'SendAndSaveCopy'`, an HTML body, and `SavedItemFolderId` set to sentitems (`app=Mail`).
- **google:** use the Gmail connector (send a message, or create a draft if the parent prefers to send it themselves).

## Step 9 – Calendar reminders for deadlines and important events

All reminders go in the parent's main calendar as invitations to PARTNER and EXTRA_INVITE. They show the parent as **Free** (not Busy). Check first that a reminder doesn't already exist from an earlier run.

**Deadlines** (registrations, forms, sign-ups): one all-day event on a single day, two days before the deadline.
- Subject: "⚠️ <what to do> (deadline <date>)"
- Body: what to do and where (for example InfoMentor → Fritidsschema)
- Reminder: the afternoon before

**Important events** (parents' evenings, parents' council, drop-in mornings, end-of-term ceremonies, theme days, staff planning days, school breaks): on the day itself.
- Events with a time: real start and end time, a location, the programme in the body, and a reminder the day before.
- Days without a time: an all-day event.
- If an event concerns the children too (theme days, closures), also add it to the children's calendars.

Provider details:
- **outlook:** `CreateItem` with `SendMeetingInvitations:'SendToAllAndSaveCopy'`, `IsAllDayEvent`, `LegacyFreeBusyStatus:'Free'`, `ResponseRequested:false`, and RequiredAttendees.
- **google:** create the event with the Google Calendar connector, with guests and "Show as: Available" (transparency `transparent`). Without a connector, include these events in the parent's `.ics` file and tell the parent to add the guests after import.

## Final reply

**Always show the full report first**, then ask about emails. The report includes:
- Events added per child, and any missing days
- Homework tasks status (overdue, due, or none)
- Development goals summary per child
- Any active extra anpassningar
- Recent assessments/grades and teacher feedback per child
- What's being taught (lesson plan highlights from Planeringar)
- Fritidsschema: registered times, any days off, closures, or staff comments
- Reminders created
- Anything the parent should check by hand, such as posts that couldn't be read or unclear dates

After the report, ask the parent whether they want the emails sent (parents' email, kids' email, both, or neither). If they say yes, send and confirm with recipients. If they say no, the report in the conversation is the deliverable.

Always remind the parent that the summary is a help, not a replacement: important information should still be checked in InfoMentor.
