# AI Dev Kit v5

### v5.2.5 (2026-04-13)

* [New] Added LibraryFilterWindow for AI Resource Library (API/Type/State filter popup with scroll support)
* [Fixed] Model metadata preservation improved after Update Models + recompile (family/price/created-at retention)
* [Improved] Added CapabilitiesField card-style flags UI (clickable cards, compact/detailed modes, dedicated USS styling)
* [Improved] AgentProfile General now uses CapabilitiesField for clearer capability authoring
* [New] Introduced CharacterPersona workflow with famous-character presets and roleplay-focused fields
* [Fixed] Added CharacterPersonaInspector and fixed Persona inspector null-property crash (ArgumentNullException)
* [Improved] StringOrTextAssetDrawer improved for multiline authoring (dynamic height, newline normalization, word-wrap)
* [Changed] Updated Enterprise docs: new AssetStore page and refreshed KeyFeatures for current provider/sample set

### v5.2.4 (2026-04-10)

* [New] Microsoft Graph: Added OneDrive RAG toolchain and shared Graph drive RAG base architecture
* [New] Microsoft Graph: Added Outlook Mail toolset (mail_*) with domain/service/runtime settings and sample integration
* [New] Microsoft Graph: Added Outlook Calendar toolset (calendar_*) with domain/service/runtime settings and sample integration
* [New] Microsoft Graph: Added To Do toolset with list/task CRUD, complete/reopen flow, and pagination support
* [Improved] Extended GraphClientSettings with OneDrive/ToDo/Mail/Calendar settings and reindexChangedFiles option
* [Improved] GraphClientSettingsEditor expanded with dedicated tabs/sections for OneDrive, SharePoint, To-Do, Mail, Calendar
* [New] Added Todo/Mail/Calendar agent samples and improved shared Graph sample extractor reuse
* [Fixed] Stabilized Lyria/WebSocket path (listener init order, weighted prompt payload shape, seed deserialization compatibility)

### v5.2.3 (2026-04-09)

* [New] Added a compact single-line property drawer for ToolSupport array elements — renders ToolType, string override, and toggle on one row
* [Fixed] Removed spurious warning log in ApiProfileExtensions.GetApiUrl for APIs without a key guide URL (e.g., LMStudio)
* [Fixed] Responses API stream now correctly finalizes on all terminal statuses (Incomplete, Failed, Canceled) — not just Completed
* [Fixed] Removed duplicate ToolTypes.Function constant definitions across 9 provider settings classes; now uses centralized ToolTypes constants
* [Fixed] Playground Agent — canceling a request now immediately removes the in-progress streaming message placeholder
* [Fixed] Late-arriving streaming callbacks after cancellation no longer recreate empty message elements in ChatScrollView

### v5.2.2 (2026-04-08)

* [Changed] Migrated hosted tool configuration from legacy ToolSettings to Options + HostedToolDefinitions pipeline
* [Improved] Updated MCP tool resolution and provider-bridge integration for the new tool-definition pipeline
* [Improved] Added LMStudio support to McpServerDefinition tool type registry
* [Improved] Generation History: Improved input/output preview rendering for image, audio, video, and file record types
* [New] Introduced UsageCalculator to centralize cost calculation logic; added free-model handling and ModelPriceResult<T> TryGet pattern
* [Changed] Added provider support guide: AIDevKit Supported Providers by Package.txt

### v5.2.1 (2026-04-05)

* [Fixed] Prompt sample buttons not appearing in Image Generator playgrounds (Character, Background, Mesh Texture, Icon Art)
* [Fixed] Negative Prompt field (Amazon Bedrock) now correctly preserved in Image Generator
* [Improved] Added dedicated Authentication section to AIDevKit Settings; reordered sections for improved readability
* [Fixed] Fixed duplicate locale entries; locale dropdown now auto-selects the first unused language when adding
* [Changed] Renamed Music tab to "Music (ElevenLabs)" for clarity
* [Changed] Migrated AIDevKitScriptUtil to ScriptFileUtil under Glitch9.Editor.CodeGen namespace

### v5.2.0 (2026-04-03)

* [Changed] Renamed AIDevKit Pro → AIDevKit Agent to better reflect its focus on AI agent development
* [Changed] Renamed AIDevKit Research Lab → AIDevKit Enterprise to better reflect its target audience
* [Improved] Each AI provider is now compiled into its own assembly definition, reducing recompile scope and improving build times
* [Changed] LMStudio moved from the Enterprise package to the Agent package
* [Improved] Agent settings panels (Audio, GeneratorBehaviour) fully migrated from IMGUI to UIToolkit
* [Changed] ModelField refactored — cleaner API, removed deprecated ApiDisplay enum and constructor parameters

### v5.1.7 (2026-03-17)

* [Changed] Reorganized PlaygroundProfiles into hub-based partial files (Script, Shader, Image, Audio, Video, Text, ImageEditor)
* [Changed] Prompt samples are now profile-driven via PromptFieldEntry.Samples; removed legacy GetPromptExamples system
* [New] Added ExtraText prompt type with fully customizable label, placeholder, and callback fields
* [Fixed] Fixed object selector click issue in OverlayObjectField
* [Changed] SpokenLanguageField refactored with cleaner architecture and EPrefs binding support
* [Improved] Output file deletion now also removes associated .meta files
* [Improved] Optimized AssetDatabase refresh behavior after file operations

### v5.1.6 (2026-03-15)

