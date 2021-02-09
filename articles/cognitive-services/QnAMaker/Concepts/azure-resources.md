---
title: Azure-resurser – QnA Maker
description: QnA Maker använder flera Azure-källor, var och en med ett annat syfte. Om du förstår hur de används individuellt kan du planera för och välja rätt pris nivå eller veta när du ska ändra pris nivån. Om du förstår hur de används i kombinationen kan du hitta och åtgärda problem när de inträffar.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: f46a0938ebb8d9fe7e032162120056dca96b9567
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979770"
---
# <a name="azure-resources-for-qna-maker"></a>Azure-resurser för QnA Maker

QnA Maker använder flera Azure-källor, var och en med ett annat syfte. Om du förstår hur de används individuellt kan du planera för och välja rätt pris nivå eller veta när du ska ändra pris nivån. Om du förstår hur de används _i kombinationen_ kan du hitta och åtgärda problem när de inträffar.

## <a name="resource-planning"></a>Resurs planering

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

När du först utvecklar en QnA Maker kunskaps bas i prototyp fasen är det vanligt att ha en enda QnA Maker resurs för både testning och produktion.

När du går vidare till utvecklings fasen i projektet bör du tänka på följande:

* Hur många språk kommer ditt kunskaps bas system att innehålla?
* Hur många regioner måste din kunskaps bas vara tillgänglig i?
* Hur många dokument i varje domän som systemet ska innehålla?

Planera att ha en enda QnA Maker resurs har alla kunskaps banker som har samma språk, samma region och samma ämnes domän kombination.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

När du först utvecklar en QnA Maker hanterad kunskaps bas i prototyp fasen är det vanligt att ha en enda QnA Maker hanterad resurs för både testning och produktion.

När du går vidare till utvecklings fasen i projektet bör du tänka på följande:

* Hur många språk kommer ditt kunskaps bas system att innehålla?
* Hur många regioner måste din kunskaps bas vara tillgänglig i?
* Hur många dokument i varje domän som systemet ska innehålla?

---

## <a name="pricing-tier-considerations"></a>Pris nivå överväganden

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Det finns vanligt vis tre parametrar som du behöver tänka på:

