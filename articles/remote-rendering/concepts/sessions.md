---
title: Remote Rendering-sessioner
description: Beskriver vad en fjärrstyrningssession är
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: a74fae74a2d0ebbb71d65420475e5772e44a8d84
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507101"
---
# <a name="remote-rendering-sessions"></a>Remote Rendering-sessioner

I Azure Remote rendering (ARR) är en *session* ett nyckel begrepp. I den här artikeln förklaras vad exakt en session är.

## <a name="overview"></a>Översikt

Azure Remote rendering fungerar genom att avlasta komplexa åter givnings uppgifter i molnet. Dessa åter givnings aktiviteter kan inte uppfyllas av någon server, eftersom de flesta moln servrar inte har GPU: er. På grund av mängden data som är inblandade och det hårda kravet på att producera resultat i interaktiva bild hastigheter, är det ansvaret för vilken server som hanterar användar förfrågningen inte kan överlämnas till en annan dator direkt, vilket kan vara möjligt för mer allmän webb trafik.

Det innebär att när du använder Azure-fjärrrendering måste en moln server med de nödvändiga maskin varu funktionerna reserveras uteslutande för att hantera dina åter givnings begär Anden. En *session* syftar på allt som används för att interagera med den här servern. Den börjar med den första begäran om att reservera (*låna*) en dator för användning, fortsätter med alla kommandon för att läsa in och manipulera modeller, och slutar med att släppa lånet på moln servern.

## <a name="managing-sessions"></a>Hantera sessioner

Det finns flera olika sätt att hantera och interagera med sessioner. Det språk oberoende sättet att skapa, uppdatera och stänga av sessioner är via [REST API för hantering av sessionen](../how-tos/session-rest-api.md). I C# och C++ exponeras dessa åtgärder via klasserna `AzureFrontend` och `AzureSession` . För Unity-program finns ytterligare verktyg som tillhandahålls av `ARRServiceUnity` komponenten.

När du är *ansluten* till en aktiv session exponeras åtgärder som att [läsa in modeller](models.md) och interagera med scenen genom `AzureSession` klassen.

### <a name="managing-multiple-sessions-simultaneously"></a>Hantera flera sessioner samtidigt

Det går inte att *ansluta* fullständigt till flera sessioner från en enhet. Du kan dock skapa, Observera och stänga av så många sessioner som du vill från ett enda program. Så länge appen inte är avsedd att någonsin ansluta till en session, behöver den inte köras på en enhet som HoloLens 2, antingen. Ett användnings fall för en sådan implementering kan vara om du vill kontrol lera sessioner via en central mekanism. En webbapp kan till exempel bygga en webbapp där flera surfplattor och HoloLenses kan logga in. Sedan kan appen Visa alternativ på surfplattor, till exempel vilken CAD-modell som ska visas. Om en användare gör ett val överförs den här informationen till alla HoloLenses för att skapa en delad upplevelse.

## <a name="session-phases"></a>Faser i sessionen

Varje session genomgår flera faser.

### <a name="session-startup"></a>Starta session

