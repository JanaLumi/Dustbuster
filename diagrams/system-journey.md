title: "System Journey — The Story of the System",
text: `<p>Every day at eight in the morning, Dustbuster wakes up and looks at your inbox. It reads each new email carefully — not for meaning, but for pattern. It asks three questions in order. First: is this someone you’ve explicitly protected? If yes, it steps back and leaves the email exactly where it is. Second: does this match a rule you’ve taught it — a sender domain, a subject keyword, something you’ve seen before and decided to banish? If yes, it labels the email and moves it out of your way. Third: does it match a known noise pattern — something automated and low-value, but worth keeping just in case? If yes, same treatment. If none of these match, the email stays in your inbox. Dustbuster doesn’t guess. It only acts on what it knows.</p>
    <p>Every ten days, something larger happens. A master cycle runs — nine steps in sequence, like a slow tide moving through the system. It sweeps the archive, checking whether anything has been sitting in the review queue long enough to be discarded, or whether anything has overstayed its welcome in the inbox. It rescues emails that might have been caught too aggressively. It purges the oldest discarded mail — anything that’s been waiting in the discard pile for more than seventy-five days gets moved to trash, and Gmail will finish the job thirty days after that. Then it runs the learning algorithm: it looks at what’s been discarded recently, finds senders who keep appearing, and writes suggestions to a tab in your config sheet. You review those suggestions at your own pace, mark the ones you agree with, and on the next cycle they become permanent rules. The system learns, but only with your sign-off.</p>
    <p>The cycle also checks your outreach. Any email you’ve sent and marked as waiting — a cold contact, a pitch, a follow-up you’re tracking — gets examined. If a reply has arrived, the thread is moved to replied and logged. If ten days have passed with no answer, the thread gets flagged and starred so it surfaces visibly. Then the cycle checks whether any dormant threads have unexpectedly come back to life — someone replying to something months old — and if so, wakes them up. Finally it writes a ten-day summary, schedules your next reminder, and updates the status panel.</p>
    <p>Once a month, on the second, a quieter check runs in the background. It looks at all your project and organisation labels — threads grouped by community, by company, by relationship — and asks how long it’s been since anything moved in each one. If a group has been silent for nine months or more, it gets marked as hibernated. It doesn’t disappear. It just moves to the edge of the map, waiting to be woken if someone writes again.</p>
    <p>Running alongside all of this is an org discovery process you trigger manually when you want it. Dustbuster scans your sent mail and your inbox together, looking for domains and contacts that appear repeatedly in both directions — real relationships, not just newsletters. It surfaces these as candidates. You review them, approve the ones that matter, and the system creates a dedicated label for each one and applies it retroactively to all matching threads.</p>
    <p>Your own role in the system is small but decisive. You send emails and apply a waiting label. You review suggestions and approve or reject them. You run org discovery when the moment feels right. Everything else runs without you — quietly, on a rhythm, getting slightly smarter each cycle.</p>`,
