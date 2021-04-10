---
title: 'Översikt över Azure Relay .NET standard-API: er | Microsoft Docs'
description: I den här artikeln sammanfattas några av de viktigaste en översikt över Azure Relay Hybridanslutningar .NET standard-API.
ms.topic: article
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 724fb1a62b82036b4a0fa8b9f4f3608293f608a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625139"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Översikt över Azure Relay Hybridanslutningar .NET standard API

I den här artikeln sammanfattas några av de viktigaste Azure Relay Hybridanslutningar [API: er](/dotnet/api/microsoft.azure.relay)för .net-standard klienten.
  
## <a name="relay-connection-string-builder-class"></a>Klass för relä anslutnings sträng

[RelayConnectionStringBuilder][RelayConnectionStringBuilder] -klassen formaterar anslutnings strängar som är speciella för relä hybridanslutningar. Du kan använda den för att kontrol lera formatet på en anslutnings sträng, eller för att bygga en anslutnings sträng från grunden. Se följande kod för ett exempel:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

Du kan också skicka en anslutnings sträng direkt till- `RelayConnectionStringBuilder` metoden. Med den här åtgärden kan du kontrol lera att anslutnings strängen har ett giltigt format. Om någon av parametrarna är ogiltig genererar konstruktorn en `ArgumentException` .

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Hybrid anslutnings ström

[HybridConnectionStream][HCStream] -klassen är det primära objektet som används för att skicka och ta emot data från en Azure Relay slut punkt, oavsett om du arbetar med en [HybridConnectionClient][HCClient]eller en [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Hämta en hybrid anslutnings ström

#### <a name="listener"></a>Lyssnare

Med hjälp av ett [HybridConnectionListener][HCListener] -objekt kan du hämta ett `HybridConnectionStream` objekt på följande sätt:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Klient

Med hjälp av ett [HybridConnectionClient][HCClient] -objekt kan du hämta ett `HybridConnectionStream` objekt på följande sätt:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Tar emot data

[HybridConnectionStream][HCStream] -klassen möjliggör tvåvägs kommunikation. I de flesta fall får du ständigt från strömningen. Om du läser text från data strömmen kanske du också vill använda ett [StreamReader](/dotnet/api/system.io.streamreader) -objekt, vilket gör det lättare att parsa data. Du kan till exempel läsa data som text, i stället för som `byte[]` .

Följande kod läser enskilda text rader från data strömmen tills en uppsägning begärs:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Skickar data

När en anslutning har upprättats kan du skicka ett meddelande till relä slut punkten. Eftersom anslutningsobjektet ärver data [strömmen](/dotnet/api/system.io.stream)skickar du dina data som en `byte[]` . I följande exempel visas hur du gör detta:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Men om du vill skicka text direkt, utan att behöva koda strängen varje tid, kan du figursätta `hybridConnectionStream` objektet med ett [StreamWriter](/dotnet/api/system.io.streamwriter) -objekt.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure Relay går du till följande länkar:

* [Referens för Microsoft. Azure. Relay](/dotnet/api/microsoft.azure.relay)
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Tillgängliga relä-API: er](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener
