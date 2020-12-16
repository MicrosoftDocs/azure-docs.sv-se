---
title: Skapa, hantera och skydda administratörer och fråga API-nycklar
titleSuffix: Azure Cognitive Search
description: En API-nyckel styr åtkomsten till tjänstens slut punkt. Administratörs nycklar ger Skriv behörighet. Det går att skapa frågeinställningar för skrivskyddad åtkomst.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 29a314553584843ed6241b9311e9d72b42ec8705
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516408"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Skapa och hantera API-nycklar för en Azure Kognitiv sökning-tjänst

Alla begär anden till en Sök tjänst behöver en skrivskyddad tjänst `api-key` som har skapats specifikt för din tjänst. `api-key`Är den enda mekanismen för att autentisera åtkomsten till din Sök tjänst slut punkt och måste inkluderas på varje begäran. 

+ I [rest-lösningar](search-get-started-rest.md)anges vanligt vis API-nyckeln i ett begär ande huvud

+ I [.net-lösningar](search-howto-dotnet-sdk.md)anges en nyckel ofta som en konfigurations inställning och skickas sedan som en [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)

Nycklar skapas med Sök tjänsten under tjänst etableringen. Du kan visa och hämta nyckel värden i [Azure Portal](https://portal.azure.com).

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Portal sida, Hämta inställningar, nycklar avsnitt" border="false":::

## <a name="what-is-an-api-key"></a>Vad är en API-nyckel?

En API-nyckel är en sträng som består av slumpmässigt genererade siffror och bokstäver. Genom [rollbaserade behörigheter](search-security-rbac.md)kan du ta bort eller läsa nycklarna, men du kan inte ersätta en nyckel med ett användardefinierat lösen ord eller använda Active Directory som primär autentiseringsmetod för att få åtkomst till Sök åtgärder. 

Två typer av nycklar används för att få åtkomst till din Sök tjänst: administratör (Läs-och Skriv behörighet) och fråga (skrivskyddad).

|Nyckel|Description|Gränser|  
|---------|-----------------|------------|  
|Administratör|Ger fullständig behörighet till alla åtgärder, inklusive möjligheten att hantera tjänsten, skapa och ta bort index, indexerare och data källor.<br /><br /> Två administratörs nycklar, som kallas *primära* och *sekundära* nycklar i portalen, genereras när tjänsten skapas och kan återskapas individuellt på begäran. Med två nycklar kan du rulla över en nyckel när du använder den andra nyckeln för fortsatt åtkomst till tjänsten.<br /><br /> Administratörs nycklar anges bara i huvuden för HTTP-begäran. Du kan inte placera en Admin-API-nyckel i en URL.|Högst 2 per tjänst|  
|Söka i data|Ger skrivskyddad åtkomst till index och dokument, och distribueras vanligt vis till klient program som utfärdar Sök begär Anden.<br /><br /> Frågeinställningar skapas på begäran. Du kan skapa dem manuellt i portalen eller via programmering via [hanterings REST API](/rest/api/searchmanagement/).<br /><br /> Du kan ange frågeinställningar i ett HTTP-begärandehuvuden för Sök-, förslags-eller söknings åtgärder. Du kan också skicka en sessionsnyckel som en parameter på en URL. Beroende på hur ditt klient program formulerar begäran kan det vara lättare att skicka nyckeln som en frågeparameter:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 per tjänst|  

 Visuellt är det ingen skillnad mellan en administratörs nyckel eller en fråga. Båda nycklarna är strängar som består av 32 slumpmässigt genererade alpha-numeriska tecken. Om du förlorar reda på vilken typ av nyckel som anges i ditt program kan du [kontrol lera nyckel värden i portalen](https://portal.azure.com) eller använda [REST API](/rest/api/searchmanagement/) för att returnera värdet och nyckel typen.  

> [!NOTE]  
>  Det anses vara en låg säkerhets praxis för att skicka känsliga data, till exempel en `api-key` i fråge-URI. Av den anledningen accepterar Azure Kognitiv sökning bara en sessionsnyckel som en `api-key` i frågesträngen och du bör undvika att göra detta om inte innehållet i indexet ska vara offentligt tillgängligt. Som en allmän regel rekommenderar vi att du skickar ditt `api-key` som ett begär ande huvud.  

## <a name="find-existing-keys"></a>Hitta befintliga nycklar

Du kan hämta åtkomst nycklar i portalen eller via [hanterings REST API](/rest/api/searchmanagement/). Mer information finns i [Hantera administratör och fråga API-nycklar](search-security-api-keys.md).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Visa en lista över [Sök tjänsterna](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)  för din prenumeration.
3. Välj tjänsten och på sidan Översikt klickar du på **Inställningar**  > **nycklar** för att Visa administratörs-och frågeinställningar.

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
> Ett kod exempel som visar användning av nyckel användningen finns i [fråga ett Azure kognitiv sökning-index i C#](./search-get-started-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Återskapa administratörs nycklar

Två administratörs nycklar skapas för varje tjänst så att du kan rotera en primär nyckel med hjälp av den sekundära nyckeln för affärs kontinuitet.

1. På sidan **Inställningar**  > **nycklar** kopierar du den sekundära nyckeln.
2. Uppdatera API-nyckel inställningarna för alla program för att använda den sekundära nyckeln.
3. Återskapa den primära nyckeln.
4. Uppdatera alla program så att de använder den nya primära nyckeln.

Om du oavsiktligt återskapar båda nycklarna samtidigt, kommer alla klient begär Anden som använder dessa nycklar att Miss förorsakade HTTP 403. Innehåll tas dock inte bort och du är inte utelåst permanent. 

Du kan fortfarande komma åt tjänsten via portalen eller hanterings lagret ([REST API](/rest/api/searchmanagement/), [PowerShell](./search-manage-powershell.md)eller Azure Resource Manager). Hanterings funktionerna är avgörande genom ett prenumerations-ID som inte är en tjänst-API-nyckel och är därför fortfarande tillgängligt även om dina API-nycklar inte är det. 

När du har skapat nya nycklar via portalen eller hanterings lagret återställs åtkomsten till ditt innehåll (index, indexerare, data källor, synonym mappningar) när du har de nya nycklarna och anger dessa nycklar för förfrågningar.

## <a name="secure-api-keys"></a>Secure API – nycklar

Nyckel säkerhet säkerställs genom att begränsa åtkomst via portal-eller Resource Manager-gränssnitten (PowerShell eller kommando rads gränssnittet). Som anges kan prenumerations administratörer Visa och återskapa alla API-nycklar. Som en försiktighets åtgärd granskar du roll tilldelningarna för att förstå vem som har åtkomst till administratörs nycklarna.

+ I instrument panelen för tjänsten klickar du på **åtkomst kontroll (IAM)** och sedan på fliken **roll tilldelningar** för att Visa roll tilldelningar för din tjänst.

Medlemmar i följande roller kan visa och återskapa nycklar: ägare, deltagare, [search service deltagare](../role-based-access-control/built-in-roles.md#search-service-contributor)

> [!Note]
> För identitets-baserad åtkomst över Sök resultat kan du skapa säkerhets filter för att trimma resultat efter identitet, ta bort dokument som beställaren inte ska ha åtkomst till. Mer information finns i [säkerhets filter](search-security-trimming-for-azure-search.md) och [säkra med Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Se även

+ [Rollbaserad åtkomst kontroll i Azure Kognitiv sökning](search-security-rbac.md)
+ [Hantera med hjälp av PowerShell](search-manage-powershell.md) 
+ [Artikel om prestanda och optimering](search-performance-optimization.md)