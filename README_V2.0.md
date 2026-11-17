# InfoMentor School Week – a Claude Cowork skill

## Quick start

This Claude skill helps parents read new school posts, check homework tasks, development goals and fritidsschema, prepare clear summaries and reminders, and, if they choose, copy school schedules from InfoMentor into separate child calendars. You still log in yourself with BankID, and InfoMentor remains the source of truth.

**What is Claude Cowork?** Claude Cowork can be understood as a digital version of a user working on your computer. It can open and review information, use the browser, work with files, and complete multi-step tasks through the tools you allow it to access. You give it a goal, follow its progress, and review the result before accepting it. It works under your direction and does not replace your responsibility for checking the result.

**What is a Claude Skill?** A Claude Skill is a reusable package of instructions and supporting resources that teaches Claude how to perform a specific task consistently. This InfoMentor skill gives Claude the steps for collecting school information, preparing summaries and reminders, and, if selected, updating family calendars.

### 1. Get Claude Pro

Create or sign in to your Claude account and make sure you have an active Claude Pro subscription. Claude Pro is required to use this skill in Claude Cowork. The subscription is not only useful for the InfoMentor routine: it can also support writing, planning, summarizing documents, researching topics, organizing information, and many other everyday tasks. It is especially useful together with Cowork, where Claude can complete longer, multi-step activities using the files and tools you choose to provide.

### 2. Download and add the skill

On this page, click the green **Code** button and choose **Download ZIP**. The ZIP holds one folder with `SKILL.md` in it; `SKILL.md` is the skill. In Claude, open **Settings** and find the **Skills** section. Upload the skill folder as a ZIP file, then turn the skill on.

### 3. First run

In Claude Cowork, type something like:

```
do the school week
```

The first time, Claude asks a few short questions about you, your children, and your preferences (Outlook or Gmail, email language, whether to include calendar imports). Claude repeats your answers back for you to confirm before doing anything. The municipality is preset to **Stockholms stad (Vårdnadshavare)**. If yours is different, just tell Claude.

### 4. Every week

Run the same request again. Scan the QR code with the BankID app when Claude asks, then read the summary. It takes a few minutes.

It works with **Outlook.com** or **Gmail + Google Calendar**.

---

## What you get each week

Each run covers the **two school weeks starting next Monday**. Claude always shows you a full report in the conversation — that's the main deliverable and it's never skipped. On top of that you can optionally get: the children's schedules in their calendars, an email for the parents, an email for the kids, and calendar blockers. Claude asks before sending any email.

### 1. The children's schedules (optional)

If you choose to include calendar imports during setup, Claude copies each child's school schedule into that child's own calendar. This step is entirely optional — you can use the skill for summaries and emails only.

When enabled:

- Claude reads each child's schedule in InfoMentor (**Schema**) and switches between your children in InfoMentor's user menu.
- Every lesson goes into that child's own calendar, plus breaks, transitions and lunch if you chose to include short slots. Each event has the title and time only, with no location and no notifications, so the kids' phones don't buzz all day.
- Before adding anything, Claude checks that the period is empty in that calendar, so running it twice doesn't create duplicates.
- Afterwards, Claude checks that the number of events per day matches InfoMentor.
- Days with no lessons, such as a staff planning day (studiedag) or a school break (lov), are mentioned in the summary so you know why they're empty.
- If your children are in different classes or have different subjects (for example music on different days), each child gets their own correct schedule.

### 2. School news (Information)

Claude reads all recent posts in the Information section of InfoMentor — school newsletters, fritids newsletters, monthly letters, registrations and sign-ups, and parent event invitations. It opens each post, reads the text (including image-only posts and PDF attachments), and pulls out anything you need to act on: deadlines, events, things to bring, staff changes, and what's happening in class. This is where most of the "To do", "This week", "Coming up", and "FYI" content in the report comes from.

### 3. Homework tasks

Claude checks the Uppgifter (tasks) section in InfoMentor for each child and reports any overdue or upcoming homework. If teachers use this feature, you'll see each task's subject, due date, and status. If no tasks are posted, the summary says so.

### 4. Development goals

Claude reads each child's latest development conference (Samtal) in the Dokumentation section. This includes the child's prioritised goals per subject — for example "practise reading bar charts and pie charts in maths" or "dare to raise your hand more" — and what the school asks parents to support at home. If a child has an active extra adaptation (extra anpassning), such as small-group work with a special education teacher, that's included too.

