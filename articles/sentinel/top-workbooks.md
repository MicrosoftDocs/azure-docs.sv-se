---
title: Vanliga Azure Sentinel-arbetsböcker | Microsoft Docs
description: Lär dig mer om de vanligaste arbets böckerna för att använda populära, inbyggda Azure Sentinel-resurser.
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
ms.topic: reference
ms.date: 03/07/2021
ms.author: bagol
ms.openlocfilehash: a37501498a9222025860702a7f29dccc9abfc8f7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102450072"
---
# <a name="commonly-used-azure-sentinel-workbooks"></a>Vanliga Azure Sentinel-arbetsböcker

I följande tabell visas de vanligaste, inbyggda Azure Sentinel-arbetsböckerna.

Få åtkomst till arbets böcker i Azure Sentinel under **Threat Management**  >  -**arbetsböcker** till vänster och Sök sedan efter den arbets bok som du vill använda. Mer information finns i [Självstudier: visualisera och övervaka dina data](tutorial-monitor-your-data.md).

|Arbets boks namn  |Beskrivning  |
|---------|---------|
|**Analys effektivitet**     |  Ger insikter om effektiviteten i analys reglerna för att hjälpa dig att uppnå bättre SOC prestanda. <br><br>Mer information finns i [verktygs uppsättningen för Data-Driven SOCS](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152).|
|**Azure-aktivitet**     |     Ger omfattande insikter om organisationens Azure-aktivitet genom att analysera och korrelera alla användar åtgärder och händelser. <br><br>Mer information finns i [granskning med Azure aktivitets loggar](audit-sentinel-data.md#auditing-with-azure-activity-logs).    |
|**Gransknings loggar för Azure AD**     |  Använder Azure Active Directory gransknings loggar för att ge insikter om Azure AD-scenarier. <br><br>Mer information finns i  [snabb start: kom igång med Azure Sentinel](quickstart-get-visibility.md).     |
|**Azure AD audit, aktivitets-och inloggnings loggar**     |   Ger insikter om Azure Active Directory gransknings-, aktivitets-och inloggnings data med en arbets bok. Den här arbets boken kan användas av både säkerhet och Azure-administratörer.      |
|**Azure AD-inloggningsloggar**     | Använder inloggnings loggarna i Azure AD för att ge insikter om Azure AD-scenarier.        |
|**Cybersäkerhet förfallo modell certifiering (CMMC)**     |   Innehåller en mekanism för att Visa logg frågor som är justerade för CMMC-kontroller i Microsoft-portföljen, inklusive Microsofts säkerhets erbjudanden, Office 365, teams, Intune, Windows Virtual Desktop och så vidare. <br><br>Mer information finns i [cybersäkerhet förfallo modell certifiering (CMMC) arbets bok i offentlig för hands version](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cybersecurity-maturity-model-certification-cmmc/ba-p/2111184).|
|**Hälso övervakning av data insamling**     |   Ger insikter om data inmatnings status för din arbets yta. Visa övervakare och identifiera avvikelser som hjälper dig att fastställa hälso tillståndet för din arbets ytas data insamling.  <br><br>Mer information finns i [övervaka hälso tillståndet för dina data anslutningar med den här Azure Sentinel-arbetsboken](monitor-data-connector-health.md).    |
|**Händelse analys**     |  Med kan du utforska, granska och påskynda Windows-händelseloggen, inklusive information om händelser och attribut, till exempel säkerhet, program, system, installation, katalog tjänst, DNS och så vidare.       |
|**Exchange Online**     |Ger insikter om Microsoft Exchange Online genom att spåra och analysera alla Exchange-åtgärder och användar aktiviteter.         |
|**Identitet och åtkomst**     |   Ger inblick i identitets-och åtkomst åtgärder i Microsofts produkt användning, via säkerhets loggar som innehåller gransknings-och inloggnings loggar.     |
|**Incident översikt**     |   Utformad för att hjälpa till med prioritering och undersökning genom att tillhandahålla detaljerad information om en incident, inklusive allmän information, enhets data, prioritering tid, tids gräns och kommentarer. <br><br>Mer information finns i [verktygs uppsättningen för Data-Driven SOCS](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152).      |
|**Utreda insikter**     | Ger analytiker insikter om incident-, bok märkes-och enhets data. Vanliga frågor och detaljerade visualiseringar kan hjälpa analytiker att undersöka misstänkta aktiviteter.       |
|**Microsoft Cloud App Security identifierings loggar**     |   Innehåller information om de molnappar som används i din organisation och insikter från användnings trender och data för att öka detalj nivån för vissa användare och program.  <br><br>Mer information finns i [ansluta data från Microsoft Cloud App Security](connect-cloud-app-security.md).|
|**MITRE att&CK arbets bok**     |   Innehåller information om MITRE att&CK täckning för Azure Sentinel.      |
|**Office 365**     |  Ger insikter om Office 365 genom att spåra och analysera alla åtgärder och aktiviteter. Öka detalj nivån i SharePoint-, OneDrive-och Exchange-data.       |
|**Säkerhets aviseringar**     |  Innehåller en instrument panel för säkerhets aviseringar för aviseringar i din Azure Sentinel-miljö. <br><br>Mer information finns i [skapa incidenter automatiskt från Microsofts säkerhets aviseringar](create-incidents-from-alerts.md).      |
|**Effektivitet för säkerhets åtgärder**     |  Avsedd för SOC-ansvariga (Security Operations Center) för att visa allmänna effektivitets mått och mått gällande prestanda för teamet. <br><br>Mer information finns i [Hantera din SOC bättre med incident mått](manage-soc-with-incident-metrics.md).  |
|**Hotinformation**     | Ger insikter om hot indikatorer, inklusive typ och allvarlighets grad för hot, Hot aktivitet över tid och korrelation med andra data källor, inklusive Office 365 och brand väggar.  <br><br>Mer information finns i [Importera Hot information till Azure Sentinel](import-threat-intelligence.md).      |
|**Granskning av arbets yta**     |  Innehåller en gransknings rapport för arbets ytan som gör att du kan förstå att köra frågor i din arbets yta.   <br><br>Mer information finns i [Granska Azure Sentinel-frågor och-aktiviteter](audit-sentinel-data.md).  |
|     |         |

