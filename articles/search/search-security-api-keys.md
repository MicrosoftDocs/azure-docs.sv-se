---
title: Autentisering med API-nyckel
titleSuffix: Azure Cognitive Search
description: En API-nyckel styr inkommande åtkomst till tjänstens slut punkt. Administratörs nycklar ger Skriv behörighet. Det går att skapa frågeinställningar för skrivskyddad åtkomst.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 8b2e85744923fb2e7e474e049df1536aebc56f3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99536941"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Skapa och hantera API-nycklar för en Azure Kognitiv sökning-tjänst

Alla begär anden till en Sök tjänst behöver en skrivskyddad API-nyckel som har skapats specifikt för din tjänst. API-nyckeln är den enda mekanismen för autentisering av inkommande begär anden till din Sök tjänst slut punkt och krävs på varje begäran. 

+ I [rest-lösningar](search-get-started-rest.md) `api-key` anges vanligt vis i ett begär ande huvud

+ I [.net-lösningar](search-howto-dotnet-sdk.md)anges en nyckel ofta som en konfigurations inställning och skickas sedan som en [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)

Du kan visa och hantera API-nycklar i [Azure Portal](https://portal.azure.com), eller via [POWERSHELL](/powershell/module/az.search), [Azure CLI](/cli/azure/search)eller [REST API](/rest/api/searchmanagement/).

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Portal sida, Hämta inställningar, nycklar avsnitt" border="false":::

## <a name="what-is-an-api-key"></a>Vad är en API-nyckel?

En API-nyckel är en unik sträng som består av slumpmässigt genererade siffror och bokstäver som skickas på varje begäran till Sök tjänsten. Tjänsten accepterar begäran, om både själva begäran och nyckeln är giltiga. 

Två typer av nycklar används för att få åtkomst till din Sök tjänst: administratör (Läs-och Skriv behörighet) och fråga (skrivskyddad).

|Nyckel|Beskrivning|Gränser|  
|---------|-----------------|------------|  
|Administratör|Ger fullständig behörighet till alla åtgärder, inklusive möjligheten att hantera tjänsten, skapa och ta bort index, indexerare och data källor.<br /><br /> Två administratörs nycklar, som kallas *primära* och *sekundära* nycklar i portalen, genereras när tjänsten skapas och kan återskapas individuellt på begäran. Med två nycklar kan du rulla över en nyckel när du använder den andra nyckeln för fortsatt åtkomst till tjänsten.<br /><br /> Administratörs nycklar anges bara i huvuden för HTTP-begäran. Du kan inte placera en administrations-API-nyckel i en URL.|Högst 2 per tjänst|  
|Fråga|Ger skrivskyddad åtkomst till index och dokument, och distribueras vanligt vis till klient program som utfärdar Sök begär Anden.<br /><br /> Frågeinställningar skapas på begäran.<br /><br /> Du kan ange frågeinställningar i ett HTTP-begärandehuvuden för Sök-, förslags-eller söknings åtgärder. Du kan också skicka en sessionsnyckel som en parameter på en URL. Beroende på hur ditt klient program formulerar begäran kan det vara lättare att skicka nyckeln som en frågeparameter:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 per tjänst|  

 Visuellt är det ingen skillnad mellan en administratörs nyckel eller en fråga. Båda nycklarna är strängar som består av 32 slumpmässigt genererade alpha-numeriska tecken. Om du förlorar reda på vilken typ av nyckel som anges i ditt program kan du [kontrol lera nyckel värden i portalen](https://portal.azure.com).  

> [!NOTE]  
> Det anses vara en låg säkerhets praxis för att skicka känsliga data, till exempel en `api-key` i fråge-URI. Av den anledningen accepterar Azure Kognitiv sökning bara en sessionsnyckel som en `api-key` i frågesträngen och du bör undvika att göra detta om inte innehållet i indexet ska vara offentligt tillgängligt. Som en allmän regel rekommenderar vi att du skickar ditt `api-key` som ett begär ande huvud.  

## <a name="find-existing-keys"></a>Hitta befintliga nycklar

Du kan få åtkomst nycklar i portalen eller via [PowerShell](/powershell/module/az.search), [Azure CLI](/cli/azure/search)eller [REST API](/rest/api/searchmanagement/).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Visa en lista över [Sök tjänsterna](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) för din prenumeration.
1. Välj tjänsten och på sidan Översikt klickar du på **Inställningar**  > **nycklar** för att Visa administratörs-och frågeinställningar.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Portal sidan, Visa inställningar, avsnittet nycklar" border="false":::

## <a name="create-query-keys"></a>Skapa frågeinställningar

Frågeinställningar används för skrivskyddad åtkomst till dokument i ett index för åtgärder som riktar sig mot en dokument samling. Sök-, filter-och förslags frågor är alla åtgärder som tar en fråga. Alla skrivskyddade åtgärder som returnerar system data eller objekt definitioner, till exempel en index definition eller index status, kräver en administratörs nyckel.

Att begränsa åtkomsten och åtgärderna i klient program är viktigt för att skydda Sök resurserna på din tjänst. Använd alltid en nyckel i stället för en administratörs nyckel för en fråga som kommer från en klient app.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Visa en lista över [Sök tjänsterna](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)  för din prenumeration.
3. Välj tjänsten och på sidan Översikt klickar du på **Inställningar**  > **nycklar**.
4. Klicka på **Hantera frågeinställningar**.
5. Använd den frågenamn som redan har genererats för din tjänst eller skapa upp till 50 nya frågeinställningar. Standard nyckeln har inte namnet, men ytterligare frågeinställningar kan namnges för hanterbarhet.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Skapa eller Använd en frågegrupp" border="false":::

> [!Note]
> Ett kod exempel som visar användning av frågesträngar finns i [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Återskapa administratörs nycklar

Två administratörs nycklar skapas för varje tjänst så att du kan rotera en primär nyckel med hjälp av den sekundära nyckeln för affärs kontinuitet.

1. På sidan **Inställningar**  > **nycklar** kopierar du den sekundära nyckeln.
2. Uppdatera API-nyckel inställningarna för alla program för att använda den sekundära nyckeln.
3. Återskapa den primära nyckeln.
4. Uppdatera alla program så att de använder den nya primära nyckeln.

Om du oavsiktligt återskapar båda nycklarna samtidigt, kommer alla klient begär Anden som använder dessa nycklar att Miss förorsakade HTTP 403. Innehåll tas dock inte bort och du är inte utelåst permanent. 

Du kan fortfarande komma åt tjänsten via portalen eller via programmering. Hanterings funktionerna är avgörande genom ett prenumerations-ID som inte är en tjänst-API-nyckel och är därför fortfarande tillgängligt även om dina API-nycklar inte är det. 

När du har skapat nya nycklar via portalen eller hanterings lagret återställs åtkomsten till ditt innehåll (index, indexerare, data källor, synonym mappningar) när du har de nya nycklarna och anger dessa nycklar för förfrågningar.

## <a name="secure-api-keys"></a>Säkra API-nycklar

Genom [rollbaserade behörigheter](search-security-rbac.md)kan du ta bort eller läsa nycklarna, men du kan inte ersätta en nyckel med ett användardefinierat lösen ord eller använda Active Directory som primär autentiseringsmetod för att få åtkomst till Sök åtgärder. 

Nyckel säkerhet säkerställs genom att begränsa åtkomst via portal-eller Resource Manager-gränssnitten (PowerShell eller kommando rads gränssnittet). Som anges kan prenumerations administratörer Visa och återskapa alla API-nycklar. Som en försiktighets åtgärd granskar du roll tilldelningarna för att förstå vem som har åtkomst till administratörs nycklarna.

+ I instrument panelen för tjänsten klickar du på **åtkomst kontroll (IAM)** och sedan på fliken **roll tilldelningar** för att Visa roll tilldelningar för din tjänst.

Medlemmar i följande roller kan visa och återskapa nycklar: ägare, deltagare, [search service deltagare](../role-based-access-control/built-in-roles.md#search-service-contributor)

## <a name="see-also"></a>Se även

+ [Säkerhet i Azure Kognitiv sökning](search-security-overview.md)
+ [Rollbaserad åtkomst kontroll i Azure Kognitiv sökning](search-security-rbac.md)
+ [Hantera med hjälp av PowerShell](search-manage-powershell.md) 