code: `flowchart TD
  %%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#EEEDFE', 'primaryBorderColor': '#534AB7', 'primaryTextColor': '#26215C', 'lineColor': '#888780', 'secondaryColor': '#E1F5EE', 'tertiaryColor': '#FAEEDA', 'fontSize': '16px'}}}%%
  classDef trigger fill:#FAEEDA,stroke:#854F0B,color:#412402
  classDef process fill:#E1F5EE,stroke:#0F6E56,color:#04342C
  classDef decision fill:#EEEDFE,stroke:#534AB7,color:#26215C
  classDef human fill:#FAECE7,stroke:#993C1D,color:#4A1B0C
  classDef terminal fill:#F1EFE8,stroke:#5F5E5A,color:#2C2C2A

  DAILY["Daily trigger\n8am"] --> SORT["runInboxSorting()"]
  TENDAY["Daily trigger 7am\nfires every 10 days"] --> GATEWAY["dustbusterMasterGateway()"]
  MONTHLY["Monthly trigger\n2nd at 2am"] --> HIBCHECK["runHibernationCheck()"]

  SORT --> EXC{"Matches\nException?"}
  EXC -->|"Yes"| SKIP["Skip — leave untouched"]
  EXC -->|"No"| DRULE{"Matches\nDumpster-Fire rule?"}
  DRULE -->|"Yes"| DUMP["Label 03-Dumpster-Fire\narchive"]
  DRULE -->|"No"| NRULE{"Matches\nNoise rule?"}
  NRULE -->|"Yes"| NOISE["Label Noise\narchive"]
  NRULE -->|"No"| AUTO{"Matches\nLayer-1 auto-pattern?"}
  AUTO -->|"Yes"| DUMP
  AUTO -->|"No"| INBOX["Stays in inbox\nunlabeled"]

  GATEWAY --> G1["1. Archive sweep"]
  G1 --> G2["2. Rescue scan"]
  G2 --> G3["3. Purge old Dumpster-Fire"]
  G3 --> G4["4. Learning algorithm"]
  G4 --> G5["5. Outreach check"]
  G5 --> G6["6. Wake-on-reply check"]
  G6 --> G7["7. Write 10-day summary"]
  G7 --> G8["8. Schedule calendar reminder"]
  G8 --> G9["9. Update status panel"]

  G1 --> SWEEP1["01-Review-Pending over 30 days\n→ 03-Dumpster-Fire"]
  G1 --> SWEEP2["02 or 03 in inbox over 30 days\n→ archive"]
  G1 --> SWEEP3["Transient exception over 45 days\n→ 01-Review-Pending"]
  G1 --> SWEEP4["Permanent exception over 10 years\n→ 01-Review-Pending"]

  G3 --> PURGECHK{"03-Dumpster-Fire\nover 75 days?"}
  PURGECHK -->|"Yes"| TRASH["Move to Trash\nauto-deleted day 105"]

  G4 --> LEARN["Scan 03-Dumpster-Fire\nfind repeat senders"]
  LEARN --> SUGGEST["Write to\nSuggested-Rules tab"]
  SUGGEST -.->|"you review,\nmark YES"| PROMOTE["promoteApprovedRules()"]
  PROMOTE --> DRULE

  G5 --> OUTCHK{"Outreach/Waiting\nhas reply?"}
  OUTCHK -->|"Yes"| REPLIED["Label Outreach/Replied\nlog to sheet"]
  OUTCHK -->|"No — over 10 days"| OVERDUE["Label Follow-Up-Due\nstar thread"]
  OUTCHK -->|"No — still waiting"| WAIT["Stays Waiting"]

  G6 --> WAKECHK{"Hibernated thread\nhas new reply?"}
  WAKECHK -->|"Yes"| WAKE["Move back to active label"]

  HIBCHECK --> HIBQ{"Projects/Groups/Orgs\ninactive 9+ months?"}
  HIBQ -->|"Yes"| HIB["Rename to x-prefix\nhibernated"]

  HUMAN1(["You: send email"]) -.->|"apply manually"| MANUAL["Outreach/Waiting"]
  MANUAL -.-> OUTCHK

  HUMAN2(["You: review tabs"]) -.-> SUGGEST
  HUMAN2 -.-> ORGCAND["Org-Candidates tab"]

  DISCOVERY["runOrgDiscovery()\nrun manually"] --> ORGCAND
  ORGCAND -.->|"you approve"| PROMOTEORG["promoteOrgCandidates()"]
  PROMOTEORG --> ORGLABEL["Create Orgs/ label\napply retroactively"]
  
  class DAILY,TENDAY,MONTHLY trigger
  class SORT,DUMP,NOISE,INBOX,G1,G2,G3,G4,G5,G6,G7,G8,G9,SWEEP1,SWEEP2,SWEEP3,SWEEP4,TRASH,LEARN,SUGGEST,PROMOTE,REPLIED,OVERDUE,WAIT,WAKE,DISCOVERY,PROMOTEORG,ORGLABEL,SKIP process
  class EXC,DRULE,NRULE,AUTO,PURGECHK,OUTCHK,WAKECHK,HIBQ decision
  class HUMAN1,HUMAN2,MANUAL,ORGCAND human
  class GATEWAY,HIBCHECK terminal`
