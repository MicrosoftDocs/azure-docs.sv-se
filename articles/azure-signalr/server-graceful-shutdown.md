---
title: Stoppa din app server på ett smidigt sätt.
description: Den här artikeln innehåller information om en välstängd Signals app server
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201679"
---
# <a name="server-graceful-shutdown"></a>Servern är korrekt avstängd
Microsoft Azure SignalR-tjänsten har två lägen för att stänga av en server på ett smidigt sätt. 

Den största fördelen med att använda den här funktionen är att förhindra att kunden upplever en oväntad anslutning. 

I stället kan du antingen vänta dina klient anslutningar så att de stängs i förhållande till din affärs logik, eller till och med migrera klient anslutningen till en annan server utan att förlora data. 

## <a name="how-it-works"></a>Så här fungerar det

I allmänhet kommer det att finnas fyra steg i en korrekt avstängnings process:

1. **Ange att servern är offline**

    Det innebär att inga fler klient anslutningar kommer att dirigeras till den här servern.

2. **Utlös `OnShutdown` hookar**

    Du kan registrera avstängnings hookar för varje hubb som du äger på servern.
    De kommer att anropas med avseende på den registrerade ordningen direkt efter att vi fick ett **FINACK** -svar från vår Azure SignalR-tjänst, vilket innebär att den här servern har angetts offline i Azure SignalR-tjänsten.

    Du kan skicka meddelanden eller göra vissa rensnings jobb i det här steget, när alla uppstängnings-hookar har körts, fortsätter vi till nästa steg.

3. **Vänta tills alla klient anslutningar är klara**, beror på vilket läge du väljer, det kan vara:

    **Läge inställt på WaitForClientsToClose**

    Azure SignalR service kommer att innehålla befintliga klienter.

    Du kan behöva utforma ett sätt, t. ex. sända ett stängnings meddelande till alla klienter, och sedan låta dina klienter bestämma när de ska stängas/återanslutas.

    Läs [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample) för exempel användning, som vi sänder meddelandet "avsluta" för att utlösa klienten nära i avstängnings Hook.

    **Läge inställt på MigrateClients**

    Azure SignalR-tjänsten kommer att försöka omdirigera klient anslutningen på den här servern till en annan giltig Server. 
    
    I det här scenariot `OnConnectedAsync` `OnDisconnectedAsync` aktive ras den nya servern och den gamla servern med en `IConnectionMigrationFeature` uppsättning i `HttpContext` , som kan användas för att identifiera om klient anslutningen migrerades – i vår migrering. Det kan vara användbart särskilt för tillstånds känsliga scenarier.

    Klient anslutningen migreras omedelbart när det aktuella meddelandet har levererats, vilket innebär att nästa meddelande skickas till den nya servern.

4. **Stoppa Server anslutningar**

    När alla klient anslutningar har stängts/migrerats, eller om timeout (30 s som standard) har överskridits,

    SignalR Server SDK kommer att fortsätta avstängnings processen till det här steget och stänga alla Server anslutningar.

    Klient anslutningar tas fortfarande bort om det inte gick att stänga/migrera. Till exempel har ingen lämplig mål server/aktuellt klient-till-Server-meddelande har avslut ATS.

## <a name="sample-codes"></a>Exempel koder.

Lägg till följande alternativ när `AddAzureSignalR` :

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>Konfigurera `OnConnected` och `OnDisconnected` medan du ställer in korrekt avslutnings läge på `MigrateClients` .

Vi har introducerat "IConnectionMigrationFeature" för att ange om en anslutning har migrerats in/ut.

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```