---
title: Integrera loggar med ArcSight med Azure Monitor | Microsoft Docs
description: Lär dig hur du integrerar Azure Active Directory loggar med ArcSight med Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc7bcab04da005fd0d46d18e7b708dcb1c9d58e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89230525"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integrera Azure Active Directory-loggar med ArcSight med Azure Monitor

[Micro Focus-ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) är en lösning för säkerhets informations-och händelse hantering (Siem) som hjälper dig att identifiera och reagera på säkerhetshot på din plattform. Nu kan du dirigera Azure Active Directory (Azure AD)-loggar till ArcSight med Azure Monitor med hjälp av ArcSight-anslutaren för Azure AD. Med den här funktionen kan du övervaka din klient för säkerhets kompromisser med ArcSight.  

I den här artikeln får du lära dig hur du dirigerar Azure AD-loggar till ArcSight med hjälp av Azure Monitor. 

## <a name="prerequisites"></a>Krav

För att använda funktionen behöver du:
* En Azure Event Hub som innehåller Azure AD-aktivitets loggar. Lär dig hur du [strömmar dina aktivitets loggar till en Event Hub](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 
* En konfigurerad instans av ArcSight syslog NG daemon SmartConnector (SmartConnector) eller ArcSight Load Balancer. Om händelserna skickas till ArcSight Load Balancer skickas de därför till SmartConnector av Load Balancer.

Hämta och öppna [konfigurations guiden för ArcSight-SmartConnector för Azure Monitor Event Hub](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292). Den här guiden innehåller de steg du behöver för att installera och konfigurera ArcSight-SmartConnector för Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrera Azure AD-loggar med ArcSight

1. Slutför först stegen i avsnittet **krav** i konfigurations guiden. Det här avsnittet innehåller följande steg:
    * Ange användar behörigheter i Azure för att se till att det finns en användare med **ägar** rollen för att distribuera och konfigurera anslutningen.
    * Öppna portar på servern med syslog NG daemon SmartConnector, så att det går att komma åt från Azure. 
    * Distributionen kör ett Windows PowerShell-skript, så du måste aktivera PowerShell för att köra skript på den dator där du vill distribuera anslutningen.

2. Följ stegen i avsnittet **distribuera anslutnings komponenten** i konfigurations guiden för att distribuera anslutningen. Det här avsnittet beskriver hur du hämtar och extraherar anslutningen, konfigurerar program egenskaper och kör distributions skriptet från den extraherade mappen. 

3. Använd stegen i **Verifiera distributionen i Azure** för att se till att kopplingen är inställd och fungerar som den ska. Kontrollera följande:
    * De nödvändiga Azure Functions skapas i din Azure-prenumeration.
    * Azure AD-loggarna strömmas till rätt mål. 
    * Program inställningarna från distributionen sparas i program inställningarna i Azure Function-appar. 
    * En ny resurs grupp för ArcSight skapas i Azure med ett Azure AD-program för ArcSight-anslutningen och lagrings kontona som innehåller de mappade filerna i CEF-format.

4. Slutligen slutför du stegen efter distribution i konfigurations guiden för konfiguration **efter distribution** . I det här avsnittet beskrivs hur du utför ytterligare konfiguration om du är på en App Service plan för att förhindra att funktionen appar går inaktiva efter en tids gräns, konfigurerar strömning av resurs loggar från händelsehubben och uppdaterar SmartConnector för SysLog NG-daemonen för att associera det med det nya lagrings kontot.

5. I konfigurations guiden beskrivs också hur du anpassar anslutnings egenskaperna i Azure och hur du uppgraderar och avinstallerar anslutningen. Det finns också ett avsnitt om prestanda förbättringar, inklusive uppgradering till en [Azure-förbruknings plan](https://azure.microsoft.com/pricing/details/functions) och konfigurering av en ArcSight-Load Balancer om händelse inläsningen är större än vad en enda syslog ng daemon-SmartConnector kan hantera.

## <a name="next-steps"></a>Nästa steg

[Konfigurations guide för ArcSight-SmartConnector för Azure Monitor Event Hub](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)