---
---

# Files

AI Dev Kit provides comprehensive file management operations for working with AI provider storage.

## Available Operations

### Upload Operations

```csharp
// Upload generic file
await Api.OpenAI.UploadFile(file).ExecuteAsync();

// Upload image
await Api.OpenAI.UploadImage(texture).ExecuteAsync();

// Upload audio
await Api.OpenAI.UploadAudio(audioClip).ExecuteAsync();

// Upload screenshot
await Api.OpenAI.UploadScreenshot().ExecuteAsync();
```

### Management Operations

```csharp
// List files
var files = await Api.OpenAI.ListFiles().ExecuteAsync();

// Download file
var fileData = await Api.OpenAI.DownloadFile(fileId).ExecuteAsync();

// Delete file
await Api.OpenAI.DeleteFile(fileId).ExecuteAsync();
```

## Common Use Cases

### Upload for Fine-tuning

```csharp
IFile trainingData = CreateTrainingFile();
var uploadResult = await Api.OpenAI
    .UploadFile(trainingData)
    .SetPurpose("fine-tune")
    .ExecuteAsync();

string fileId = uploadResult.Id;
```

### Upload for Assistants

```csharp
IFile document = LoadDocument();
var uploadResult = await Api.OpenAI
    .UploadFile(document)
    .SetPurpose("assistants")
    .ExecuteAsync();
```

### Upload for Vision

```csharp
Texture2D image = capturedImage;
var uploadResult = await Api.OpenAI
    .UploadImage(image)
    .SetPurpose("vision")
    .ExecuteAsync();
```

## File Purposes

Different providers support different file purposes:

| Purpose      | Description         | Use Case         |
| ------------ | ------------------- | ---------------- |
| `assistants` | Assistant API files | RAG, file search |
| `vision`     | Vision API files    | Image analysis   |
| `fine-tune`  | Fine-tuning data    | Model training   |
| `batch`      | Batch API files     | Bulk processing  |

## Provider Support

### OpenAI

```csharp
// Full support for all operations
await Api.OpenAI.UploadFile(file).ExecuteAsync();
await Api.OpenAI.ListFiles().ExecuteAsync();
await Api.OpenAI.DeleteFile(fileId).ExecuteAsync();
```

### Anthropic

```csharp
// Upload and download support
await Api.Anthropic.UploadFile(file).ExecuteAsync();
await Api.Anthropic.DownloadFile(fileId).ExecuteAsync();
```

### Google

```csharp
// Limited file operations
await Api.Google.UploadFile(file).ExecuteAsync();
```

## Next Steps

* [Upload File](../../../AIDevKit/Unity.AIDevKit/3_requests/utilities/files/upload-file.md) - Upload generic files
* [Upload Image](../../../AIDevKit/Unity.AIDevKit/3_requests/utilities/files/upload-image.md) - Upload images
* [Upload Audio](../../../AIDevKit/Unity.AIDevKit/3_requests/utilities/files/upload-audio.md) - Upload audio
* [Upload Screenshot](../../../AIDevKit/Unity.AIDevKit/3_requests/utilities/files/upload-screenshot.md) - Capture and upload
* [Download File](../../../AIDevKit/Unity.AIDevKit/3_requests/utilities/files/download-file.md) - Download files
* [Delete File](../../../AIDevKit/Unity.AIDevKit/3_requests/utilities/files/delete-file.md) - Remove files
* [List Files](../../../AIDevKit/Unity.AIDevKit/3_requests/utilities/files/list-files.md) - Browse files
