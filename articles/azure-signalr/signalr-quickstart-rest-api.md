---
title: Snabbstart – Azure SignalR Service REST API
description: Lär dig hur du använder REST API med Azure SignalR service i följande exempel. Hitta information om REST API specifikation.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 2613f91e8c7f1ad3a05792a9a165f4560c09a637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94874533"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Snabbstart: Sända realtidsmeddelanden från konsolapp

Azure SignalR Service har [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) för att stödja server till klient-kommunikationsscenarier, till exempel sändning. Du kan använda valfritt programmeringsspråk som göra REST API-anrop. Du kan publicera meddelanden till alla anslutna klienter, en specifik klient per namn eller en grupp med klienter.

I den här snabbstarten får du lära dig hur du sänder meddelanden från en kommandoradsapp till anslutna klientappar i C#.

## <a name="prerequisites"></a>Förutsättningar

Den här snabbstarten kan köras på macOS, Windows eller Linux.

* [.NET Core SDK](https://www.microsoft.com/net/download/core)
* Valfri textredigerare eller kodredigerare.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsapi).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com/> med ditt Azure-konto.

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsapi).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsapi).

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Medan tjänsten distribueras ska vi förbereda koden. Klona [exempelappen från GitHub](https://github.com/aspnet/AzureSignalR-samples.git), ange SignalR Service-anslutningssträngen och kör programmet lokalt.

1. Öppna ett git-terminalfönster. Byt till en mapp där du vill klona exempelprojektet.

1. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```
Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsapi).

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

Det här exemplet är en konsolapp som visar användningen av Azure SignalR Service. Den har två lägen:

- Serverläge: använder enkla kommandon för att anropa Azure SignalR Service REST API.
- Klientläge: anslut till Azure SignalR Service och få meddelanden från servern.

Du kan även hitta hur du skapar en åtkomsttoken för autentisering med Azure SignalR Service.

### <a name="build-the-executable-file"></a>Skapa den körbara filen

Vi använder macOS osx.10.13-x64 som exempel. Det finns [referenser](/dotnet/core/rid-catalog) till hur du bygger på andra plattformar.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Starta en klient

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Starta en server

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsapi).

## <a name="run-the-sample-without-publishing"></a>Kör exemplet utan att publicera

Du kan även köra kommandot nedan för att starta en server eller klient

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Använda användarhemligheter för att ange anslutningssträng

Du kan köra `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` i exemplets rotkatalog. Efter det behöver du inte alternativet `-c "<ConnectionString>"` längre.

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsapi).

## <a name="usage"></a>Användning

När servern har startats använder du kommandot för att skicka meddelandet:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Du kan starta flera klienter med olika klientnamn.

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsapi).

## <a name="integration-with-third-party-services"></a><a name="usage"> </a> Integrering med tjänster från tredje part

Azure SignalR-tjänsten tillåter tredjepartstjänster att integrera med systemet.

### <a name="definition-of-technical-specifications"></a>Definition av tekniska specifikationer

Följande tabell visar alla versioner av de REST API:er som stöds hittills. Du hittar även definitionsfilen för varje specifika version

Version | API-tillstånd | Door | Specifik
--- | --- | --- | ---
`1.0-preview` | Tillgänglig | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Tillgänglig | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

Listan över tillgängliga API:er för varje specifika version finns i följande lista.

API | 1,0 – för hands version | 1.0
--- | --- | ---
[Sänd till alla](#broadcast) | **&#x2713;** | **&#x2713;**
[Sänd till en grupp](#broadcast-group) | **&#x2713;** | **&#x2713;**
Sänd till vissa grupper | **&#x2713;** (inaktuellt) | `N / A`
[Skicka till en användare](#send-user) | **&#x2713;** | **&#x2713;**
Skicka till vissa användare | **&#x2713;** (inaktuellt) | `N / A`
[Lägga till användare i en grupp](#add-user-to-group) | `N / A` | **&#x2713;**
[Ta bort en användare från en grupp](#remove-user-from-group) | `N / A` | **&#x2713;**
[Kontrol lera användar förekomst](#check-user-existence) | `N / A` | **&#x2713;**
[Ta bort en användare från alla grupper](#remove-user-from-all-groups) | `N / A` | **&#x2713;**
[Skicka till en anslutning](#send-connection) | `N / A` | **&#x2713;**
[Lägg till en anslutning till en grupp](#add-connection-to-group) | `N / A` | **&#x2713;**
[Ta bort en anslutning från en grupp](#remove-connection-from-group) | `N / A` | **&#x2713;**
[Stänga en klient anslutning](#close-connection) | `N / A` | **&#x2713;**
[Tjänsthälsa](#service-health) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Sänd till alla

Version | API HTTP-metoden | Begärans-URL | Begärandetext
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Samma som ovan

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Sänd till en grupp

Version | API HTTP-metoden | Begärans-URL | Begärandetext
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Samma som ovan

<a name="send-user"> </a>
### <a name="sending-to-a-user"></a>Skicka till en användare

Version | API HTTP-metoden | Begärans-URL | Begärandetext
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Samma som ovan

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Lägga till användare i en grupp

Version | API HTTP-metoden | Begärans-URL
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Ta bort en användare från en grupp

Version | API HTTP-metoden | Begärans-URL
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="check-user-existence"> </a>
### <a name="check-user-existence-in-a-group"></a>Kontrol lera användarnas existens i en grupp

API-version | API HTTP-metoden | Begärans-URL
---|---|---
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups/<group-name>`
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>` 

Statuskod för svar | Beskrivning
---|---
`200` | Användaren finns
`404` | Användaren finns inte

<a name="remove-user-from-all-groups"> </a>
### <a name="remove-a-user-from-all-groups"></a>Ta bort en användare från alla grupper

API-version | API HTTP-metoden | Begärans-URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups`

<a name="send-connection"> </a>
### <a name="send-message-to-a-connection"></a>Skicka meddelande till en anslutning

API-version | API HTTP-metoden | Begärans-URL | Begärandetext
---|---|---|---
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`

<a name="add-connection-to-group"> </a>
### <a name="add-a-connection-to-a-group"></a>Lägg till en anslutning till en grupp

API-version | API HTTP-metoden | Begärans-URL
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="remove-connection-from-group"> </a>
### <a name="remove-a-connection-from-a-group"></a>Ta bort en anslutning från en grupp

API-version | API HTTP-metoden | Begärans-URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="close-connection"> </a>
### <a name="close-a-client-connection"></a>Stänga en klient anslutning

API-version | API HTTP-metoden | Begärans-URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>?reason=<close-reason>`

<a name="service-health"> </a>
### <a name="service-health"></a>Service Health

API-version | API HTTP-metoden | Begärans-URL
---|---|---                             
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/health`

Statuskod för svar | Beskrivning
---|---
`200` | Tjänsten är felfri
`5xx` | Tjänst fel

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsapi).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsapi).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder REST API för att sända real tids meddelanden från SignalR-tjänsten till klienter. Nu ska du läsa mer om hur du utvecklar och distribuerar Azure Functions med en signal tjänst bindning, som är byggd ovanpå REST API.

> [!div class="nextstepaction"]
> [Utveckla Azure Functions med hjälp av Azure SignalR service-bindningar](signalr-quickstart-azure-functions-csharp.md)