* [Breaking] Streaming request types separated — GenerativeRequest is now a non-streaming base; StreamingGenerativeRequest introduced for streaming-capable requests
* [Breaking] GenerativeMediaRequest renamed to GenerativeVisualRequest to remove ambiguity with audio modalities
* [Breaking] Removed IGenerativeRequest.GetRequestParameters() and all provider-specific overrides (~30 files)
* [Breaking] Removed GenerativeSequence, ISequentialRequest, and sequence-only execution surface
* [Changed] IUnifiedApiRequestOptions replaced by IProviderRequestOptions; removed IGenerativeRequestOptions intermediate interface
* [Changed] Consolidated GenerateDefaultFileName() overrides into a single FileNameKey abstract property on GenerativeRequest
* [Fixed] SpeechGenerationRequest.EndpointType was incorrectly returning RequestType.Transcription; corrected to SpeechGeneration
* [Fixed] PixelLab animation payload now always includes required fields; mixed-size tensor errors resolved
* [Fixed] Texture encoding now handles compressed/non-readable textures via RenderTexture + ReadPixels fallback
* [New] ElevenLabsAudioOptions added as a dedicated provider options class for ElevenLabs audio format configuration
* [Improved] Generation History: Migrated to NDJSON append-log format with split header + detail payload for improved load performance
* [Improved] Provider balance button now opens a context menu with a Refresh option to fetch latest credits from the API
* [Improved] Added new generic AIResourceCatalogBase; ModelCatalog, VoiceCatalog, and UploadedFileCatalog updated to use it

### v5.1.5 (2026-03-12)

* [New] LM Studio provider — LMStudioClient with Chat, Models, and MCP services; supports both OpenAI-compatible and native LM Studio APIs
* [New] Native LM Studio stateful multi-turn chat via conversation_id sessions
* [New] LMStudio editor integration — LMStudioSettingsEditor and LMStudioSettingsProvider added
* [Fixed] Generation History total price label now updates correctly
* [Improved] GenerationRecordWindow migrated from IMGUI to UIToolkit
* [Improved] VoiceMetadataWindow migrated from IMGUI to UIToolkit
* [Improved] Model Library and Voice Library now include a Display menu to toggle visible columns
* [Improved] MatrixTableBase now stretches vertically when the window is resized
* [Improved] Added SupportsCustomSeed provider capability flag; ApiRules now reads from provider settings instead of hardcoded logic
* [Improved] Model update prompt now shows a 3-button dialog with a Never Ask Again option
* [Fixed] Added Stability AI API profile entry; fixed missing profile causing About window exception

### v5.1.4 (2026-03-11)

* [Improved] GuideWindowBase redesigned — unified scroll view, font-size slider, and content height calculation across all guide windows
* [New] Added ProviderChatApiSupportsWindow — matrix table showing which providers support each ChatApiType
* [New] Added AgentToolGuideWindow — in-editor guide covering FunctionCallManager, IFunctionExecutor, and RegisterToolExecutor patterns
* [New] Added ApiRules.ChatApi partial with SupportsChatApiType(Api, ChatApiType) extension method
* [Changed] ModelRules.cs split into 5 categorized partials for maintainability
* [New] Added ApiRules.ModelCapability partial; extracted SupportsCapabilities/SupportsCapability from ModelRules
* [Fixed] Generation record header alignment corrected in GenerationRecordDetailsWindow (CSS class conflict resolved)
* [Fixed] GenerationRecord.EndpointType type changed from string to EndpointType struct to fix silent serialization failures
* [Changed] GenerationRecord.Metadata replaced with RequestContext (CurlContext) — stores full HTTP request context

### v5.1.1 (2026-03-11)

* [Fixed] Corrected property name bindings in the Agent settings custom inspector

### v5.1.0 (2026-03-11)

* [Changed] GenerationRecord.EndpointType type changed from string to EndpointType struct for correct serialization
* [Changed] GenerationRecord.InputFiles legacy field removed; input files now read exclusively from IPrompt/ILoadablePrompt
* [Changed] GenerationRecord.Metadata replaced with RequestContext (CurlContext) for structured HTTP context storage
* [Changed] IGenerativeRequest.GetRequestParameters() removed; replaced by RequestOptions.OnContextCaptured event
* [New] IGenerativeRequest.RecordedContext (CurlContext) property added — populated after HTTP WithBody() is called
* [Fixed] Generation record detail window header alignment corrected (CompactListWindow USS class conflict resolved)

### v5.0.6 (2026-02-25)

* [Fixed] Custom inspector rendering issues
* [Fixed] Unity 2022 LTS compatibility errors

### v5.0.4 (2026-02-25)

* [Fixed] Initialization error in auto-generated code snippets

### v5.0.3 (2026-02-25)

* [Fixed] Incorrect output type casting in audio generation pipeline

### v5.0.0 (2026-02-09)

* [Breaking] Rebuilt the streaming architecture from the ground up
* [Improved] Unified streaming pipeline across all modalities: text, audio, image, binary, and multimodal
* [Improved] Introduced a layered stream pipeline: Transport → Converter → Event → Aggregation
* [Improved] Standardized StreamEvent<TPayload> with an isolated StreamHeader for consistent event handling
* [Improved] Replaced ad-hoc parsing-based streaming with a clean IStreamConverter abstraction
* [Improved] Unified streaming and non-streaming paths via GenerativeEvent<TPayload, TFinal>
* [Improved] Standardized all generative outputs to the Generated<T> type
* [Improved] Improved output determinism and event ordering consistency
* [Improved] Significantly reduced API surface fragmentation across providers
