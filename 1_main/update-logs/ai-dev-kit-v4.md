# AI Dev Kit v4

### v4.8.5 (2025-12-09)

* [Improved] API key validation logic and error messaging
* [Fixed] Minor UI issues in the Model Library and Voice Library windows
* [Changed] Renamed IRequestParam → IRequestParameter for clarity
* [Changed] Renamed RequestParamType → RequestParameterType for clarity
* [Improved] General error handling improvements
* [New] Amazon AWS — Initial integration (Enterprise)
* [New] Amazon Bedrock (chat and model services)
* [New] Amazon S3 (file storage)
* [New] Amazon Polly (text-to-speech)
* [New] Amazon Transcribe (speech-to-text)

### v4.8.4 (2025-12-04)

* [Fixed] GroqCloud client request failures
* [Fixed] Anthropic client serialization issues
* [Fixed] xAI client connectivity issues
* [Fixed] Ollama client errors
* [Fixed] Ollama chat stream handler parsing
* [Changed] FileDownload now returns IFile instead of FileContentData
* [Changed] Migrated Settings Provider UI to UIToolkit
* [Fixed] Stack overflow in ImageGeneratorWindow
* [Changed] Image API: Renamed ImageInpaintRequest → ImageEditRequest
* [New] Image API: Added ImageEditType enum (required for all image edit requests)
* [New] Image API: Added precise per-request usage calculation
* [New] Image API: Added Amazon Titan image generation configurations
* [New] Image API: Added Stability AI image generation configurations
* [Improved] Embedding API: Extended EmbeddingPrompt to support Audio and Video input types
* [New] AI21 Labs — Added Chat, Models, and Files API support (Enterprise)
* [New] Amazon Bedrock — Added Count Tokens, Models, Converse API adapter, Titan Image adapter, and Stability adapter (Enterprise)

### v4.8.3 (2025-12-04)

* [Fixed] GroqCloud, Anthropic, xAI, and Ollama client issues
* [Fixed] Ollama chat stream handler
* [Fixed] Stack overflow crash in ImageGeneratorWindow
* [Changed] Migrated Settings Provider UI to UIToolkit
* [Changed] Image API: Renamed ImageInpaintRequest → ImageEditRequest; added ImageEditType
* [New] Image API: Added Titan and Stability AI configurations to image requests
* [Improved] Embedding API: Added Audio and Video input type support
* [New] AI21 Labs — Added Chat, Models, and Files API (Enterprise)
* [New] Amazon Bedrock — Added Count Tokens, Models, Converse API, Titan Image, and Stability adapters (Enterprise)

### v4.8.2 (2025-12-03)

* [New] Added music generation request type (GENMusic)
* [New] Implemented ElevenLabs music generation
* [Changed] Refactored and cleaned up the ElevenLabs client
* [New] Added Mistral moderation API support
* [Changed] Consolidated duplicate HarmCategory entries
* [New] Added file download (FileDownload) and signed URL (FileGetSignedUrl) request types
* [New] Added Mistral text-to-speech and streaming TTS
* [New] Added OCR request type (GENOcr), OcrModelType, and OcrPrompt
* [New] Added Music Generator Window
* [New] Added OCR Text Generator Window
* [Improved] UserProfile and AgentProfile custom inspectors
* [Changed] Removed unused GUI and assembly code

### v4.8.1 (2025-12-01)

* [Changed] Reorganized and cleaned up project file structure
* [Changed] Standardized naming conventions across the codebase
* [New] Cohere API — chat streaming, function calling, and message content support (Enterprise)
* [New] Mistral API — thinking-token processing and function calling support (Enterprise)
* [Changed] Refactored MessageContent and ContentPart for extensibility
* [New] Added new ContentPart types: DocumentPart, ThinkingPart, ReferencePart
* [Changed] Simplified streaming API: replaced CreateStreamAsync + await foreach with StreamAsync + await foreach

### v4.8.0 (2025-11-30)

* [Fixed] Model and Voice assets path normalization issues resolved


### v4.7.29 (2025-11-29)


* [Fixed] Unity 2022 backward compatibility restored


### v4.7.28 (2025-11-28)


* [New] DeepSeek API support (Agent package)