När du ställer in ARR för att [skapa en ny session](../how-tos/session-rest-api.md#create-a-session)är det första alternativet att returnera ett [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)för session. Detta UUID gör att du kan fråga efter information om sessionen. UUID och grundläggande information om sessionen är bestående i 30 dagar, så du kan fråga informationen även efter att sessionen har stoppats. I det här läget rapporteras **sessionstillståndet** som att **Starta**.

Sedan försöker Azure Remote rendering hitta en server som kan vara värd för din session. Det finns två parametrar för den här sökningen. Först kommer den bara att reservera servrar i din [region](../reference/regions.md). Det beror på att nätverks fördröjningen i flera regioner kan vara för hög för att garantera en vettigt upplevelse. Den andra faktorn är den önskade *storlek* som du har angett. I varje region finns ett begränsat antal servrar som kan uppfylla [*standard*](../reference/vm-sizes.md) -eller [*Premium*](../reference/vm-sizes.md) storleks förfrågan. Om alla servrar med den begärda storleken för närvarande används i din region kommer det därför inte att skapas någon session. Orsaken till att ett problem [kan frågas](../how-tos/session-rest-api.md#get-sessions-properties).

> [!IMPORTANT]
> Om du begär en *standard* Server storlek och begäran Miss lyckas på grund av hög efter frågan, vilket inte innebär att det inte går att begära en *Premium* -Server. Så om det är ett alternativ åt dig kan du prova att gå tillbaka till en *Premium* Server-storlek.

När tjänsten hittar en lämplig server måste den kopiera rätt virtuell dator (VM) till den för att omvandla den till en Azure-fjärrrendering-värd. Den här processen tar flera minuter. Därefter startas den virtuella datorn och över gången till **sessionen** är **klar**.

I det här läget väntar servern enbart på din inaktuella information. Detta är även den punkt från vilken du debiteras för tjänsten.

### <a name="connecting-to-a-session"></a>Ansluta till en session

När sessionen är *klar*kan du *ansluta* till den. När du är ansluten kan enheten skicka kommandon för att läsa in och ändra modeller. Varje ARR-värd betjänar bara en klient enhet i taget, så när en klient ansluter till en session har den exklusiv kontroll över det återgivna innehållet. Det innebär också att åter givnings prestandan inte varierar beroende på orsaker utanför kontrollen.

> [!IMPORTANT]
> Även om bara en klient kan *ansluta* till en session, kan grundläggande information om sessioner, till exempel deras aktuella tillstånd, frågas utan att ansluta.

När en enhet är ansluten till en session kommer försök av andra enheter att ansluta att Miss lyckas. Men när den anslutna enheten kopplar från, antingen frivilligt eller på grund av en viss typ av haveri, kommer sessionen att acceptera en annan anslutningsbegäran. Alla tidigare tillstånd (inlästa modeller och sådana) tas bort så att nästa anslutande enhet får en ren bakgrunds bild. Sessioner kan därför återanvändas flera gånger, av olika enheter och det kan vara möjligt att dölja start omkostnader för sessionen från slutanvändaren i de flesta fall.

> [!IMPORTANT]
> Fjärrservern ändrar aldrig statusen för data på klient sidan. Alla mutationer av data (t. ex. omvandlings uppdateringar och inläsnings begär Anden) måste utföras av klient programmet. Alla åtgärder uppdaterar klientens tillstånd omedelbart.

### <a name="session-end"></a>Session slut

När du begär en ny session anger du en *maximal låne tid*, vanligt vis i intervallet en till åtta timmar. Detta är den tid under vilken värden accepterar dina inaktuella värden.

Det finns två vanliga orsaker till att en session slutar. Antingen begär du att sessionen ska stoppas manuellt eller att den längsta låne tiden upphör att gälla. I båda fallen stängs alla aktiva anslutningar till värden omedelbart och tjänsten stängs av på den servern. Servern får sedan tillbaka till Azure-poolen och kan få hjälp av rekvisitioner för andra orsaker. Det går inte att ångra eller avbryta en session. Att fråga **sessionens tillstånd** i en stoppad session returnerar antingen **stoppad** eller **upphört**, beroende på om den stängdes manuellt eller på grund av att den maximala låne tiden har uppnåtts.

En session kan också stoppas på grund av ett problem.

I samtliga fall faktureras du inte ytterligare när en session har stoppats.

> [!WARNING]
> Oavsett om du ansluter till en session och hur länge, inte påverkar faktureringen. Vad du betalar för tjänsten beror på *sessionens varaktighet*, vilket innebär den tid som en server är exklusivt reserverad för dig och de begärda maskin varu funktionerna ( [allokerad storlek](../reference/vm-sizes.md)). Om du startar en session ansluter du i fem minuter och stoppar sedan inte sessionen, så att den fortsätter tills dess att lånet upphör att gälla, så faktureras du för den fullständiga låne tiden för sessionen. Den *längsta låne tiden* är i huvudsak ett säkerhets nät. Det spelar ingen roll om du begär en session med en låne tid på åtta timmar och sedan bara använder den i fem minuter, om du stoppar sessionen manuellt efteråt.

#### <a name="extend-a-sessions-lease-time"></a>Utöka en sessions låne tid

Du kan [utöka låne tiden](../how-tos/session-rest-api.md#update-a-session) för en aktiv session, om den visar att du behöver den längre.

## <a name="example-code"></a>Exempelkod

Koden nedan visar en enkel implementering av att starta en session, väntar på *klart* läge, ansluta och sedan koppla från och stänga av igen.

```cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

Flera `AzureFrontend` och `AzureSession` instanser kan underhållas, manipuleras och efter frågas från kod. Men bara en enskild enhet kan ansluta till en `AzureSession` i taget.

Livs längden för en virtuell dator är inte kopplad till `AzureFrontend` instansen eller `AzureSession` instansen. `AzureSession.StopAsync` måste anropas för att stoppa en session.

Det permanenta sessions-ID: t kan frågas via `AzureSession.SessionUUID()` och cachelagras lokalt. Med det här ID: t kan ett program anropa `AzureFrontend.OpenSession` för att binda till den sessionen.

När `AzureSession.IsConnected` är sant `AzureSession.Actions` returnerar en instans av `RemoteManager` , som innehåller funktionerna för att [läsa in modeller](models.md), manipulera [entiteter](entities.md)och [fråga efter information](../overview/features/spatial-queries.md) om den återgede scenen.

## <a name="next-steps"></a>Nästa steg

* [Entiteter](entities.md)
* [Modeller](models.md)
