# AI Dev Kit v3

### v.3.11.22 (Jun 26, 2025) – *Preview*

* [New] Added PixelLab API (Agent-only preview; may move to addon)
* [Fixed] Fixed streaming LLM response issues
* Upgraded multiple Generator UIs

### v.3.11.20 (Jun 20, 2025) – *Preview*

* Refactored `EndpointType(int)` → `RequestType(string)`
* [New] Added TTS + Transcript streaming
* [New] Added new `LogProb` class
* Renamed `RealtimeAudioPlayer` → `StreamingAudioPlayer`
* Extended `StreamingAudioPlayer` (input sample rate, crackling remover, UnityEvents)
* [New] Added TTS Streaming Demo + Demo 8

### v.3.11.18–19 (Jun 19, 2025) – *Hotfix*

* Potential fixes for IL2CPP build issues

### v.3.11.17 (May 29, 2025) – *Hotfix*

* [Fixed] Fixed Speech Generator stack overflow
* [Fixed] Fixed `AddressableUtils` issues
* [New] Added Google Voices + TTS Tasks

### v.3.11.16 (May 29, 2025) – *Preview*

* New Generator Windows tested/debugged
* [New] Added multiple outputs for `GeneratedText` + `Moderation`
* [New] Added GridView select-action
* Abstracted `GeneratorWindowSettings`
* [New] Added multi-selection in Generator Windows
* Removed obsolete `GENImageVariationTask`

### v.3.11.15 (May 28, 2025) – *Preview*

* New Generators: Avatar, Background, Code, SFX, Video, Component
* [New] Added yield/await to all `GENTasks`
* [New] Added extra outputs to prompt records
* [Fixed] Fixed Prompt Field resize handle
* Updated base GeneratorWindow class

### v.3.11.14 (May 27, 2025) – *Preview*

* New EditorTool layout test

### v.3.11.13 (May 24, 2025) – *Preview*

* New Modular Chatbot component

  * Integrates STT (input), TTS (output), ImageGen
  * Auto-resolves response type (TTS/Image)
  * Internal AudioSource for TTS playback

### v.3.11.12 (May 24, 2025) – *Preview*

* New Components: TTS, STT, Image Generator
* [Fixed] Fixed GoogleFile upload + MIMEType issues
* [Fixed] Fixed some icon path issues

### v.3.11.11 (May 24, 2025) – *Preview*

* [Fixed] Fixed `EditorChat` + `GENChatTask` Unity crash

### v.3.11.10 (May 24, 2025) – *Preview*

⚠️ Major refactor (clean install required)

* `IUniFile` → `IFile`
* `UniFileBase<T>` → `File<T>`
* `UniAudioFile` → `File<AudioClip>`
* `UniImageFile` → `File<Texture2D>`
* `UniVideoFile`, `UniFile` → `RawFile`
* `AIProvider` → `Api`
* `UploadedFile` → `ApiFile`
* `RealtimeAudioStreamer` → `RealtimeAudioPlayer`
* Removed `FormFile`, `SerializedContent`

### v.3.11.09 (May 22, 2025) – *Hotfix*

* [Fixed] Fixed Generator Window not saving model change

### v.3.11.08 (May 22, 2025) – *Hotfix*

* [Fixed] Fixed Ollama streaming issues

### v.3.11.07 (May 21, 2025) – *Preview*

* Assistants API integration v3 (tested)
* Realtime API integration v2 (tested)
* [New] Added WebSocketEventReceiver, ToolEventReceiver, StreamingAudioEventReceiver
* [New] Added Realtime + Receiver icons
* Moved `GENChat` + `ChatSession` to Agent-only
* [Fixed] Fixed catalogue duplication issues
* Upgraded model window UI
* [Fixed] Fixed demo scenes

### v.3.11.06 (May 21, 2025) – *Preview*

* Split Chat Event Receiver into two classes:

  * Chat Event Receiver (string)
  * Chat Event Receiver (ChatMessage)

### v.3.11.05 (May 21, 2025) – *Preview*

* [New] Added save folder button in Chatbot inspector
* [New] Added TTS to SpeechAPI
* [Fixed] Fixed expandable text field

### v.3.11.04 (May 21, 2025) – *Preview*

* Multiple major changes:

  * FunctionManager supports return values
  * Google File Manager implemented (upload + management)
  * [New] Added VoiceLibrary filter window
  * [New] Added multi-response support
  * Forced model/voice dropdown update
