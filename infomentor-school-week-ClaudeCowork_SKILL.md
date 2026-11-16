---
name: infomentor-school-week
description: Weekly InfoMentor routine for parents. Copies the children's school schedules into their calendars, summarises InfoMentor news, emails a parent version and a kids' version, and adds calendar reminders for important events and deadlines. Works with Outlook or Google (Gmail and Google Calendar).
---

# InfoMentor school week routine (shareable version)

Run this when the parent asks to "do the school week", "redo", or similar.

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
- Send emails directly, or show a draft first?

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
SEND_MODE:            <ask: send | draft first>

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

## Step 5 – Write and send two emails

SEND_MODE = draft first: show both texts and send only once the parent confirms. SEND_MODE = send: send directly to the addresses given in the setup, and never to anyone else.

**Parents' version**, to PARTNER. Subject: "School highlights, week of <dates>". Sections:
- To do: deadlines first, in bold
- This week
- Homework
- Coming up
- FYI

Short and factual.

**Kids' version**, one email to all children who have an email address, written in KIDS_EMAIL_LANGUAGE. Subject: "Your school week, <dates> 🌟". Warm, simple language for their age, a few emojis, signed PARENT_SIGNOFF_KIDS. Sections:
- At fritids: day by day
- Just for you: tag each child in bold (**@Name**) for anything that only applies to them
- Homework
- In class
- Get ready for: fun upcoming events
- Challenge for the week

Leave out admin and staff details.

### Sending

- **outlook:** `CreateItem` with `MessageDisposition:'SendAndSaveCopy'`, an HTML body, and `SavedItemFolderId` set to sentitems (`app=Mail`).
- **google:** use the Gmail connector (send a message, or create a draft if the parent prefers to send it themselves).

## Step 6 – Calendar reminders for deadlines and important events

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

Keep it short:
- Events added per child, and any missing days
- Emails sent or drafted, with recipients
- Reminders created
- Anything the parent should check by hand, such as posts that couldn't be read or unclear dates

Always remind the parent that the summary is a help, not a replacement: important information should still be checked in InfoMentor.
