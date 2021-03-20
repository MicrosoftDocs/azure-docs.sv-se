---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.custom: devx-track-csharp
ms.openlocfilehash: 51b919c97a15946f57211cf8fe12d7c5efe435bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934656"
---
## <a name="upload-the-audio"></a>Ladda upp ljudet

Innan asynkron avskrift kan utföras måste du skicka ljudet till konversations avskrifts tjänsten med hjälp av Microsofts kognitiva tal klient-SDK (version 1.13.0 eller senare).

I den här exempel koden visas hur du skapar en konversations beskrivare för endast asynkront läge. För att strömma ljud till medskrivaren lägger du till kod för ljuddirektuppspelning härledda från att skriva [upp konversationer i real tid med talet SDK](../../../../how-to-use-conversation-transcription.md). 

```csharp
async Task CompleteContinuousRecognition(ConversationTranscriber recognizer, string conversationId)
{
    recognizer.SessionStopped += (s, e) =>
    {
        m_taskCompletionSource.TrySetResult(0);
    };
    string canceled = string.Empty;

    recognizer.Canceled += (s, e) => {
        canceled = e.ErrorDetails;
        if (e.Reason == CancellationReason.Error)
        {
            m_taskCompletionSource.TrySetResult(0);
        }
    };

    await recognizer.StartTranscribingAsync().ConfigureAwait(false);
    await Task.WhenAny(m_taskCompletionSource.Task, Task.Delay(TimeSpan.FromSeconds(10)));
    await recognizer.StopTranscribingAsync().ConfigureAwait(false);
}

async Task<List<string>> GetRecognizerResult(ConversationTranscriber recognizer, string conversationId)
{
    List<string> recognizedText = new List<string>();
    recognizer.Transcribed += (s, e) =>
    {
        if (e.Result.Text.Length > 0)
        {
            recognizedText.Add(e.Result.Text);
        }
    };

    await CompleteContinuousRecognition(recognizer, conversationId);

    recognizer.Dispose();
    return recognizedText;
}

async Task UploadAudio()
{
    // Create the speech config object
    // Substitute real information for "YourSubscriptionKey" and "Region"
    SpeechConfig speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "Region");
    speechConfig.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");

    // Set the property for asynchronous transcription
    speechConfig.SetServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

    // Set the property for real-time plus asynchronous transcription
    //speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

    // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
    // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
    PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
    // Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
    AudioStreamFormat audioStreamFormat = AudioStreamFormat.GetWaveFormatPCM((long)16000, (short)16,(short)8);
    // Create an input stream
    AudioInputStream audioStream = AudioInputStream.CreatePullStream(wavfilePullStreamCallback, audioStreamFormat);

    // pick a conversation Id that is a GUID.
    String conversationId = Guid.NewGuid().ToString();

    // Create a Conversation
    using (var conversation = await Conversation.CreateConversationAsync(speechConfig, conversationId))
    {
        using (var conversationTranscriber = TrackSessionId(new ConversationTranscriber(AudioConfig.FromStreamInput(audioStream))))
        {
            await conversationTranscriber.JoinConversationAsync(conversation);
            // Helper function to get the real time transcription results
            var result = await GetRecognizerResult(conversationTranscriber, conversationId);
        }
    }
}
```

Om du vill ha ett real tids _plus_ asynkront, kommentera och ta bort kommentarer till rätt rader med kod enligt följande:

```csharp
// Set the property for asynchronous transcription
//speechConfig.SetServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.SetServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Hämta avskrifts resultat

Installera **Microsoft. CognitiveServices. Speech. Remoteconversation version 1.13.0 eller senare** via NuGet.

### <a name="sample-transcription-code"></a>Exempel kod för avskrift

När du har gjort det `conversationId` kan du skapa en **RemoteConversationTranscriptionClient** för avskrifts avskrift i klient programmet för att fråga om status för den asynkrona avskriften. Skapa ett objekt av  **RemoteConversationTranscriptionOperation** för att få ett tids krävande [Åtgärds](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/core/Azure.Core#consuming-long-running-operations-using-operationt) objekt. Du kan kontrol lera status för åtgärden eller vänta tills den är klar. 

```csharp
// Create the speech config
SpeechConfig config = SpeechConfig.FromSubscription("YourSpeechKey", "YourSpeechRegion");
// Create the speech client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(config);
// Create the remote operation
RemoteConversationTranscriptionOperation operation = 
                            new RemoteConversationTranscriptionOperation(conversationId, client);

// Wait for operation to finish
await operation.WaitForCompletionAsync(TimeSpan.FromSeconds(10), CancellationToken.None);
// Get the result of the long running operation
var val = operation.Value.ConversationTranscriptionResults;
// Print the fields from the results
foreach (var item in val)
{
    Console.WriteLine($"{item.Text}, {item.ResultId}, {item.Reason}, {item.UserId}, {item.OffsetInTicks}, {item.Duration}");
    Console.WriteLine($"{item.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)}");
}
Console.WriteLine("Operation completed");

```
