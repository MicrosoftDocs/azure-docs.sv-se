---
title: Anslut Windows säkerhets händelse data till Azure Sentinel | Microsoft Docs
description: Lär dig att använda anslutnings verktyget för säkerhets händelser för att strömma alla säkerhets händelser från dina Windows-system till Azure Sentinel-arbetsytan.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: bc75ee64174957ad6486146b4da6f8a66a2120e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570550"
---
# <a name="connect-windows-security-events"></a>Ansluta till Windows säkerhetshändelser 

Med Security Events Connector kan du strömma alla säkerhets händelser från dina Windows-system (servrar och arbets stationer, fysiska och virtuella) till din Azure Sentinel-arbetsyta. På så sätt kan du Visa säkerhets händelser i Windows på dina instrument paneler, använda dem för att skapa anpassade aviseringar och förlita dig på dem för att förbättra dina undersökningar, vilket ger dig mer information om din organisations nätverk och hur du utökar dina säkerhets funktioner. Du kan välja vilka händelser som ska strömmas mellan följande uppsättningar: <a name="event-sets"></a>

- **Alla händelser** – alla Windows-säkerhets-och AppLocker-händelser.
- **Vanliga** – en standard uppsättning händelser för gransknings syfte. En fullständig användar Gransknings logg ingår i den här uppsättningen. Den innehåller till exempel både användar inloggnings-och användar inloggnings händelser (händelse-ID 4624, 4634). Det finns också gransknings åtgärder, till exempel säkerhets grupps ändringar, Kerberos-åtgärder för domänkontrollanter och andra typer av händelser i linje med godkända metod tips.

    Den **gemensamma** händelse uppsättningen kan innehålla vissa typer av händelser som inte är så vanliga.  Detta beror på att huvud punkten i den **gemensamma** uppsättningen är att minska mängden händelser till en mer hanterbar nivå, samtidigt som den fullständiga gransknings spårnings kapaciteten fortfarande upprätthålls.

- **Minimal** – en liten uppsättning händelser som kan tyda på potentiella hot. Den här uppsättningen innehåller inte en fullständig gransknings historik. Det täcker bara händelser som kan tyda på en lyckad överträdelse och andra viktiga händelser som har mycket låg frekvens av en förekomst. Till exempel innehåller den lyckade och misslyckade användar inloggningar (händelse-ID 4624, 4625), men innehåller inte information om utloggning (4634) som, när det är viktigt för granskning, inte meningsfullt för att upptäcka överträdelse och har en relativt hög volym. Merparten av data volymen i den här uppsättningen består av inloggnings händelser och händelser för process skapande (händelse-ID 4688).

- **Ingen** -inga säkerhets-eller AppLocker-händelser. (Den här inställningen används för att inaktivera anslutningen.)

    I följande lista finns en fullständig analys av händelse-ID: n för säkerhet och app Locker för varje uppsättning:

    | Händelse uppsättning | Insamlade händelse-ID: n |
    | --- | --- |
    | **Minimal** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006 |
    | **Common** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614,,,,,,. 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727 , 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764,,,, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059 , 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004,,,,, |

> [!NOTE]
> Insamling av säkerhets händelser inom kontexten för en enskild arbets yta kan konfigureras antingen från Azure Security Center eller Azure Sentinel, men inte med båda. Om du integrerar Azure Sentinel i en arbets yta som redan får Azure Defender-aviseringar från Azure Security Center och har kon figurer ATS för att samla in säkerhets händelser, har du två alternativ:
> - Lämna samlingen säkerhets händelser i Azure Security Center som den är. Du kan fråga efter och analysera dessa händelser i Azure Sentinel och i Azure Defender. Du kommer dock inte att kunna övervaka anslutningens anslutnings status eller ändra dess konfiguration i Azure Sentinel. Om detta är viktigt för dig bör du överväga det andra alternativet.
>
> - [Inaktivera insamling av säkerhets händelser](../security-center/security-center-enable-data-collection.md) i Azure Security Center och Lägg sedan till anslutnings verktyget för säkerhets händelser i Azure Sentinel. Precis som med det första alternativet kan du fråga efter och analysera händelser i både Azure Sentinel och Azure Defender/ASC, men du kommer nu att kunna övervaka anslutnings status för anslutningen eller ändra dess konfiguration i och endast i Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Konfigurera Windows Security Events Connector

Samla in säkerhets händelser i Windows i Azure Sentinel:

