You can use this [live editor](https://mermaid.ai/live/) to display the diagram. Copy-Paste the content below in the appropriate fields.

CONFIG:
{
  "theme": "forest",
  "look": "neo",
  "fontFamily": "'Inter Variable', sans-serif",
  "darkMode": "true",
  "layout": "fixed",
  "showgrid": "false",
  "themeVariables": {
    "lineColor": "#00C853"
  }
}

MERMAID SCRIPT:
flowchart TD
    DAILY["Daily trigger\n8am"] --> SORT["runInboxSorting()"]
    TENDAY["Daily trigger 7am\nfires every 10 days"] --> GATEWAY["dustbusterMasterGateway()"]
    MONTHLY["Monthly trigger\n2nd at 2am"] --> HIBCHECK["runHibernationCheck()"]

    SORT --> EXC{"Matches\nException?"}
    EXC -->|"Yes"| SKIP["Skip — leave untouched"]
    EXC -->|"No"| DRULE{"Matches\nDumpster-Fire rule?"}
    DRULE -->|"Yes"| DUMP["Label 03-Dumpster-Fire\nArchive"]
    DRULE -->|"No"| NRULE{"Matches\nNoise rule?"}
    NRULE -->|"Yes"| NOISE["Label Noise\nArchive"]
    NRULE -->|"No"| AUTO{"Matches\nLayer-1 pattern?"}
    AUTO -->|"Yes"| DUMP
    AUTO -->|"No"| INBOX["Stays in inbox\nunlabeled"]

    GATEWAY --> G1["1. Archive sweep"]
    G1 --> G2["2. Rescue scan"]
    G2 --> G3["3. Purge old Dumpster-Fire"]
    G3 --> G4["4. Learning algorithm"]
    G4 --> G5["5. Outreach check"]
    G5 --> G6["6. Wake-on-reply check"]
    G6 --> G7["7. Write 10-day summary"]
    G7 --> G8["8. Schedule reminder"]
    G8 --> G9["9. Update status panel"]

    G1 --> SWEEP1["01-Review-Pending\nover 30 days → 03"]
    G1 --> SWEEP2["02 or 03 in inbox\nover 30 days → archive"]
    G1 --> SWEEP3["Transient exception\nover 45 days → 01"]
    G1 --> SWEEP4["Permanent exception\nover 10 years → 01"]

    G3 --> PURGECHK{"03-Dumpster-Fire\nover 75 days?"}
    PURGECHK -->|"Yes"| TRASH["Move to Trash\nGmail auto-deletes day 105"]

    G4 --> LEARN["Scan 03-Dumpster-Fire\nfind repeat senders"]
    LEARN --> SUGGEST["Write to\nSuggested-Rules tab"]
    SUGGEST -.->|"you review,\nmark YES"| PROMOTE["promoteApprovedRules()"]
    PROMOTE --> DRULE

    G5 --> OUTCHK{"Outreach/Waiting\nthread has reply?"}
    OUTCHK -->|"Yes"| REPLIED["Label Replied\nLog to sheet"]
    OUTCHK -->|"No, over 10 days"| OVERDUE["Label Follow-Up-Due\nStar thread"]
    OUTCHK -->|"No, still waiting"| WAIT["Stays Waiting"]

    G6 --> WAKECHK{"Hibernated thread\nhas new reply?"}
    WAKECHK -->|"Yes"| WAKE["Move back to\nactive label"]

    HIBCHECK --> HIBQ{"Projects/Groups/Orgs\nlabel inactive 9+ months?"}
    HIBQ -->|"Yes"| HIB["Rename to x-prefix\nhibernated"]

    HUMAN1(["You: send email"]) -.->|"apply manually"| MANUAL["Outreach/Waiting"]
    MANUAL -.-> OUTCHK

    HUMAN2(["You: review tabs"]) -.-> SUGGEST
    HUMAN2 -.-> ORGCAND["Org-Candidates tab"]

    DISCOVERY["runOrgDiscovery()\nrun manually"] --> ORGCAND
    ORGCAND -.->|"you approve"| PROMOTEORG["promoteOrgCandidates()"]
    PROMOTEORG --> ORGLABEL["Create Orgs/ label\napply retroactively"]

    classDef trigger fill:#FAEEDA,stroke:#854F0B,color:#412402
    classDef process fill:#E1F5EE,stroke:#0F6E56,color:#04342C
    classDef decision fill:#EEEDFE,stroke:#534AB7,color:#26215C
    classDef human fill:#FAECE7,stroke:#993C1D,color:#4A1B0C
    classDef terminal fill:#F1EFE8,stroke:#5F5E5A,color:#2C2C2A

    class DAILY,TENDAY,MONTHLY trigger
    class SORT,DUMP,NOISE,INBOX,G1,G2,G3,G4,G5,G6,G7,G8,G9,SWEEP1,SWEEP2,SWEEP3,SWEEP4,TRASH,LEARN,SUGGEST,PROMOTE,REPLIED,OVERDUE,WAIT,WAKE,HIB,DISCOVERY,PROMOTEORG,ORGLABEL,SKIP process
    class EXC,DRULE,NRULE,AUTO,PURGECHK,OUTCHK,WAKECHK,HIBQ decision
    class HUMAN1,HUMAN2,MANUAL,ORGCAND human
    class GATEWAY,HIBCHECK terminal