* [Fixed] Runtime path normalization issues
* [Fixed] Editor path normalization issues
* [Fixed] Transcriber component issues


### v4.7.24-4.7.27 (2025-11-26 ~ 2025-11-28)


* [New] GroqCloud file support in Data Manager
* [New] `GetCredits()` request - returns prepaid balance (used/total)
* [New] FileMetadata to file classes


* [Fixed] Multiple filtered popup issues (#2, #3, #4)
* [Fixed] Path issues resolved


* [Changed] Removed unused 'RESEARCH_LAB' script define


### v4.7.23 (2025-11-23 ~ 2025-11-24)


* [New] OpenRouter Files API (Data Manager)
* [New] Voice `GetVoice()` extension method
* [New] GENSpeech `SetVoice()` extension method
* [New] GENSpeech `SetModel()` extension method (non-generic)
* [New] Model `GetModel()` extension method
* [New] Embedding Request `SetModel()` extension method
* [New] Get Voice API (Google Gemini, ElevenLabs)
* [New] File upload / list / delete requests (OpenRouter)


* [Fixed] Voice Metadata Generator / Model Metadata Generator Window (error popup fixed)
* [Fixed] Streaming handler resolved
* [Fixed] Streaming Audio Player fixed
* [Fixed] Auto-resolve PlaybackFormat from request format
* [Fixed] Filtered Popup search function fixed
* [Fixed] Generated Voice `OnPronounce()` callback fixed


* [Changed] Dev Chat tool type changed from Coding Action -> Text Chat


### v4.7.22 (2025-11-21)


* [New] Perplexity API support (+OpenRouter)
* [New] xAI API support (+OpenRouter)
* [New] Cohere API support (+OpenRouter)
* [New] Mistral API support (+OpenRouter)
* [New] OpenRouter API support (OpenRouter)
* [New] OpenRouter Files API support
* [New] New model metadata system
* [New] Voice metadata system
* [New] Enterprise tier (Agent users exclusive, preview feature)


* [New] Chat request with tool calls (Cohere, Mistral, OpenRouter, Perplexity, xAI)
* [New] Chat request without tool calls (Cohere, Mistral, OpenRouter, Perplexity, xAI)
* [New] Tool calling (Cohere, Mistral, OpenRouter, Perplexity, xAI)
* [New] Tool conversion (Cohere, Mistral, OpenRouter, Perplexity, xAI)
* [New] Streaming response (Cohere, Mistral, OpenRouter, Perplexity, xAI)
* [New] Streaming tool calls (Cohere, Mistral, OpenRouter, Perplexity, xAI)
* [New] Input audio (Cohere, Mistral, OpenRouter, Perplexity, xAI)
* [New] Multiple system messages (Cohere, Mistral, OpenRouter, Perplexity, xAI)


* [Changed] Provider naming: ElevenLabs Audio API -> ElevenLabs
* [Changed] Provider naming: OpenRouter (Text Only) -> OpenRouter


* [Fixed] OpenAI Realtime Metadata (event id, item id, etc.)
* [Fixed] Tool Events now properly pushed to Debug Window


### v4.7.20 (2025-11-19)


* [New] Duplicate Model / Voice feature in Model Library & Voice Library tools
* [New] Model / Voice preset management system
* [New] Voice preset management UI


* [Changed] Data Manager: Files are now represented as Unity assets instead of pure C# classes
* [Changed] FileManager class changed to FileCatalogue
* [Changed] File assets changed to class File<T> where T can be AudioClip, Texture2D, or VideoClip


### v4.7.18-4.7.19 (2025-11-16 ~ 2025-11-18)


* [New] Multiple Tool result events
* [New] Input audio format handling
* [New] Output audio format handling
* [New] Anthropic Message Batches API
* [New] OpenRouter File API (untested)
* [New] Workspace window for navigating editor tools


* [Fixed] File API issues
* [Fixed] GenerateSpeech issues


### v4.7.16 (2025-11-11 ~ 2025-11-13)


* [New] File manager tool (File API management tool)
* [New] DLL missing warning window at Editor start
* [New] Anthropic Prompt Caching API
* [New] OpenAI GPT-4o Audio Preview API
* [New] Google Gemini Context Caching API


* [Fixed] Fixed input delay for speech transcriber
* [Fixed] Fixed Speech Generator double slash issue
* [Fixed] Improved Google Gemini client error message


### v4.7.10-4.7.11 (2025-11-09)


* [New] Tool error messages to Debug/Log window
* [New] Tool failure events
* [New] Utility class to get model capabilities at runtime
* [New] OpenAI Responses API Draft (preview, limited testing)


* [Changed] Tool output now logged to Debug/Log window (tool name shown for better readability)


### v4.7.7-4.7.9 (2025-11-07 ~ 2025-11-08)


* [New] JSON Schema tool description support
* [New] Google Gemini grounding feature with Google Search
* [New] Google Gemini grounding feature Code Execution
* [New] Local Shell Tool support (Windows/Mac/Linux command execution)
* [New] Google Gemini audio generation API


* [Changed] Simplified GEN Tasks


* [Fixed] OpenAI Realtime API audio output issue
* [Fixed] Multiple Tool-related errors


### v4.7.7 (2025-11-05)


* [New] Tool Definition with Google Gemini support
* [New] Tool calls from AI Responses with Google Gemini
* [New] Server => Client tool confirmation (supported on OpenAI, Google Gemini)
* [New] Server => Client request data (supported on OpenAI)
* [New] MCP Server support (stable version)
* [New] Google Gemini audio input support
* [New] Google Gemini video input support
* [New] Google MakerSuite files API (untested)


* [Changed] Obsolete Anthropic client APIs


* [Fixed] Various minor issues


### v4.7.6 (2025-11-03)


* [Fixed] Speech Generator audio caching
* [Fixed] Fixed Voice Changer issue


### v4.7.5 (2025-10-29)


* [New] OpenAI Realtime API – text/audio streaming support
* [New] OpenAI Realtime API – client => server function calls
* [New] OpenAI Realtime API – server => client function requests
* [New] OpenAI Realtime API – audio response streaming (input/output formats selection)
* [New] OpenAI Realtime API – assistant voice customization
* [New] OpenAI Realtime API – audio input/output event hooks
* [New] OpenAI Realtime API – conversation item serialization
* [New] Assistants API – thread run support
* [New] Assistants API – streaming support
* [New] Assistants API – tool output submission


* [Fixed] Model Library issues
* [Fixed] Multiple demo scenes issues


### v4.7.4 (2025-10-27)


* [Fixed] Model / Voice Library changes not reflecting in prefabs/scenes


### v4.7.2 (2025-10-26)


* [Fixed] ScriptableObject Toolkit initialization issues


### v4.7.1 (2025-10-24)


* [New] Auto-conversion to .wav format if format not supported
* [New] Editor tooltips for model library fields


* [Fixed] GENTask errors
* [Fixed] Voice metadata generator
* [Fixed] Model metadata generator


### v4.7.0 (2025-10-23)


* [New] Lyria Music Player component
* [New] Lyria Music Track (ScriptableObject)
* [New] Google tools: Google Search, Code Executor, URL Context
* [New] OpenAI Streaming Image API
* [New] TextEmbeddingTask (`.GENTextEmbedding()`)

#### v.4.6.5 (Sep 21, 2025) – *Preview*

* [New] Implemented **Responses API**
* [New] Implemented **MCP Tools**
* [New] Implemented **Local Shell Tools**

#### v.4.6.2 (Sep 2025)

* [New] Internal unitypackage build (`40602`) / packaging updates

#### v.4.5.32 (Sep 03, 2025) – *Pre-Release*

* [New] Added **Voice Changer Window** (new Generator Window)
* [New] Upgraded **Generator Window UIs**
* [New] Fixed **Assistant Profile inspector** issues

#### v.4.5.27 (Aug 30, 2025)

* [New] Fixed missing marker issues
* [New] Fixed initial setup issues
* [New] Cleaned up initial setup process

#### v.4.5.13 (Aug 22, 2025) – *Pre-Release*

* [New] Fixed **assistant chatbot** demo scene
* [New] Fixed **realtime chatbot** demo scene
* [New] Upgraded **MonoBehaviour** and **ScriptableObject** custom editors

#### v.4.5.11 (Aug 22, 2025)

* [New] Fixed broken sample scenes
* [New] Upgraded model asset inspector

#### v.4.5.09 (Aug 22, 2025) – *Hotfix*

* [New] Generator Window hotfixes

#### v.4.5.06 (Aug 22, 2025) – *Pre-Release*

* [New] Merged **Agent assembly** into **Core** (simpler setup)
* [New] Added **Audio Clip–based Prompt Generator** window
* [New] Fixed streaming chat not pushing response messages
* [New] Added Editor session info formatter to Anthropic chat requests
* [New] Polished & stabilized GenAI Windows
* [New] Verified Unity 2022 compatibility

#### v.4.2.22 (Aug 10, 2025) – *Preview*

* [New] Voice Library GUI updates
* [New] Voice Metadata system upgrades
* [New] New **Locale** management system (replaces `SystemLanguage`, stays compatible)

#### v.4.2.21 (Aug 10, 2025) – *Preview*

* [New] Test implementation of **Microsoft Azure TTS/STT** (experimental)

#### v.4.2.20 (Aug 08, 2025) – *Preview*

* [New] Applied Chatbot/Assistant naming rules

  * Components: `Chatbot` (e.g., Realtime Assistant → Realtime Chatbot)
  * Profiles: Assistant Profiles
* [New] Removed icons from foldout headers
* [New] Various editor GUI refinements

#### v.4.2.19 (Aug 07, 2025) – *Hotfix*

* [New] Cleaned up inspectors
* [New] Fixed model ID issue in usage tracking (ChatSession)

#### v.4.2.18 (Aug 07, 2025) – *Preview*

* [New] Introduced **Generative Profile System**
* [New] Added ScriptableObject assets:

  * Local Chatbot Profile
  * Assistants API Chatbot Profile
  * Realtime Assistant Profile
* [New] Implemented custom inspectors for new profiles
* [New] Renamed: `VoiceGender` → `Gender`, `Chatbot` → `LocalChatbot`, `ChatbotBase` → `Chatbot`, `Moderator` → `ContentModerator`
* [New] Enhanced ScriptableObject debugging tools; removed redundant editor code

#### v.4.2.17 (Aug 06, 2025) – *Hotfix*

* [New] Added fallback codes for **ProjectContext** (ScriptableObject)

#### v.4.2.16 (Aug 05, 2025) – *Preview*

* [New] Added **Artificial Analysis API** (internal)
* [New] Refactored model metadata resolvers

#### v.4.2.15 (Aug 06, 2025) – *Hotfix*

* [New] Fixed chat profile not applying to Chatbot

#### v.4.2.14 (Aug 05, 2025) – *Hotfix*

* [New] Added fallback codes to Chatbot, ChatSession, ChatSessionController

#### v.4.2.13 (Aug 05, 2025) – *Hotfix*

* [New] Fixed prompt checks

#### v.4.2.12 (Aug 04, 2025) – *Preview*

* [New] Added **Realtime Assistant Profile**
* [New] Refactored Realtime Assistant
* [New] Refactored Chatbot (Assistants API)
* [New] Removed legacy Assistant lifecycle events
* [New] Added new Assistant status event
* [New] Added Conversation Management support
* [New] Redesigned FileManager editor UI
* [New] Added Assistant Thread TreeView editor window

#### v.4.2.11 (Aug 01, 2025) – *Preview*

* [New] Refactored **UnityEvents** & custom inspector handling
* [New] Improved UnityEvent serialization & GUI rendering
* [New] Added editor icons
* [New] Renamed collections: `ReferencedDictionary` → `SerializableDictionary`, `Metadata` → `SerializableMetadata`, `Database` → `SerializableDatabase`

#### v.4.2.10 (Jul 29, 2025) – *Preview*

* [New] Continuing Agent sample fixes
* [New] Removed unused editor icons
* [New] Organized editor icons & GUI labels

#### v.4.2.9 (Jul 29, 2025) – *Preview*

* [New] Finished new inspector designs
* [New] Fixed WebGL build issues (not fully tested)
* [New] Added/replaced editor icons

#### v.4.2.8 (Jul 28, 2025) – *Hotfix*

* [New] Minor fixes ("dumb mistakes")

#### v.4.2.7 (Jul 28, 2025) – *Preview*

* [New] New inspector designs (almost done)

#### v.4.2.6 (Jul 28, 2025) – *Preview*

* [New] Working on new inspector designs (not finished)
* [New] Separated **Speech Recorder** from AI components that use recording

#### v.4.2.5 (Jul 28, 2025) – *Preview*

* [New] WIP: new **Inspector designs** (not finished)
* [New] **Speech Recorder** separated from AI components using recording feature

#### v.4.2.4 (Jul 27, 2025) – *Preview*

* [New] Refactored **Chatbot & ChatSession** architecture

  * `ChatSession` now holds **mutable data only**
  * Introduced **ChatbotProfile** (ScriptableObject) for immutable data (`Create > AIDevKit > Chatbot`)
  * Chatbot requires both `ChatbotProfile` + `ChatSession`
* [New] Added **Custom Inspectors** for Voice & Model assets
* [New] Added fallback logic if Default Resources aren't generated

#### v.4.2.3 (Jul 25, 2025) – *Preview*

* [New] Fixed new chat issue in EditorChat
* [New] Moved Styles into **Gizmos** folder
* [New] Improved **Generator Window UI**
* [New] Improved **Hub Window UI**
* [New] Tested & debugged **GroqCloud TTS**

#### v.4.2.2 (Jul 24, 2025) – *Preview*

* [New] Added **GroqCloud integration** (Chat tested; STT/TTS untested)
* [New] Improved model & voice metadata management
* [New] Redesigned **GENTask option system** → `.SetOptions(TOption options)`

  * Example: `.SetOptions(new OpenAICompletionOptions())`
* [New] Added `temperature`, `seed`, `stopSequences` to **GENResponse** & ChatSession
* [New] Removed `ReasoningOptions`, `WebSearchOptions`, `AdvancedModelSettings`, `SpeechOutputOptions` → migrated into provider-specific option classes

#### v.4.2.1 (Jul 23, 2025) – *Preview*

* [New] `ApiSettings` are no longer singletons → access via clients

  * Example: `OpenAI.DefaultInstance.Settings` (instead of `OpenAISettings.Instance`)
* [New] New **IApiUser** interface for in-game users

  * Provides `Id` + `GetApiKey(Api api)` override
* [New] Improved UI for **AI Dev Kit Hub (Explorer)**
* [New] Refactored **ApiSettings architecture**
* [New] Added dedicated custom editor for `.asset` files

#### v.4.2.0 (Jul 22, 2025) – *Preview*

⚠️ *Super Warning*: Remove previous version completely (many GUID breaks).

* [New] Complete cleanup of project + strict assembly management
* [New] Added **GENPixelArt** to fluent API
* [New] API-specific enums renamed (e.g., `OpenAI.ImageDetail`, `GoogleTypes.PersonGeneration`)
* [New] Added `CancelLastRequest()`, `CancelAllRequests()` to `AIComponent`
* [New] **Project Context settings** separated from `AIDevKitSettings` → standalone Editor Window

#### v.4.1.3 (Jul 18, 2025) – *Preview*

* [New] New **UnityEvent Wrappers & Improvements**

  * `StreamingUnityEvent<>` wraps 3 UnityEvents: `OnStartStreaming`, `OnReceiveData`, `OnFinishStreaming`
* [New] Improved **CustomPropertyDrawer** (new collapsed texture)
* [New] Renamed Components:

  * `AIComponentBase` → `AIComponent`
  * `GeneratorComponentBase` → `AIContentGenerator`
  * `AudioRecordingComponentBase` → `SpeechToContentGenerator`

#### v.4.1.2 (Jul 17, 2025) – *Preview*

* [New] Added `Api` parameter to **GENTasks**
* [New] Added stricter **Api checks**
* [New] Added new **UnityEvent system** (replace chat event receivers)
* [New] Restored demo scenes (not tested)
* [New] Added storage location selection to **Chat Session**
* [New] Added automatic Chat Session creation on Chatbot start

#### v.4.1.1 (Jul 16, 2025) – *Preview*

* [New] Component reworks completed
* [New] ExTreeView reworks completed
* [New] Added attachment transfer in **GENStructTask**
* [New] Fixed broken demo scenes

#### v.4.1.0 (Jul 15, 2025) – *Preview*

* [New] Complete rework of **component architectures** & custom editors
* [New] Model/Voice Library abstractions (unstable)
* [New] Removed UniTask async methods with return values from components
* [New] Renamed Components:

  * `TextToSpeech` → `SpeechGenerator`
  * `SpeechToText` → `SpeechTranscriber`
* [New] Renamed Classes:

  * `Content` → `ChatContent`
  * `ContentPart` → `ChatContentPart`
  * `RequestType` → `RequestTypes`
* [New] Removed `IChatbot`

#### v.4.0.9 (Jul 14, 2025) – *Preview*

* [New] Chatbot inspector rework (in progress)
* [New] Added **task queue handler** to all components (not tested)
* [New] Added **Shader Generator** windows

#### v.4.0.8 (Jul 13, 2025) – *Preview*

* [New] Prompt history saved as **JSON** (instead of ScriptableObject)

  * Now trackable on devices and at runtime
  * More stable than ScriptableObject (slightly slower)
* [New] Minor Generator Windows fixes

#### v.4.0.7 (Jul 13, 2025) – *Preview*

* [New] Fixed File management system
* [New] Added Demo: **Chatbot + Speech-to-Text**

#### v.4.0.6 (Jul 11, 2025) – *Preview*

* [New] Refactored **ChatSession** for performance
* [New] Added `autoStopRecordingOnSilence` option to **AudioRecordingAIComponent**
* [New] Updated **RealtimeAudioRecorder**
* [New] Fixed modality flag dropdown in Model Library
* [New] Renamed Classes:

  * `ChatCompletionStreamHandler` → `StreamingChatHandler`
  * `ChatCompletionChunk` → `StreamingChatChunk`
  * `AIServerSentEventHandler` → `ServerSentEventHandler`
  * `AIServerSentEvent<T>` → `ServerSentEvent<T>`
  * `AIServerSentEventBuffer<T>` → `ServerSentEventBuffer<T>`

#### v.4.0.5 (Sep 21, 2025) – *Preview*

* [New] Implemented **Responses API**
* [New] Implemented **MCP Tools**
* [New] Implemented **Local Shell Tools**

#### v.4.0.4 (Aug 30, 2025) – *Preview*

* [New] Fixed missing marker issues
* [New] Fixed initial setup issues
* [New] Cleaned up initial setup process

#### v.4.0.3 (Aug 22, 2025) – *Pre-Release*

* [New] Fixed **assistant chatbot demo** scene
* [New] Fixed **realtime chatbot demo** scene
* [New] Upgraded **MonoBehaviour components custom editors**
* [New] Upgraded **ScriptableObject assets custom editors**

#### v.4.0.2 (Aug 22, 2025) – *Pre-Release*

* [New] Merged **Agent assembly** into **Core** to simplify setup
* [New] Added **Audio Clip–based Prompt Generator** window
* [New] Fixed **streaming chat not pushing response messages**
* [New] Added **Editor session info formatter** to Anthropic chat requests
* [New] Polished & stabilized **GenAI Windows**
* [New] Verified **Unity 2022 compatibility**

#### v.4.0.1 (Aug 10, 2025) – *Preview*

* [New] Test implementation of **Microsoft Azure TTS, STT**
* [New] Voice Library GUI updates
* [New] Voice Metadata system upgrades
* [New] New **Locale management system** (replaces SystemLanguage, backward compatible)

#### v.4.0.0 (Aug 7, 2025) – *Preview*

* [New] Introduced **Generative Profile System**
* [New] Added **Local Chatbot Profile**, **Assistants API Chatbot Profile**, **Realtime Assistant Profile**
* [New] Implemented custom inspectors for new generative/chatbot profiles
* [New] Renamed:

  * `VoiceGender` → `Gender`
  * `Chatbot` → `LocalChatbot`
  * `ChatbotBase` → `Chatbot`
  * `Moderator` → `ContentModerator`
* [New] Enhanced **ScriptableObject debugging tools**
* [New] Removed redundant editor code