* Renamed:

  * `ModelCapability` → `ModelFeature`
  * `AIDevKit.FileData` → `UploadedFile`
  * `Google.FileData` → `GoogleFileData`
  * `Google.File` → `GoogleFile`
  * `OpenAI.File` → `OpenAIFile`
* Removed old/deprecated classes (e.g., `UploadFileRequest`)

### v.3.11.03 (May 21, 2025) – *Hotfix*

* [Fixed] Fixed duplicate entry issue in models window
* Renamed tasks:

  * `GENImageEdit` → `GENInpaint`
  * `GENTranscript` now returns `Transcript`
  * `GENTranslation` now returns `string`

### v.3.11.02 (May 21, 2025) – *Preview*

* Introduced `GENSequence` prototype
* [New] Added `GENModerationTask` + Image Moderation (OpenAI)
* Implemented UnityEngine UI extension methods for `GENTask`
* Removed Google GenerativeAIModel + ChatSession
* Cleaned up CRUDClient/Service

### v.3.11.01 (May 21, 2025) – *Preview*

* [New] Added `ChatSession.Rewind()` (Google-style)
* [New] Added moderation handling improvements
* Merged `GeneratedContent` → `ChatCompletion`

### v.3.11.00 (May 21, 2025) – *Preview*

* Major refactor of CRUDClient + CRUDService
* Refactored `ChatMessage` class into: UserMessage, SystemMessage, AssistantMessage, ToolMessage
* Integrated moderation into GENChat + Chatbot (new ModerationHandler)
* Updated demo scenes

### v.3.10.04 (Jun 5, 2025) – *Preview*

* [New] Added entry deletion to Model Catalogue
* [New] Added “Delete All Deprecated Models” option
* [New] Added Custom Model deletion support
* Special row text colors for custom/obsolete models
* [New] Added Chatbot inspector redesign for beginners
* [New] Added Moderator module to Chatbot
* Minor bug fixes

### v.3.10.01 (Jun 4, 2025) – *Preview*

* [Fixed] Fixed conversion issues
* [New] Added “Edit Script with AI” (Inspector right-click)
* [New] Added silence detection to Speech To Text component

### v.3.8.21 (Jun 1, 2025) – *Preview*

* [Fixed] Fixed build issues
* Cleaned editor scripts

### v.3.8.20 (May 30, 2025) – *Preview*

* [Fixed] Fixed Ollama server connection issue
* [Fixed] Fixed model update window termination issue

### v.3.8.19 (May 30, 2025) – *Preview*

* [New] Added click-to-show prompt history details in Generator Window
* [Fixed] Fixed total price calculation in Prompt History window

### v.3.8.18 (May 30, 2025) – *Preview*

* Tested & debugged Google TTS
* `GeneratedAsset` → `GeneratedFile`
* Generator Windows remember last prompt state
* [Fixed] Fixed Unity Component Generator
* [Fixed] Fixed Code Generator
* Upgraded Generator Windows UI
* [Fixed] Fixed Google MIMEType parsing issue
* Changed STT component Transcript receiver → Text receiver (Transcript still available via code)

### v.3.8.17 (May 29, 2025) – *Preview*

* Hotfix for Speech Generator stack overflow
* [Fixed] Fixed `AddressableUtils` issues
* [New] Added Google Voices
* [New] Added Google TTS Tasks

### v.3.8.16 (May 29, 2025) – *Preview*

* All new Generator Windows tested & debugged
* `GeneratedText`: string, string\[], usage
* Moderation: `SafetySetting`, `SafetySetting[]`, usage
* [Fixed] Fixed video prompt record showing as TTS record
* [New] Added select-action to GridView
* Abstracted `GeneratorWindowSettings`
* [New] Added multi-selection to Generator Windows
* Merged `AIDevKitSettings.RuntimeOutputPath` + `EditorOutputPath` → `OutputPath`
* Removed `GENImageVariationTask` (DALL-E2 only, deprecated)

### v.3.8.15 (May 28, 2025) – *Preview*

