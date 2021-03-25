---
title: Hantera åtkomst till Azure Sentinel-data efter resurs | Microsoft Docs
description: Den här artikeln förklarar hur du kan hantera åtkomst till Azure Sentinel-data av de resurser som en användare kan komma åt. Genom att hantera åtkomst per resurs kan du ge endast åtkomst till vissa data, utan hela Azure Sentinel-upplevelsen. Den här metoden kallas även för resurs kontexten RBAC.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: fc1246d079760fd86513840aebbffa34d192f8ed
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044183"
---
# <a name="manage-access-to-azure-sentinel-data-by-resource"></a>Hantera åtkomst till Azure Sentinel-data efter resurs

Vanligt vis har användare som har åtkomst till en Azure Sentinel-arbetsyta också till gång till alla data i arbets ytan, inklusive säkerhets innehåll. Administratörer kan använda [Azure-roller](roles.md) för att konfigurera åtkomst till särskilda funktioner i Azure Sentinel, beroende på åtkomst kraven i teamet.

Du kan dock ha vissa användare som bara behöver åtkomst till vissa data i din Azure Sentinel-arbetsyta, men inte ha åtkomst till hela Azure Sentinel-miljön. Du kanske till exempel vill tillhandahålla ett icke-SOC-team med åtkomst till Windows-händelseloggen för de servrar som de äger.

I sådana fall rekommenderar vi att du konfigurerar rollbaserad åtkomst kontroll (RBAC) baserat på de resurser som tillåts för användarna, i stället för att ge dem åtkomst till Azure Sentinel-arbetsytan eller vissa Azure Sentinel-funktioner. Den här metoden kallas även för att konfigurera **resurs kontexten RBAC**.

När användarna har åtkomst till Azure Sentinel-data via de resurser som de har åtkomst till i stället för Azure Sentinel-arbetsytan, kan de Visa loggar och arbets böcker med följande metoder:

- **Via själva resursen**, till exempel en virtuell Azure-dator. Använd den här metoden om du bara vill visa loggar och arbets böcker för en speciell resurs.

- **Via Azure Monitor**. Använd den här metoden när du vill skapa frågor som sträcker sig över flera resurser och/eller resurs grupper. När du navigerar till loggar och arbets böcker i Azure Monitor definierar du omfattningen för en eller flera enskilda resurs grupper eller resurser.

Aktivera resurs kontexten RBAC i Azure Monitor. Mer information finns i [Hantera åtkomst till logg data och arbets ytor i Azure Monitor](../azure-monitor/logs/manage-access.md).

