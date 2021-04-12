---
title: Vägledning för personliga data som lagras i Azure Log Analytics | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar personliga data som lagras i Azure Log Analytics och metoder för att identifiera och ta bort dem.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: 03c4babc8fa70c951d80b720c4d4693968011532
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772271"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Riktlinjer för personliga data som lagras i Log Analytics och Application Insights

Log Analytics är ett data lager där personliga data troligt vis hittas. Application Insights lagrar data i en Log Analytics partition. Den här artikeln beskriver var i Log Analytics och Application Insights sådana data vanligt vis hittas, samt de funktioner som är tillgängliga för att hantera sådana data.

> [!NOTE]
> I den här artikeln refererar _data_ till data som skickas till en Log Analytics-arbetsyta, medan _program data_ refererar till data som samlas in av Application Insights. Om du använder en arbets yta-baserad Application Insights resurs, kommer informationen om loggdata att gälla, men om du använder den klassiska Application Insights-resursen tillämpas program data.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="strategy-for-personal-data-handling"></a>Strategi för personlig data hantering

Även om det är upp till dig och ditt företag för att ta reda på vilken strategi du ska använda för att hantera dina privata data (om du vill), är följande några möjliga metoder. De visas i prioritetsordning från en teknisk visnings punkt från de flesta till minst bättre:

* Stoppa, om möjligt, stoppa insamling av, obfuscate, maskera eller på annat sätt justera data som samlas in för att utesluta att de betraktas som "privata". Detta är _av_ den bästa metoden, vilket innebär att du slipper skapa en mycket kostsam och påverkan på data hanterings strategin.
* Om det inte är möjligt försöker du normalisera data för att minska påverkan på data plattformen och prestandan. I stället för att logga ett explicit användar-ID ska du till exempel skapa en uppslags data som korrelerar användar namnet och deras information till ett internt ID som sedan kan loggas någon annan stans. På så sätt kan en av dina användare be dig att ta bort sin personliga information, men det är möjligt att bara ta bort raden i uppslags tabellen som motsvarar användaren. 
* Slutligen, om privata data måste samlas in, skapar du en process runt rensnings-API-sökvägen och den befintliga API-sökvägen för frågor för att uppfylla alla skyldigheter som du kan ha vid export och borttagning av privata data som är associerade med en användare.

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Var ska du söka efter privata data i Log Analytics?

Log Analytics är ett flexibelt lager, som när du förväntar dig ett schema för dina data, kan du åsidosätta alla fält med anpassade värden. Dessutom kan alla anpassade scheman matas in. Det är därför omöjligt att säga exakt var du kan hitta privata data i din speciella arbets yta. Följande platser är dock lämpliga start punkter i inventeringen:

### <a name="log-data"></a>Loggdata

* *IP-adresser*: Log Analytics samlar in flera olika IP-uppgifter över flera olika tabeller. Följande fråga visar till exempel alla tabeller där IPv4-adresser har samlats in under de senaste 24 timmarna:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Användar-ID*: användar-ID: n finns i många olika lösningar och tabeller. Du kan söka efter ett visst användar namn i hela data uppsättningen med kommandot search:
    ```
    search "[username goes here]"
    ```
  Kom ihåg att se till att du inte bara kan läsa av människo användare, utan även GUID som direkt kan spåras tillbaka till en viss användare!
* *Enhets*-ID: t. ex. användar-ID: n anses ibland "privat". Använd samma metod som i listan ovan för användar-ID: n för att identifiera tabeller där detta kan vara ett problem. 
* *Anpassade data*: Log Analytics tillåter samlingen på flera olika sätt: anpassade loggar och anpassade fält, [API för http-datainsamling](../logs/data-collector-api.md) och anpassade data som samlas in som en del av system händelse loggarna. Alla dessa är känsliga för att innehålla privata data och bör undersökas för att kontrol lera om det finns några sådana data.
* *Lösnings fångade data*: eftersom lösnings mekanismen är en öppen och avslutad, rekommenderar vi att du visar alla tabeller som genereras av lösningar för att säkerställa efterlevnad.

