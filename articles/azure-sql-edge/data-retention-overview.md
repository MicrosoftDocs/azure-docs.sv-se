---
title: Översikt över data bevarande princip – Azure SQL Edge
description: Lär dig mer om data bevarande principen i Azure SQL Edge
keywords: SQL Edge, data kvarhållning
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: bb059a946c03f41e5b65944eec67070f84ee6b08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90976335"
---
# <a name="data-retention-overview"></a>Översikt över datakvarhållning

Insamling och lagring av data från anslutna IoT-enheter är viktigt för att driva och få drifts-och affärs insikter. Men med tanke på mängden data som kommer från dessa enheter, blir det viktigt för organisationer att noggrant planera mängden data som de vill behålla och med vilken kornig het. Även om det är önskvärt att alla data behålls är det inte alltid praktiskt. Dessutom begränsas mängden data som kan behållas av mängden lagrings utrymme som är tillgängligt på IoT-eller Edge-enheterna. 

I Azure SQL Edge Database-administratörer kan definiera data bevarande princip för en SQL Edge-databas och dess underliggande tabeller. När data lagrings principen har definierats körs en bakgrunds system aktivitet för att rensa eventuella inaktuella (gamla) data från användar tabellerna. 

> [!Note]
> Data som rensats från tabellen går inte att återskapa. Det enda möjliga sättet att återställa rensade data är att återställa databasen från en äldre säkerhets kopia.

Snabbstarter:

- [Aktivera och inaktivera data lagrings principer](data-retention-enable-disable.md)
- [Hantera historiska data med bevarande princip](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>Så här fungerar data kvarhållning

Du kan använda DDL-instruktioner för att konfigurera datakvarhållning. Mer information finns i [Aktivera och inaktivera principer för data lagring](data-retention-enable-disable.md). För automatisk borttagning av de föråldrade posterna måste datakvarhållning först aktive ras för både databasen och de tabeller som du vill rensa i databasen. 

När data kvarhållning har kon figurer ATS för en tabell körs en bakgrunds aktivitet för att identifiera de föråldrade posterna i en tabell och ta bort dessa poster. Om den automatiska rensningen av aktiviteterna inte körs eller inte kan fortsätta med borttagningarna, kan en manuell rensnings åtgärd utföras på dessa tabeller. Mer information om automatiska och manuella rensningar finns i [Automatisk och manuell rensning](data-retention-cleanup.md).

## <a name="limitations-and-restrictions"></a>Begränsningar och begränsningar

- Datakvarhållning, om aktive rad, inaktive ras automatiskt när databasen återställs från en fullständig säkerhets kopia eller återkopplas. 
- Det går inte att aktivera datakvarhållning för en temporal historik tabell
- Datakvarhållning filter colomn kan inte ändras. Om du vill ändra kolumnen inaktiverar du datakvarhållning i tabellen.  

## <a name="next-steps"></a>Nästa steg

- [Machine Learning och artificiell intelligens med ONNX i SQL Edge](onnx-overview.md).
- [Skapa en IoT-lösning från slut punkt till slut punkt med SQL Edge med hjälp av IoT Edge](tutorial-deploy-azure-resources.md).
- [Data strömning i Azure SQL Edge](stream-data.md)
