---
title: Så här installerar och använder du Log Analytics-vyer | Microsoft Docs
description: Lär dig hur du installerar och använder Log Analytics-vyer för Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
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
ms.openlocfilehash: 86ad698793d562f93f9972903ca21e50c209c79c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580006"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Installera och Använd Log Analytics-vyerna för Azure Active Directory

I vyerna Azure Active Directory Log Analytics kan du analysera och söka i Azure AD-aktivitets loggarna i Azure AD-klienten. I aktivitets loggarna för Azure AD ingår:

* Gransknings loggar: [aktivitets rapporten gransknings loggar](concept-audit-logs.md) ger dig till gång till historiken för alla uppgifter som utförs i din klient organisation.
* Inloggnings loggar: med [rapporten inloggnings aktivitet](concept-sign-ins.md)kan du bestämma vem som utförde de uppgifter som rapporteras i gransknings loggarna.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Log Analytics-vyerna behöver du:

* En Log Analytics arbets yta i din Azure-prenumeration. Lär dig hur du [skapar en arbets yta för Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).
* Slutför först stegen för att [dirigera Azure AD-aktivitets loggarna till din Log Analytics-arbetsyta](howto-integrate-activity-logs-with-log-analytics.md).
* Ladda ned vyerna från [GitHub-lagringsplatsen](https://aka.ms/AADLogAnalyticsviews) till den lokala datorn.

## <a name="install-the-log-analytics-views"></a>Installera Log Analytics-vyer

1. Navigera till din Log Analytics-arbetsyta. För att göra det går du först till [Azure Portal](https://portal.azure.com) och väljer **alla tjänster**. Skriv **Log Analytics** i text rutan och välj **Log Analytics arbets ytor**. Välj den arbets yta som du dirigerade aktivitets loggarna till, som en del av förutsättningarna.
2. Välj **Visa designer**, Välj **Importera** och välj sedan **Välj fil** för att importera vyerna från den lokala datorn.
3. Välj de vyer som du laddade ned från kraven och välj **Spara** för att spara importen. Gör detta för vyn **Azure AD-konto etablerings händelser** och **inloggnings händelser** .

## <a name="use-the-views"></a>Använda vyerna

1. Navigera till din Log Analytics-arbetsyta. För att göra det går du först till [Azure Portal](https://portal.azure.com) och väljer **alla tjänster**. Skriv **Log Analytics** i text rutan och välj **Log Analytics arbets ytor**. Välj den arbets yta som du dirigerade aktivitets loggarna till, som en del av förutsättningarna.

2. När du är i arbets ytan väljer du **arbets ytans Sammanfattning**. Du bör se följande tre vyer:

    * **Azure AD-kontots etablerings händelser**: i den här vyn visas rapporter om etablerings aktiviteter, till exempel antalet nya användare som har skapats och etablerings felen, antalet användare som har uppdaterats och uppdaterings felen och antalet användare som har upprättat och motsvarande problem.    
    * **Inloggnings händelser**: i den här vyn visas de mest relevanta rapporterna om övervakning av inloggnings aktivitet, till exempel inloggningar av program, användare, enhet, samt en sammanfattningsvy som spårar antalet inloggningar över tid.

3. Välj någon av dessa vyer för att hoppa till de enskilda rapporterna. Du kan också ställa in aviseringar på någon av rapport parametrarna. Vi kan till exempel ställa in en avisering för varje gång ett inloggnings fel uppstår. Om du vill göra detta väljer du först vyn **inloggnings händelser** , väljer **Logga in fel över tid** och väljer sedan **analys** för att öppna sidan information med den faktiska frågan bakom rapporten. 

    ![Skärm bild som visar sidan med analys information som har frågan för rapporten.](./media/howto-install-use-log-analytics-views/details.png)


4. Välj **Ange avisering** och välj sedan **när den anpassade loggs ökningen är &lt; logiskt odefinierad &gt;** under avsnittet **aviserings villkor** . Eftersom vi vill Avisera när det finns ett inloggnings fel anger du **tröskelvärdet** för standard varnings logiken till **1** och väljer sedan **klart**. 

    ![Konfigurera signallogiken](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Ange ett namn och en beskrivning för aviseringen och ange allvarlighets grad till **Varning**.

    ![Skapa regel](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Välj den åtgärds grupp som ska aviseras. I allmänhet kan detta vara antingen ett team som du vill meddela via e-post eller SMS, eller så kan det vara en automatiserad aktivitet med Webhooks, Runbooks, functions, Logic Apps eller externa ITSM-lösningar. Lär dig hur du [skapar och hanterar åtgärds grupper i Azure Portal](../../azure-monitor/alerts/action-groups.md).

7. Välj **skapa aviserings regel** för att skapa aviseringen. Nu får du en avisering varje gång ett inloggnings fel uppstår.

## <a name="next-steps"></a>Nästa steg

* [Så här analyserar du aktivitets loggar med Azure Monitor loggar](howto-analyze-activity-logs-log-analytics.md)
* [Kom igång med Azure Monitor loggar i Azure Portal](../../azure-monitor/logs/log-analytics-tutorial.md)