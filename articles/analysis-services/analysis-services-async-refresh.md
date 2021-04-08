---
title: Asynkron uppdatering av Azure Analysis Servicess modeller | Microsoft Docs
description: Beskriver hur du använder Azure Analysis Services REST API för att koda asynkron uppdatering av modell data.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: e9fd20fd42e9fe1eb0e98766798e5c759c974c97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92013907"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Asynkron uppdatering med REST API

Genom att använda valfritt programmeringsspråk som stöder REST-anrop kan du utföra asynkrona data uppdaterings åtgärder på dina Azure Analysis Services tabell modeller. Detta inkluderar synkronisering av skrivskyddade repliker för utskalning av frågor. 

Data uppdaterings åtgärder kan ta lite tid beroende på flera faktorer, inklusive data volym, optimerings nivå med partitioner osv. Dessa åtgärder har traditionellt anropats med befintliga metoder som att använda [Tom](/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (tabell objekts modell), [PowerShell](/analysis-services/powershell/analysis-services-powershell-reference) -cmdletar eller [TMSL](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (tabell modell skript språk). Dessa metoder kan dock kräva en ofta otillförlitlig, tids krävande HTTP-anslutningar.

REST API för Azure Analysis Services gör att data uppdaterings åtgärder kan utföras asynkront. Genom att använda den REST API är tids krävande HTTP-anslutningar från klient program inte nödvändiga. Det finns även andra inbyggda funktioner för tillförlitlighet, till exempel automatiska återförsök och batch-incheckningar.

## <a name="base-url"></a>Grundläggande URL

Bas-URL: en följer detta format:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Överväg till exempel en modell med namnet AdventureWorks på en server med namnet `myserver` , som finns i Azure-regionen USA, västra. Server namnet är:

```
asazure://westus.asazure.windows.net/myserver 
```

Bas-URL: en för det här Server namnet är:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Genom att använda bas-URL: en kan du lägga till resurser och åtgärder baserat på följande parametrar: 

![Asynkron uppdatering](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Allt som slutar i **s** är en samling.
- Allt som slutar med **()** är en funktion.
- Något annat är en resurs/ett objekt.

Du kan till exempel använda verbet POST i samlingen uppdaterings samling för att utföra en uppdaterings åtgärd:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Autentisering

Alla anrop måste autentiseras med en giltig Azure Active Directory-token (OAuth 2) i Authorization-huvudet och måste uppfylla följande krav:

- Token måste antingen vara en användartoken eller ett huvud namn för program tjänsten.
- Token måste ha rätt mål grupp inställt på `https://*.asazure.windows.net` .
- Användaren eller programmet måste ha tillräcklig behörighet på servern eller modellen för att kunna göra det begärda anropet. Behörighets nivån bestäms av roller i modellen eller administratörs gruppen på servern.

    > [!IMPORTANT]
    > För närvarande krävs **Server administratörs** roll behörigheter.

## <a name="post-refreshes"></a>PUBLICERA/refreshes

Om du vill utföra en uppdatering använder du verbet POST i/refreshes-samlingen för att lägga till ett nytt uppdaterings objekt i samlingen. Plats rubriken i svaret innehåller uppdaterings-ID: t. Klient programmet kan koppla från och kontrol lera statusen senare om det behövs eftersom det är asynkront.

Endast en uppdaterings åtgärd accepteras i taget för en modell. Om det finns en aktuell uppdaterings åtgärd som körs och en annan har skickats returneras HTTP-statuskoden 409 i konflikt.

Bröd texten kan likna följande:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parametrar

Du behöver inte ange parametrar. Standardvärdet används.

| Namn             | Typ  | Beskrivning  |Standardvärde  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | Typ av bearbetning som ska utföras. Typerna justeras med kommando typerna TMSL [Refresh](/analysis-services/tmsl/refresh-command-tmsl) : full, clearValues, beräkning, dataOnly, Automatic och defragmentering. Det finns inte stöd för att lägga till typen.      |   automatiskt      |
| `CommitMode`     | Enum  | Anger om objekt ska allokeras i batchar eller bara när de är slutförda. Lägena är: standard, transaktionell, partialBatch.  |  transaktions       |
| `MaxParallelism` | Int   | Det här värdet anger det maximala antalet trådar som bearbetnings kommandon ska köras parallellt för. Det här värdet justeras med egenskapen MaxParallelism som kan anges i kommandot TMSL [Sequence](/analysis-services/tmsl/sequence-command-tmsl) eller med andra metoder.       | 10        |
| `RetryCount`     | Int   | Anger hur många gånger åtgärden ska försöka utföras innan fel.      |     0    |
| `Objects`        | Matris | En matris med objekt som ska bearbetas. Varje-objekt innehåller: "Tabell" när hela tabellen eller "table" och "partition" bearbetas vid bearbetning av en partition. Om inga objekt anges uppdateras hela modellen. |   Bearbeta hela modellen      |

CommitMode är lika med partialBatch. Den används när du gör en första belastning av stora data uppsättningar som kan ta flera timmar. Om uppdaterings åtgärden Miss lyckas efter att du har utfört en eller flera batchar fortsätter de genomförda batcharna att bli allokerade (den kommer inte att återställa genomförda batchar).

> [!NOTE]
> Vid tidpunkten för skrivning är batchstorleken värdet MaxParallelism, men det här värdet kan ändras.

### <a name="status-values"></a>Status värden

|Statusvärde  |Beskrivning  |
|---------|---------|
|`notStarted`    |   Åtgärden har inte startats ännu.      |
|`inProgress`     |   Åtgärd pågår.      |
|`timedOut`     |    Tids gränsen nåddes för åtgärden baserat på den angivna tids gränsen för användaren.     |
|`cancelled`     |   Åtgärden avbröts av användaren eller systemet.      |
|`failed`     |   Åtgärden misslyckades.      |
|`succeeded`      |   Åtgärden har slutförts.      |

## <a name="get-refreshesrefreshid"></a>Hämta/refreshes/\<refreshId>

Om du vill kontrol lera status för en uppdaterings åtgärd använder du hämta verbet i uppdaterings-ID: t. Här är ett exempel på svars texten. Om åtgärden pågår `inProgress` returneras statusen.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>Hämta/refreshes

Om du vill hämta en lista över historiska uppdaterings åtgärder för en modell använder du kommandot GET verb i/refreshes-samlingen. Här är ett exempel på svars texten. 

> [!NOTE]
> Vid tidpunkten för skrivning lagras och returneras de senaste 30 dagarna av uppdaterings åtgärder, men det här antalet kan ändras.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-07T02:06:57.1838734Z",
        "endTime": "2017-12-07T02:07:00.4929675Z",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T01:05:54.157324Z",
        "endTime": "2017-12-07T01:05:57.353371Z",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>TA bort/refreshes/\<refreshId>

Om du vill avbryta en pågående uppdatering använder du DELETE-verbet på uppdaterings-ID: t.

## <a name="post-sync"></a>Publicera/Sync

När du har utfört uppdaterings åtgärder kan det vara nödvändigt att synkronisera nya data med repliker för frågans utskalning. Om du vill utföra en synkronisera-åtgärd för en modell använder du kommandot POST-verb i/Sync-funktionen. Plats rubriken i svaret innehåller Sync-åtgärdens ID.

## <a name="get-sync-status"></a>Hämta/Sync-status

Om du vill kontrol lera statusen för en Sync-åtgärd använder du GET-verbet som skickar åtgärds-ID: t som en parameter. Här är ett exempel på svars texten:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Värden för `syncstate` :

- 0: replikerar. Databasfiler replikeras till en målmapp.
- 1: återuppväcks. Databasen reserveras på skrivskyddade Server instanser.
- 2: slutförd. Synkroniseringsåtgärden har slutförts.
- 3: misslyckades. Synkroniseringsåtgärden misslyckades.
- 4: Slutför. Synkroniseringsåtgärden har slutförts men rensnings stegen utförs.

## <a name="code-sample"></a>Kodexempel

Här är ett C#-kod exempel som hjälper dig att komma igång, [RestApiSample på GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Använda kod exemplet

1.    Klona eller hämta lagrings platsen. Öppna RestApiSample-lösningen.
2.    Hitta rad **klienten. =...** och ange din [bas-URL](#base-url).

Kod exemplet använder autentisering av [tjänstens huvud namn](#service-principal) .

### <a name="service-principal"></a>Tjänstens huvudnamn

Se [skapa tjänstens huvud namn – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md) och [lägga till ett huvud namn för tjänsten i rollen Server administratör](analysis-services-addservprinc-admins.md) för mer information om hur du konfigurerar ett huvud namn för tjänsten och tilldelar de nödvändiga behörigheterna i Azure som. När du har slutfört stegen utför du följande steg:

1.    I kod exemplet letar du reda på **sträng auktoritet =...**, ersätter **common** med organisationens klient-ID.
2.    Kommentera/ta bort kommentaren så att ClientCredential-klassen används för att instansiera objektet cred. Se till \<App ID> att \<App Key> värdena och är tillgängliga på ett säkert sätt eller Använd certifikatbaserad autentisering för tjänstens huvud namn.
3.    Kör exemplet.


## <a name="see-also"></a>Se även

[Stickprov](analysis-services-samples.md)   
[REST-API](/rest/api/analysisservices/servers)