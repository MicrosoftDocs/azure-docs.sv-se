---
title: Så här felsöker du inloggnings fel rapporter | Microsoft Docs
description: Lär dig hur du felsöker inloggnings fel med hjälp av Azure Active Directory rapporter i Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de9f5a21f38f741dcf65d285446d9482716d4f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85608134"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Anvisningar: Felsöka inloggningsfel med hjälp av Azure Active Directory-rapporter

I [inloggnings rapporten](concept-sign-ins.md) i Azure Active Directory (Azure AD) kan du få svar på frågor om hur du hanterar åtkomst till program i din organisation, inklusive:

- Vilket inloggningsmönster har en användare?
- Hur många användare har en användare loggat in under en vecka?
- Vad är status för dessa inloggningar?


Dessutom kan inloggnings rapporten också hjälpa dig att felsöka inloggnings fel för användare i din organisation. I den här guiden får du lära dig hur du isolerar ett inloggnings fel i inloggnings rapporten och använder den för att förstå rotor saken till felet.

## <a name="prerequisites"></a>Förutsättningar

Du behöver:

* En Azure AD-klient med en Premium-licens (P1/P2). Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version.
* En användare, som finns i rollen **Global administratör**, **säkerhets administratör**, **säkerhets läsare** eller **rapport läsare** för klienten. Dessutom kan alla användare komma åt sina egna inloggningar. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Felsöka inloggnings fel med hjälp av inloggnings rapporten

1. Gå till [Azure-portalen](https://portal.azure.com) och välj din katalog.
2. Välj **Azure Active Directory** och välj **Inloggningar** i avsnittet **Övervakning**. 
3. Använd de angivna filtren för att begränsa felen, antingen genom användar namn eller objekt identifierare, program namn eller datum. Dessutom väljer du **fel** från List rutan **status** för att visa endast misslyckade inloggningar. 

    ![Filtrera resultat](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identifiera den misslyckade inloggning som du vill undersöka. Välj den för att öppna fönstret Ytterligare information med mer information om den misslyckade inloggningen. Anteckna **felkoden för inloggningen** och **fel orsaken**. 

    ![Välj post](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Du kan också hitta den här informationen på fliken **fel sökning och support** i fönstret information.

    ![Fel sökning och support](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. Fel orsaken beskriver felet. I ovanstående scenario är fel orsaken till exempel ett **ogiltigt användar namn eller lösen ord eller ogiltigt lokalt användar namn eller lösen ord**. Korrigeringen är att bara logga in igen med rätt användar namn och lösen ord.

7. Du kan få ytterligare information, inklusive förslag på åtgärder, genom att söka efter felkoden **50126** i det här exemplet i [referensen till inloggnings fel](reference-sign-ins-error-codes.md). 

8. Om allt annat Miss lyckas, eller problemet kvarstår trots den rekommenderade åtgärden, [öppnar du ett support ärende](../fundamentals/active-directory-troubleshooting-support-howto.md) enligt stegen på fliken **fel sökning och support** . 

## <a name="next-steps"></a>Nästa steg

* [Referens för inloggnings fel koder](reference-sign-ins-error-codes.md)
* [Översikt över inloggnings rapporter](concept-sign-ins.md)
