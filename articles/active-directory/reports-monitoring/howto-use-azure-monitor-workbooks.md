---
title: Azure Monitor arbets böcker för rapporter | Microsoft Docs
description: Lär dig hur du använder Azure Monitor-arbetsböcker för Azure Active Directory rapporter.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b84c38b28b51f867160272883465242fc81ff2bf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588046"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Använda Azure Monitor-arbetsböcker för Azure Active Directory rapporter

> [!IMPORTANT]
> För att optimera de underliggande frågorna i den här arbets boken, klicka på "redigera", klicka på inställnings ikonen och välj den arbets yta där du vill köra frågorna. Arbets böcker som standard väljer alla arbets ytor där du dirigerar dina Azure AD-loggar. 

Vill du:

- Förstå resultatet av dina [principer för villkorlig åtkomst](../conditional-access/overview.md) på användarnas inloggnings upplevelser?

- Felsök inloggnings fel för att få en bättre översikt över din organisations inloggnings hälsa och för att snabbt lösa problem?

- Vet vem som använder äldre autentiseringar för att logga in i din miljö? (Genom att [blockera äldre autentisering](../conditional-access/block-legacy-authentication.md)kan du förbättra din klients skydd.)

- Behöver du känna till konsekvenserna av principer för villkorlig åtkomst i din klient organisation?

- Vill du ha möjlighet att granska: inloggnings logg frågor, arbets boken rapporterar hur många användare som har beviljats eller nekats åtkomst, samt hur många användare som ignorerade principer för villkorlig åtkomst vid åtkomst till resurser?

- Intresse rad av att utveckla en djupare förståelse av: arbets boks informationen per villkor så att en princips inverkan kan lösas per villkor, inklusive enhets plattform, enhets tillstånd, klient-app, inloggnings risk, plats och program?

- Få djupare insikter om inloggnings logg frågor, arbets boken rapporterar hur många användare som har beviljats eller nekats åtkomst, samt hur många användare som ignorerade principer för villkorlig åtkomst vid åtkomst till resurser.

- För att hjälpa dig att åtgärda de här frågorna innehåller Azure Active Directory arbets böcker för övervakning. [Azure Monitor arbets böcker](../../azure-monitor/visualize/workbooks-overview.md) kombinerar text, analys frågor, mått och parametrar till omfattande interaktiva rapporter.



Den här artikeln:

- Förutsätter att du är bekant med hur du [skapar interaktiva rapporter med hjälp av övervaka arbets böcker](../../azure-monitor/visualize/workbooks-overview.md).

- Förklarar hur du använder övervaknings arbets böcker för att förstå effekterna av dina principer för villkorlig åtkomst, för att felsöka inloggnings fel och för att identifiera äldre autentiseringar.
 


## <a name="prerequisites"></a>Förutsättningar

Om du vill använda övervaknings arbets böcker behöver du:

- En Azure Active Directory klient med en Premium-licens (P1 eller P2). Lär dig hur du [skaffar en Premium-licens](../fundamentals/active-directory-get-started-premium.md).

- En [Log Analytics-arbetsyta](../../azure-monitor/logs/quick-create-workspace.md).

- [Åtkomst](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions) till Log Analytics-arbetsytan
- Följande roller i Azure Active Directory (om du får åtkomst till Log Analytics via Azure Active Directory Portal)
    - Säkerhetsadministratör
    - Säkerhetsläsare
    - Rapport läsare
    - Global administratör

## <a name="roles"></a>Roller
Du måste vara i någon av följande roller och ha [åtkomst till underliggande Log Analytics](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) arbets yta för att hantera arbets böckerna:
-   Global administratör
-   Säkerhetsadministratör
-   Säkerhetsläsare
-   Rapport läsare
-   Program administratör

## <a name="workbook-access"></a>Arbets boks åtkomst 

För att få åtkomst till arbets böcker:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Navigera till **Azure Active Directory**  >  **övervakning** av  >  **arbets böcker**. 

1. Välj en rapport eller mall eller Välj **Öppna** i verktygsfältet. 