> [!NOTE]
> Om dina data inte är en Azure-resurs, t. ex. syslog, CEF eller AAD-data eller data som samlas in av en anpassad insamlare, måste du manuellt konfigurera resurs-ID som används för att identifiera data och aktivera åtkomst.
>
> Mer information finns i [explicit konfigurera resurs kontexten RBAC](#explicitly-configure-resource-context-rbac).
>
## <a name="scenarios-for-resource-context-rbac"></a>Scenarier för resurs kontexten RBAC

I följande tabell beskrivs scenarier där RBAC-kontexten för resurs kontext är mest användbar. Observera skillnaderna i åtkomst kraven mellan SOC team och icke-SOC team.

| Typ av krav |SOC-team  |Icke-SOC team  |
|---------|---------|---------|
|**Behörigheter**     | Hela arbets ytan        |   Endast vissa resurser      |
|**Data åtkomst**     |  Alla data i arbets ytan       | Endast data för resurser som teamet har behörighet att komma åt        |
|**Erfarenhet**     |  Den fullständiga Azure Sentinel-upplevelsen, eventuellt begränsad av de [funktionella behörigheter](roles.md) som tilldelats användaren       |  Logga endast frågor och arbets böcker       |
|     |         |         |

Om ditt team har liknande åtkomst krav för det icke-SOC-team som beskrivs i tabellen ovan, kan resurs kontexten RBAC vara en lämplig lösning för din organisation.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>Alternativa metoder för att implementera resurs kontexten RBAC

Beroende på vilka behörigheter som krävs i din organisation kanske det inte finns en fullständig lösning med hjälp av resurs kontexten RBAC.

I följande lista beskrivs scenarier där andra lösningar för data åtkomst kan passa dina krav bättre:

|Scenario  |Lösning  |
|---------|---------|
|**Ett dotter bolag har ett SOC-team som kräver en fullständig Azure Sentinel-upplevelse**.     |  I det här fallet använder du en arkitektur med flera arbets ytor för att avgränsa dina data behörigheter. <br><br>Mer information finns i: <br>- [Utöka Azure Sentinel över arbets ytor och innehavare](extend-sentinel-across-workspaces-tenants.md)<br>    - [Arbeta med incidenter i flera arbets ytor samtidigt](multiple-workspace-view.md)          |
|**Du vill ge åtkomst till en speciell typ av händelse**.     |  Ange till exempel en Windows-administratör med åtkomst till Windows säkerhets händelser i alla system. <br><br>I sådana fall använder du [RBAC på tabell nivå](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) för att definiera behörigheter för varje tabell.       |
| **Begränsa åtkomsten till en mer detaljerad nivå, antingen baserat på resursen, eller endast en delmängd av fälten i en händelse**   |   Du kanske till exempel vill begränsa åtkomsten till Office 365-loggar baserat på en användares dotter bolag. <br><br>I det här fallet ger du åtkomst till data med hjälp av inbyggd integrering med [Power BI instrument paneler och rapporter](../azure-monitor/visualize/powerbi.md).      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>Konfigurera manuellt resurs kontexten RBAC

Använd följande steg om du vill konfigurera resurs kontexten RBAC, men dina data är inte en Azure-resurs.

Till exempel innehåller data i Azure Sentinel-arbetsytan som inte är Azure-resurser syslog, CEF eller AAD-data eller data som samlas in av en anpassad insamlare.

**För att explicit konfigurera resurs kontexten RBAC**:

1. Kontrol lera att du har [aktiverat resurs kontexten RBAC](../azure-monitor/logs/manage-access.md) i Azure Monitor. 

1. [Skapa en resurs grupp](../azure-resource-manager/management/manage-resource-groups-portal.md) för varje grupp användare som behöver komma åt dina resurser utan hela Azure Sentinel-miljön.

    Tilldela [logg läsar behörigheter](../azure-monitor/logs/manage-access.md#resource-permissions) för varje grupp medlemmar.

1. Tilldela resurser till resurs grupps grupper som du har skapat och tagga händelser med relevanta resurs-ID: n.

    När Azure-resurser skickar data till Azure Sentinel märks logg posterna automatiskt med resurs-ID för data källan.

    > [!TIP]
    > Vi rekommenderar att du grupperar de resurser som du beviljar åtkomst för under en speciell resurs grupp som skapats för ändamålet.
    >
    > Om du inte kan göra det kontrollerar du att ditt team har logg läsar behörigheter direkt till de resurser som du vill att de ska ha åtkomst till.
    >

    Mer information om resurs-ID: n finns i:

    - [Resurs-ID: n med logg vidarebefordran](#resource-ids-with-log-forwarding)
    - [Resurs-ID med Logstash-samling](#resource-ids-with-logstash-collection)
    - [Resurs-ID: n med Log Analytics API-samlingen](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>Resurs-ID: n med logg vidarebefordran

När händelser samlas in med hjälp av [common Event format (CEF)](connect-common-event-format.md) eller [syslog](connect-syslog.md)används logg vidarebefordran för att samla in händelser från flera käll system.

Till exempel när en virtuell CEF-eller syslog-vidarebefordrande virtuell dator lyssnar efter källor som skickar Syslog-händelser och vidarebefordrar dem till Azure Sentinel, tilldelas logg vidarebefordran VM-resurs-ID till alla händelser som de vidarebefordrar.

Om du har flera team ser du till att du har separata virtuella datorer för logg vidarebefordran som bearbetar händelserna för varje separat team.

Genom att skilja dina virtuella datorer till exempel ser du till att Syslog-händelser som tillhör Team A samlas in med hjälp av den insamlade VM A.

> [!TIP]
> - När du använder en lokal virtuell dator eller en annan virtuell dator i molnet, till exempel AWS, som logg vidarebefordrare, kontrollerar du att den har ett resurs-ID genom att implementera [Azure-bågen](../azure-arc/servers/overview.md).
> - Om du vill skala din VM-miljö för logg vidarebefordran kan du skapa en [skalnings uppsättning för virtuella datorer](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) för att samla in dina CEF-och Sylog


### <a name="resource-ids-with-logstash-collection"></a>Resurs-ID med Logstash-samling

Om du samlar in dina data med hjälp av [plugin-programmet](connect-logstash.md)för Azure Sentinel Logstash använder du fältet **azure_resource_id** för att konfigurera din anpassade insamlare att inkludera resurs-ID: t i dina utdata.

Om du använder resurs kontexten RBAC och vill att de händelser som samlas in av API ska vara tillgängliga för vissa användare använder du resurs-ID för den resurs grupp som du [skapade för användarna](#explicitly-configure-resource-context-rbac).

Följande kod visar till exempel en exempel-Logstash konfigurations fil:

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> Du kanske vill lägga till flera `output` avsnitt för att särskilja taggarna som tillämpas på olika händelser.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>Resurs-ID: n med Log Analytics API-samlingen

När du samlar in med hjälp av [API: et för Log Analytics data insamling](../azure-monitor/logs/data-collector-api.md)kan du tilldela händelser med ett resurs-ID med hjälp av http [*x-MS-AzureResourceId*](../azure-monitor/logs/data-collector-api.md#request-headers) begär ande huvudet.

Om du använder resurs kontexten RBAC och vill att de händelser som samlas in av API ska vara tillgängliga för vissa användare använder du resurs-ID för den resurs grupp som du [skapade för användarna](#explicitly-configure-resource-context-rbac).


## <a name="next-steps"></a>Nästa steg

Mer information finns i [behörigheter i Azure Sentinel](roles.md).