---
title: Visualisera nätverks trafik mönster med verktyg för öppen källkod
titleSuffix: Azure Network Watcher
description: Den här sidan beskriver hur du använder Network Watcher paket avbildning med Capanalysis för att visualisera trafik mönster till och från dina virtuella datorer.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 0503f6910186b42bf381c662c7942e37c28bfdf0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709520"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualisera nätverks trafik mönster till och från dina virtuella datorer med hjälp av verktyg med öppen källkod

Paket fångster innehåller nätverks data som gör det möjligt att utföra nätverks data utredning och djup paket inspektion. Det finns många öppna käll verktyg som du kan använda för att analysera paket fångster för att få insikter om ditt nätverk. Ett sådant verktyg är CapAnalysis, en paket insamlings verktyg med öppen källkod. Visualisering av paket insamlings data är ett värdefullt sätt att snabbt få insikter om mönster och avvikelser i nätverket. Visualiseringar ger också ett sätt att dela sådana insikter på ett enkelt sätt som kan förbrukas.

Azures Network Watcher ger dig möjlighet att samla in data genom att låta dig utföra paket avbildningar i nätverket. Den här artikeln innehåller en genom gång av hur du kan visualisera och få insikter från paket fångster med hjälp av CapAnalysis med Network Watcher.

## <a name="scenario"></a>Scenario

Du har ett enkelt webb program som distribueras på en virtuell dator i Azure vill använda verktyg med öppen källkod för att visualisera nätverks trafiken för att snabbt identifiera flödes mönster och eventuella avvikelser. Med Network Watcher kan du hämta en paket avbildning av din nätverks miljö och lagra den direkt på ditt lagrings konto. CapAnalysis kan sedan mata in paket avbildningen direkt från Storage-blobben och visualisera innehållet.

![scenario][1]

## <a name="steps"></a>Steg

### <a name="install-capanalysis"></a>Installera CapAnalysis

Om du vill installera CapAnalysis på en virtuell dator kan du läsa de officiella anvisningarna här https://www.capanalysis.net/ca/how-to-install-capanalysis .
För att få åtkomst till CapAnalysis måste du öppna port 9877 på den virtuella datorn genom att lägga till en ny inkommande säkerhets regel. Mer information om hur du skapar regler i nätverks säkerhets grupper finns i [skapa regler i en befintlig NSG](../virtual-network/manage-network-security-group.md#create-a-security-rule). När regeln har lagts till ska du kunna komma åt CapAnalysis från `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Använd Azure-Network Watcher för att starta en paket insamlings session

Med Network Watcher kan du avbilda paket för att spåra trafik in och ut från en virtuell dator. Du kan läsa anvisningarna i [Hantera paket fångster med Network Watcher](network-watcher-packet-capture-manage-portal.md) för att starta en paket insamlings session. En paket fångst kan lagras i en lagrings-blob som kan nås av CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Ladda upp en paket fångst till CapAnalysis
Du kan direkt ladda upp en paket avbildning som tas av Network Watcher med hjälp av fliken "Importera från URL" och ange en länk till lagrings-bloben där paket fångsten lagras.

När du tillhandahåller en länk till CapAnalysis, se till att lägga till en SAS-token till lagrings-BLOB-URL: en.  Det gör du genom att navigera till signaturen för delad åtkomst från lagrings kontot, ange tillåtna behörigheter och trycka på knappen generera SAS för att skapa en token. Du kan sedan lägga till SAS-token till Packet Capture Storage BLOB URL.

Den resulterande URL: en kommer att se ut ungefär som följande URL: `http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere`


### <a name="analyzing-packet-captures"></a>Analysera paket fångster

CapAnalysis erbjuder olika alternativ för att visualisera paket fångsten, som var och en ger analyser från ett annat perspektiv. Med dessa visuella sammanfattningar kan du förstå dina trender för nätverks trafiken och snabbt upptäcka eventuell ovanlig aktivitet. Några av dessa funktioner visas i följande lista:

1. Flow-tabeller

    Den här tabellen innehåller en lista över flöden i paket data, tidsstämpeln som associeras med flöden och de olika protokoll som är kopplade till flödet, samt käll-och mål-IP.

    ![capanalysis flödes sida][5]

1. Protokoll översikt

    I det här fönstret kan du snabbt se distributionen av nätverks trafik över olika protokoll och geografiska områden.

    ![Översikt över capanalysis-protokoll][6]

1. Statistik

    I det här fönstret kan du Visa statistik över nätverks trafiken – byte som skickats och tagits emot från käll-och mål-IP-adresser, flöden för var och en av käll-och mål-IP-adresser, protokoll som används för olika flöden och längden på flöden.

    ![capanalysis-statistik][7]

1. Geomappning

    I det här fönstret får du en karta över nätverks trafiken, med färger som skal änd ras till volymen trafik från varje land/region. Du kan välja markerade länder/regioner om du vill visa ytterligare flödes statistik, till exempel hur stor andel av data som skickas och tas emot från IP-adresser i landet/regionen.

    ![geomappning][8]

1. Filter

    CapAnalysis innehåller en uppsättning filter för snabb analys av vissa paket. Du kan till exempel välja att filtrera data efter protokoll för att få detaljerade insikter om den här del mängden av trafiken.

    ![filter][11]

    Besök [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) för att lära dig mer om alla CapAnalysis-funktioner.

## <a name="conclusion"></a>Slutsats

Med Network Watcher kan du samla in de data som krävs för att utföra nätverks-data utredning och bättre förstå nätverks trafiken. I det här scenariot visade vi hur paket insamlingar från Network Watcher enkelt kan integreras med visualiserings verktyg med öppen källkod. Genom att använda verktyg med öppen källkod, till exempel CapAnalysis för att visualisera paket, kan du utföra djup paket inspektion och snabbt identifiera trender inom nätverks trafiken.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om NSG Flow-loggar kan du besöka [NSG Flow-loggar](network-watcher-nsg-flow-logging-overview.md)

Lär dig hur du visualiserar dina NSG Flow-loggar med Power BI genom att besöka [visualisera NSG flöden loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
