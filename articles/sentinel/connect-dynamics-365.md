---
title: Ansluta Dynamics 365-loggar till Azure Sentinel | Microsoft Docs
description: Lär dig att använda aktivitets kopplingen för Dynamics 365 Common Data Service (CD) för att få information om pågående administrations-, användar-och support aktiviteter.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98104199"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>Anslut Dynamics 365-aktivitets loggar till Azure Sentinel

Med aktivitets kopplingarna för [Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) common data service (CD) får du inblick i administrations-, användar-och support aktiviteter, samt Microsoft Social engagemang loggnings händelser. Genom att ansluta Dynamics 365 CRM-loggar till Azure Sentinel kan du visa dessa data i arbets böcker, använda dem för att skapa anpassade aviseringar och utnyttja dem för att förbättra din gransknings process. Den här nya Azure Sentinel-anslutningen samlar in Dynamics CD-data från Office Management-API: et. Om du vill veta mer om de Dynamics CD-aktiviteter som granskas i Power Platform kan du gå [till aktivera och använda loggning av aktiviteter](/power-platform/admin/enable-use-comprehensive-auditing).

> [!IMPORTANT]
>
> Aktivitets kopplingen för Dynamics 365 Common Data Service (CD) är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha en [Microsoft Dynamics 365-produktions licens](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description). Den här kopplingen är inte tillgänglig för sandbox-miljöer.
    - En Microsoft 365 Enterprise [E3-eller E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) -prenumeration krävs för att utföra aktivitets loggning.

- Hämta data från Office Management-API: et:
    - Du måste vara global administratör för din klient.

    - [Office gransknings loggning](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) måste vara aktiverat i [Office Security och Compliance Center](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance).

## <a name="enable-the-dynamics-365-activities-data-connector"></a>Aktivera data koppling för Dynamics 365-aktiviteter

1. Välj **data kopplingar** på navigerings menyn i Azure Sentinel.

1. Från **data kopplings** galleriet väljer du **Dynamics 365 (för hands version)** och väljer sedan **Öppna kopplings sida** i förhands gransknings fönstret.

1. På fliken **instruktioner** , under **konfiguration**, klickar du på **Anslut**. 

    När kopplingen har Aktiver ATS tar det cirka 30 minuter tills du kommer att kunna se data som kommer in i grafen. 

    Enligt Office- [gransknings loggen i Compliance Center](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)kan det ta upp till 30 minuter eller upp till 24 timmar efter att en händelse inträffar för att motsvarande Gransknings logg post ska returneras i resultaten.

1. Du hittar gransknings loggarna för Microsoft Dynamics i `Dynamics365Activity` tabellen. Se tabellens [schema referens](/azure/azure-monitor/reference/tables/dynamics365activity).

## <a name="querying-the-data"></a>Fråga efter data

1. På navigerings menyn i Azure Sentinel väljer du **loggar**.

1. Kör följande fråga för att kontrol lera att loggar tas emot:

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter data från Dynamics 365-aktiviteter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång med att identifiera hot med Azure Sentinel, med [inbyggda](tutorial-detect-threats-built-in.md) eller [anpassade](tutorial-detect-threats-custom.md) regler.
