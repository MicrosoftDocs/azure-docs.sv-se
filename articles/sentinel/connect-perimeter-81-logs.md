---
title: Ansluta perimeter 81-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter perimeter 81-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: e70bfd12b018e785e7b3b91e098c99fea1101c04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093105"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>Anslut dina perimeter 81-aktivitets loggar till Azure Sentinel

> [!IMPORTANT]
> Data kopplingen för perimeter 81 i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här artikeln förklaras hur du ansluter enheten för [Perimeter 81-aktivitets loggar](https://www.perimeter81.com/) till Azure Sentinel. Med anslutnings loggarna för perimeter 81 kan du enkelt ta med dina perimeter 81-data till Azure Sentinel, så att du kan visa dem i arbets böcker, använda dem för att skapa anpassade aviseringar och lägga till dem för att förbättra undersökningen.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha Läs behörighet till delade nycklar för arbets ytan.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>Konfigurera och koppla perimeter 81-aktivitets loggar

Aktivitets loggarna för perimeter 81 kan integrera och exportera loggar direkt till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** på navigerings menyn.

1. Välj **Perimeter 81 aktivitets loggar** från galleriet och klicka sedan på knappen **Öppna kopplings sida** .

1. På sidan perimeter 81 aktivitets loggar Connector kopierar du **arbets ytans ID** och **primär nyckel** och klistrar in dem i perimeter 81 [enligt anvisningarna här](https://support.perimeter81.com/hc/en-us/articles/360012680780).

1. När du har slutfört instruktionerna visas de anslutna data typerna på sidan Azure Sentinel Connector.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under **CustomLogs**  -  **Perimeter81_CL**.

Det kan ta upp till 20 minuter innan dina loggar börjar visas.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter aktivitets loggar för perimeter 81 till Azure Sentinel. Om du vill dra full nytta av de funktioner som är inbyggda i denna data anslutning klickar du på fliken **Nästa steg** på data anslutnings sidan. Där hittar du en färdig arbets bok och några exempel frågor så att du kan komma igång med att hitta användbar information.

Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
