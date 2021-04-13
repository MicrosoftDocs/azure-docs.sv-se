---
title: Migrera till Anslutningsövervakare från Anslutningsövervakare
titleSuffix: Azure Network Watcher
description: Lär dig hur du migrerar till Anslutningsövervakare från Anslutningsövervakare.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: fc5bcc7f0cd11160b33bb6501526fce9f29d710b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366393"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>Migrera till Anslutningsövervakare från Anslutningsövervakare (klassisk)

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya anslutningsövervakare i Anslutningsövervakare (klassisk) men du kan fortsätta att använda befintliga anslutningsövervakare som skapats före den 1 juli 2021. För att minimera tjänstavbrott för dina aktuella arbetsbelastningar migrerar du [från Anslutningsövervakare (klassisk)](migrate-to-connection-monitor-from-connection-monitor-classic.md)  till den nya Anslutningsövervakare i Azure Network Watcher före den 29 februari 2024.

Du kan migrera befintliga anslutningsövervakare till nya, förbättrade Anslutningsövervakare med bara några få klick och utan driftstopp. Mer information om fördelarna finns i [Anslutningsövervakare](./connection-monitor-overview.md).

## <a name="key-points-to-note"></a>Viktiga punkter att notera

Migreringen hjälper till att skapa följande resultat:

* Agenter och brandväggsinställningar fungerar som de är. Inga ändringar krävs. 
* Befintliga anslutningsövervakare mappas till Anslutningsövervakare > Test Group > Test-format. Genom att **välja Redigera** kan du visa och ändra egenskaperna för den nya Anslutningsövervakare, ladda ned en mall för att göra ändringar i Anslutningsövervakare och skicka den via Azure Resource Manager. 
* Virtuella Azure-datorer med Network Watcher-tillägget skickar data till både arbetsytan och måtten. Anslutningsövervakare gör data tillgängliga via de nya måtten (ChecksFailedPercent och RoundTripTimeMs) i stället för de gamla måtten (ProbesFailedPercent och AverageRoundtripMs). De gamla måtten migreras till nya mått som ProbesFailedPercent -> ChecksFailedPercent och AverageRoundtripMs -> RoundTripTimeMs.
* Dataövervakning:
   * **Aviseringar:** Migreras automatiskt till de nya måtten.
   * **Instrumentpaneler och integreringar:** Kräv manuell redigering av måttuppsättningen. 
    
## <a name="prerequisites"></a>Förutsättningar

1. Om du använder en anpassad arbetsyta ska du kontrollera Network Watcher är aktiverat i din prenumeration och i regionen för Log Analytics-arbetsytan. Om inte visas felmeddelandet "Innan du försöker migrera aktiverar du Network Watcher-tillägget i valprenumerationen och platsen för den la-arbetsyta som valts".
1. Om virtuella datorer som används som källor i anslutningsövervakaren (klassisk) inte längre har Network Watcher-tillägget aktiverat visas ett felmeddelande om att anslutningsövervakare som har följande tester inte kan importeras eftersom en eller flera virtuella Azure-datorer inte har network watcher-tillägget installerat. Installera network watcher-tillägget och klicka på Uppdatera för att importera dem."



## <a name="migrate-the-connection-monitors"></a>Migrera anslutningsövervakarna

1. Om du vill migrera de äldre anslutningsövervakarna till den nyare **väljer Anslutningsövervakare** och sedan **Migrera anslutningsövervakare.**

    ![Skärmbild som visar migreringen av anslutningsövervakare till Anslutningsövervakare.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Välj din prenumeration och anslutningsövervakare som du vill migrera och välj sedan **Migrera valt.** 

Med bara några få klickningar har du migrerat de befintliga anslutningsövervakarna till Anslutningsövervakare. När du har migrerat från CM (klassisk) till CM kan du inte se övervakaren under CM (klassisk)

Nu kan du anpassa Anslutningsövervakare, ändra standardarbetsytan, ladda ned mallar och kontrollera migreringsstatusen. 

När migreringen har påbörjas sker följande ändringar: 
* Den Azure Resource Manager resursen ändras till den nyare anslutningsövervakaren.
    * Namn, region och prenumeration för anslutningsövervakaren förblir oförändrade. Resurs-ID:t påverkas inte.
    * Om anslutningsövervakaren inte är anpassad skapas en Log Analytics-standardarbetsyta i prenumerationen och i regionen för anslutningsövervakaren. Det är på den här arbetsytan som övervakningsdata lagras. Testresultatdata lagras också i måtten.
    * Varje test migreras till en testgrupp med namnet *defaultTestGroup.*
    * Käll- och målslutpunkter skapas och används i den nya testgruppen. Standardnamnen är *defaultSourceEndpoint* och *defaultDestinationEndpoint*.
    * Målporten och avsökningsintervallet flyttas till en testkonfiguration som kallas *defaultTestConfiguration*. Protokollet anges baserat på portvärdena. Tröskelvärden för lyckade och andra valfria egenskaper lämnas tomma.
* Måttaviseringar migreras till Anslutningsövervakare måttaviseringar. Måtten är olika, därav ändringen. Mer information finns i [Övervakning av nätverksanslutning med Anslutningsövervakare](./connection-monitor-overview.md#metrics-in-azure-monitor).
* De migrerade anslutningsövervakarna visas inte längre som den äldre anslutningsövervakarlösningen. De är nu endast tillgängliga för användning i Anslutningsövervakare.
* Alla externa integreringar, till exempel instrumentpaneler i Power BI och Grafana, och integreringar med SIEM-system (Säkerhetsinformation och händelsehantering) måste migreras manuellt. Det här är det enda manuella steg som du behöver utföra för att migrera konfigurationen.

## <a name="next-steps"></a>Nästa steg

Mer information om Anslutningsövervakare finns i:
* [Migrera från Övervakare av nätverksprestanda till Anslutningsövervakare](./migrate-to-connection-monitor-from-network-performance-monitor.md)
* [Skapa Anslutningsövervakare med hjälp av Azure Portal](./connection-monitor-create-using-portal.md)