The development goals appear in the parents' email FYI section and help shape the kids' weekly challenge. Claude does not include the full conference text — only the parts you can act on.

Claude also checks Bedömningar (assessments) and Planeringar (lesson plans), which are separate sections in the InfoMentor Hub (their own tiles on the dashboard, not part of Dokumentation). These are visible to guardians and contain subject assessment levels, teacher comments with recommendations for home support, and detailed lesson plans showing what's currently being taught. In Bedömningar, Claude reads through every subject's assessment — not just the one that loads first — to pick up all teacher comments and assessment levels. Any recent assessments or feedback appear in the parents' report, and lesson plan highlights feed into the kids' "In class" section.

### 5. Fritidsschema (after-school care)

Claude checks the Fritidsschema section in InfoMentor for each child. This shows the registered arrival and departure times for fritids (after-school care), along with any days marked as off, school closures, or comments from staff.

The report (and the parents' email, if sent) includes a brief summary of each child's registered fritids times. **Any day without fritids hours — or marked as school closed — is flagged at the top of the report in the "To do" section**, not buried in the Fritidsschema section, because the parent may need to arrange alternative care on short notice. Claude also cross-checks closure announcements from the Information posts with the Fritidsschema to catch discrepancies. If all children have the same standard registration and no closures, the summary says so without listing every day.

### 6. The parents' email (optional — sent only when you say yes)

After showing you the full report, Claude asks whether you want the emails sent. You can choose to send both, just one, or neither — the report in the conversation is always there regardless. When you say yes, the email is sent from **your own** Outlook or Gmail to the partner you named during setup.

**Subject:** `School highlights, week of <dates>`

It's short and factual, with these sections:

| Section | What's in it |
|---|---|
| **To do** | Everything you need to act on, deadlines first and in bold. For example registering for holiday fritids, forms to sign, or things to bring. |
| **This week** | Events that affect you or the kids: parents' evenings, meetings, theme days, staff planning days, early closing. |
| **Homework** | What each subject has assigned, with any links the teachers shared. |
| **Fritidsschema** | Each child's registered fritids times, any days off or closures, and any staff comments. |
| **Coming up** | Important dates further ahead, so nothing comes as a surprise. |
| **Assessments** | Any recent grades, subject assessments, or teacher feedback per child. |
| **Development goals** | Each child's prioritised goals from their latest development conference, and what parents can support at home. Any active extra adaptations. |
| **FYI** | Staff changes, new routines, things the school asks parents to help with at home. What's being taught (from Planeringar). |

Example:

> **To do**
> - **Register Christmas break fritids in InfoMentor → Fritidsschema, deadline Mon 23 Nov.** Register days off too.
>
> **This week**
> - Wed 18 Nov, 16:30–18:30: Parents' evening at the school. Class meeting at 16:45.

### 7. The kids' email (optional — sent only when you say yes)

One shared email to all your children who have an email address, written in the language you chose (English by default, so they practise). It's warm, simple and has a few emojis, and it's signed the way you chose (for example "Love, Pappa"). Only sent when you confirm after reviewing the report.

**Subject:** `Your school week, <dates> 🌟`

| Section | What's in it |
|---|---|
| **At fritids** | What's happening day by day. |
| **Just for you** | Anything that only applies to one child, with that child tagged by name, for example **@Anna**: different subject days, things to bring, or an individual task. Things that apply to all of them are marked "All of you". |
| **Homework** | Explained in kid language. |
| **In class** | What they're learning right now. |
| **Get ready for** | Fun things coming up, like theme days or performances. |
| **Challenge for the week** | A small goal tied to something the school asked parents to encourage, such as table manners or reading. |

Admin details and staff matters are left out of the kids' email.

Example:

> **Just for you**
> - **@Anna:** you have Music on Thursday and Sloyd on Friday. Remember your things for sloyd!
> - **@Leo and @Maja:** you have Music on Tuesday.

### 8. Calendar blockers

Claude adds blockers to **your** calendar and sends them as calendar invitations to your partner, and to your work email if you gave one. That way they also show in a work calendar, which many parents look at more often. Blockers are always shown as **Free**, so they never block your working time or make you look busy. Claude never adds the same blocker twice, even when you run the routine again.

**Deadlines** (registrations, forms, sign-ups)
- **One all-day event, two days before the deadline.** For example, a deadline on Monday 23 Nov gets a blocker on Saturday 21 Nov.
- Title like: `⚠️ Register Christmas break fritids in InfoMentor (deadline Mon 23 Nov)`
- The description says exactly what to do and where (for example InfoMentor → Fritidsschema), plus the key details from the school's post.
- A reminder pops up the afternoon before.

**Important events** (on the day itself)
- **Events with a time**, such as parents' evenings, parents' council meetings, drop-in mornings and end-of-term ceremonies: added with the real start and end time, the place, and the programme in the description. If there's a class meeting, the classroom is included when it's known. You get a reminder the day before.
- **Days without a time**, such as theme days, staff planning days and school breaks: added as all-day events. A school break spans the whole break.
- **Events that concern the children** (theme days, closures) are also added to the kids' calendars, so they see them too.
- Events that have already passed are skipped.

---

## What you need

| | |
|---|---|
| **Claude Pro** | A subscription to Claude, with Cowork enabled |
| **BankID** | To log in to InfoMentor as a guardian (vårdnadshavare) |
| **Outlook.com or Gmail** | Gmail is used together with Google Calendar |
| **One calendar per child** *(optional)* | Only needed if you want Claude to copy school schedules into calendars |

---

## Creating child calendars (optional)

Skip this section if you only want summaries and emails. Each child gets their own calendar in **your** account, shared with the child. The child then sees it in their own calendar app. Claude only ever adds events to your copy.

**Outlook.com**

1. Open the calendar at [outlook.live.com](https://outlook.live.com/calendar), choose **Add calendar**, then **Create blank calendar**.
2. Give it a clear name, for example `Anna-Åk3`.
3. Right-click the calendar, choose **Sharing and permissions**, and add the child's email address with **Can view all details**.
4. The child accepts the invitation in their own Outlook. The schedule then appears in their Outlook app.

**Google Calendar**

1. Open [calendar.google.com](https://calendar.google.com), click **+** next to **Other calendars**, then **Create new calendar**.
2. Give it a clear name, for example `Anna-Åk3`.
3. In the calendar's settings, under **Share with specific people**, add the child's Google account with **See all event details**.
4. The child sees the calendar in their Google Calendar app.

If you use Gmail, connect the **Gmail** and **Google Calendar** connectors in Claude if they're available to you.

---

## How the schedule gets into the calendar

This section applies only if you chose to include calendar imports.

| Method | Works with | Your work each week |
|---|---|---|
| Directly in Outlook | Outlook | Scan the QR code |
| Google Calendar connector | Google | Scan the QR code |
| Calendar file (.ics) to import | Google | About a minute per child (**Settings → Import & export**) |
| Browser, with weekly repeating events | Google | Slow the first time, then only changes |

---

## First-run questions

During your first run, Claude asks in a few short rounds:

- **About you:** your name, your email, and how you sign off to the kids (Pappa, Mamma or a name).
- **Extra recipients:** whether a partner should get the summaries and reminders, and whether a work email should get the reminders too (handy if you mostly look at your work calendar).
- **Your children:** their names exactly as shown in InfoMentor's user menu, their email addresses, and their calendar names (if using calendar imports).
- **Preferences:**
  - Outlook or Gmail
  - Whether to copy school schedules into calendars
  - Whether to include short slots like breaks and 5-minute transitions (if using calendars)
  - Which language the kids' email should use

---

## Good to know

- **You log in yourself.** Claude never handles passwords; you scan BankID every time.
- **Only your recipients.** Emails go only to the addresses you give during setup.
- **Check anyway.** InfoMentor stays the source of truth; the summary helps you focus on what matters.
- **Outlook is the tested path.** It has been used for a couple of months. The Google options are newer, so check on the first run that events land in the right calendars at the right times.
- **Your settings are personal.** Don't commit a version of `SKILL.md` that has your family's names or email addresses filled in.
- **You can limit the scope.** You don't have to run the full routine every time. If you only want Claude to check one part of InfoMentor — for example just the Information (news posts), just the Fritidsschema, or just the Bedömningar — say so when you start. For example: "do the school week but only check Information" or "just check bedömningar for all kids". Claude will skip the sections you didn't ask for and give you a shorter report.
- **Turn off training on your data.** This skill works with your children's school information. In Claude, go to **Settings → Privacy** and make sure **"Improve Claude"** (or the equivalent toggle for allowing Anthropic to use your conversations for model training) is turned **off**. This ensures your family's data is not used to train future models.

---

## Files

```
SKILL.md    # the skill Claude follows
README.md   # this guide
```

---

Shared by Esmaeil Nik Arman. Questions and improvements are welcome; open an issue or get in touch.
