---
title: Strömma Azure Active Directory loggar till Azure Monitor loggar | Microsoft Docs
description: Lär dig hur du integrerar Azure Active Directory loggar med Azure Monitor loggar
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f328ed44252f7fb314552d6d05df9806f59d972
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591132"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integrera Azure AD-loggar med Azure Monitor loggar

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Med Azure Monitor loggar kan du fråga efter specifika händelser, analysera trender och utföra korrelationer mellan olika data källor. Med integreringen av Azure AD-aktivitets loggar i Azure Monitor loggar kan du nu utföra uppgifter som:

 * Jämför dina inloggnings loggar för Azure AD mot säkerhets loggar som publicerats av Azure Security Center

 * Felsök prestanda Flask halsar på programmets inloggnings sida genom att korrelera program prestanda data från Azure Application insikter.  

Följande video från en antändning visar fördelarna med att använda Azure Monitor loggar för Azure AD-loggar i praktiska användar scenarier.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

I den här artikeln får du lära dig hur du integrerar Azure Active Directory (Azure AD)-loggar med Azure Monitor.

## <a name="supported-reports"></a>Rapporter som stöds

Du kan dirigera gransknings aktivitets loggar och inloggnings aktivitets loggar till Azure Monitor loggar för vidare analys. 

* **Spårningsloggar**: [Rapporten om spårningsloggaktivitet](concept-audit-logs.md) ger dig åtkomst till historiken för varje uppgift som utförs i din klientorganisation.
* **Inloggningsloggar**: Med [rapporten om inloggningsaktivitet](concept-sign-ins.md) kan du se vem som har utfört de uppgifter som rapporteras i spårningsloggarna.
* **Etablerings loggar**: med [etablerings loggarna](../app-provisioning/application-provisioning-log-analytics.md)kan du övervaka vilka användare som har skapats, uppdaterats och tagits bort i alla program från tredje part. 

> [!NOTE]
> B2C-relaterade spårnings- och inloggningsaktivitetsloggar stöds inte just nu.
>

## <a name="prerequisites"></a>Krav 

För att använda funktionen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärdering](https://azure.microsoft.com/free/).
* En Azure AD-klientorganisation.
* En användare som är *global administratör* eller *säkerhetsadministratör* för Azure AD-klientorganisationen.
* En Log Analytics arbets yta i din Azure-prenumeration. Lär dig hur du [skapar en arbets yta för Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

## <a name="licensing-requirements"></a>Licenskrav

Om du använder den här funktionen krävs en Azure AD Premium P1-eller P2-licens. Hitta rätt licens för dina behov i [Jämför allmänt tillgängliga funktioner i Free, Basic och Premium-utgåvorna](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="send-logs-to-azure-monitor"></a>Skicka loggar till Azure Monitor

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Välj **Azure Active Directory**  >  **diagnostikinställningar inställningar**  ->  **Lägg till diagnostisk inställning**. Du kan också välja **export inställningar** på sidan **gransknings loggar** eller **inloggnings** sidor för att komma till konfigurations sidan för diagnostikinställningar.  
    
3. I menyn **diagnostikinställningar** väljer du kryss rutan **Skicka till Log Analytics arbets yta** och väljer sedan **Konfigurera**.

4. Välj den Log Analytics arbets yta som du vill skicka loggarna till, eller skapa en ny arbets yta i dialog rutan som visas.  

5. Gör endera eller båda av följande:
    * Om du vill skicka gransknings loggar till Log Analytics arbets ytan markerar du kryss rutan **AuditLogs** . 
    * Om du vill skicka inloggnings loggar till Log Analytics arbets ytan markerar du kryss rutan **SignInLogs** .

6. Spara inställningen genom att välja **Spara**.

    ![Diagnostikinställningar](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Efter cirka 15 minuter, kontrol lera att händelserna strömmas till din Log Analytics-arbetsyta.

## <a name="next-steps"></a>Nästa steg

* [Analysera Azure AD-aktivitets loggar med Azure Monitor loggar](howto-analyze-activity-logs-log-analytics.md)
* [Installera och Använd Log Analytics-vyerna för Azure Active Directory](howto-install-use-log-analytics-views.md)