### <a name="application-data"></a>Programdata

* *IP-adresser*: medan Application Insights som standard OBFUSCATE alla IP-adressintervall till "0.0.0.0", är det ett ganska vanligt mönster att åsidosätta det här värdet med den faktiska användar-IP-adressen för att underhålla sessionsinformation. Analytics-frågan nedan kan användas för att hitta tabeller som innehåller värden i IP-adress kolumnen förutom "0.0.0.0" under de senaste 24 timmarna:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Användar-ID*: som standard använder Application Insights slumpmässigt genererade ID: n för spårning av användar-och-session. Det är dock vanligt att se dessa fält som åsidosätts för att lagra ett ID som är relevant för programmet. Till exempel: användar namn, AAD GUID osv. Dessa ID: n anses ofta vara i omfattning som personliga data och bör därför hanteras på lämpligt sätt. Vår rekommendation är alltid att försöka obfuscate eller maskera dessa ID: n. Fält där dessa värden ofta finns är session_Id, user_Id, user_AuthenticatedId, user_AccountId, samt customDimensions.
* *Anpassade data*: Application Insights gör att du kan lägga till en uppsättning anpassade dimensioner i vilken datatyp som helst. Dessa dimensioner *kan vara data* . Använd följande fråga för att identifiera anpassade dimensioner som samlats in under de senaste 24 timmarna:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *InMemory-och överförings data*: Application Insights spårar undantag, begär Anden, beroende anrop och spår. Privata data kan ofta samlas in på koden och HTTP-anrops nivån. Granska tabellerna undantag, förfrågningar, beroenden och spår för att identifiera dessa data. Använd [telemetri-initierare](../app/api-filtering-sampling.md) där det är möjligt att obfuscate dessa data.
* *Snapshot debugger avbildningar*: funktionen [Snapshot debugger](../app/snapshot-debugger.md) i Application Insights gör att du kan samla in fel söknings ögonblicks bilder när ett undantag påträffas i programmets produktions instans. Ögonblicks bilder kommer att exponera den fullständiga stack spårningen som leder till undantag och värden för lokala variabler i varje steg i stacken. Den här funktionen tillåter tyvärr inte selektiv borttagning av fäst punkter eller programmerings åtkomst till data i ögonblicks bilden. Om standard lagrings takten för ögonblicks bilder inte uppfyller dina krav, är det därför dags att inaktivera funktionen.

## <a name="how-to-export-and-delete-private-data"></a>Exportera och ta bort privata data

