---
title: Översikt över Azure Resource Graph
description: Förstå hur Azure Resource Graph-tjänsten möjliggör komplexa frågor om resurser i stor skala över prenumerationer och klienter.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: b5df124d07b8ecfb20f5dec08830d8156e8df2cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919149"
---
# <a name="what-is-azure-resource-graph"></a>Vad är Azure Resource Graph?

Azure Resource Graph är en tjänst i Azure som är utformad för att utöka Azures resurs hantering genom att tillhandahålla effektiv och genomförd resurs utforskning med möjlighet att fråga i skala över en specifik uppsättning prenumerationer så att du effektivt kan styra din miljö. De här frågorna innehåller följande funktioner:

- Möjlighet att fråga resurser med komplex filtrering, gruppering och sortering av resursegenskaper.
- Möjlighet att utforska resurser på ett iterativt, baserat på styrnings krav.
- Möjlighet att utvärdera effekten av att tillämpa principer i en omfattande molnmiljö.
- Möjlighet att [göra detalj ändringar i resurs egenskaper](./how-to/get-resource-changes.md) (för hands version).

I den här dokumentationen går vi igenom varje funktion i detalj.

> [!NOTE]
> Azure Resource Graph-befogenheter Azure Portal Sök fältet, den nya webbläsaren "alla resurser" och Azure policy [ändrings historikens](../policy/how-to/determine-non-compliance.md#change-history) 
>  _visuella skillnad_. Den är utformad för att hjälpa kunder att hantera storskaliga miljöer.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hur kompletterar Resource Graph Resurshantering i Azure

Resource Manager stöder för närvarande frågor över grundläggande resurs fält, specifikt resurs namn, ID, typ, resurs grupp, prenumeration och plats. Resource Manager tillhandahåller också anläggningar för att anropa enskilda resurs leverantörer för detaljerade egenskaper en resurs i taget.

Du kan använda Azure Resource Graph för att få åtkomst till dessa egenskaper, och resursprovidrarna returnerar utan att behöva göra enskilda anrop till varje enskild resursprovider. En lista över resurs typer som stöds finns i [referensen tabell och resurs typ](./reference/supported-tables-resources.md). Ett alternativt sätt att se resurs typer som stöds är via [schema läsaren i Azure Resource Graph Explorer](./first-query-portal.md#schema-browser).

Med Azure Resource Graph kan du:

- Få åtkomst till egenskaperna som returneras av resurs leverantörer utan att behöva göra enskilda anrop till varje resurs leverantör.
- Visa de senaste 14 dagarna i ändrings historiken som har gjorts till resursen för att se vilka egenskaper som har ändrats och när. (förhandsversion)

> [!NOTE]
> Som _förhands gransknings_ funktion `type` har vissa objekt ytterligare egenskaper som inte är tillgängliga för Resource Manager. Mer information finns i [utökade egenskaper (för hands version)](./concepts/query-language.md#extended-properties).

## <a name="how-resource-graph-is-kept-current"></a>Hur resurs diagram hålls aktuellt

När en Azure-resurs uppdateras, kommer resurs grafen att meddelas via resurs hanteraren för ändringen.
Resurs diagram uppdaterar sedan sin databas. Resurs diagram utför också en vanlig _fullständig genomsökning_. Den här genomsökningen säkerställer att resurs diagram data är aktuella om det finns uteblivna meddelanden eller när en resurs uppdateras utanför Resource Manager.

> [!NOTE]
> Resurs diagram använder ett `GET` till det senaste icke-förhandsgransknings-API: t för varje resurs leverantör för att samla in egenskaper och värden. Därför kanske den förväntade egenskapen inte är tillgänglig. I vissa fall har den API-version som används åsidosatts för att ge mer aktuella eller allmänt använda egenskaper i resultaten. I [Visa API-versionen för varje resurs typ](./samples/advanced.md#apiversion) exempel finns en fullständig lista i din miljö.

## <a name="the-query-language"></a>Frågespråket

Nu när du har en bättre förståelse för vad Azure Resource Graph är, ska vi ta en titt på hur du skapar frågor.

Det är viktigt att förstå att Azure Resource Graph-frågespråket baseras på [Kusto-frågespråket](/azure/data-explorer/data-explorer-overview) som används av Azure Data Explorer.

Information om åtgärder och funktioner som kan användas med Azure Resource Graph finns i [Frågespråket i Resource Graph](./concepts/query-language.md). Information om hur man söker efter resurser finns i [Utforska resurser](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Behörigheter i Azure Resource Graph

Om du vill använda resurs diagram måste du ha rätt behörighet i [Azure rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) med minst Läs behörighet till de resurser som du vill fråga. Utan minst `read`-behörigheter för Azure-objektet eller -objektgruppen returneras inte resultat.

> [!NOTE]
> Resurs diagram använder de prenumerationer som är tillgängliga för ett huvud konto under inloggningen. Om du vill se resurser för en ny prenumeration som lagts till under en aktiv session måste huvud kontot uppdatera kontexten. Den här åtgärden sker automatiskt när du loggar ut och in igen.

Azure CLI och Azure PowerShell använder prenumerationer som användaren har åtkomst till. När du använder REST API direkt tillhandahålls prenumerations listan av användaren. Om användaren har åtkomst till någon av prenumerationerna i listan returneras frågeresultaten för de prenumerationer som användaren har åtkomst till. Detta är detsamma som när [resurs grupper anropas – lista](/rest/api/resources/resourcegroups/list) \- du får resurs grupper som du har åtkomst till utan att du behöver ange att resultatet kan vara delvis. Om det inte finns några prenumerationer i prenumerations listan som användaren har rätt behörighet till är svaret _403_ (förbjudet).

> [!NOTE]
> I **förhands gransknings** rest Apis versionen `2020-04-01-preview` kan prenumerations listan vara ommitted.
> När både- `subscriptions` och `managementGroupId` -egenskaperna inte är definierade i begäran anges-klientens _omfång_ . Mer information finns i [omfånget för frågan](./concepts/query-language.md#query-scope).

## <a name="throttling"></a>Begränsning

Som en kostnads fri tjänst begränsas frågor till resurs diagram för att ge bästa möjliga upplevelse och svars tid för alla kunder. Om din organisation vill använda resurs Graph API för storskaliga och frekventa frågor använder du portalen feedback från [Portal sidan resurs diagram](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Ange ditt affärs ärende och markera kryss rutan Microsoft kan skicka e-post om din feedback för att teamet ska kontakta dig.

Resurs diagram begränsar frågor på användar nivå. Tjänst svaret innehåller följande HTTP-huvuden:

- `x-ms-user-quota-remaining` (int): den återstående resurs kvoten för användaren. Det här värdet mappar till antal frågor.
- `x-ms-user-quota-resets-after` (hh: mm: SS): tids perioden tills en användares kvot förbrukning återställs

Mer information finns i [rikt linjer för begränsade begär Anden](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Kör din första fråga

Azure Resource Graph Explorer, som är en del av Azure Portal, aktiverar resurs diagram frågor direkt i Azure Portal. Fäst resultaten som dynamiska diagram för att tillhandahålla dynamisk information i real tid till ditt Portal arbets flöde. Mer information finns i [första frågan med Azure Resource Graph Explorer](./first-query-portal.md).

Resurs diagram stöder Azure CLI, Azure PowerShell, Azure SDK för python med mera. Frågan är strukturerad samma för varje språk. Lär dig hur du aktiverar resurs diagram med:

- [Azure Portal och resurs diagram Utforskaren](./first-query-portal.md) 
- [Azure CLI](./first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](./first-query-powershell.md#add-the-resource-graph-module)
- [Python](./first-query-python.md#add-the-resource-graph-library)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [frågespråket](./concepts/query-language.md).
- Se språket som används i [Start frågor](./samples/starter.md).
- Se avancerade användnings områden i [avancerade frågor](./samples/advanced.md).
