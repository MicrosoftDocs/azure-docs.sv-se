---
title: Godkänn eller neka åtkomst begär Anden – hantering av Azure AD-berättigande
description: Lär dig hur du använder min åtkomst Portal för att godkänna eller Neka förfrågningar till ett Access-paket i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: fddb3b171e5a26273cb2e0045f11e3a4dbb48c5f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655109"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Godkänn eller neka åtkomst begär anden i hantering av Azure AD-rättigheter

Med hantering av Azure AD-rättighet kan du konfigurera principer för att kräva godkännande för åtkomst paket och välja en eller flera god kännare. I den här artikeln beskrivs hur utsedda god kännare kan godkänna eller Neka förfrågningar om åtkomst paket.

## <a name="open-request"></a>Öppen förfrågan

Det första steget för att godkänna eller neka åtkomst begär Anden är att hitta och öppna åtkomstbegäran som väntar på godkännande. Det finns två sätt att öppna åtkomstbegäran.

**Nödvändig roll:** God kännare

1. Sök efter ett e-postmeddelande från Microsoft Azure som ber dig godkänna eller neka en begäran. Här är ett exempel på ett e-postmeddelande:

    ![Godkänn begäran om åtkomst till paket e-post](./media/entitlement-management-shared/approver-request-email.png)

1. Klicka på länken **Godkänn eller neka begäran** för att öppna åtkomstbegäran.

1. Logga in på portalen för åtkomst.

Om du inte har e-postmeddelandet kan du hitta åtkomst förfrågningar som väntar på ditt godkännande genom att följa dessa steg.

1. Logga in på min åtkomst-Portal på [https://myaccess.microsoft.com](https://myaccess.microsoft.com) .  (För amerikanska myndigheter är domänen i min åtkomst Portal länk `myaccess.microsoft.us` .)

1. På den vänstra menyn klickar du på **godkännanden** för att se en lista över åtkomst förfrågningar som väntar på godkännande.

1. På fliken **väntande** söker du efter begäran.

## <a name="view-requestors-answers-to-questions-preview"></a>Visa svar på fråge ställare (för hands version)

1. Gå till fliken **godkännanden** i min åtkomst.

1. Gå till den begäran som du vill godkänna och klicka på **information**. Du kan också klicka på **Godkänn** eller **neka** om du är redo att fatta ett beslut.

1. Klicka på **information om begäran**.

    ![Min åtkomst Portal – åtkomstbegäran – Klicka på information om begäran](./media/entitlement-management-request-approve/requestor-information-request-details.png)

1. Informationen från beställaren visas längst ned i panelen.

    ![Skärm bild som visar information om begäran](./media/entitlement-management-request-approve/requestor-information-requestor-answers.png)

1. Utifrån den information som begärren tillhandahöll kan du sedan godkänna eller avvisa begäran. Se stegen i godkänna eller neka begäran om vägledning.

## <a name="approve-or-deny-request"></a>Godkänn eller neka begäran

När du har öppnat en åtkomstbegäran som väntar på godkännande kan du se information som gör det lättare att godkänna eller avvisa beslutet.

**Nödvändig roll:** God kännare

1. Klicka på länken **Visa** för att öppna fönstret åtkomstbegäran.

1. Klicka på **information** om du vill visa information om åtkomstbegäran.

    Informationen omfattar användarens namn, organisation, start-och slutdatum för åtkomst om det tillhandahålls, affärs justering, när begäran skickades och när begäran upphör att gälla.

1. Klicka på **Godkänn** eller **neka**.

1. Ange en orsak vid behov.

    ![Skärm bild som visar sidan där du accepterar eller nekar begäran.](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Klicka på **Skicka** för att skicka in ditt beslut.

    Om en princip har kon figurer ATS med flera god kännare behöver endast en god kännare fatta ett beslut om det väntande godkännandet. När en god kännare har skickat sitt beslut till åtkomstbegäran slutförs begäran och är inte längre tillgänglig för de andra god kännarna att godkänna eller neka begäran. De andra god kännarna kan se förfrågnings beslutet och besluts fattaren i sin åtkomst Portal. För närvarande stöds endast enstegs-godkännande.

    Om ingen av de konfigurerade god kännarna kan godkänna eller neka åtkomstbegäran förfaller begäran efter den konfigurerade begär ande tiden. Användaren får ett meddelande om att deras åtkomst förfrågan har upphört att gälla och att de behöver skicka åtkomstbegäran igen.

## <a name="next-steps"></a>Nästa steg

- [Begär åtkomst till ett Access-paket](entitlement-management-request-access.md)
- [Begär process och e-postmeddelanden](entitlement-management-process.md)
