flowchart TD
    subgraph triggers["Triggers (time-based)"]
        T1["Every 15 min"]
        T2["Every 1 hour"]
        T3["Sunday 6pm"]
    end

    T1 --> INBOX_START["processInbox()"]
    T2 --> OUT_START["processOutreach()"]
    T3 --> REPORT_START["writeWeeklyReport()"]

    %% --- INBOX SORTING FLOW ---
    INBOX_START --> SEARCH["Search unread inbox\nmax 50 threads"]
    SEARCH --> LOOP1{"For each thread"}
    LOOP1 --> READ["Read sender, subject,\nbody (first 1000 chars)"]
    READ --> RULECHECK{"Matches any\nINBOX_RULE?"}
    RULECHECK -->|"Yes"| APPLYLABEL["Apply label(s)\nWorksearch/category"]
    RULECHECK -->|"No"| LOOP1
    APPLYLABEL --> LOOP1
    LOOP1 -->|"Done"| INBOX_END(["Inbox pass complete"])

    %% --- OUTREACH TRACKING FLOW ---
    OUT_START --> GETWAITING["Get all threads\nlabeled Outreach/Waiting"]
    GETWAITING --> LOOP2{"For each thread"}
    LOOP2 --> CHECKREPLY{"More than 1\nmessage in thread?"}
    CHECKREPLY -->|"Yes, replied"| MOVEREPLIED["Remove Waiting + Follow-Up-Due\nAdd Outreach/Replied"]
    MOVEREPLIED --> LOGSHEET["logReplyReceived()\nwrite/update row in\nOutreach Log tab"]
    LOGSHEET --> LOOP2

    CHECKREPLY -->|"No reply yet"| GETTIER{"Which tier label?\nHot / Warm / Cold"}
    GETTIER --> DEADLINE{"Days since sent\n>= tier deadline?"}
    DEADLINE -->|"Yes, overdue"| FLAGOVERDUE["Add Outreach/Follow-Up-Due\nStar the message"]
    FLAGOVERDUE --> LOOP2
    DEADLINE -->|"No, still waiting"| LOOP2
    LOOP2 -->|"Done"| OUT_END(["Outreach pass complete"])

    %% --- WEEKLY REPORT FLOW ---
    REPORT_START --> COUNTLABELS["Count threads per label:\nWaiting, Replied, Follow-Up-Due,\nHot, Warm, Cold,\nApplications, Interviews,\nRejections, Recruiter-Outreach"]
    COUNTLABELS --> CALCRATE["Calculate response rate\nreplied / total sent"]
    CALCRATE --> WRITEROW["Append row to\nWeekly Summary tab"]
    WRITEROW --> REPORT_END(["Report written"])

    %% --- MANUAL HUMAN STEPS ---
    HUMAN1(["You: send outreach email"]) -.->|"manually apply"| MANUALLABEL["Outreach/Waiting +\nOutreach/Hot or Warm or Cold"]
    MANUALLABEL -.-> GETWAITING

    HUMAN2(["You: review weekly sheet"]) -.-> WRITEROW

    classDef trigger fill:#FAEEDA,stroke:#854F0B,color:#412402
    classDef process fill:#E1F5EE,stroke:#0F6E56,color:#04342C
    classDef decision fill:#EEEDFE,stroke:#534AB7,color:#26215C
    classDef human fill:#FAECE7,stroke:#993C1D,color:#4A1B0C
    classDef endpoint fill:#F1EFE8,stroke:#5F5E5A,color:#2C2C2A

    class T1,T2,T3 trigger
    class INBOX_START,SEARCH,READ,APPLYLABEL,GETWAITING,MOVEREPLIED,LOGSHEET,FLAGOVERDUE,COUNTLABELS,CALCRATE,WRITEROW process
    class LOOP1,LOOP2,RULECHECK,CHECKREPLY,GETTIER,DEADLINE decision
    class HUMAN1,HUMAN2,MANUALLABEL human
    class INBOX_END,OUT_END,REPORT_END endpoint