1. Välj **data kopplingar** på navigerings menyn i Azure Sentinel. I listan över anslutningar, klickar du på **säkerhets händelser** och sedan på knappen **Öppna kopplings sidan** längst ned till höger. Följ sedan anvisningarna på skärmen på fliken **instruktioner** , enligt beskrivningen i resten av det här avsnittet.

1. Kontrol lera att du har rätt behörigheter enligt beskrivningen i avsnittet **krav** på kopplings sidan.

1. Ladda ned och installera [Log Analytics-agenten](../azure-monitor/agents/log-analytics-agent.md) (kallas även Microsoft Monitoring Agent eller MMA) på de datorer som du vill strömma säkerhets händelser till i Azure Sentinel.

    För Azure-Virtual Machines:
    
    1. Klicka på **Installera agent på Azures virtuella Windows-dator** och sedan på länken som visas nedan.
    1. För varje virtuell dator som du vill ansluta klickar du på namnet i listan som visas till höger och klickar sedan på **Anslut**.

    För Windows-datorer som inte är Azure-datorer (fysisk, virtuell lokal eller virtuell i ett annat moln):

    1. Klicka på **Installera agent på en Windows-dator som inte är Azure** och sedan på länken som visas nedan.
    1. Klicka på lämpliga nedladdnings länkar som visas till höger under Windows- **datorer**.
    1. Med hjälp av den hämtade körbara filen installerar du agenten på de Windows-system som du väljer och konfigurerar den med hjälp av **arbets ytans ID och nycklar** som visas under de nedladdnings länkar som anges ovan.

    > [!NOTE]
    >
    > Om du vill tillåta att Windows-system utan nödvändig Internet anslutning ändå strömma händelser till Azure Sentinel, laddar du ned och installerar **OMS-gatewayen** på en annan dator med hjälp av länken längst ned till höger, för att fungera som en proxy.  Du måste fortfarande installera Log Analytics-agenten på varje Windows-system vars händelser du vill samla in.
    >
    > Mer information om det här scenariot finns i [dokumentationen för **Log Analytics Gateway**](../azure-monitor/agents/gateway.md).

    Ytterligare information om installation och ytterligare information finns i dokumentationen till [ **Log Analytics agent**](../azure-monitor/agents/agent-windows.md).

1. Välj vilken händelse uppsättning ([alla, vanliga eller minimala](#event-sets)) som du vill strömma.

1. Klicka på **Uppdatera**.

1. Om du vill använda det relevanta schemat i Log Analytics för säkerhets händelser i Windows skriver `SecurityEvent` du i frågefönstret.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta ungefär 20 minuter innan loggarna börjar visas i Log Analytics. 

### <a name="configure-the-security-events-connector-for-anomalous-rdp-login-detection"></a>Konfigurera Security Events Connector för identifiering av avvikande RDP-inloggning

> [!IMPORTANT]
> Identifiering av avvikande RDP-inloggning är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel kan använda Machine Learning (ML) till säkerhets händelse data för att identifiera avvikande Remote Desktop Protocol (RDP) inloggnings aktivitet. Scenarierna är:

- **Ovanlig IP** – IP-adressen har sällan eller aldrig observerats under de senaste 30 dagarna

- **Ovanlig Geo-Location** – IP-adress, stad, land och ASN har sällan eller aldrig observerats under de senaste 30 dagarna

- **Ny användare** – en ny användare loggar in från en IP-adress och en Geo-Location, båda eller någon av de som inte förväntades vara beroende av data från 30 dagar tidigare.

**Konfigurationsanvisningar**

1. Du måste samla in RDP-inloggnings data (händelse-ID 4624) via data anslutnings tjänsten för **säkerhets händelser** . Kontrol lera att du har valt en [händelse som har angetts](#event-sets) förutom "ingen" för att strömma till Azure Sentinel.

1. Klicka på **analys** på Azure Sentinel-portalen och klicka sedan på fliken **regelmallar** . Välj **identifierings regeln (förhands granskning) avvikande RDP-inloggning** och flytta skjutreglaget **status** till **aktive rad**.

    > [!NOTE]
    > Eftersom Machine Learning-algoritmen kräver 30 dagars data för att bygga en bas linje profil för användar beteendet, måste du tillåta att säkerhets händelse data för 30 dagar samlas in innan eventuella incidenter kan identifieras.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Windows säkerhets händelser till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång med att identifiera hot med Azure Sentinel, med [inbyggda](tutorial-detect-threats-built-in.md) eller [anpassade](tutorial-detect-threats-custom.md) regler.

