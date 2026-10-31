%%{init: {'theme':'neutral'}}%%
flowchart LR
    subgraph EDGE["Headset (works offline)"]
        direction TB
        EL["3 Electrodes<br/>forehead + 2 behind ear"]
        TG["TGAM board"]
        ES["ESP32<br/>gesture detect, menu, profile cache"]
        OUT["Air speaker + bone conduction"]
        EL -->|analog EEG| TG -->|UART| ES -->|I2S| OUT
    end

    subgraph APP["Mobile App (Expo dev build)"]
        direction TB
        PAIR["QR pairing + anonymous account"]
        CAL["60 s calibration"]
        PH["Phrases, patterns, caregiver confirm"]
        ONNX["ONNX Runtime Mobile<br/>per-user classifier"]
        Q["Offline sync queue"]
    end

    subgraph CLOUD["Azure Cloud"]
        direction TB
        subgraph ID["Identity and API"]
            ENTRA["Entra External ID"]
            APIM["API Management"]
            FN["Functions / Container Apps"]
        end
        subgraph DATA["Data"]
            COS["Cosmos DB<br/>encrypted profiles"]
            BLOB["Blob Storage<br/>consented EEG"]
            KV["Key Vault"]
        end
        subgraph AI["AI"]
            FOUND["Microsoft Foundry<br/>LLM context ranking"]
            SPEECH["Azure AI Speech<br/>TTS, STT, voices"]
            SAFE["AI Content Safety<br/>socials moderation"]
            AML["Azure ML<br/>training, registry, RAI dashboard"]
        end
        subgraph LIVE["Live and insight"]
            PUB["Web PubSub<br/>urgent alerts"]
            PBI["Power BI<br/>therapist dashboard"]
            AI2["Application Insights"]
        end
        IOT["IoT Hub + Device Provisioning<br/>OTA firmware and models"]
    end

    CG["Caregiver phone"]
    TH["Therapist / school"]
    DEV["GitHub Actions CI/CD"]

    ES <-->|BLE| APP
    PAIR --> ENTRA
    Q -.->|TLS sync| APIM --> FN
    FN --> COS
    FN --> BLOB
    FN --> KV
    FN -.-> FOUND
    FN -.-> SPEECH
    FN -.-> SAFE
    BLOB -.-> AML
    AML -.->|new model| FN
    FN -.-> IOT -.->|OTA| ES
    FN -.->|ranked menu + phrase audio| Q
    ES -.->|urgent selection| Q
    FN --> PUB --> CG
    COS --> PBI --> TH
    APP -.-> AI2
    FN -.-> AI2
    DEV -.->|deploy| FN
