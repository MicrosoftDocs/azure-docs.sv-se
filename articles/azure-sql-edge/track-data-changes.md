---
title: Spåra data ändringar i Azure SQL Edge
description: Lär dig mer om ändrings spårning och registrering av ändrings data i Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: dddaad3e171c757b353deb81ffcb77cfbe706340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98108270"
---
# <a name="track-data-changes-in-azure-sql-edge"></a>Spåra data ändringar i Azure SQL Edge

Azure SQL Edge stöder de två SQL Server funktionerna som spårar ändringar av data i en databas: [ändrings spårning](/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) och [insamling av ändrings data](/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Dessa funktioner gör det möjligt för program att fastställa ändringar av data ändringar (Insert-, Update-och Delete-åtgärder) som har gjorts till användar tabeller i en databas. Du kan aktivera registrering av ändrings data och ändrings spårning på samma databas. Inga särskilda överväganden krävs.

Möjligheten att fråga efter data som har ändrats i en databas är ett viktigt krav för att vissa program ska vara effektiva. För att fastställa data ändringar måste programutvecklare implementera en anpassad spårnings metod i sina program med hjälp av en kombination av utlösare, tidsstämpel-kolumner och ytterligare tabeller. Att skapa dessa program omfattar vanligt vis mycket arbete att implementera, leder till schema uppdateringar och har ofta en hög prestanda.

I händelse av en IoT-lösning, där du regelbundet behöver flytta data från gränsen till ett moln eller ett Data Center, kan ändrings spårning vara mycket användbart. Användare kan snabbt och effektivt fråga efter ändringar från den senaste synkroniseringen och överföra ändringarna till molnet eller data Center målet. Mer information finns i [fördelarna med att använda registrering av ändrings data eller spårning av ändringar](/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Dessa två funktioner är inte desamma. Mer information finns i [funktions skillnader mellan registrering av ändrings data och ändrings spårning](/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Registrering av ändringsdata

Information om hur den här funktionen fungerar finns i [om registrering av ändrings data](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Information om hur du aktiverar eller inaktiverar den här funktionen finns i [Aktivera och inaktivera registrering av ändrings data](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server).

Information om hur du administrerar och övervakar den här funktionen finns i [administrera och övervaka registrering av ändrings data](/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server).

Information om hur du frågar och arbetar med ändrade data finns i [arbeta med ändrings data](/sql/relational-databases/track-changes/work-with-change-data-sql-server).

> [!NOTE]
> Funktioner för att ändra data insamling som är beroende av CLR stöds inte på Azure SQL Edge.

## <a name="change-tracking"></a>Spårning av ändringar

Information om hur den här funktionen fungerar finns i [om ändrings spårning](/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Information om hur du aktiverar eller inaktiverar den här funktionen finns i [Aktivera och inaktivera ändrings spårning](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server).

Information om hur du administrerar, övervakar och hanterar den här funktionen finns i [administrera och övervaka ändrings spårning](/sql/relational-databases/track-changes/manage-change-tracking-sql-server).

Information om hur du frågar och arbetar med ändrade data finns i [arbeta med ändrings data](/sql/relational-databases/track-changes/work-with-change-tracking-sql-server).

## <a name="temporal-tables"></a>Temporala tabeller

Azure SQL Edge stöder även funktionen temporala tabeller i SQL Server. Den här funktionen (kallas även för *temporala system versions tabeller*) ger inbyggt stöd för att tillhandahålla information om data som lagras i tabellen vid en viss tidpunkt. Funktionen ger inte bara bara information om de data som är korrekta vid aktuell tidpunkt.

En temporal system versions tabell är en typ av användar tabell som är utformad för att bevara en fullständig historik över data ändringar och för att möjliggöra enkel tidpunkts analys. Den här typen av temporal tabell kallas en temporal system versions tabell, eftersom giltighets perioden för varje rad hanteras av systemet (det vill säga databas motorn).

Varje temporal tabell har två explicit definierade kolumner, var och en med en `datetime2` datatyp. Dessa kolumner kallas för *period* kolumner. Systemet använder bara dessa period kolumner för att registrera giltighets perioden för varje rad varje gång en rad ändras.

Förutom dessa period kolumner innehåller en temporal tabell även en referens till en annan tabell med ett speglat schema. Systemet använder den här tabellen för att automatiskt lagra den tidigare versionen av raden varje gången en rad i den temporala tabellen uppdateras eller tas bort. Den här extra tabellen kallas för *Historik* tabellen, medan huvud tabellen som lagrar aktuella (faktiska) rad versioner kallas den *aktuella* tabellen, eller helt enkelt som den temporala tabellen. När en temporal tabell skapas kan användarna ange en befintlig historik tabell (den måste vara kompatibel med schemat) eller låta systemet skapa standard historik tabellen.

Mer information finns i [temporala tabeller](/sql/relational-databases/tables/temporal-tables).

## <a name="next-steps"></a>Nästa steg

- [Data strömning i Azure SQL Edge ](stream-data.md)
- [Machine Learning och AI med ONNX i Azure SQL Edge ](onnx-overview.md)
- [Konfigurera replikering till Azure SQL Edge](configure-replication.md)
- [Säkerhetskopiera och återställa databaser i Azure SQL Edge](backup-restore.md)
