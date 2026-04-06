# 이미지 생성 기능 구현 계획

## 1. 새로운 커맨드 추가

**위치**: `Assets\Glitch9\AIDevKit.Sheets\Editor\Scripts\Spreadsheet\Messages\Commands\Backend\SingleRowBackendCommands.cs`

```csharp
class GenerateImageInCellCommand : SingleRowBackendCommand
{
    public Model ImageModel { get; }
    public string PromptText { get; set; }
    public CellEntry TargetCell { get; }  // 단일 이미지 셀
    
    public GenerateImageInCellCommand(
        Model imageModel,
        string promptText,
        RowEntry targetedRow,
        CellEntry targetCell,
        string contributorId = null)
        : base(targetedRow, new[] { targetCell }, contributorId)
    {
        ImageModel = imageModel;
        PromptText = promptText;
        TargetCell = targetCell;
    }
    
    public override void Validate()
    {
        base.Validate();
        ThrowIf.Null(ImageModel, nameof(ImageModel));
        ThrowIf.NullOrEmpty(PromptText, nameof(PromptText));
        
        if (TargetCell.DataType != DataType.Sprite && 
            TargetCell.DataType != DataType.Texture2D)
            throw new InvalidOperationException("Target cell must be an image type.");
    }
}
```

## 2. CommandSplitter 확장

**위치**: `Assets\Glitch9\AIDevKit.Sheets\Editor\Scripts\Spreadsheet\Backend\Utility\GenerateCellsCommandSplitter.cs`

```csharp
public static List<SpreadsheetBackendCommand> SplitCommands(
    List<TargetRow> targetedRows,
    bool overwriteExistingCells,
    string promptText = null)
{
    bool translate = SpreadsheetHub.IsLocalizationMode;
    
    if (translate) { /* 기존 로직 */ }
    
    // 이미지 셀과 텍스트 셀 분리
    var (imageCells, textCells) = SeparateImageAndTextCells(targetedRows);
    
    List<SpreadsheetBackendCommand> commands = new();
    
    // 이미지 생성 커맨드
    if (!imageCells.IsNullOrEmpty())
        commands.AddRange(BuildImageGenerationCommands(imageCells, promptText));
    
    // 텍스트 생성 커맨드 (기존)
    if (!textCells.IsNullOrEmpty())
        commands.AddRange(BuildAIGenerationCommands(textCells, overwriteExistingCells, promptText));
    
    return commands;
}

private static (List<(RowEntry, CellEntry)> images, List<TargetRow> texts) 
    SeparateImageAndTextCells(List<TargetRow> targetedRows)
{
    List<(RowEntry, CellEntry)> imageCells = new();
    List<TargetRow> textRows = new();
    
    foreach (TargetRow targetRow in targetedRows)
    {
        List<CellEntry> nonImageCells = new();
        
        foreach (CellEntry cell in targetRow.TargetedCells)
        {
            if (cell.DataType.IsImageType())
                imageCells.Add((targetRow.Row, cell));
            else
                nonImageCells.Add(cell);
        }
        
        if (nonImageCells.Count > 0)
        {
            targetRow.TargetedCells = nonImageCells;
            textRows.Add(targetRow);
        }
    }
    
    return (imageCells, textRows);
}

private static List<SpreadsheetBackendCommand> BuildImageGenerationCommands(
    List<(RowEntry row, CellEntry cell)> imageCells,
    string promptText)
{
    List<SpreadsheetBackendCommand> commands = new();
    Model imageModel = SpreadsheetHub.CurrentImageModel;
    
    foreach (var (row, cell) in imageCells)
    {
        // RowMetadata의 PromptContext를 기본 프롬프트로 사용
        string cellPrompt = string.IsNullOrEmpty(promptText) 
            ? row.PromptContext 
            : promptText;
            
        if (string.IsNullOrEmpty(cellPrompt))
        {
            ShtLog.Warning($"No prompt for image generation in cell {cell.CellId}; skipping.");
            continue;
        }
        
        commands.Add(new GenerateImageInCellCommand(
            imageModel: imageModel,
            promptText: cellPrompt,
            targetedRow: row,
            targetCell: cell));
    }
    
    return commands;
}
```

## 3. Backend Controller 확장

**위치**: `Assets\Glitch9\AIDevKit.Sheets\Editor\Scripts\Spreadsheet\Backend\Controllers\GenerationBackendController.cs`

