---
title: Skapa en Sök tjänst i portalen
titleSuffix: Azure Cognitive Search
description: I den här snabb starten av portalen lär du dig hur du konfigurerar en Azure Kognitiv sökning-resurs i Azure Portal. Välj resursgrupper, regioner, samt SKU eller prisnivå.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/15/2021
ms.openlocfilehash: e183d81355d4db81e677f34b02330ddb9b631957
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651994"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Snabbstart: Skapa en Azure Cognitive Search-tjänst i portalen

[Azure kognitiv sökning](search-what-is-azure-search.md) är en Azure-resurs som används för att lägga till en fullständig texts ökning i anpassade appar. Du kan enkelt integrera det med andra Azure-tjänster som tillhandahåller data eller ytterligare bearbetning, med appar på nätverks servrar eller med program vara som körs på andra moln plattformar.

Du kan skapa Sök tjänsten med hjälp av [Azure Portal](https://portal.azure.com/), som beskrivs i den här artikeln. Du kan också använda [Azure PowerShell](search-manage-powershell.md), [Azure CLI](/cli/azure/search), [hanterings REST API](/rest/api/searchmanagement/)eller en [Azure Resource Manager tjänstmall](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

[![Animerad GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

## <a name="before-you-start"></a>Innan du börjar

Följande tjänst egenskaper har åtgärd ATS för livs längden för tjänsten – om du ändrar någon av dem krävs en ny tjänst. Eftersom de är fasta bör du tänka på användnings konsekvenserna när du fyller i varje egenskap:

+ Tjänst namnet blir en del av URL-slutpunkten ([gransknings tips](#name-the-service) för användbara tjänst namn).
+ [Tjänst nivån](search-sku-tier.md) påverkar faktureringen och ställer in en övre gräns för kapacitet. Vissa funktioner är inte tillgängliga på den kostnads fria nivån.
+ Tjänste region kan avgöra om vissa scenarier är tillgängliga. Om du behöver [hög säkerhetsfunktioner](search-security-overview.md) eller [AI-berikning](cognitive-search-concept-intro.md)måste du skapa Azure-kognitiv sökning i samma region som andra tjänster, eller i regioner som tillhandahåller funktionen i fråga. 

## <a name="subscribe-free-or-paid"></a>Prenumerera (kostnadsfritt eller betalt)

[Öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) och använd kostnadsfria krediter för att prova betalda Azure-tjänster. Efter att krediterna är slut behåller du kontot och fortsätter använda kostnadsfria Azure-tjänster, till exempel Websites. Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering.

Du kan också [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Med en MSDN-prenumeration får du krediter varje månad som du kan använda Azure-betaltjänster. 

## <a name="find-azure-cognitive-search"></a>Hitta Azure-Kognitiv sökning

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Klicka på plus tecknet (**"+ skapa resurs"**) i det övre vänstra hörnet.

1. Använd Sök fältet för att hitta "Azure kognitiv sökning" eller navigera till resursen via **Web**  >  **Azure kognitiv sökning**.

:::image type="content" source="media/search-create-service-portal/find-search3.png" alt-text="Skapa en resurs i portalen" border="false":::

## <a name="choose-a-subscription"></a>Välj en prenumeration

Om du har mer än en prenumeration väljer du en för Sök tjänsten. Om du implementerar [Double Encryption](search-security-overview.md#double-encryption) eller andra funktioner som är beroende av hanterade tjänst identiteter väljer du samma prenumeration som den som används för Azure Key Vault eller andra tjänster för vilka hanterade identiteter används.

## <a name="set-a-resource-group"></a>Ange en resurs grupp

En resurs grupp är en behållare som innehåller relaterade resurser för din Azure-lösning. Det krävs för Sök tjänsten. Det är också användbart för att hantera alla resurser, inklusive kostnader. En resurs grupp kan bestå av en tjänst eller flera tjänster som används tillsammans. Om du till exempel använder Azure Kognitiv sökning för att indexera en Azure Cosmos DB databas kan du göra båda tjänsterna i samma resurs grupp i hanterings syfte. 

Om du inte kombinerar resurser till en enda grupp, eller om befintliga resurs grupper är fyllda med resurser som används i orelaterade lösningar, skapar du en ny resurs grupp för din Azure Kognitiv sökning-resurs. 

:::image type="content" source="media/search-create-service-portal/new-resource-group.png" alt-text="Skapa en ny resursgrupp" border="false":::

Med tiden kan du spåra aktuella och projicerade kostnader hela tiden, eller så kan du Visa avgifter för enskilda resurser. Följande skärm bild visar vilken typ av kostnads information som du kan se när du kombinerar flera resurser i en grupp.

:::image type="content" source="media/search-create-service-portal/resource-group-cost-management.png" alt-text="Hantera kostnader på resurs grupps nivå" border="false":::

> [!TIP]
> Resurs grupper fören klar rensningen eftersom borttagning av en grupp tar bort alla tjänster i den. Om du har ett prototypprojekt som använder flera tjänster kan du placera dem i samma resursgrupp. Då är det lättare att rensa upp när projektet är slutfört.

## <a name="name-the-service"></a>Namnge tjänsten

I instans information anger du ett tjänst namn i **URL** -fältet. Namnet är en del av URL-slutpunkten mot vilken API-anrop utfärdas: `https://your-service-name.search.windows.net` . Till exempel, om du vill att slutpunkten ska vara `https://myservice.search.windows.net`, anger du `myservice`.

Kraven för tjänstnamn:

+ Det måste vara unikt inom namnområdet search.windows.net
+ Det måste vara mellan 2 och 60 tecken långt
+ Du måste använda små bokstäver, siffror eller bindestreck ("-")
+ Använd inte bindestreck ("-") i de första 2 tecknen eller som det sista enkla tecknet
+ Du får inte använda flera bindestreck ("--") var som helst

> [!TIP]
> Om du tror att du kommer att använda flera tjänster rekommenderar vi att du inkluderar regionen (eller platsen) i tjänst namnet som en namngivnings konvention. Tjänster inom samma region kan utbyta data utan kostnad, så om Azure Kognitiv sökning är i västra USA och du har andra tjänster även i västra USA `mysearchservice-westus` kan du spara en resa på sidan Egenskaper när du bestämmer hur du ska kombinera eller bifoga resurser.

## <a name="choose-a-location"></a>Välj en plats

Azure Kognitiv sökning är tillgängligt i de flesta regioner, enligt beskrivningen i de [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=search). Om du använder flera Azure-tjänster väljer du i allmänhet en region som också är värd för dina data eller program tjänster. Detta minimerar eller avvärderar bandbredds avgifter för utgående data (det kostar inget att debitera utgående data när tjänsterna är i samma region).

+ [AI-anrikning](cognitive-search-concept-intro.md) kräver Cognitive Services vara i samma fysiska region som Azure kognitiv sökning. Det finns bara några regioner som inte tillhandahåller båda. Sidan [produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=search) indikerar dubbel tillgänglighet genom att visa två staplade kryss rutor. En otillgänglig kombination saknar en bock markering:

  :::image type="content" source="media/search-create-service-portal/region-availability.png" alt-text="Regional tillgänglighet" border="true":::

+ Krav för affärs kontinuitet och haveri beredskap (BCDR) bör uppfyllas genom att flera Sök tjänster skapas i [regionala par](../best-practices-availability-paired-regions.md#azure-regional-pairs). Om du till exempel arbetar i Nordamerika kan du välja östra USA och västra USA, norra centrala USA och södra Central oss, för varje Sök tjänst.

Några funktioner har begränsad tillgänglighet baserat på regioner. Begränsningar beskrivs i funktions dokumentationen:

+ [Dubbel kryptering](search-security-overview.md#double-encryption)

+ ["Tillgänglighetszoner" i skalning för prestanda](search-performance-optimization.md#availability-zones).

## <a name="choose-a-pricing-tier"></a>Välj en pris nivå

Azure Kognitiv sökning erbjuds för närvarande på [flera pris nivåer](https://azure.microsoft.com/pricing/details/search/): kostnads fri, Basic, standard eller Storage optimerad. Nivåerna har olika [kapacitet och begränsningar](search-limits-quotas-capacity.md). Se [Välj en pris nivå](search-sku-tier.md) för vägledning.

Basic och standard är de vanligaste alternativen för produktions arbets belastningar, men de flesta kunder börjar med den kostnads fria tjänsten. Viktiga skillnader mellan nivåer är partitionens storlek och hastighet samt begränsningar för antalet objekt som du kan skapa.

Kom ihåg att det inte går att ändra pris nivån när tjänsten har skapats. Om du behöver en högre eller lägre nivå måste du återskapa tjänsten.

## <a name="create-your-service"></a>Skapa din tjänst

När du har angett nödvändiga indata kan du gå vidare och skapa tjänsten. 

:::image type="content" source="media/search-create-service-portal/new-service3.png" alt-text="Granska och skapa tjänsten" border="false":::

Tjänsten distribueras på några minuter. Du kan övervaka förloppet via Azure-meddelanden. Överväg att fästa tjänsten på instrument panelen för enkel åtkomst i framtiden.

:::image type="content" source="media/search-create-service-portal/monitor-notifications.png" alt-text="Övervaka och fäst tjänsten" border="false":::

## <a name="get-a-key-and-url-endpoint"></a>Hämta en nyckel och URL-slutpunkt

Om du inte använder portalen kräver program mässig åtkomst till din nya tjänst att du anger URL-slutpunkten och en API-nyckel för autentisering.

1. På sidan **Översikt** letar du upp och kopierar URL-slutpunkten på höger sida av sidan.

1. På sidan **nycklar** kopierar du antingen en av administratörs nycklarna (de är likvärdiga). API-administratörsnycklarna krävs för att skapa, uppdatera och ta bort objekt i tjänsten. Som kontrast ger frågeinställningar Läs åtkomst till index innehåll.

   :::image type="content" source="media/search-create-service-portal/get-url-key.png" alt-text="Översikts sida för tjänsten med URL-slutpunkt" border="false":::

Det behövs ingen slutpunkt eller nyckel för portalbaserade uppgifter. Portalen är redan länkad till din Azure Kognitiv sökning-resurs med administratörs behörighet. För en portal genom gång börjar du med [snabb start: skapa ett Azure kognitiv sökning-index i portalen](search-get-started-portal.md).

## <a name="scale-your-service"></a>Skala din tjänst

När tjänsten har etablerats kan du skala den så att den passar dina behov. Om du väljer standard nivån för din Azure Kognitiv sökning-tjänst kan du skala din tjänst i två dimensioner: repliker och partitioner. Om du hade valt Basic-nivån hade du bara kunnat lägga till repliker. Skalning är inte tillgängligt om du etablerar tjänsten utan kostnad.

Med ***Partitioner*** kan tjänsten lagra och söka igenom fler dokument.

Med ***Repliker*** kan tjänsten hantera en högre belastning av sökfrågor.

Om du lägger till resurser blir din månatliga faktura större. [Priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) kan hjälpa dig att förstå hur fakturering påverkas när du lägger till resurser. Kom ihåg att du kan ändra resurser baserat på belastning. Du kan till exempel öka resurserna för att skapa den fullständiga initiala indexeringen och sedan minska resurserna till en nivå som är mer lämplig för inkrementell indexering.

> [!Important]
> En tjänst måste ha [2 repliker för skrivskyddad SLA och 3 repliker för läs-/skriv-SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Gå till söktjänstsidan i Azure-portalen.
1. I det vänstra navigerings fönstret väljer du **Inställningar**  >  **skala**.
1. Använd reglaget om du vill lägga till resurser av endera typ.

:::image type="content" source="media/search-create-service-portal/settings-scale.png" alt-text="Lägg till kapacitet via repliker och partitioner" border="false":::

> [!Note]
> Lagring och hastighet per partition ökar med högre nivåer. Mer information finns i [kapacitet och begränsningar](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>När ska du lägga till en andra tjänst?

De flesta kunder använder bara en tjänst som etablerats på en nivå som har [rätt resursbalans](search-sku-tier.md). En tjänst kan vara värd för flera index i enlighet med [gränsvärdena för den nivå du väljer](search-capacity-planning.md), med indexen isolerade från varandra. I Azure Kognitiv sökning kan begär Anden bara dirigeras till ett index, vilket minimerar risken för oavsiktlig eller avsiktlig data hämtning från andra index i samma tjänst.

Även om de flesta kunder bara använder en tjänst kan det vara nödvändigt med tjänstredundans om de operativa kraven omfattar följande:

+ [Verksamhets kontinuitet och haveri beredskap (BCDR)](../best-practices-availability-paired-regions.md). Azure Kognitiv sökning ger inte omedelbar redundans vid ett avbrott.

+ [Arkitekturer för flera innehavare](search-modeling-multitenant-saas-applications.md) anropar ibland för två eller flera tjänster.

+ Globalt distribuerade program kan kräva Sök tjänster i varje geografi för att minimera svars tiden.

> [!NOTE]
> I Azure Kognitiv sökning kan du inte åtskilja indexering och frågor om åtgärder. Därför skulle du aldrig skapa flera tjänster för åtskiljda arbets belastningar. Index tillfrågas alltid om vilken tjänst den skapades i (du kan inte skapa ett index i en tjänst och kopiera den till en annan).

Det behövs ingen andra tjänst för hög tillgänglighet. Hög tillgänglighet för frågor uppnås när du använder minst 2 repliker i samma tjänst. Replik uppdateringar är sekventiella, vilket innebär att minst en fungerar när en tjänst uppdatering distribueras. Mer information om drift tid finns i [service nivå avtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Nästa steg

När du har slutfört en tjänst kan du fortsätta i portalen för att skapa ditt första index.

> [!div class="nextstepaction"]
> [Snabb start: skapa ett Azure Kognitiv sökning-index i portalen](search-get-started-portal.md)

Vill du optimera och Spara på dina moln utgifter?

> [!div class="nextstepaction"]
> [Börja analysera kostnaderna med Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)