Som vi nämnt i avsnittet [strategi för hantering av personliga data](#strategy-for-personal-data-handling) tidigare rekommenderas det __starkt__ att om det är möjligt, för att omstrukturera data insamlings principen för att inaktivera insamlingen av privata data, dölja eller maskera den, eller på annat sätt ändra den för att ta bort den från att anses som "privat". Att hantera data leder till att du och ditt team kan definiera och automatisera en strategi, bygga ett gränssnitt för dina kunder så att de kan interagera med sina data via och löpande underhålls kostnader. Vidare är det kostsamt kostsamt för Log Analytics och Application Insights, och en stor mängd samtidiga frågor eller rensnings-API-anrop har möjlighet att negativt påverka all annan interaktion med Log Analytics funktioner. I detta fall är det verkligen några giltiga scenarier där privata data måste samlas in. I dessa fall bör data hanteras enligt beskrivningen i det här avsnittet.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Visa och exportera

För både Visa och exportera data begär Anden, ska [API för Log Analytics fråga](https://dev.loganalytics.io/) eller  [API för Application Insights fråga](https://dev.applicationinsights.io/quickstart) användas. Logik för att konvertera en form av data till en lämplig som du kan leverera till användarna är upp till dig att implementera. [Azure Functions](https://azure.microsoft.com/services/functions/) är en bra plats som värd för sådan logik.

> [!IMPORTANT]
>  Det stora flertalet rensnings åtgärder kan utföras mycket snabbare än SLA, och **det formella service avtalet för slut för ande av rensnings åtgärder anges till 30 dagar** på grund av deras tunga påverkan på den data plattform som används. Detta SLA uppfyller GDPR-kraven. Det är en automatiserad process så det finns inget sätt att begära att en åtgärd hanteras snabbare. 

### <a name="delete"></a>Ta bort

> [!WARNING]
> Borttagningar i Log Analytics är destruktiva och icke-reversibela! Var ytterst försiktig när de körs.

Vi har gjort tillgängliga som en del av en sekretess hantering som hanterar en *rensnings* -API-sökväg. Den här sökvägen bör användas sparsamt på grund av risken som är kopplad till att göra detta, den potentiella prestanda påverkan och potentialen att skeva alla agg regeringar, mätningar och andra aspekter av dina Log Analytics data. I avsnittet [strategi för personlig data hantering](#strategy-for-personal-data-handling) finns alternativa metoder för att hantera privata data.

> [!NOTE]
> När rensnings åtgärden har utförts går det inte att komma åt data medan [status för rensnings åtgärden](/rest/api/loganalytics/workspacepurge/getpurgestatus) *väntar*. 

Rensa är en hög privilegie rad åtgärd som ingen app eller användare i Azure (inklusive resurs ägaren) har behörighet att köra utan att uttryckligen beviljas en roll i Azure Resource Manager. Den här rollen är _data rensning_ och bör delegeras försiktigt på grund av risken för data förlust. 

> [!IMPORTANT]
> För att kunna hantera system resurser begränsas rensnings förfrågningar vid 50 förfrågningar per timme. Du bör batch-köra rensnings begär Anden genom att skicka ett enda kommando vars predikat innehåller alla användar identiteter som kräver rensning. Använd [operatorn i](/azure/kusto/query/inoperator) för att ange flera identiteter. Du bör köra frågan innan du kör rensnings förfrågan för att verifiera att resultatet förväntas. 



När Azure Resource Manager rollen har tilldelats är två nya API-sökvägar tillgängliga: 

#### <a name="log-data"></a>Loggdata

* [Efter rensning](/rest/api/loganalytics/workspacepurge/purge) – tar ett objekt som anger data parametrar som ska tas bort och returnerar en referens-GUID 
* Hämta rensnings status: POST rensnings anropet returnerar ett "x-MS-status-plats"-huvud som innehåller en URL som du kan anropa för att fastställa status för ditt rensnings-API. Exempel:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Även om vi förväntar dig att de flesta rensnings åtgärder ska gå snabbare än vårt service avtal, på grund av deras tunga påverkan på den data plattform som används av Log Analytics, **anges det formella service avtalet för slut för ande av rensnings åtgärder till 30 dagar**. 

#### <a name="application-data"></a>Programdata

* [Efter rensning](/rest/api/application-insights/components/purge) – tar ett objekt som anger data parametrar som ska tas bort och returnerar en referens-GUID
* Hämta rensnings status: POST rensnings anropet returnerar ett "x-MS-status-plats"-huvud som innehåller en URL som du kan anropa för att fastställa status för ditt rensnings-API. Exempel:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Det stora flertalet rensnings åtgärder kan slutföras mycket snabbare än SLA, på grund av den stora påverkan på den data plattform som används av Application Insights, **det formella service avtalet för slut för ande av rensnings åtgärder anges till 30 dagar**.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur Log Analytics data samlas in, bearbetas och skyddas finns i [Log Analytics data säkerhet](../logs/data-security.md).
- Mer information om hur Application Insights data samlas in, bearbetas och skyddas finns i [Application Insights data säkerhet](../app/data-retention-privacy.md).