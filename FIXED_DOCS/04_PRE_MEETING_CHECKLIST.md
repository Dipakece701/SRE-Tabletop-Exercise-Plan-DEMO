# PRE-MEETING CHECKLIST — Do These Before the Call Tomorrow

> **Date:** October 21, 2026 (Tonight / Early Morning October 22)
> **Meeting Time:** October 22, 2026 at 10:15 AM

---

## Things To Do RIGHT NOW (Tonight)

### 1. Send the Scenario Brief to Constantin on Slack

The original checklist says this was due today (October 21).

**What to send:**
- The file: `02_Scenario_Brief.md` from the `3_REFERENCE_DOCS` folder.
- The file: `00_Incident_Procedure_v1.md` from the `3_REFERENCE_DOCS` folder.

**What to write in your Slack message:**

> Hi Constantin, here are the pre-reads for tomorrow's SRE Tabletop Exercise
> at 10:15 AM. The first document is our Incident Response Procedure v1.0,
> and the second is the scenario brief for the two tracks we will walk
> through. No action needed from you tonight — just a heads-up so you know
> what to expect. See you tomorrow!

---

### 2. Send the Calendar Invite (If Not Already Sent)

Open the file `03_Calendar_Invite_Details.md` in the `3_REFERENCE_DOCS` folder.
Copy the invite text and create a meeting in Outlook or Teams:

- **Title:** SRE Tabletop Exercise: APIM 5xx & Key Vault Security Simulation (AB#34970 / SOC 2)
- **Date:** Thursday, October 22, 2026
- **Time:** 10:15 AM to 11:15 AM
- **Attendees:**
  - constantin.pricochi@qcellsces.onmicrosoft.com
  - maurice.kennedy@qcellsces.onmicrosoft.com
  - aditya.mehta@qcellsces.onmicrosoft.com
  - dipak.singh@qcellsces.onmicrosoft.com

---

### 3. Practice Reading the Meeting Script Once

Open `01_MEETING_SCRIPT_COMBINED.md` from the `FIXED_DOCS` folder and read
through it once out loud. Time yourself. The whole script should fit within
60 minutes comfortably.

---

## Things To Do IN THE MORNING (Before the Call)

### 4. Set Up Your Screens

- **Shared Screen:** Open `02_LIVE_NOTES_TEMPLATE.md` in Notepad or VS Code.
  This is what the team will see.
- **Your Private Screen:** Open `01_MEETING_SCRIPT_COMBINED.md` — your teleprompter.
- **Backup Window:** Open `03_QA_CHEAT_SHEET.md` — in case someone asks a tough question.
- **Browser Tab:** Open `https://dev.azure.com/qcellsces/Helios/_workitems/edit/34970`

### 5. Test Your Screen Sharing

Join the Teams/Zoom meeting 2 minutes early and test that screen sharing
works and shows the Live Notes template.

---

## Things To Do AFTER the Call

### 6. Create the 6 Tickets in Azure DevOps

Go to `https://dev.azure.com/qcellsces/Helios/_workitems/edit/34970`.

Open the `2_INDIVIDUAL_TICKETS` folder from the ORIGINAL documentation
(not the FIXED_DOCS folder). For each of the 6 files:

1. Click "Add link" on AB#34970.
2. Choose Link type: Child, Work item type: Task.
3. Copy-paste the Title, Assigned To, Tags, and Description from the file.
4. Click Save.

The 6 tickets are:
1. Ticket 1: APIM Gateway 5xx incident simulation record.
2. Ticket 2: Key Vault security incident simulation record.
3. Ticket 3: Action Item — On-call roster in Wiki (Owner: Constantin).
4. Ticket 4: Action Item — Severity Classification Rubric (Owner: Dipak).
5. Ticket 5: Action Item — Slack runbook links (Owner: Aditya).
6. Ticket 6: Action Item — Key Vault rotation automation (Owner: Maurice).

**ALSO create any NEW action items** that came up during the meeting
(check your Live Notes for anything the team added).

---

### 7. Post the Completed Live Notes to Azure DevOps

Copy the completed `02_LIVE_NOTES_TEMPLATE.md` (with all the blanks filled
in during the call) and paste it as a comment on work item AB#34970.

---

### 8. Publish to ADO Wiki

Post the completed Post-Incident Review to the Azure DevOps Wiki at:
`Helios Wiki / QRE / Incident Retrospectives / 2026-10-22-SRE-Tabletop-Exercise`

---

### 9. Mark AB#34970 as Done

Once all evidence is attached, change the state of work item AB#34970
to "Resolved" or "Done."

---

## Summary: Your 4 Files For Tomorrow

| File | Purpose | Who sees it? |
|---|---|---|
| `01_MEETING_SCRIPT_COMBINED.md` | Your teleprompter. Read this line by line. | Only you (private screen). |
| `02_LIVE_NOTES_TEMPLATE.md` | The live notes. Fill this in during the call. | Everyone (shared screen). |
| `03_QA_CHEAT_SHEET.md` | Predicted questions and answers. | Only you (backup reference). |
| `04_PRE_MEETING_CHECKLIST.md` | This file. What to do before and after. | Only you. |