* [New] Added Avatar Generator
* [New] Added Background Generator
* [New] Added `yield await` to all GENTasks
* [New] Added method to append extra outputs to prompt records
* [New] Added `GENCode` (generate C# code for Unity)
* [New] Added new Generator GUIs: Code, SFX, Video, Component
* [Fixed] Fixed Prompt Field resize handle
* [New] Added `GENVideo` prompt records
* `BaseGeneratorWindow` → `GeneratorWindowBase`
* [New] Added “Animated Toggle IMGUI”

### v.3.8.14 (May 27, 2025) – *Preview*

* New EditorTool layout test

### v.3.8.13 (May 24, 2025) – *Preview*

* [New] Added Modular Chatbot (inherits from Chatbot)

  * Supports Speech-to-Text for voice input
  * Supports Text-to-Speech for voice output
  * Supports Image generation in responses
  * Auto-resolves response type and plays TTS or renders images
  * Internal `AudioSource` handling for TTS playback

### v.3.8.12 (May 24, 2025) – *Preview*

* [New] Added new Components: Text-to-Speech, Speech-to-Text, Image Generator
* [Fixed] Fixed GoogleFile upload issues
* [Fixed] Fixed GoogleFile MIMEType issues
* [Fixed] Fixed some icon path issues

### v.3.8.11 (May 24, 2025) – *Preview*

* [Fixed] Fixed EditorChat issues
* [Fixed] Fixed `GENChatTask` Unity crash

### v.3.8.09 (May 24, 2025) – *Preview*

⚠️ Clean install required due to core refactoring

* `IUniFile` → `IFile`
* `UniFileBase<T>` → `File<T>` (abstract → class)
* `UniAudioFile` → `File<AudioClip>`
* `UniImageFile` → `File<Texture2D>`
* `UniVideoFile` → `RawFile`
* `UniFile` → `RawFile`
* `AIProvider` → `Api`
* `UploadedFile` → `ApiFile`
* `RealtimeAudioStreamer` → `RealtimeAudioPlayer`
* `StreamAudioPlayer` → `StreamingAudioPlayer`
* Removed `FormFile`, `SerializedContent` (replaced with `RawFile`, `File<T>`)  

### v.3.7.9 (May 21, 2025) – *Preview*

* Split Chat Event Receiver into two derived classes:

  * Chat Event Receiver (string)
  * Chat Event Receiver (ChatMessage)

### v.3.7.8 (May 21, 2025) – *Preview*

* [New] Added “open save folder” button in Chatbot inspector
* [New] Added TTS feature to SpeechAPI
* [Fixed] Fixed expandable text field

### v.3.7.7 (May 21, 2025) – *Preview*

* Integrated Assistants API (v3) and Realtime API (v2) – tested
* [New] Added WebSocketEventReceiver, ToolEventReceiver, StreamingAudioEventReceiver
* [New] Added new icons for Realtime + Receivers
* Moved `GENChat`, `ChatSession` to Agent-only
* Fully resolved catalogue duplication issues
* New/Deprecated Model window UI upgrade
* [Fixed] Fixed demo scenes (except Moderation)

### v.3.7.6 (May 29, 2025) – *Preview*

* Upgraded Assistants API (v3) + Realtime API (v2)
* [New] Added WebSocketEventReceiver, ToolEventReceiver, StreamingAudioEventReceiver
* [New] Added new icons for Realtime + Receivers
* Moved `GENChat`, `ChatSession` to Agent-only
* Fully resolved catalogue duplication issues
* New Model window UI; fixed demo scenes

### v.3.7.5 (May 22, 2025) – *Hotfix*

* Finished 80% of GitBook docs
* Tested/re-ordered Generate menu for Unity6
* EditorChat input auto-resizes
* [New] Added audio input + multi-attachments
* Flattened RealtimeEventReceiver hierarchy

### v.3.7.3 (May 21, 2025) – *Hotfix*

* [Fixed] Fixed `ChatRole` conversion issue
* [Fixed] Fixed `ChatStreamHandler`

### v.3.7.2 (May 21, 2025) – *Preview*

* [Fixed] Fixed duplicate model entries in window
* `GENImageEdit` → `GENInpaint`
* `GENTranscript` returns `Transcript`
* `GENTranslation` returns `string`

### v.3.7.1 (May 21, 2025) – *Preview*

* [New] Added `ChatSession.Rewind()`
* [New] Added `GENModerationTask`, Image Moderation
* [New] Added `GENSequence` prototype + Unity `Object` extension methods
* Applied Google-style moderation handling
* Cleaned up CRUD code
* Removed Google GenerativeAI model impl + ChatSession

### v.3.7.0 (May 21, 2025) – *Preview*

* [New] Added OpenAI Projects API
* Integrated moderation into `GENChat` + `Chatbot`
* [New] Added UnityEvent-based receivers (NonStreamingChat, StreamingChat, Speech, Realtime)
* Refactored CRUDClient/Service
* Refactored `ChatMessage` into `UserMessage/SystemMessage/AssistantMessage/ToolMessage`

### v.3.6.18–v.3.6.23 (May 18–23, 2025) – *Hotfixes*

* [New] Added `WebSearchOptions`, `SpeechOutputOptions`
* Moved catalogue system to common assembly (Free compatible)
* Changed catalogue update behavior (replace → add)
* [Improved] Improved key encryption + library GUIs
* [Improved] Improved Chatbot
* Abstracted catalogues
* Multiple hotfixes: Ollama server, Model Catalogue flags, EditorChat
* Chatbot gained UnityEvents

### v.3.6.14–v.3.6.16 (May 14–16, 2025) – *Hotfixes*

* DebugMode for APIs + AIDevKit
* [New] Added STT Demo, ElevenLabs fixes
* Multipart form fixes for ElevenLabs/Google
* Realtime API moved to Agent-only, renamed to `RealtimeAssistant`
* `SpeechToTextAPI` → `SpeechAssistant` (now STT/translation/voice changer)
* `ChatAPI` → `Chatbot`
* [New] Added voice changer demo
* Removed legacy components (ImageGenAPI, TTSAPI, GeminiAPI)

### v.3.6.8–v.3.6.12 (May 8–12, 2025) – *Stable hotfixes*

* Model Catalogue, Gemini, TTS, and LLM non-streaming fixes

### v.3.6.7 (May 7, 2025) – *Stable v7*

* [New] Added reasoning to `GENTask`
* [New] Added toolcalls to `GeneratedText`/`GeneratedContent`
* [New] Added `UniVideoFile`
* [New] Added streaming usage return to all APIs
* [Fixed] Fixed history saving, treeview updates, request timeouts
* [Improved] Improved EditorChat error handling

### v.3.6.6 (May 6, 2025) – *Stable v6*

* New `ChatStreamHandler` supporting tools/functions
* Usage tracking for `GENText`, `GENChat`
* Prompt History supports images & audio
* [New] Added `GENVideo` task + Editor menu
* Merged Completion + ChatCompletion
* [Fixed] Fixed Imagen, STT, and other API bugs
* Refactored prompt history creation

### v.3.6.4 (May 2, 2025) – *Stable v3*

* Removed model price simulator
* Merged Model Library + Catalogue
* Merged Voice Library + Catalogue
* Combined `Model` + `ModelProfile`, `Voice` + `VoiceProfile`
* [New] Added OpenAI metadata for models/voices
* Auto-updated ModelSelector cache on additions
* [Fixed] Fixed Editor Chat UX issues (input focus, flickering, autosave debounce)

### v.3.6.3 (May 1, 2025) – *Stable v2*

* Stabilized Model Selector
* [New] Added famous/stable models as default
* [New] Added cost info in Model Catalogue (OpenRouter only)

### v.3.6.2 (May 1, 2025) – *Stable*

* [Fixed] Fixed Assistants API & demo scenes
* [New] Added fallbacks for missing model entries
* [Improved] Improved model name analyzer
* Enforced strict defaults for paths, models, and voices
* Stabilization works

### v.3.6.0 (Apr 28–29, 2025) – *Unstable*

* Removed `VoiceSelectorGUI`; added static `VoicePopupGUI`
* [Improved] Improved meta resolvers
* [New] Added image + function support to `GENChat`
* [New] Added Ollama/OpenRouter integration with model families
* [Improved] Improved `ModelSelectorGUI` with defaults, flags, loading screens

### v.3.5.3 (Apr 27, 2025) – *Unstable*

* Redesigned & abstracted chat system
* Removed `StringOr<MessageContent>` converters
* [New] Added custom path option to `ScriptableDatabase`
* Remade chat demo scene

### v.3.5.2 (Apr 24, 2025) – *Unstable*

* Refactored core REST client for flexible API abstraction
* Removed `Query()` in favor of `List()`
* Refactored `RESTRequest` with body support
* [New] Added Ollama Embeddings, OpenRouter API, and more model management features
* Unified and renamed multiple core classes (e.g., `ToolDefinition` → `ToolCall`)

### v.3.5.1 (Apr 23, 2025)

* Refined REST download / realtime load asset conditions
* Wrote and added WebGL MPEG decoder
* [Fixed] Fixed “Generate” menu output path
* Refactored and redesigned `EditorSpeech`
* Persisted GenWindows output path across compiles
* [New] Added troubleshooting docs for plugin update errors

### v.3.5.0 (Apr 22, 2025)

* [Fixed] Fixed vision prompt word-wrap and missing field issues
* Enabled multi-image generation for models without native support
* Eliminated GUI errors in `EditorTools`
* [New] Added drag & drop to editor vision
* [Fixed] Fixed `EditorSpeech` result display
* Redesigned `EditorSpeech`; output path now persists in GenWindows
* Refined REST download / realtime asset load
* [New] Added WebGL MPEG decoder
* [Fixed] Fixed “Generate” menu output path
* [New] Added troubleshooting docs for plugin update errors

### v.3.4.19 (Apr 21, 2025)

* [New] Added ElevenLabs prompt history builder
* [Fixed] Fixed `CustomEditor` not saving values
* Integrated `FunctionManager` into `ChatCompletionTool` and `ChatSessionTool`
* [Fixed] Fixed broken demo scenes
* [Fixed] Fixed OpenAI prompt history builder
* [Fixed] Fixed Google prompt history builder
* Refactored `PromptHistoryViewer`

### v.3.4.18 (Apr 20, 2025)

* Changed edit icon for clarity
* [New] Added argument support to `FunctionManager`
* [New] Added save button to `CodeGen`
* [Fixed] Fixed multi-image rendering in `IconGen`
* [Improved] Improved `ModelSelector` by hiding providers with no models

### v.3.4.17 (Apr 20, 2025)

* [Fixed] Fixed index out of range issue in `VoiceSelectorGUI`

### v.3.4.16 (Apr 18, 2025)

* [New] Added `FunctionManager`
* \[Demo] Realtime API + Function integration

### v.3.4.15 (Apr 18, 2025)

* [Fixed] Fixed model categorizing regex
* [New] Added cancel request feature to `GENTask`
* Implemented `IconGenWindow`
* Cleaned up codegen files
* [New] Added output path tracking for `GeneratedImage` and `GeneratedAudio`
* [Fixed] Fixed realtime API sample voice paths
* [Fixed] Fixed Addressables `amsdef`

### v.3.4.14 (Apr 18, 2025)

* Removed implicit operator from `EPrefs` to prevent Mono JIT crash

### v.3.4.13 (Apr 17, 2025)

* [New] Added missing constraints to:
  * `Samples.Editor`
  * `MaterialDesign`
  * `MaterialDesign.Editor`
* Moved `Setup` folder outside of assembly

### v.3.4.12 (Apr 17, 2025)

* [Fixed] Fixed ElevenLabs voice library errors

### v.3.4.08 (Apr 17, 2025)

* Refactored `EditorChatToolbar` as separate component
* Implemented export chat as `.txt`
* Displayed chat list names as date instead of thread ID
* [New] Added more options to `GENText` and ElevenLabs `GENTasks`
* [New] Added Texture generator
* [New] Added default model settings to each API
* [New] Added fallback model support using default models
* Renamed `ImageModel` → `DallEModel`, `TTSModel` → `OpenAITTS`
* [Fixed] Fixed issue fetching Google models
* [Improved] Improved model family categorization

### v.3.4.07 (Apr 16, 2025)

* [Fixed] Fixed TTS file path issues
* [Fixed] Fixed build errors related to `StartOnInit`
* [Fixed] Fixed Unity hang on startup
* [Fixed] Fixed model update triggering every time
* [New] Added ElevenLabs subscription feature
* [New] Added `VoiceChanger` and `AudioIsolation` to `GENTask`
* [New] Added more options to `GENText`
* [Improved] Improved `EditorChat` GUI and stream handling
* [New] Added drag & drop support in `EditorChat`
* [New] Added AI-powered script menu options:
  * "Improve Readability"
  * "Refactor"

### v.3.4.16 (Apr 18, 2025)

* [New] Added model categorizing regex
* [New] Added cancel request feature to `GENTask`
* New feature: `IconGenWindow`
* [New] Added output path tracking to `GeneratedImage` and `GeneratedAudio`
* [Fixed] Fixed Addressables `amsdef`

### v.3.4.2 (Apr 13, 2025)

* Broken GUIDs fixed

### v.3.2.0 (Apr 10, 2025)

* Compile errors resolved
* Refactored tons of codes

### v.3.1.4 (Apr 7, 2025)

* Build errors resolved

### v.3.1.2 (Mar 31, 2025)

* [New] Added Google image models (Gemini, Imagen) to Editor Tool
* [New] Added new Google model families
* Refactored the project

### v.3.0.0 (Mar 17, 2025)

* [New] Added automatic model updates on Editor startup
* [New] Added automatic model enum code generation