* **Det data flöde du behöver från tjänsten**:
    * Välj lämplig [app-plan](https://azure.microsoft.com/pricing/details/app-service/plans/) för din app service utifrån dina behov. Du kan [skala upp](../../../app-service/manage-scale-up.md) eller ned appen.
    * Detta bör även påverka din Azure **kognitiv sökning** SKU-val, se mer information [här](../../../search/search-sku-tier.md). Dessutom kan du behöva justera Kognitiv sökning [kapacitet](../../../search/search-capacity-planning.md) med repliker.

* **Storlek och antal kunskaps baser**: Välj lämplig [Azure Search-SKU](https://azure.microsoft.com/pricing/details/search/) för ditt scenario. Normalt bestämmer du hur många kunskaps baser du behöver baserat på antalet olika ämnes domäner. När en ämnes domän (för ett enskilt språk) ska finnas i en kunskaps bas.

    Du kan publicera N-1 kunskaps baser på en viss nivå, där N är de maximala index som tillåts på nivån. Kontrol lera också den maximala storleken och antalet dokument som tillåts per nivå.

    Om din nivå till exempel har 15 tillåtna index, kan du publicera 14 kunskaps banker (1 index per publicerad kunskaps bas). Det femtonde indexet används för alla kunskaps banker för redigering och testning.

* **Antal dokument som källor**: den kostnads fria SKU: n för QNA Maker hanterings tjänsten begränsar antalet dokument som du kan hantera via portalen och API: erna till 3 (med en storlek på 1 MB). Standard-SKU: n har ingen gräns för antalet dokument som du kan hantera. Se mer information [här](https://aka.ms/qnamaker-pricing).

I följande tabell får du några rikt linjer på hög nivå.

|                            | QnA Maker hantering | App Service | Azure Cognitive Search | Begränsningar                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **Experimentering**        | Kostnads fri SKU             | Kostnads fri nivå   | Kostnads fri nivå    | Publicera upp till 2 KB, 50 MB storlek  |
| **Utvecklings-och test miljö**   | Standard-SKU         | Delad      | Grundläggande        | Publicera upp till 14 KB, 2 GB storlek    |
| **Produktions miljö** | Standard-SKU         | Basic       | Standard     | Publicera upp till 49 KB, 25 GB storlek |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Det finns vanligt vis tre parametrar som du behöver tänka på:

* **Det data flöde du behöver från tjänsten**:
    * QnA Maker hanterad (för hands version) är en kostnads fri tjänst och data flödet är för närvarande begränsade till 10 TPS för API: er för hantering och förutsägelser.
    * Detta bör även påverka din Azure **kognitiv sökning** SKU-val, se mer information [här](../../../search/search-sku-tier.md). Dessutom kan du behöva justera Kognitiv sökning [kapacitet](../../../search/search-capacity-planning.md) med repliker.

* **Storlek och antal kunskaps baser**: Välj lämplig [Azure Search-SKU](https://azure.microsoft.com/pricing/details/search/) för ditt scenario. Normalt bestämmer du hur många kunskaps baser du behöver baserat på antalet olika ämnes domäner. När en ämnes domän (för ett enskilt språk) ska finnas i en kunskaps bas.

    Med QnA Maker hanterad (för hands version) kan du välja att konfigurera QnA Maker-tjänsten för KB på ett enda språk eller flera språk. Du kan välja det här alternativet när du skapar den första kunskaps basen i din QnA Maker hanterade tjänst (för hands version).

    ![QnA Maker hanterad (för hands version) flerspråkigt kunskaps bas val](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

    Du kan publicera N-1 kunskaps baser om ett enskilt språk eller N/2-kunskaps baser på olika språk i en viss nivå, där N är det maximala antalet index som tillåts på nivån. Kontrol lera också den maximala storleken och antalet dokument som tillåts per nivå.

    Om din nivå till exempel har 15 tillåtna index, kan du publicera 14 kunskaps baser på samma språk (1 index per publicerad kunskaps bas). Det femtonde indexet används för alla kunskaps banker för redigering och testning. Om du väljer att ha kunskaps banker på olika språk kan du bara publicera 7 kunskaps baser.

* **Antal dokument som källor**: QNA Maker hanterad (för hands version) är en kostnads fri tjänst och det finns inga begränsningar för antalet dokument som du kan lägga till som källor. Se mer information [här](https://aka.ms/qnamaker-pricing).

I följande tabell får du några rikt linjer på hög nivå.

|                            |Azure Cognitive Search | Begränsningar                      |
| -------------------------- |------------ | -------------------------------- |
| **Experimentering**        |Kostnads fri nivå    | Publicera upp till 2 KB, 50 MB storlek  |
| **Utvecklings-och test miljö**   |Grundläggande        | Publicera upp till 14 KB, 2 GB storlek    |
| **Produktions miljö** |Standard     | Publicera upp till 49 KB, 25 GB storlek |

---

## <a name="recommended-settings"></a>Rekommenderade inställningar

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

|Mål frågor per sekund | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 replik   | S1, 1 replik    |
| 50         | S3, 10 repliker       | S1, 12 repliker         |
| 80         | S3, 10 repliker      |  S3, 12 repliker  |
| 100         | P3V2, 10 repliker  | S3, 12 repliker, 3 partitioner   |
| 200 till 250         | P3V2, 20 repliker | S3, 12 repliker, 3 partitioner    |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

QnA Maker Managed är en kostnads fri tjänst och data flödet är för närvarande begränsade till 10 transaktioner per sekund för både hanterings-API: er och förutsägelse-API: er. Om du vill rikta in dig på 10 transaktioner per sekund för tjänsten rekommenderar vi att du har SKU (1 instans) av Azure Kognitiv sökning.

---

## <a name="when-to-change-a-pricing-tier"></a>När du ska ändra en pris nivå

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

|Uppgradera|Anledning|
|--|--|
|[Uppgradera](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) SKU för QnA Maker hantering|Du vill ha fler QnA-par eller dokument källor i din kunskaps bas.|
|[Uppgradera](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service SKU och kontrol lera Kognitiv sökning nivå och [skapa kognitiv sökning repliker](../../../search/search-capacity-planning.md)|Kunskaps basen behöver för att betjäna fler förfrågningar från din klient app, till exempel en chatt-robot.|
|[Uppgradera](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Kognitiv sökning-tjänst|Du planerar att ha många kunskaps baser.|

Hämta de senaste körnings uppdateringarna genom att [uppdatera App Service i Azure Portal](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

[Uppgradera](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Kognitiv sökning service när du planerar att ha många kunskaps baser.

---

## <a name="keys-in-qna-maker"></a>Nycklar i QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Din QnA Maker-tjänst har två typer av nycklar: **redigerings nycklar** och **fråge slut punkts nycklar** som används med körnings miljön som finns i App Service.

Använd de här nycklarna när du gör förfrågningar till tjänsten via API: er.

![Nyckelhantering](../media/qnamaker-how-to-key-management/key-management.png)

|Namn|Plats|Syfte|
|--|--|--|
|Redigering/prenumerations nyckel|[Azure-portalen](https://azure.microsoft.com/free/cognitive-services/)|Dessa nycklar används för att få åtkomst till [API: erna för QNA Maker Management-tjänsten](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Med dessa API: er kan du redigera frågorna och svaren i din kunskaps bas och publicera din kunskaps bas. Nycklarna skapas när du skapar en ny QnA Maker-tjänst.<br><br>Sök efter dessa nycklar på **Cognitive Services** resurs på sidan **nycklar** .|
|Frågans slut punkts nyckel|[QnA Maker-portalen](https://www.qnamaker.ai)|Dessa nycklar används för att skicka frågor till den publicerade kunskaps bas slut punkten för att få svar på en användar fråga. Du använder vanligt vis den här fråge slut punkten i din Chat-robot eller i klient program koden som ansluter till QnA Maker-tjänsten. De här nycklarna skapas när du publicerar din QnA Maker-kunskaps bas.<br><br>Sök efter dessa nycklar på sidan **tjänst inställningar** . Hitta den här sidan från användarens meny längst upp till höger på sidan på den nedrullningsbara menyn.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Hitta redigerings nycklar i Azure Portal

Du kan visa och återställa dina redigerings nycklar från Azure Portal, där du skapade QnA Maker resursen. Dessa nycklar kan kallas för prenumerations nycklar.

1. Gå till QnA Maker resursen i Azure Portal och välj den resurs som har _Cognitive Servicess_ typ:

    ![QnA Maker resurs lista](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Gå till **nycklar och slut punkt**:

    ![Prenumerations nyckel för QnA Maker Managed (för hands version)](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>Hitta fråge slut punkts nycklar i QnA Maker Portal

Slut punkten finns i samma region som resursen eftersom slut punkts nycklarna används för att anropa kunskaps basen.

Slut punkts nycklar kan hanteras från [QNA Maker-portalen](https://qnamaker.ai).

1. Logga in på [QNA Maker Portal](https://qnamaker.ai), gå till din profil och välj sedan **tjänst inställningar**:

    ![Slut punkts nyckel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visa eller Återställ dina nycklar:

    > [!div class="mx-imgBorder"]
    > ![Slut punkts nyckel hanterare](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Uppdatera dina nycklar om du tror att de har komprometterats. Detta kan kräva motsvarande ändringar i klient programmet eller bot koden.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Tjänsten QnA Maker Managed (för hands version) har två typer av nycklar: **redigerings nycklar** och **Azure kognitiv sökning nycklar** som används för att få åtkomst till tjänsten i kundens prenumeration.

Använd de här nycklarna när du gör förfrågningar till tjänsten via API: er.

![Hanterad för hands version av Key Management](../media/qnamaker-how-to-key-management/qnamaker-v2-key-management.png)

|Namn|Plats|Syfte|
|--|--|--|
|Redigering/prenumerations nyckel|[Azure-portalen](https://azure.microsoft.com/free/cognitive-services/)|Dessa nycklar används för att få åtkomst till [API: erna för QNA Maker Management-tjänsten](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Med dessa API: er kan du redigera frågorna och svaren i din kunskaps bas och publicera din kunskaps bas. Nycklarna skapas när du skapar en ny QnA Maker-tjänst.<br><br>Sök efter dessa nycklar på **Cognitive Services** resurs på sidan **nycklar** .|
|Administratörs nyckel för Azure Kognitiv sökning|[Azure-portalen](../../../search/search-security-api-keys.md)|Dessa nycklar används för att kommunicera med Azure kognitiv Search-tjänsten som distribueras i användarens Azure-prenumeration. När du associerar en Azure kognitiv-sökning med tjänsten QnA Maker Managed (förhands granskning) skickas administratörs nyckeln automatiskt vidare till tjänsten QnA Maker. <br><br>Du hittar dessa nycklar på **Azure kognitiv sökning** -resursen på sidan **nycklar** .|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Hitta redigerings nycklar i Azure Portal

Du kan visa och återställa redigerings nycklarna från Azure Portal, där du skapade resursen QnA Maker Managed (för hands version). Dessa nycklar kan kallas för prenumerations nycklar.

1. Gå till resursen QnA Maker Managed (för hands version) i Azure Portal och välj den resurs som har *Cognitive Servicess* typ:

    ![Resurs lista för QnA Maker Managed (för hands version)](../media/qnamaker-how-to-key-management/qnamaker-v2-resource-list.png)

2. Gå till **nycklar och slut punkt**:

    ![Prenumerations nyckel för QnA Maker Managed (för hands version)](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="update-the-resources"></a>Uppdatera resurserna

Lär dig hur du uppgraderar de resurser som används av din kunskaps bas. QnA Maker Managed (för hands version) är **kostnads fritt** i för hands versionen. 

---

## <a name="management-service-region"></a>Region för hanterings tjänst

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Hanterings tjänsten för QnA Maker används endast för QnA Maker-portalen och för inledande data bearbetning. Den här tjänsten är endast tillgänglig i regionen **USA, västra** . Ingen kund information lagras i den här västra USA-tjänsten.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

I QnA Maker hanterad (för hands version) är både hanterings-och förutsägelse tjänsterna samplacerade i samma region. För närvarande QnA Maker hanterad (för hands version) tillgänglig i **södra centrala USA, norra Europa och östra Australien**.

---

## <a name="resource-naming-considerations"></a>Överväganden för resurs namn givning

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

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

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Resurs namnet för den QnA Maker hanterade resursen (för hands version), till exempel `qna-westus-f0-b` , används också för att ge de andra resurserna.

Med Azure Portal skapa-fönstret kan du skapa en QnA Maker hanterad resurs (för hands version) och välja pris nivåer för de andra resurserna.

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal för QnA Maker hanterad (för hands version) av resurs skapande ](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png) när resurserna har skapats, har de samma namn.

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal resurs lista QnA Maker hanterad (för hands version)](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

> [!TIP]
> Skapa en ny resurs grupp när du skapar en QnA Maker-resurs. Det gör att du kan se alla resurser som är associerade med den QnA Maker hanterade resursen (för hands version) när du söker efter resurs grupp.

> [!TIP]
> Använd en namngivnings konvention för att ange pris nivåer i namnet på resursen eller resurs gruppen. När du får fel meddelanden när du skapar en ny kunskaps bas eller lägger till nya dokument, är gränsen för Kognitiv sökning pris nivån ett vanligt problem.

---

## <a name="resource-purposes"></a>Resurs syfte

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Varje Azure-resurs som skapats med QnA Maker har ett specifikt syfte:

* QnA Maker resurs
* Kognitiv sökning resurs
* App Service
* App Plans tjänst
* Application Insights tjänst

### <a name="qna-maker-resource"></a>QnA Maker resurs

QnA Maker-resursen ger till gång till API: erna för redigering och publicering samt det NLP-baserade (Natural Language Processing)-baserade andra rangordnings skiktet (Ranging #2) för QnA-par vid körning.

Den andra rangordningen använder intelligenta filter som kan innehålla metadata och Uppföljnings-prompter.

#### <a name="qna-maker-resource-configuration-settings"></a>QnA Maker inställningar för resurs konfiguration

När du skapar en ny kunskaps bas i [QNA Maker Portal](https://qnamaker.ai)är **språk** inställningen den enda inställning som tillämpas på resurs nivå. Du väljer språk när du skapar den första kunskaps basen för resursen.

### <a name="cognitive-search-resource"></a>Kognitiv sökning resurs

[Kognitiv sökning](../../../search/index.yml) resurs används för att:

* Lagra QnA-par
* Ange den inledande rangordningen (Ranger #1) för QnA-par vid körning

#### <a name="index-usage"></a>Index användning

Resursen behåller ett index som fungerar som test index och de återstående indexen korreleras med en publicerad kunskaps bas.

En resurs som är priss ätts 15 index, innehåller 14 publicerade kunskaps banker och ett index används för att testa alla kunskaps baser. Det här test indexet är partitionerat av kunskaps basen så att en fråga som använder det interaktiva test fönstret använder test index men bara returnerar resultat från den partition som är associerad med den aktuella kunskaps basen.

#### <a name="language-usage"></a>Språk användning

Den första kunskaps basen som skapas i QnA Maker-resursen används för att fastställa det _enskilda_ språk som har angetts för kognitiv sökning resursen och alla dess index. Du kan bara ha _en språk uppsättning_ för en QNA Maker-tjänst.

#### <a name="using-a-single-cognitive-search-service"></a>Använda en enda Kognitiv sökning-tjänst

Om du skapar en QnA-tjänst och dess beroenden (till exempel Sök) via portalen skapas en Sök tjänst för dig och länkas till QnA Maker tjänsten. När resurserna har skapats kan du uppdatera App Service-inställningen för att använda en tidigare befintlig Sök tjänst och ta bort den som du nyss skapade.

Lär dig [hur du konfigurerar](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QNA Maker att använda en annan kognitiv tjänst resurs än den som skapats som en del av processen för att skapa en QNA Maker resurs.

### <a name="app-service-and-app-service-plan"></a>App service-och App Service-plan

[App Service](../../../app-service/index.yml) används av klient programmet för att få åtkomst till publicerade kunskaps banker via runtime-slutpunkten.

För att fråga den publicerade kunskaps basen använder alla publicerade kunskaps baser samma URL-slutpunkt, men anger **kunskaps bas-ID: t** i vägen.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) används för att samla in chatt och telemetri. Granska vanliga [Kusto-frågor](../how-to/get-analytics-knowledge-base.md) för information om tjänsten.

### <a name="share-services-with-qna-maker"></a>Dela tjänster med QnA Maker

QnA Maker skapar flera Azure-resurser. Använd följande tabell för att ta reda på vad du kan och inte kan dela för att minska hanteringen och dra nytta av kostnads delning:

|Tjänst|Dela|Anledning|
|--|--|--|
|Cognitive Services|X|Inte möjlig enligt design|
|App Service-plan|✔|Fast disk utrymme som allokerats för en App Service plan. Om andra appar som delar samma App Service plan använder betydande disk utrymme, kommer QnAMaker App Service-instansen att drabbas av problem.|
|App Service|X|Inte möjlig enligt design|
|Application Insights|✔|Kan delas|
|Söktjänst|✔|1. `testkb` är ett reserverat namn för QnAMaker-tjänsten. det kan inte användas av andra.<br>2. synonym mappningen med namnet `synonym-map` är reserverad för QnAMaker-tjänsten.<br>3. antalet publicerade kunskaps baser är begränsat av Sök tjänst nivån. Om det finns lediga index är andra tjänster som kan använda dem.|

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Varje Azure-resurs som skapats med QnA Maker hanterad (för hands version) har ett specifikt syfte:

* QnA Maker resurs
* Kognitiv sökning resurs

### <a name="qna-maker-resource"></a>QnA Maker resurs

Den QnA Maker hanterade resursen (för hands version) ger åtkomst till API: erna för redigering och publicering, är värd för rangordnings körningen och ger telemetri.

### <a name="azure-cognitive-search-resource"></a>Azure Kognitiv sökning-resurs

[Kognitiv sökning](../../../search/index.yml) resurs används för att:

* Lagra QnA-par
* Ange den inledande rangordningen (Ranger #1) för QnA-par vid körning

#### <a name="index-usage"></a>Index användning

Du kan publicera N-1 kunskaps baser om ett enskilt språk eller N/2-kunskaps baser på olika språk i en viss nivå, där N är de maximala index som tillåts i Azure Kognitiv sökning-nivån. Kontrol lera också den maximala storleken och antalet dokument som tillåts per nivå.

Om din nivå till exempel har 15 tillåtna index, kan du publicera 14 kunskaps baser på samma språk (1 index per publicerad kunskaps bas). Det femtonde indexet används för alla kunskaps banker för redigering och testning. Om du väljer att ha kunskaps banker på olika språk kan du bara publicera 7 kunskaps baser.

#### <a name="language-usage"></a>Språk användning

Med QnA Maker hanterad (för hands version) kan du välja att konfigurera QnA Maker tjänsten för kunskaps baser på ett enda språk eller flera språk. Du gör det här valet under skapandet av den första kunskaps basen i QnA Makers tjänsten. Se [hur](#pricing-tier-considerations) du aktiverar språk inställningar per kunskaps bas.

---

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om QnA Maker [Knowledge Base](../index.yml)
* Förstå [livs cykeln för kunskaps bas](development-lifecycle-knowledge-base.md)
* Granska [begränsningar](../limits.md) för tjänster och kunskaps bas