```csharp
public bool CanHandle(SpreadsheetBackendCommand cmd)
    => cmd is GenerateCellsInRowCommand 
        or GenerateNewRows 
        or GenerateImageInCellCommand;

public async UniTask ExecuteAsync(SpreadsheetBackendCommand cmd, SpreadsheetBackendRequestContext ctx)
{
    m_Service ??= new AIGenerationService();
    
    switch (cmd)
    {
        case GenerateCellsInRowCommand genCellsInRows:
            await ExecuteAsync(genCellsInRows, ctx);
            return;
            
        case GenerateNewRows genNewRows:
            await ExecuteAsync(genNewRows, ctx);
            return;
            
        case GenerateImageInCellCommand genImage:
            await ExecuteAsync(genImage, ctx);
            return;
    }
}

private async UniTask ExecuteAsync(GenerateImageInCellCommand cmd, SpreadsheetBackendRequestContext ctx)
{
    // ImageGenerationRequest 사용
    var request = new ImageGenerationRequest
    {
        Prompt = cmd.PromptText,
        Model = cmd.ImageModel.ModelId,
        // 필요한 설정 추가 (size, quality 등)
    };
    
    // API 호출 (AIGenerationService에 메서드 추가 필요)
    byte[] imageData = await m_Service.GenerateImageAsync(
        model: cmd.ImageModel,
        request: request,
        ctx.Token);
    
    if (imageData == null || imageData.Length == 0)
    {
        ShtLog.Error("Failed to generate image.");
        return;
    }
    
    // Texture2D 또는 Sprite로 변환
    Texture2D texture = new Texture2D(2, 2);
    if (!texture.LoadImage(imageData))
    {
        ShtLog.Error("Failed to load generated image data.");
        return;
    }
    
    // 에셋으로 저장
    string assetPath = SaveImageAsAsset(texture, cmd.TargetCell);
    
    // Cell 업데이트
    SheetCell imageCell = cmd.TargetCell.DataType == DataType.Sprite
        ? new SpriteCell(cmd.TargetCell.ColumnId, assetPath)
        : new Texture2DCell(cmd.TargetCell.ColumnId, assetPath);
    
    cmd.TargetCell.UpdateCellValue(imageCell);
}

private string SaveImageAsAsset(Texture2D texture, CellEntry cellEntry)
{
    // TableName/Images/RowKey_ColumnName.png 형식으로 저장
    string tableName = SpreadsheetHub.CurrentTableName;
    string rowKey = cellEntry.RowEntry.Key ?? cellEntry.RowEntry.RowId;
    string columnName = cellEntry.ColumnName;
    
    string directory = $"Assets/Generated/{tableName}/Images";
    if (!System.IO.Directory.Exists(directory))
        System.IO.Directory.CreateDirectory(directory);
    
    string fileName = $"{rowKey}_{columnName}.png";
    string fullPath = $"{directory}/{fileName}";
    
    byte[] pngData = texture.EncodeToPNG();
    System.IO.File.WriteAllBytes(fullPath, pngData);
    
    AssetDatabase.Refresh();
    AssetDatabase.ImportAsset(fullPath);
    
    return fullPath;
}
```

## 4. GenerationPanel 개선

**위치**: `Assets\Glitch9\AIDevKit.Sheets\Editor\Scripts\Spreadsheet\Window\5_GenerationPanel\SpreadsheetGenerationPanel.cs`

### OnGenerate 수정

```csharp
private void OnGenerate()
{
    // 현재 선택된 셀이 이미지 타입인지 확인
    bool isImageGeneration = false;
    
    if (SpreadsheetFocus.FocusedCell != null && 
        SpreadsheetFocus.FocusedCell.DataType.IsImageType())
    {
        isImageGeneration = true;
    }
    
    SpreadsheetHub.DispatchCommand(
        new GenerateSelectedCells(
            overwriteExistingCells: SpreadsheetStates.OverwriteExistingValues.Value));
}
```

## 5. AIGenerationService 확장

**위치**: `Assets\Glitch9\AIDevKit.Sheets\Editor\Scripts\Spreadsheet\Backend\Services\AIGenerationService.cs`

```csharp
public async UniTask<byte[]> GenerateImageAsync(
    Model model,
    ImageGenerationRequest request,
    CancellationToken token = default)
{
    // 기존 AIDevKit의 ImageGenerationRequest 활용
    var response = await AIClient.Instance.ImageAsync(request, token);
    
    if (response == null || response.Data.IsNullOrEmpty())
        return null;
    
    // URL에서 이미지 다운로드 또는 base64 디코드
    string imageUrl = response.Data[0].Url;
    if (!string.IsNullOrEmpty(imageUrl))
    {
        using var webClient = new System.Net.WebClient();
        return await webClient.DownloadDataTaskAsync(imageUrl).AsUniTask();
    }
    
    return null;
}
```

## 6. 테스트 시나리오

1. **단일 이미지 셀 생성**
   * 이미지 타입 컬럼의 셀 선택
   * PromptContext에 프롬프트 입력
   * Generate 버튼 클릭
   * 이미지 생성 및 에셋 저장 확인
2. **다중 셀 생성 (이미지 + 텍스트 혼합)**
   * 여러 컬럼 선택 (이미지 + 텍스트)
   * 이미지와 텍스트 커맨드가 분리되어 실행되는지 확인
3. **Inpaint 기능 (추가)**
   * 기존 이미지 있는 셀 선택
   * Edit Image 버튼으로 이미지 수정

## 구현 순서

1. ✅ `GenerateImageInCellCommand` 클래스 추가
2. ✅ `GenerateCellsCommandSplitter` 확장 (이미지/텍스트 분리)
3. ✅ `GenerationBackendController` 확장 (이미지 생성 로직)
4. ✅ `AIGenerationService`에 `GenerateImageAsync` 메서드 추가
5. ✅ 에셋 저장 로직 구현
6. 🔲 Inpaint 기능 구현 (선택사항)
