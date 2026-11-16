# InfoMentor School Week – a Claude Cowork skill

A skill for **Claude Cowork** that takes care of the weekly InfoMentor routine for parents in **Stockholms stad**:

- Logs in to InfoMentor. You scan the BankID QR code yourself; Claude never sees or types your login details.
- Copies each child's school schedule for the next two weeks into that child's own calendar.
- Reads all new InfoMentor posts, including ones that are only images or PDFs.
- Sends two summaries: a short one for the parents (deadlines first), and a friendly one for the kids (in English by default, so they practise). A child gets tagged by name when something only applies to them.
- Adds calendar reminders for important events (parents' evenings, theme days, end-of-term ceremonies) and for deadlines (for example registering for holiday fritids), two days before each deadline.

It works with **Outlook.com** or **Gmail + Google Calendar**.

> The summaries are a help, not a replacement. Important information should still be checked in InfoMentor, but it becomes much easier to focus on what matters.

---

## What you need

| | |
|---|---|
| **Claude Cowork** | In the Claude desktop app |
| **BankID** | To log in to InfoMentor as a guardian (vårdnadshavare) |
| **Outlook.com or Gmail** | Gmail is used together with Google Calendar |
| **One calendar per child** | Created in your account and shared with the child |

---

## Setup

### 1. Download the skill

On this page, click the green **Code** button and choose **Download ZIP**. The ZIP holds one folder with `SKILL.md` in it; `SKILL.md` is the skill.

### 2. Add it to Claude

In Claude, open **Settings** and find the **Skills** section. Upload the skill folder as a ZIP file, then turn the skill on.

### 3. Create one calendar per child

Each child gets their own calendar in **your** account, shared with the child. The child then sees it in their own calendar app. Claude only ever adds events to your copy.

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

### 4. First run: answer a few questions

In Claude Cowork, type something like:

```
do the school week
```

The first time, Claude asks a few short questions:

- **About you:** your name, your email, and how you sign off to the kids (Pappa, Mamma or a name).
- **Extra recipients:** whether a partner should get the summaries and reminders, and whether a work email should get the reminders too (handy if you mostly look at your work calendar).
- **Your children:** their names exactly as shown in InfoMentor's user menu, their email addresses, and their calendar names.
- **Preferences:**
  - Outlook or Gmail
  - Whether to include short slots like breaks and 5-minute transitions
  - Which language the kids' email should use
  - Whether emails are sent directly or shown as drafts first

Claude repeats your answers back for you to confirm before doing anything.

The municipality is preset to **Stockholms stad (Vårdnadshavare)**. If yours is different, just tell Claude.

### 5. Every week

Run the same request again. Scan the QR code with the BankID app when Claude asks, then read the summary. It takes a few minutes.

---

## How the schedule gets into the calendar

| Method | Works with | Your work each week |
|---|---|---|
| Directly in Outlook | Outlook | Scan the QR code |
| Google Calendar connector | Google | Scan the QR code |
| Calendar file (.ics) to import | Google | About a minute per child (**Settings → Import & export**) |
| Browser, with weekly repeating events | Google | Slow the first time, then only changes |

---

## Good to know

- **You log in yourself.** Claude never handles passwords; you scan BankID every time.
- **Only your recipients.** Emails go only to the addresses you give during setup.
- **Check anyway.** InfoMentor stays the source of truth; the summary helps you focus.
- **Outlook is the tested path.** It has been used for a couple of months. The Google options are newer, so check on the first run that events land in the right calendars at the right times.
- **Your settings are personal.** Don't commit a version of `SKILL.md` that has your family's names or email addresses filled in.

---

## Files

```
SKILL.md    # the skill Claude follows
README.md   # this guide
```

---

Shared by Esmaeil Nik Arman. Questions and improvements are welcome; open an issue or get in touch.
