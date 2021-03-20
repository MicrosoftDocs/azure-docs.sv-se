---
title: Användnings-och insikts rapport | Microsoft Docs
description: Introduktion till användnings-och insikts rapport i Azure Active Directory portalen
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 54bce5e839786862a6dac9aeb685dd364547a09a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685043"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Användnings-och insikts rapport i Azure Active Directorys portalen

Med hjälp av rapporten användning och insikter kan du få en programinriktad vy över dina inloggnings data. Du kan hitta svar på följande frågor:

*   Vilka är de mest använda programmen i min organisation?
*   Vilka program har de mest misslyckade inloggningarna? 
*   Vilka är de vanligaste inloggnings felen för varje program?

## <a name="prerequisites"></a>Förutsättningar 

Om du vill komma åt data från rapporten användning och insikter behöver du:

* En Azure AD-klient
* En Azure AD Premium-licens (P1/P2) som visar inloggnings data
* En användare i rollerna global administratör, säkerhets administratör, säkerhets läsare eller rapport läsare. Dessutom kan alla användare (icke-administratörer) komma åt sina egna inloggningar. 

## <a name="access-the-usage-and-insights-report"></a>Öppna rapporten användning och insikter

1. Navigera till [Azure Portal](https://portal.azure.com).
2. Välj rätt katalog, Välj **Azure Active Directory** och välj **företags program**.
3. I avsnittet **aktivitet** väljer du **användning & insikter** för att öppna rapporten. 

![Skärm bild som visar användning & insikter som valts från avsnittet aktivitet.](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Använda rapporten

I rapporten användning och insikter visas en lista över program med en eller flera inloggnings försök, och du kan sortera efter antalet lyckade inloggningar, misslyckade inloggningar och antalet lyckade inloggningar.

Om du klickar på **Läs in mer** längst ned i listan kan du Visa fler program på sidan. Du kan välja datum intervall för att visa alla program som har använts inom intervallet.

![Skärm bild som visar användning & insikter för program aktivitet där du kan välja ett intervall och Visa inloggnings aktivitet för olika appar.](./media/concept-usage-insights-report/usage-and-insights-report.png)

Du kan också ange fokus för ett angivet program. Välj **Visa inloggnings aktivitet** för att se inloggnings aktiviteten över tid för programmet och de vanligaste felen.  

När du väljer en dag i program användnings diagrammet visas en detaljerad lista över inloggnings aktiviteterna för programmet.  

:::image type="content" source="./media/concept-usage-insights-report/usage-and-insights-application-report.png" alt-text="Skärm bild som visar användning & insikter för ett särskilt program där du kan se en graf för inloggnings aktiviteten.":::

## <a name="next-steps"></a>Nästa steg

* [Rapport över inloggningar](concept-sign-ins.md)
