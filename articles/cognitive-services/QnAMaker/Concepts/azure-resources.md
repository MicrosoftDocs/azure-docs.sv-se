---
title: Azure-resurser – QnA Maker
description: QnA Maker använder flera Azure-källor, var och en med ett annat syfte. Om du förstår hur de används individuellt kan du planera för och välja rätt pris nivå eller veta när du ska ändra pris nivån. Om du förstår hur de används i kombinationen kan du hitta och åtgärda problem när de inträffar.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 06731c97217e3b6a5c498743b83f1d16bab791eb
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548437"
---
# <a name="azure-resources-for-qna-maker"></a>Azure-resurser för QnA Maker

QnA Maker använder flera Azure-källor, var och en med ett annat syfte. Om du förstår hur de används individuellt kan du planera för och välja rätt pris nivå eller veta när du ska ändra pris nivån. Om du förstår hur de används _i kombinationen_ kan du hitta och åtgärda problem när de inträffar.

## <a name="resource-planning"></a>Resurs planering

När du först utvecklar en QnA Maker kunskaps bas i prototyp fasen är det vanligt att ha en enda QnA Maker resurs för både testning och produktion.

När du går vidare till utvecklings fasen i projektet bör du tänka på följande:

* Hur många språk ditt kunskaps bas system ska innehålla
* Hur många regioner du behöver din kunskaps bas för att vara tillgänglig i/från
* Hur många dokument i varje domän som systemet ska innehålla

Planera att ha en enda QnA Maker resurs har alla kunskaps banker som har samma språk, samma region och samma ämnes domän kombination.

## <a name="pricing-tier-considerations"></a>Pris nivå överväganden

Det finns vanligt vis tre parametrar som du behöver tänka på:

* **Det data flöde du behöver från tjänsten**:
    * Välj lämplig [app-plan](https://azure.microsoft.com/pricing/details/app-service/plans/) för din app service utifrån dina behov. Du kan [skala upp](https://docs.microsoft.com/azure/app-service/manage-scale-up) eller ned appen.
    * Detta bör även påverka din Azure **kognitiv sökning** SKU-val, se mer information [här](https://docs.microsoft.com/azure/search/search-sku-tier). Dessutom kan du behöva justera Kognitiv sökning [kapacitet](../../../search/search-capacity-planning.md) med repliker.

* **Storlek och antal kunskaps baser**: Välj lämplig [Azure Search-SKU](https://azure.microsoft.com/pricing/details/search/) för ditt scenario. Normalt bestämmer du hur många kunskaps baser du behöver baserat på antalet olika ämnes domäner. När en ämnes domän (för ett enskilt språk) ska finnas i en kunskaps bas.

    Du kan publicera N-1 kunskaps baser på en viss nivå, där N är de maximala index som tillåts på nivån. Kontrol lera också den maximala storleken och antalet dokument som tillåts per nivå.

    Om din nivå till exempel har 15 tillåtna index, kan du publicera 14 kunskaps banker (1 index per publicerad kunskaps bas). Det femtonde indexet används för alla kunskaps banker för redigering och testning.

* **Antal dokument som källor**: den kostnads fria SKU: n för QNA Maker hanterings tjänsten begränsar antalet dokument som du kan hantera via portalen och API: erna till 3 (med en storlek på 1 MB). Standard-SKU: n har ingen gräns för antalet dokument som du kan hantera. Se mer information [här](https://aka.ms/qnamaker-pricing).

I följande tabell får du några rikt linjer på hög nivå.

|                            | QnA Maker hantering | App Service | Azure Cognitive Search | Begränsningar                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **Experimentering**        | Kostnads fri SKU             | Kostnads fri nivå   | Kostnads fri nivå    | Publicera upp till 2 KB, 50 MB storlek  |
| **Utvecklings-och test miljö**   | Standard-SKU         | Delad      | Basic        | Publicera upp till 14 KB, 2 GB storlek    |
| **Produktions miljö** | Standard-SKU         | Basic       | Standard     | Publicera upp till 49 KB, 25 GB storlek |

## <a name="recommended-settings"></a>Rekommenderade inställningar

|Mål frågor per sekund | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 instans   | S1, 1 instans    |
| 50         | S3, 10 instanser       | S1, 12 instanser         |
| 80         | S3, 10 instanser      |  S3, 12 instanser  |
| 100         | P3V2, 10 instanser  | S3, 12 instanser, 3 partitioner   |
| 200 till 250         | P3V2, 20 instanser | S3, 12 instanser, 3 partitioner    |

## <a name="when-to-change-a-pricing-tier"></a>När du ska ändra en pris nivå

|Uppgradera|Orsak|
|--|--|
|[Uppgradera](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) SKU för QnA Maker hantering|Du vill ha fler QnA-par eller dokument källor i din kunskaps bas.|
|[Uppgradera](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service SKU och kontrol lera Kognitiv sökning nivå och [skapa kognitiv sökning repliker](../../../search/search-capacity-planning.md)|Kunskaps basen behöver för att betjäna fler förfrågningar från din klient app, till exempel en chatt-robot.|
|[Uppgradera](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Kognitiv sökning-tjänst|Du planerar att ha många kunskaps baser.|

Hämta de senaste körnings uppdateringarna genom att [uppdatera App Service i Azure Portal](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="resource-naming-considerations"></a>Överväganden för resurs namn givning

Resurs namnet för QnA Maker resursen, till exempel `qna-westus-f0-b` , används också för att ge de andra resurserna.

Med Azure Portal skapa-fönstret kan du skapa en QnA Maker resurs och välja pris nivåer för de andra resurserna.

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal för att skapa QnA Maker-resurs](../media/concept-azure-resource/create-blade.png)

När resurserna har skapats har de samma namn, förutom den valfria Application Insights resursen, som postpends tecken till namnet.

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal resurs lista](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Skapa en ny resurs grupp när du skapar en QnA Maker-resurs. Det gör att du kan se alla resurser som är associerade med QnA Maker resursen när du söker efter resurs grupp.

> [!TIP]
> Använd en namngivnings konvention för att ange pris nivåer i namnet på resursen eller resurs gruppen. När du får fel meddelanden när du skapar en ny kunskaps bas eller lägger till nya dokument, är gränsen för Kognitiv sökning pris nivån ett vanligt problem.

## <a name="resource-purposes"></a>Resurs syfte

Varje Azure-resurs som skapats med QnA Maker har ett specifikt syfte:

* QnA Maker resurs
* Kognitiv sökning resurs
* App Service
* App Plans tjänst
* Application Insights tjänst


### <a name="cognitive-search-resource"></a>Kognitiv sökning resurs

[Kognitiv sökning](../../../search/index.yml) resurs används för att:

* Lagra QnA-par
* Ange den inledande rangordningen (Ranger #1) för QnA-par vid körning

#### <a name="index-usage"></a>Index användning

Resursen behåller ett index som fungerar som test index och de återstående indexen korreleras med en publicerad kunskaps bas.

En resurs som är priss ätts 15 index, innehåller 14 publicerade kunskaps banker och ett index används för att testa alla kunskaps baser. Det här test indexet är partitionerat av kunskaps basen så att en fråga som använder det interaktiva test fönstret använder test index men bara returnerar resultat från den partition som är associerad med den aktuella kunskaps basen.

#### <a name="language-usage"></a>Språk användning

Den första kunskaps basen som skapas i QnA Maker-resursen används för att fastställa det _enskilda_ språk som har angetts för kognitiv sökning resursen och alla dess index. Du kan bara ha _en språk uppsättning_ för en QNA Maker-tjänst.

### <a name="qna-maker-resource"></a>QnA Maker resurs

QnA Maker-resursen ger till gång till API: erna för redigering och publicering samt det NLP-baserade (Natural Language Processing)-baserade andra rangordnings skiktet (Ranging #2) för QnA-par vid körning.

Den andra rangordningen använder intelligenta filter som kan innehålla metadata och Uppföljnings-prompter.

#### <a name="qna-maker-resource-configuration-settings"></a>QnA Maker inställningar för resurs konfiguration

När du skapar en ny kunskaps bas i [QNA Maker Portal](https://qnamaker.ai)är **språk** inställningen den enda inställning som tillämpas på resurs nivå. Du väljer språk när du skapar den första kunskaps basen för resursen.

### <a name="app-service-and-app-service-plan"></a>App service-och App Service-plan

[App Service](../../../app-service/index.yml) används av klient programmet för att få åtkomst till publicerade kunskaps banker via runtime-slutpunkten.

För att fråga den publicerade kunskaps basen använder alla publicerade kunskaps baser samma URL-slutpunkt, men anger **kunskaps bas-ID: t** i vägen.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) används för att samla in chatt och telemetri. Granska vanliga [Kusto-frågor](../how-to/get-analytics-knowledge-base.md) för information om tjänsten.

## <a name="share-services-with-qna-maker"></a>Dela tjänster med QnA Maker

QnA Maker skapar flera Azure-resurser. Använd följande tabell för att ta reda på vad du kan och inte kan dela för att minska hanteringen och dra nytta av kostnads delning:

|Tjänst|Dela|Orsak|
|--|--|--|
|Cognitive Services|X|Inte möjlig enligt design|
|App Service-plan|✔|Fast disk utrymme som allokerats för en App Service plan. Om andra appar som delar samma App Service plan använder betydande disk utrymme, kommer QnAMaker App Service-instansen att drabbas av problem.|
|App Service|X|Inte möjlig enligt design|
|Application Insights|✔|Kan delas|
|Söktjänst|✔|1. `testkb` är ett reserverat namn för QnAMaker-tjänsten. det kan inte användas av andra.<br>2. synonym mappningen med namnet `synonym-map` är reserverad för QnAMaker-tjänsten.<br>3. antalet publicerade kunskaps baser är begränsat av Sök tjänst nivån. Om det finns lediga index är andra tjänster som kan använda dem.|

### <a name="using-a-single-cognitive-search-service"></a>Använda en enda Kognitiv sökning-tjänst

Om du skapar en QnA-tjänst och dess beroenden (till exempel Sök) via portalen skapas en Sök tjänst för dig och länkas till QnA Maker tjänsten. När resurserna har skapats kan du uppdatera App Service-inställningen för att använda en tidigare befintlig Sök tjänst och ta bort den som du nyss skapade.

Lär dig [hur du konfigurerar](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QNA Maker att använda en annan kognitiv tjänst resurs än den som skapats som en del av processen för att skapa en QNA Maker resurs.

## <a name="management-service-region"></a>Region för hanterings tjänst

Hanterings tjänsten för QnA Maker används endast för QnA Maker-portalen och för inledande data bearbetning. Den här tjänsten är endast tillgänglig i regionen **USA, västra** . Ingen kund information lagras i den här västra USA-tjänsten.

## <a name="keys-in-qna-maker"></a>Nycklar i QnA Maker

Din QnA Maker-tjänst har två typer av nycklar: **redigerings nycklar** och **fråge slut punkts nycklar** som används med körnings miljön som finns i App Service.

Om du letar efter din **prenumerations nyckel** [har du ändrat terminologin](#subscription-keys).

Använd de här nycklarna när du gör förfrågningar till tjänsten via API: er.

![Nyckelhantering](../media/qnamaker-how-to-key-management/key-management.png)

|Namn|Plats|Syfte|
|--|--|--|
|Redigerings nyckel|[Azure-portalen](https://azure.microsoft.com/free/cognitive-services/)|Dessa nycklar används för att få åtkomst till [API: erna för QNA Maker Management-tjänsten](https://go.microsoft.com/fwlink/?linkid=2092179). Med dessa API: er kan du redigera frågorna och svaren i din kunskaps bas och publicera din kunskaps bas. Nycklarna skapas när du skapar en ny QnA Maker-tjänst.<br><br>Sök efter dessa nycklar på **Cognitive Services** resurs på sidan **nycklar** .|
|Frågans slut punkts nyckel|[QnA Maker-portalen](https://www.qnamaker.ai)|Dessa nycklar används för att skicka frågor till den publicerade kunskaps bas slut punkten för att få svar på en användar fråga. Du använder vanligt vis den här fråge slut punkten i din Chat-robot eller i klient program koden som ansluter till QnA Maker-tjänsten. De här nycklarna skapas när du publicerar din QnA Maker-kunskaps bas.<br><br>Sök efter dessa nycklar på sidan **tjänst inställningar** . Hitta den här sidan från användarens meny längst upp till höger på sidan på den nedrullningsbara menyn.|

### <a name="subscription-keys"></a>Prenumerationsnycklar

Term redigerings-och fråge slut punkts nyckeln är rätt villkor. Föregående term var **prenumerations nyckel**. Om du ser en annan dokumentation som hänvisar till prenumerations nycklar motsvarar dessa referenser till redigerings-och fråge slut punkts nycklar (används i körnings miljön).

Du måste veta vad nyckeln har åtkomst till, kunskaps bas hantering eller kunskaps bas frågor för att veta vilken nyckel du behöver hitta.

## <a name="recommended-settings-for-network-isolation"></a>Rekommenderade inställningar för nätverks isolering

* Skydda kognitiva tjänst resurser från offentlig åtkomst genom [att konfigurera det virtuella nätverket](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal).
* Skydda App Service (QnA Runtime) från offentlig åtkomst:
    * Tillåt enbart trafik från kognitiva tjänst-IP: er. Dessa ingår redan i service tag gen "CognitiveServicesManagement". Detta krävs för att skapa API: er (Skapa/uppdatera KB) för att anropa app service och uppdatera Azure Search-tjänsten enligt detta.
    * Se till att du även tillåter andra start punkter som bot service, QnA Maker portal (kan vara ditt företags nätverket) osv. för förutsägelse "GenerateAnswer" API-åtkomst.
    * Läs [Mer information om service märken.](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om QnA Maker [Knowledge Base](knowledge-base.md)
* Förstå [livs cykeln för kunskaps bas](development-lifecycle-knowledge-base.md)
* Granska [begränsningar](../limits.md) för tjänster och kunskaps bas

