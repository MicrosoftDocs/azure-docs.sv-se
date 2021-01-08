---
title: Azure Functions utdata från Azure Stream Analytics
description: I den här artikeln beskrivs Azure Functions som utdata för Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: e5ea7a1abbbd6ab4be32955179227fbd539cf641
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019626"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Azure Functions utdata från Azure Stream Analytics

Azure Functions är en server lös beräknings tjänst som du kan använda för att köra kod på begäran utan att behöva etablera eller hantera infrastruktur. Du kan implementera kod som utlöses av händelser som inträffar i Azure-eller partner tjänster. Den här möjligheten att Azure Functions att svara på utlösare gör det till ett naturligt resultat för Azure Stream Analytics. Med det här nätverkskortet kan användarna ansluta Stream Analytics till Azure Functions och köra ett skript eller ett kod avsnitt som svar på en rad olika händelser.

Azure Functions utdata från Stream Analytics är inte tillgängliga i regionerna Azure Kina 21Vianet och Azure Tyskland (T-Systems International). Anslutning till Azure Functions i ett virtuellt nätverk (VNet) från ett Stream Analytics jobb som körs i ett kluster med flera innehavare stöds inte heller.

Azure Stream Analytics anropar Azure Functions via HTTP-utlösare. Azure Functions-utmatnings kortet är tillgängligt med följande konfigurerbara egenskaper:

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Funktionsapp |Namnet på din Azure Functions-app. |
| Funktion |Namnet på funktionen i din Azure Functions-app. |
| Nyckel |Om du vill använda en Azure-funktion från en annan prenumeration kan du göra det genom att ange nyckeln för att få åtkomst till din funktion. |
| Max storlek på batch |En egenskap som låter dig ange den maximala storleken för varje utgående batch som skickas till din Azure-funktion. Den inkommande enheten är i byte. Som standard är det här värdet 262 144 byte (256 KB). |
| Max antal batchar  |En egenskap som gör att du kan ange det maximala antalet händelser i varje batch som skickas till Azure Functions. Standardvärdet är 100. |

Azure Stream Analytics förväntar sig HTTP-status 200 från Functions-appen för batchar som har bearbetats.

När Azure Stream Analytics tar emot en 413-undantag ("http-begäran är för stor") från en Azure-funktion minskar storleken på batcharna som den skickar till Azure Functions. I din Azure Function-kod använder du detta undantag för att kontrol lera att Azure Stream Analytics inte skickar överändrade batchar. Kontrol lera också att maximalt antal batchar och storleks värden som används i funktionen stämmer överens med de värden som anges i Stream Analytics portalen.

> [!NOTE]
> Under test anslutningen skickar Stream Analytics en tom batch som Azure Functions för att testa om anslutningen mellan de två fungerar. Se till att din functions-app hanterar tomma batch-begäranden för att kontrol lera att test anslutningen lyckas.

I en situation där det inte finns någon händelse vilplan i ett tids fönster genereras inga utdata. Därför anropas inte funktionen **computeResult** . Detta beteende är konsekvent med de inbyggda mängd funktionerna i window.

## <a name="partitioning"></a>Partitionering

Partitionsnyckel baseras på PARTITION BY-satsen i frågan. Antalet utgående skrivare följer inmatnings partitionering för [helt parallella frågor](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Batchstorlek för utdata

Standard grupp storleken är 262 144 byte (256 KB). Standard antalet händelser per batch är 100. Batchstorleken kan konfigureras och ökas eller minskas i Stream Analytics utmatnings alternativ.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Azure CLI](quick-create-azure-cli.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med en ARM-mall](quick-create-azure-resource-manager.md)
* [Snabb start: skapa ett Stream Analytics jobb med Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb i Visual Studio Code](quick-create-visual-studio-code.md)