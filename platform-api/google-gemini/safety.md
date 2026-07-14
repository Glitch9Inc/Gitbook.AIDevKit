# 🛡️ Safety

The following code snippet shows how to set safety settings in your `GenerateContent` call. This sets the harassment (`HARM_CATEGORY_HARASSMENT`) and hate speech (`HARM_CATEGORY_HATE_SPEECH`) categories to `BLOCK_LOW_AND_ABOVE` which blocks any content that has a low or higher probability of being harassment or hate speech.

{% tabs %}
{% tab title="C#" %}
```csharp
using Glitch9.AI.Google.GenerativeAI;

var model = new GenerativeModel(GeminiModel.Gemini15Flash);

var response = await model.GenerateContentAsync(
    "Do these look store-bought or homemade?", 
    safetySettings: new List<SafetySetting>
{
    { new SafetySetting(HarmCategory.HateSpeech, HarmBlockThreshold.BlockLowAndAbove) },
    { new SafetySetting(HarmCategory.Harassment, HarmBlockThreshold.BlockLowAndAbove) }
});
```
{% endtab %}

{% tab title="Python (Official SDK)" %}
```python
from google.generativeai.types import HarmCategory, HarmBlockThreshold

model = genai.GenerativeModel(model_name='gemini-1.5-flash')
response = model.generate_content(
    ['Do these look store-bought or homemade?', img],
    safety_settings={
        HarmCategory.HARM_CATEGORY_HATE_SPEECH: HarmBlockThreshold.BLOCK_LOW_AND_ABOVE,
        HarmCategory.HARM_CATEGORY_HARASSMENT: HarmBlockThreshold.BLOCK_LOW_AND_ABOVE,
    }
)
```
{% endtab %}
{% endtabs %}

{% embed url="https://ai.google.dev/gemini-api/docs/safety-settings" %}

{% embed url="https://ai.google.dev/gemini-api/docs/safety-guidance" %}
