# Live Transcription Web UI -- Prototype

### Using Azure Speech-to-Text with Real-Time Diarization

This project is a **rapid prototype** of a browser-based live
transcription tool.\
It demonstrates microphone capture, real-time speech recognition,
speaker diarization, and live editing of transcript segments.

The goal is **functionality and workflow**, not production quality.

------------------------------------------------------------------------

## 🔥 Features

-   **Microphone capture** directly in the browser\
-   **Real-time transcription** using Azure Cognitive Services Speech
    SDK\
-   **Automatic speaker diarization** → segments created when speaker
    changes\
-   **Editable transcript**
    -   Edit text as it comes in\
    -   Edit speaker labels (e.g., "Speaker 1" → "Judge")\
-   **Segment-based UI** (each segment = 1 speaker turn)\
-   **Export transcript** to `.txt` or `.json`\
-   Lightweight, simple UI (no design system required)

------------------------------------------------------------------------

## 🧱 Architecture Overview

### Frontend (Primary Component)

Runs in the browser and does:

-   Microphone access (`getUserMedia`)
-   Initializes Azure Speech client\
-   Streams audio to Azure and receives partial/final results
-   Maintains an in-memory `segments[]` array\
-   Renders editable transcript blocks\
-   Handles export/download

### Backend (Optional)

A minimal backend may be used for:

-   Providing Azure Speech tokens securely (recommended)\
-   Simple hosting

If preferred, the developer can implement **frontend-only** with a token
endpoint.

------------------------------------------------------------------------

## 📦 Tech Stack

**Required**

-   JavaScript or TypeScript\
-   Azure Speech SDK for JavaScript\
-   Simple Web UI → React recommended, but plain JS is okay

**Optional**

-   Node.js backend for token exchange\
-   Vite / Webpack / Create-React-App for development

------------------------------------------------------------------------

## 🎤 User Flow

1.  User opens the page.\
2.  Clicks **Start Recording** → browser asks for mic permission.\
3.  Transcription begins:
    -   Azure returns partial results (interim text)\
    -   Azure returns final results (completed segment)\
    -   Each result contains a **speaker ID** (`Speaker 1`, `Speaker 2`,
        etc.).\
4.  UI updates by creating **segments** whenever:
    -   Azure finalizes a segment, OR\
    -   Speaker ID changes.\
5.  User can edit:
    -   Segment text\
    -   Segment speaker label\
6.  User clicks **Stop Recording**.\
7.  User exports transcript.

------------------------------------------------------------------------

## 🧩 Data Model

``` ts
type TranscriptSegment = {
  id: string;            // unique ID
  speakerId: string;     // raw from Azure, e.g. "Speaker 1"
  speakerLabel: string;  // editable label, e.g. "Judge"
  text: string;          // editable text
  isFinal: boolean;      // true when Azure finalizes segment
};
```

State:

``` ts
segments: TranscriptSegment[];
```

------------------------------------------------------------------------

## 🚀 How Transcription Works (Logic)

1.  Initialize Azure Speech with diarization enabled:

    ``` js
    speechConfig.setProperty("SpeechServiceResponse_RequestWordLevelTimestamps", "true");
    speechConfig.setProperty("SpeechServiceResponse_DiarizeSpeaker", "true");
    ```

2.  Start `ConversationTranscriber` with microphone audio.

3.  Listen for events:

    -   `transcribing` →\
        Update the current segment text (partial result).

    -   `transcribed` →\
        Mark previous segment as final.\
        Create a new segment if `speakerId` changed.

4.  Always append segments in order.

------------------------------------------------------------------------

## 🖥️ UI Requirements

The UI must contain:

### **Top Section**

-   App title\
-   `Start Recording` button\
-   `Stop Recording` button\
-   Status indicator ("Idle", "Recording...")\
-   `Export Transcript` button

### **Transcript List**

Each segment contains: - **Input field** for speaker label\
- **Editable text block**\
- Visual separation

------------------------------------------------------------------------

## 📁 Project Structure (recommended)

    /public
    /src
      /components
      /azure
      App.jsx
      main.jsx
    /server (optional)
      tokenServer.js
    README.md

------------------------------------------------------------------------

## 🔐 Azure Configuration

Environment variables:

    AZURE_SPEECH_KEY=your-key
    AZURE_SPEECH_REGION=westeurope

Frontend should **not** expose raw keys.

------------------------------------------------------------------------

## 📝 Export Format

### `.txt` export

    [Judge] Good morning, the session starts now.
    [Witness] Yes, good morning.

### `.json` export

``` json
[
  {
    "speakerLabel": "Judge",
    "speakerId": "Speaker 1",
    "text": "Good morning, the session starts now."
  }
]
```

------------------------------------------------------------------------

## 🎯 Success Criteria

-   Live transcription into segments\
-   Speaker changes create new segments\
-   User can edit text and labels\
-   Export works\
-   Runs on Chrome/Edge

------------------------------------------------------------------------

## ⚠️ Out of Scope

-   No login\
-   No database\
-   No production security\
-   No on-prem requirement\
-   No perfect diarization\
-   Minimal UI only

------------------------------------------------------------------------

## 🧪 Developer Notes

-   Prioritize **speed & functionality**\
-   Keep the code simple\
-   Avoid over-engineering\
-   Working prototype in \~1 week
