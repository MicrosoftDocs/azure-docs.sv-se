---
title: Skapa en åtkomst granskning av ett Access-paket i hantering av Azure AD-rättigheter
description: Lär dig hur du skapar en åtkomst gransknings princip för rättighets hanterings åtkomst paket i Azure Active Directory åtkomst granskningar (för hands version).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 012eefe9140703a62d7bb1074ab763191a0976cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798519"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Skapa en åtkomst granskning av ett Access-paket i hantering av Azure AD-rättigheter

För att minska risken för föråldrad åtkomst bör du aktivera regelbunden granskning av användare som har aktiva tilldelningar till ett Access-paket i hantering av Azure AD-rättigheter. Du kan aktivera granskningar när du skapar ett nytt Access-paket eller redigerar ett befintligt Access-paket. I den här artikeln beskrivs hur du aktiverar åtkomst granskningar av Access-paket.

## <a name="prerequisites"></a>Förutsättningar

Om du vill aktivera granskningar av åtkomst paket måste du uppfylla kraven för att skapa ett Access-paket:
- Azure AD Premium P2
- Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

Mer information finns i [licens krav](entitlement-management-overview.md#license-requirements).


## <a name="create-an-access-review-of-an-access-package"></a>Skapa en åtkomst granskning av ett Access-paket

Du kan aktivera åtkomst granskningar när du [skapar ett nytt åtkomst paket](entitlement-management-access-package-create.md) eller [redigerar en befintlig princip för åtkomst paket](entitlement-management-access-package-lifecycle-policy.md) . Följ dessa steg om du vill aktivera åtkomst granskningar av ett Access-paket:

1. Öppna fliken **livs cykel** för ett Access-paket och rulla ned för att **få åtkomst till recensioner**.

1. Flytta över **Kräv åtkomst granskningar** växla till **Ja**.

    ![Lägg till åtkomst granskning](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Ange datumet då granskningen **börjar.**

1. Ange sedan **gransknings frekvensen** till **varje år** **, varje månad, varje** **kvartal** eller **varje månad**.
Den här inställningen anger hur ofta åtkomst granskningar ska ske.

1. Ange **varaktigheten** för att definiera hur många dagar varje granskning av den återkommande serien ska vara öppen för ininformation från granskare. Du kan till exempel schemalägga en årlig granskning som börjar den 1 januari och är öppen för granskning i 30 dagar, så att granskarna har till slutet av månaden att svara.

1. Bredvid **granskare** väljer du **själv granskning** om du vill att användarna ska kunna utföra sin egen åtkomst granskning eller välja **specifika** granskare om du vill utse en granskare.

    ![Välj Lägg till granskare](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Om du har valt en **eller flera granskare** anger du vilka användare som ska få åtkomst granskningen:
    1. Välj **Lägg till granskare**.
    1. I fönstret **Välj granskare** söker du efter och väljer de användare som du vill ska vara en granskare.
    1. När du har valt en eller flera granskare klickar du på knappen **Välj** .

    ![Ange granskarna](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Klicka på **Granska + skapa** om du skapar ett nytt åtkomst paket eller en **uppdatering** om du redigerar ett åtkomst paket längst ned på sidan.

## <a name="view-the-status-of-the-access-review"></a>Visa status för åtkomst granskningen

Efter start datumet visas en åtkomst granskning i avsnittet **åtkomst granskningar** . Följ dessa steg om du vill visa status för en åtkomst granskning:

1. I **identitets styrning** klickar du på **åtkomst paket** och väljer sedan åtkomst paketet med status för åtkomst granskning som du vill kontrol lera.   

1. När du är i Översikt över åtkomst paketet klickar du på **åtkomst granskningar** på den vänstra menyn.
    
    ![Välj åtkomst granskningar](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. En lista visas som innehåller alla principer som har åtkomst granskningar kopplade till sig. Klicka på granska om du vill se rapporten.

    ![Lista över åtkomst granskningar](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. När du visar rapporten visas antalet användare som granskats och de åtgärder som vidtagits av granskaren.

    ![Visa gransknings status](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Åtkomst granskningar e-postmeddelanden
Du kan utse granskare, eller så kan användarna granska deras åtkomst själva. Som standard skickar Azure AD ett e-postmeddelande till granskare eller själv granskare strax efter att granskningen startar.

I e-postmeddelandet finns anvisningar om hur du granskar åtkomst till paket. Om granskningen är till för att användare ska kunna granska deras åtkomst visar du de instruktioner om hur du utför en själv granskning av deras åtkomst paket.
  
Om du har tilldelat gäst användare som granskare och de inte har accepterat sin gäst-inbjudan i Azure AD, får de inga e-postmeddelanden från åtkomst granskningar för Azure AD. De måste först godkänna inbjudan och skapa ett konto med Azure AD innan de kan ta emot e-postmeddelanden. 

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomst till Access-paket](entitlement-management-access-reviews-review-access.md)
- [Själv granskning av åtkomst paket](entitlement-management-access-reviews-self-review.md)