![Hitta Azure Monitor arbets böcker i Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Inloggnings analys

För att få åtkomst till arbets boken för inloggnings analys går du till avsnittet **användning** och väljer **inloggnings program**. 

I den här arbets boken visas följande trender:

- Alla inloggningar

- Klart

- Väntande användar åtgärd

- Fel

Du kan filtrera varje trend enligt följande kategorier:

- Tidsintervall

- Appar

- Användare

![Inloggnings analys](./media/howto-use-azure-monitor-workbooks/43.png)


För varje trend får du en uppdelning med följande kategorier:

- Location

    ![Inloggningar efter plats](./media/howto-use-azure-monitor-workbooks/45.png)

- Enhet

    ![Inloggningar per enhet](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Inloggningar med äldre autentisering 


För att få åtkomst till arbets boken för inloggningar som använder [äldre autentisering](../conditional-access/block-legacy-authentication.md), i avsnittet **användning** väljer du **inloggningar med äldre autentisering**. 

I den här arbets boken visas följande trender:

- Alla inloggningar

- Klart


Du kan filtrera varje trend enligt följande kategorier:

- Tidsintervall

- Appar

- Användare

- Protokoll

![Inloggningar från äldre autentisering](./media/howto-use-azure-monitor-workbooks/47.png)


För varje trend får du en uppdelning efter app och protokoll.

![Bakåtkompatibla inloggningar via app och protokoll](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Inloggningar efter villkorlig åtkomst 


Om du vill komma åt arbets boken för inloggningar via [principer för villkorlig åtkomst](../conditional-access/overview.md)i avsnittet **villkorlig åtkomst** väljer du **inloggningar med villkorlig åtkomst**. 

I den här arbets boken visas trender för inaktiverade inloggningar. Du kan filtrera varje trend enligt följande kategorier:

- Tidsintervall

- Appar

- Användare

![Inloggningar med villkorlig åtkomst](./media/howto-use-azure-monitor-workbooks/49.png)


För inaktiverade inloggningar får du en uppdelning av status för villkorlig åtkomst.

![Skärm bild som visar status för villkorlig åtkomst och de senaste inloggningarna.](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Conditional Access Insights

### <a name="overview"></a>Översikt

Arbets böcker innehåller inloggnings logg frågor som kan hjälpa IT-administratörer att övervaka effekten av principer för villkorlig åtkomst i sin klient organisation. Du kan rapportera om hur många användare som har beviljats eller nekats åtkomst. Arbets boken innehåller insikter om hur många användare som har kringgåt villkorliga åtkomst principer baserat på dessa användares attribut vid tidpunkten för inloggning. Den innehåller information per villkor så att påverkan av en princip kan lösas per villkor, inklusive enhets plattform, enhets tillstånd, klientens app, inloggnings risk, plats och program.

### <a name="instructions"></a>Instruktioner 
Om du vill få åtkomst till arbets boken för villkorlig åtkomst insikter väljer du arbets boken för **villkorlig åtkomst** i avsnittet villkorlig åtkomst. Den här arbets boken visar den förväntade effekten av varje princip för villkorlig åtkomst i din klient organisation. Välj en eller flera principer för villkorlig åtkomst i list rutan och begränsa arbets bokens omfattning genom att använda följande filter: 

- **Tidsintervall**

- **Användare**

- **Appar**

- **Datavy**

![Skärm bild som visar fönstret villkorlig åtkomst där du kan välja en princip för villkorlig åtkomst.](./media/howto-use-azure-monitor-workbooks/access-insights.png)


Effekt sammanfattningen visar antalet användare eller inloggningar för vilka de valda principerna har ett visst resultat. Totalt antal användare eller inloggningar för vilka de valda principerna utvärderades under det valda tidsintervallet. Klicka på en panel för att filtrera data i arbets boken med den resultat typen. 

![Skärm bild som visar paneler som används för att filtrera resultat som till exempel total, lyckad och misslyckad.](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

I den här arbets boken visas även effekten av de valda principerna uppdelade efter vart och ett av sex villkor: 
- **Enhets tillstånd**
- **Enhetsplattform**
- **Klientappar**
- **Inloggningsrisk**
- **Plats**
- **Program**

![Skärm bild som visar information från filtret för totalt antal inloggningar.](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Du kan också undersöka enskilda inloggningar som filtrerats efter de parametrar som valts i arbets boken. Sök efter enskilda användare, sorterade efter inloggnings frekvens och Visa motsvarande inloggnings händelser. 

![Skärm bild som visar enskilda inloggningar som du kan granska.](./media/howto-use-azure-monitor-workbooks/filtered.png)

## <a name="sign-ins-by-grant-controls"></a>Inloggningar genom att tilldela kontroller

Om du vill komma åt arbets boken för inloggningar genom att [bevilja kontroller](../conditional-access/controls.md)i avsnittet **villkorlig åtkomst** väljer du **inloggningar genom att bevilja kontroller**. 

Den här arbets boken visar följande inaktiverade inloggnings trender:

- Krav på MFA
 
- Kräva användningsvillkor

- Kräv sekretess policy

- Övrigt


Du kan filtrera varje trend enligt följande kategorier:

- Tidsintervall

- Appar

- Användare

![Inloggningar genom att tilldela kontroller](./media/howto-use-azure-monitor-workbooks/50.png)


För varje trend får du en uppdelning efter app och protokoll.

![Uppdelning av de senaste inloggnings programmen](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analys av inloggnings problem

Använd analys arbets boken för **inloggnings fel** för att felsöka fel med:

- Inloggningar
- Principer för villkorlig åtkomst
- Äldre autentisering 


Om du vill komma åt inloggnings uppgifterna med hjälp av villkorlig åtkomst i avsnittet **fel sökning** väljer du **inloggningar med äldre autentisering**. 

I den här arbets boken visas följande trender:

- Alla inloggningar

- Klart

- Väntande åtgärd

- Fel


Du kan filtrera varje trend enligt följande kategorier:

- Tidsintervall

- Appar

- Användare

![Felsöka inloggningar](./media/howto-use-azure-monitor-workbooks/52.png)


För att hjälpa dig att felsöka inloggningar ger Azure Monitor en uppdelning efter följande kategorier:

- Vanligaste felen

    ![Sammanfattning av de vanligaste felen](./media/howto-use-azure-monitor-workbooks/53.png)

- Inloggningar väntar på användar åtgärd

    ![Sammanfattning av inloggningar väntar på användar åtgärd](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Nästa steg

[Skapa interaktiva rapporter med hjälp av övervaka arbets böcker](../../azure-monitor/visualize/workbooks-overview.md).