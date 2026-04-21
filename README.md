flowchart LR
    subgraph Sources[Source Systems and Repositories]
        ClaimDump[Claim Dump Files]
        SOPRepo[SOP Repository / SharePoint]
        ClaimPlatform[Claims Adjudication Platform\nFacets / QNXT / CAS / Amisys]
        AgentData[Agent Data]
    end

    subgraph Ingestion[Data Ingestion Domain]
        ADF[Integration / ADF]
        APIM1[API Management]
        Preprocess[Data Preprocessing]
        ClaimStore[(Claim Data Store)]
        Blob[(Blob / Object Storage)]
        Linked[Linked Services]
    end

    subgraph Workflow[Workflow Management Domain]
        WMS[Workflow Management System\nand Reporting Dashboards]
        AgentUI[Agent UI / Dashboard]
        Admin[Admin]
        Agent[Agent]
    end

    subgraph GenAI[Core Gen AI Domain]
        Knowledge[Gen AI Service / Knowledge Manager]
        Chunking[Chunking]
        SOPPrep[SOP Preprocessing]
        RuleGen[AI Rule Generator]
        Feedback[Capture Feedback]
        Stats[Get Agent Stats]
        GetClaim[Get Claim Data]
    end

    subgraph Rules[Rule Processing Domain]
        RuleStore[(Rule Store)]
        Cache[(Redis Cache)]
        RuleExec[Rule Executor]
        ProcUnit[Processing Unit]
        RecEngine[Recommendation Engine / MS Rules]
    end

    ClaimDump --> ADF
    ADF --> APIM1
    APIM1 --> Preprocess
    Preprocess --> ClaimStore

    ClaimDump --> Linked
    Linked --> Blob
    Blob --> APIM1

    SOPRepo --> Linked
    Linked --> Blob
    Blob --> SOPPrep
    SOPPrep --> Chunking
    Chunking --> Knowledge
    Knowledge --> RuleGen
    RuleGen --> RuleStore

    ClaimStore --> GetClaim
    GetClaim --> RuleExec
    Stats --> AgentUI
    Feedback --> Knowledge

    RuleStore --> Cache
    Cache --> RuleExec
    RuleExec --> RecEngine
    ProcUnit --> RuleExec
    ProcUnit --> RecEngine

    AgentData --> WMS
    WMS --> AgentUI
    Admin --> AgentUI
    Agent --> AgentUI
    ClaimStore --> AgentUI
    AgentUI --> Feedback
    RecEngine --> AgentUI
    AgentUI --> ClaimPlatform

    ClaimPlatform --> ClaimDump
    ClaimPlatform -. manual update on claim .-> AgentUI
