%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#EEEDFE', 'primaryBorderColor': '#534AB7', 'primaryTextColor': '#26215C', 'lineColor': '#888780', 'secondaryColor': '#E1F5EE', 'tertiaryColor': '#FAEEDA', 'fontSize': '16px'}}}%%
flowchart TD
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

    classDef trigger fill:#FAEEDA,stroke:#854F0B,color:#412402
    classDef process fill:#E1F5EE,stroke:#0F6E56,color:#04342C
    classDef decision fill:#EEEDFE,stroke:#534AB7,color:#26215C
    classDef human fill:#FAECE7,stroke:#993C1D,color:#4A1B0C
    classDef terminal fill:#F1EFE8,stroke:#5F5E5A,color:#2C2C2A

    class DAILY,TENDAY,MONTHLY trigger
    class SORT,DUMP,NOISE,INBOX,G1,G2,G3,G4,G5,G6,G7,G8,G9,SWEEP1,SWEEP2,SWEEP3,SWEEP4,TRASH,LEARN,SUGGEST,PROMOTE,REPLIED,OVERDUE,WAIT,WAKE,DISCOVERY,PROMOTEORG,ORGLABEL,SKIP process
    class EXC,DRULE,NRULE,AUTO,PURGECHK,OUTCHK,WAKECHK,HIBQ decision
    class HUMAN1,HUMAN2,MANUAL,ORGCAND human
    class GATEWAY,HIBCHECK terminal
