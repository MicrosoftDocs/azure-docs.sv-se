---
title: Tjänst administration i portalen
titleSuffix: Azure Cognitive Search
description: Hantera en Azure Kognitiv sökning-tjänst, en värd för moln Sök tjänst på Microsoft Azure, med hjälp av Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 98e0137c8e48696737cd5d8d1fd4d3de925b9f7f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579800"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Tjänst administration för Azure Kognitiv sökning i Azure Portal

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST-API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Kognitiv sökning är en fullständigt hanterad, molnbaserad Sök tjänst som används för att skapa en omfattande Sök upplevelse i anpassade appar. Den här artikeln beskriver de administrations uppgifter som du kan utföra i [Azure Portal](https://portal.azure.com) för en Sök tjänst som du redan har skapat. Med portalen kan du utföra alla [hanterings uppgifter](#management-tasks) som är relaterade till tjänsten, samt innehålls hantering och utforskning. På så sätt ger portalen breda spektrum till gång till alla aspekter av Sök tjänst åtgärder.

Varje Sök tjänst hanteras som en fristående resurs. Följande bild visar Portal sidorna för en enskild kostnads fri Sök tjänst med namnet "demo-search-SVC". Även om du kanske är van vid att använda Azure PowerShell eller Azure CLI för Service Management är det bra att bekanta dig med de verktyg och funktioner som Portal sidorna tillhandahåller. Vissa uppgifter är bara enklare och snabbare att utföra i portalen än genom kod. 

## <a name="overview-home-page"></a>Översikt (start sida)

Sidan översikt är start sidan för varje tjänst. Nedan visas områdena på skärmen i röda rutor som visar aktiviteter, verktyg och paneler som du kan använda ofta, särskilt om du är nybörjare på tjänsten.

:::image type="content" source="media/search-manage/search-portal-overview-page.png" alt-text="Portal sidor för en Sök tjänst" border="true":::

| Område | Beskrivning |
|------|-------------|
| 1  | I avsnittet **Essentials** visas tjänst egenskaper, inklusive slut punkten som används när du konfigurerar anslutningar. Det visar också en snabb nivå, replikering och antal partitioner. |
| 2 | Längst upp på sidan finns en serie kommandon för att anropa interaktiva verktyg eller hantera tjänsten. Både [Importera data](search-get-started-portal.md) och [Sök Utforskaren](search-explorer.md) används ofta för prototyper och utforskning. |
| 3 | Under avsnittet **Essentials** visas en serie med flikar med flikar för snabb åtkomst till användnings statistik, service hälsomått och åtkomst till alla befintliga index, indexerare, data källor och färdighetsuppsättningar på din tjänst. Om du väljer ett index eller ett annat objekt blir ytterligare sidor tillgängliga för att visa objekt komposition, inställningar och status (om tillämpligt). |
| 4 | Till vänster kan du komma åt länkar som öppnar ytterligare sidor för att få åtkomst till API-nycklar som används på anslutningar, konfigurera tjänsten, Konfigurera säkerhet, övervaka åtgärder, automatisera uppgifter och få support. |

### <a name="read-only-service-properties"></a>Skrivskyddade tjänst egenskaper

Flera aspekter av en Sök tjänst bestäms när tjänsten är etablerad och kan inte ändras:

* Tjänst namn (du kan inte byta namn på en Sök tjänst)
* Tjänst plats (du kan inte enkelt flytta en intakt Sök tjänst till en annan region. Även om det finns en mall, är det en manuell process att flytta innehållet
* Tjänst nivå (du kan inte växla från S1 till S2, till exempel utan att skapa en ny tjänst)

## <a name="management-tasks"></a>Hanteringsuppgifter

Tjänst administrationen är lätt att använda och definieras ofta av de uppgifter som du kan utföra i förhållande till själva tjänsten:

* [Justera kapaciteten](search-capacity-planning.md) genom att lägga till eller ta bort repliker och partitioner
* [Hantera API-nycklar](search-security-api-keys.md) som används för administratörs-och fråge åtgärder
* [Kontrol lera åtkomst till administratörs åtgärder](search-security-rbac.md) via rollbaserad säkerhet
* [Konfigurera IP-brandväggens regler](service-configure-firewall.md) för att begränsa åtkomst via IP-adress
* [Konfigurera en privat slut punkt](service-create-private-endpoint.md) med hjälp av en privat Azure-länk och ett privat virtuellt nätverk
* [Övervaka tjänstens hälsa](search-monitor-usage.md): lagring, fråga volymer och svars tid

Du kan också räkna upp alla objekt som har skapats i tjänsten: index, indexerare, data källor, färdighetsuppsättningar och så vidare. På portalens översikts sida visas allt innehåll som finns på din tjänst. De flesta åtgärder på en Sök tjänst är innehålls relaterade.

Samma hanterings uppgifter som utförs i portalen kan också hanteras via programmering via API: er för [hanterings rester](/rest/api/searchmanagement/), [AZ. search PowerShell-modul](search-manage-powershell.md), [AZ search Azure CLI-modul](search-manage-azure-cli.md)och Azure SDK: er för .net, python, Java och Java Script. Administrativa uppgifter visas helt i portalen och i alla programmerings gränssnitt. Det finns ingen speciell administrativ uppgift som bara är tillgänglig i en enda spärr.

Kognitiv sökning utnyttjar andra Azure-tjänster för djupare övervakning och hantering. Av sig själv är de enda data som lagras i Sök tjänsten objekt innehåll (index, indexerare och data käll definitioner och andra objekt). Mått som rapporteras ut till Portal sidor hämtas från interna loggar på en rullande 30-dagars cykel. För kvarhållning av användar kontrollerad logg och ytterligare händelser behöver du [Azure Monitor](../azure-monitor/index.yml). Mer information om hur du konfigurerar diagnostisk loggning för en Sök tjänst finns i [samla in och analysera loggdata](search-monitor-logs.md).

## <a name="administrator-permissions"></a>Administratörs behörighet

När du öppnar översikts sidan för Search-tjänsten avgör de behörigheter som tilldelats ditt konto vilka sidor som är tillgängliga för dig. Sidan översikt i början av artikeln visar de Portal sidor som en administratör eller deltagare kommer att se.

I Azure-resurser beviljas administrativa rättigheter genom roll tilldelningar. I samband med Azure-Kognitiv sökning avgör [roll tilldelningarna](search-security-rbac.md) vem som kan allokera repliker och partitioner eller hantera API-nycklar, oavsett om de använder portalen, [POWERSHELL](search-manage-powershell.md), [Azure CLI](search-manage-azure-cli.md)eller [hantering REST-API: er](/rest/api/searchmanagement/search-howto-management-rest-api):

> [!TIP]
> Etablering eller inaktive ring av tjänsten kan utföras av en administratör för Azure-prenumeration eller tillsammans med en administratör. Med hjälp av Azure-omfattande mekanismer kan du låsa en prenumeration eller resurs för att förhindra oavsiktlig eller obehörig borttagning av Sök tjänsten av användare med administratörs behörighet. Mer information finns i [låsa resurser för att förhindra oväntad borttagning](../azure-resource-manager/management/lock-resources.md).

## <a name="next-steps"></a>Nästa steg

* Granska [övervaknings funktioner](search-monitor-usage.md) som är tillgängliga i portalen
* Automatisera med [PowerShell](search-manage-powershell.md) eller [Azure CLI](search-manage-azure-cli.md)
* Granska [säkerhetsfunktioner](search-security-overview.md) för att skydda innehåll och åtgärder
* Aktivera [diagnostisk loggning](search-monitor-logs.md) för att övervaka arbets belastningar för frågor och indexering