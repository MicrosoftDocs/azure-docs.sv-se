---
title: Anslut data från Windows Defender-brandväggen till Azure Sentinel | Microsoft Docs
description: Aktivera Windows-brandväggen i Azure Sentinel för att enkelt strömma brand Väggs händelser från Windows-datorer som har Log Analytics-agenter installerade.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: cf7e389fc4a8a8dfa88691dc034611cae3471731
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655348"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>Anslut Windows Defender-brandväggen med avancerad säkerhet till Azure Sentinel

[Med Windows Defender-brandväggen med avancerad säkerhets](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) anslutning kan Azure Sentinel enkelt mata in Windows Defender-brandväggen med avancerade säkerhets loggar från alla Windows-datorer i din arbets yta. Med den här anslutningen kan du Visa och analysera händelser i Windows-brandväggen i dina arbets böcker, för att använda dem för att skapa anpassade aviseringar och för att lägga till dem i dina säkerhets undersökningar, vilket ger dig mer information om din organisations nätverk och förbättra dina säkerhets funktioner. 

Lösningen samlar in händelser i Windows-brandväggen från Windows-datorer där en Log Analytics-agent är installerad. 

> [!NOTE]
> - Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.
>
> - Om Azure Defender-aviseringar från Azure Security Center redan har samlats in på Azure Sentinel-arbetsytan behöver du inte aktivera Windows brand Väggs lösningen via den här anslutningen. Men om du aktiverar den, kommer den inte att orsaka dubbla data. 

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på arbets ytan som de datorer som du vill övervaka är anslutna till.

- Du måste vara tilldelad rollen **Log Analytics Contributor** i SecurityInsights-lösningen på den arbets ytan, förutom alla **Azure Sentinel** -roller. [Läs mer](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>Aktivera anslutningen 

1. I Azure Sentinel-portalen väljer du **data kopplingar** på navigerings menyn.

1. Välj **Windows-brandväggen** i anslutnings galleriet och klicka på **Öppna kopplings sida**.

### <a name="instructions-tab"></a>Fliken instruktioner

- **Om dina Windows-datorer finns i Azure:**

    1. Välj **Installera agent på virtuell Azure Windows-dator**.

    1. Klicka på länken **Ladda ned & installera agent för virtuella Azure Windows-datorer >** som visas.

    1. I listan med **virtuella datorer** väljer du den Windows-dator som du vill strömma till Azure Sentinel. (Du kan välja **fönster** i kolumn filtret för operativ systemet för att säkerställa att endast virtuella Windows-datorer visas).

    1. I fönstret som öppnas för den virtuella datorn klickar du på **Anslut**.

    1. Gå tillbaka till **Virtual Machines** fönstret och upprepa föregående två steg för alla andra virtuella datorer som du vill ansluta. När du är klar går du tillbaka till fönstret **Windows-brandväggen** .

- **Om din Windows-dator inte är en virtuell Azure-dator:**

    1. Välj **Installera agent på en Windows-dator som inte är Azure**.

    1. Klicka på länken **hämta & installera agent för Windows-datorer som inte är Azure->** som visas.

    1. I fönstret **hantering av agenter** väljer du antingen **Ladda ned Windows-agent (64 bitar)** eller **ladda ned Windows agent (32 bitar)** efter behov.

    1. Kopiera **arbetsyte-ID**, **primär nyckel** och **sekundär nyckel** strängar till en textfil. Kopiera filen och den nedladdade installations filen till din Windows-dator. Kör installations filen och ange ID och nyckel strängar i text filen under installationen när du uppmanas till det.

    1. Gå tillbaka till fönstret **Windows-brandväggen** .

1. Klicka på **installera lösning**.

### <a name="next-steps-tab"></a>Fliken nästa steg

- Se tillgängliga rekommenderade arbets böcker och fråge exempel som paketerats med **Windows-brandväggen** data Connector för att få information om loggdata i Windows-brandväggen.

- Om du vill fråga efter data i Windows-brandväggen i **loggar** skriver du **WindowsFirewall** i frågefönstret.

## <a name="validate-connectivity"></a>Verifiera anslutning
 
Eftersom Windows-brandväggens loggar bara skickas till Azure-kontroll när den lokala logg filen når kapacitet, lämnar loggen på standard storleken 4096 KB troligen på hög latens. Du kan minska svars tiden genom att minska storleken på logg filen. Se anvisningarna för att [Konfigurera loggen för Windows-brandväggen](/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log). Observera att när du definierar den minsta möjliga logg storleken (1 KB) kommer det i princip att ta bort en samlings fördröjning. det kan också påverka den lokala datorns prestanda negativt. 

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Windows-brandväggen till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).