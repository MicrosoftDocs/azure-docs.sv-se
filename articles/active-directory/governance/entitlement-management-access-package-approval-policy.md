---
title: Ändra godkännande inställningarna för ett Access-paket i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig hur du ändrar inställningar för godkännande och begär ande information för ett Access-paket i Azure Active Directory rättighets hantering.
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
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48b5260e883d85899953240f6ee4f83127681c9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97591314"
---
# <a name="change-approval-and-requestor-information-preview-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Ändra godkännande-och beställarens information (för hands version) inställningar för ett Access-paket i Azure AD-hantering av rättigheter

Som en Access Package Manager kan du när som helst ändra inställningarna för godkännande och begär ande information för ett åtkomst paket genom att redigera en befintlig princip eller lägga till en ny princip.

I den här artikeln beskrivs hur du ändrar inställningarna för godkännande och begär ande information för ett befintligt Access-paket.

## <a name="approval"></a>Godkännande

I avsnittet godkännande anger du om ett godkännande krävs när användare begär det här åtkomst paketet. Godkännande inställningarna fungerar på följande sätt:

- Endast en av de valda god kännarna eller reserv god kännaren måste godkänna en begäran om godkännande av en enskild fas. 
- Endast en av de valda god kännarna från varje steg måste godkänna en begäran om godkännande med två steg.
- God kännaren kan vara ansvarig, intern sponsor eller extern sponsor beroende på vilken princip som styr åtkomsten.
- Godkännande från varje vald god kännare krävs inte för ett eller 2-stegs godkännande.
- Godkännande beslutet baseras på vilken god kännare som granskar begäran först.

En demonstration av hur du lägger till god kännare i en princip för begäran finns i följande videoklipp:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

En demonstration av hur du lägger till ett godkännande med flera steg i en princip för begäran finns i följande videoklipp:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]


## <a name="change-approval-settings-of-an-existing-access-package"></a>Ändra godkännande inställningar för ett befintligt Access-paket

Följ de här stegen för att ange godkännande inställningarna för begär Anden för Access-paketet:

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Välj antingen en princip för att redigera eller lägga till en ny princip i Access-paketet
    1. Klicka på **principer** och **Lägg sedan till en princip** om du vill skapa en ny princip.
    1. Klicka på den princip som du vill redigera och klicka sedan på **Redigera**.

1. Gå till fliken **begäran** .

1. Om du vill kräva godkännande för förfrågningar från de valda användarna anger du alternativet **Kräv godkännande** för att växla till **Ja**. Eller, om du vill att begär Anden ska godkännas automatiskt, anger du växla till **Nej**.

1. Om du vill kräva att användarna anger en motivering för att begära åtkomst paketet, ställer du in **justeringen Kräv ändring av begär ande** för att växla till **Ja**.
    
1. Kontrol lera nu om begär Anden kräver ett enda eller 2-stegs godkännande. Ange **hur många steg** som ska växlas till **1** för godkännande av enstaka steg eller ange växla till **2** för godkännande i två steg.

    ![Åtkomst till paket-begär Anden – godkännande inställningar](./media/entitlement-management-access-package-approval-policy/approval.png)


Använd följande steg för att lägga till god kännare när du har valt hur många steg du behöver: 

### <a name="single-stage-approval"></a>Godkännande med ett enda steg

1. Lägg till den **första god kännaren**:
    
    Om principen är inställd för att styra åtkomsten för användare i din katalog, kan du välja **chef som god kännare**. Eller Lägg till en speciell användare genom att klicka på **Lägg till god kännare** när du har valt Välj vissa god kännare i list menyn.
    
    ![Åtkomst till paket-begär Anden – för användare i katalog-första god kännare](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    Om den här principen är inställd för att styra åtkomsten för användare som inte finns i din katalog, kan du välja **extern sponsor** eller **intern sponsor**. Eller Lägg till en speciell användare genom att klicka på **Lägg till god kännare** eller grupper under Välj vissa god kännare.
    
    ![Åtkomst till paket-begär Anden – för användare som inte är i katalog-första god kännare](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. Om du har valt **hanterare** som den första god kännaren klickar du på **Lägg till reserv** för att välja en eller flera användare eller grupper i din katalog som en reserv god kännare. Reserv god kännare får begäran om hantering av rättigheter inte kan hitta chefen för användaren som begär åtkomst.

    Hanteraren hittas genom rättighets hantering med attributet **Manager** . Attributet finns i användarens profil i Azure AD. Mer information finns i [lägga till eller uppdatera en användares profil information med hjälp av Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).

1. Om du har valt **Välj en viss god kännare** klickar du på **Lägg till god kännare** för att välja en eller flera användare eller grupper i din katalog som ska vara god kännare.

1. I rutan under **beslut måste fattas i hur många dagar?** anger du antalet dagar som en god kännare måste granska en begäran om det här åtkomst paketet.

    Om en begäran inte godkänns inom den här tids perioden kommer den automatiskt att nekas. Användaren måste skicka en annan begäran om åtkomst paketet.

1. Om du vill att god kännare ska kunna ge en motivering för sitt beslut anger du **Ja** som krav på god kännare.

    Motiveringen är synlig för andra god kännare och begär Ande.

### <a name="2-stage-approval"></a>2 – steg godkännande

Om du valde ett 2-stegs godkännande måste du lägga till en andra god kännare.

1. Lägg till den **andra god kännaren**: 
    
    Om användarna finns i din katalog lägger du till en speciell användare som den andra god kännaren genom att klicka på **Lägg till god kännare** under Välj vissa god kännare.

    ![Åtkomst till paket-begär Anden – för användare i katalog-andra god kännare](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    Om användarna inte finns i din katalog väljer du **intern sponsor** eller **extern sponsor** som den andra god kännaren. När du har valt god kännaren lägger du till reserv god kännarna.

    ![Åtkomst till paket-begär Anden – för användare utanför katalog-andra god kännare](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. Ange antalet dagar som den andra god kännaren måste godkänna begäran i rutan under **beslut måste fattas i hur många dagar?**. 

1. Ange justeringen Kräv god kännare för att växla till **Ja** eller **Nej**.

### <a name="alternate-approvers"></a>Alternativa god kännare

Du kan ange alternativa god kännare, på samma sätt som du anger de första och andra god kännarna som kan godkänna begär Anden. Om du har alternativa god kännare kan du se till att förfrågningarna godkänns eller nekas innan de upphör att gälla (tids gräns). Du kan visa en lista över alternativa god kännare för den första god kännaren och andra god kännaren för godkännande i två steg. 

Genom att ange alternativa god kännare, i händelse av att de första eller andra god kännarna inte kunde godkänna eller neka begäran, vidarebefordras den väntande begäran till de alternativa god kännarna, enligt det vidarebefordrings schema du angav under princip konfigurationen. De får ett e-postmeddelande för att godkänna eller neka den väntande begäran.

När begäran har vidarebefordrats till alternativa god kännare kan de första eller andra god kännarna fortfarande godkänna eller neka begäran. Alternativa god kännare använder samma åtkomst webbplats för att godkänna eller neka den väntande begäran.

Vi kan lista personer eller grupper med personer som ska vara god kännare och alternativa god kännare. Se till att du listar olika uppsättningar med personer som de första, andra och alternativa god kännarna.
Om du till exempel har listat Alice och Bob som första god kännare, anger du Carol och Dave som alternativa god kännare. Använd följande steg för att lägga till alternativa god kännare i ett Access-paket:

1. Under den första god kännaren, andra god kännaren eller båda klickar du på **Visa avancerade inställningar för begäran**.

    :::image type="content" source="media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png" alt-text="Åtkomst paket – princip – Visa avancerade inställningar för begäran":::

1. Ange **om ingen åtgärd har vidtagits, vidarebefordra till alternativa god kännare?** växla till **Ja**.

1. Klicka på **Lägg till alternativa god kännare** och välj alternativa god kännare i listan.

    ![Åtkomst paket – princip – Lägg till alternativa god kännare](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

    Om du väljer chef som god kännare för den första god kännaren har du ytterligare ett alternativ, **andra nivå ansvarig som alternativ god kännare**, tillgängliga för att välja i fältet alternativ god kännare. Om du väljer det här alternativet måste du lägga till en reserv god kännare för att vidarebefordra begäran till om systemet inte kan hitta den andra nivå hanteraren.

1. I rutan **vidarebefordra till alternativ god kännare efter hur många dagar** som god kännaren måste godkänna eller neka en begäran. Om inga god kännare har godkänt eller nekat begäran före varaktigheten för begäran upphör begäran att gälla (tids gräns) och användaren måste skicka en annan begäran om åtkomst paketet. 

    Förfrågningar kan bara vidarebefordras till alternativa god kännare per dag efter det att varaktigheten för begäran har nått halva livs längden och beslutet för huvud god kännaren (erna) måste ha uppnått timeout efter minst fyra dagar. Om tids gränsen för begäran är mindre än eller lika med 3, finns det inte tillräckligt med tid för att vidarebefordra begäran till alternativa god kännare. I det här exemplet är varaktigheten för begäran 14 dagar. Det innebär att varaktigheten för begäran når en halv livs längd på dag 7. Begäran kan därför inte vidarebefordras tidigare än dag 8. Begär Anden kan inte vidarebefordras den sista dagen i varaktigheten för begäran. I exemplet kan den senaste förfrågan vidarebefordras dag 13.

## <a name="enable-requests"></a>Aktivera begär Anden

1. Om du vill att Access-paketet ska göras omedelbart tillgängligt för användare i förfrågnings principen att begära, flytta aktivera växla till **Ja**.

    Du kan alltid aktivera det i framtiden när du är klar med att skapa åtkomst paketet.

    Om du har valt **Ingen (endast administratörers direkta tilldelningar)** och du anger Tillåt till **Nej** kan administratörer inte tilldela det här åtkomst paketet direkt.

    ![Åtkomst paket-princip-aktivera princip inställning](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Klicka på **Nästa**.

## <a name="collect-additional-requestor-information-for-approval-preview"></a>Samla in ytterligare information om begär Ande för godkännande (för hands version)

För att se till att användarna får åtkomst till rätt åtkomst paket, kan du kräva att beställare besvarar egna textfält eller flera alternativ frågor vid tidpunkten för begäran. Det finns en gräns på 20 frågor per princip och en gräns på 25 svar för flera alternativ frågor. Frågorna visas sedan för god kännare för att hjälpa dem att fatta ett beslut.

1. Gå till fliken **information om beställare** och klicka på fliken **frågor** .
 
1. Skriv in vad du vill be beställaren, även kallat visnings strängen, för frågan i rutan **fråga** .

    ![Åtkomst paket – princip – aktivera inställning för begär ande information](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. Om communityn för användare som behöver åtkomst till Access-paketet inte har något gemensamt språk, kan du förbättra upplevelsen för användare som begär åtkomst på myaccess.microsoft.com. För att förbättra upplevelsen kan du ange alternativa visnings strängar för olika språk. Om en användares webbläsare till exempel är inställd på spanska och du har konfigurerat spanska visnings strängar, visas dessa strängar för användaren som begär det. Om du vill konfigurera lokalisering för förfrågningar klickar du på **Lägg till lokalisering**.
    1. I fönstret **Lägg till lokaliseringar för fråga** väljer du **språk koden** för det språk som du vill lokalisera frågan i.
    1. Skriv frågan i den **lokaliserade text** rutan på det språk som du har konfigurerat.
    1. När du har lagt till alla nödvändiga lokaliseringar klickar du på **Spara**.

    ![Åtkomst paket – princip – konfigurera lokaliserad text](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. Välj det **svars format** som du vill att beställare ska besvara. Svars formaten är: *kort text*, *flera alternativ* och *långt text*.
 
    ![Åtkomst paket-princip – Välj Visa och redigera flera alternativ svars format](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. Om du väljer flera alternativ klickar du på knappen **Visa och redigera** för att konfigurera svars alternativen.
    1. När du har valt Visa och redigera fönstret **Visa/redigera fråga** öppnas.
    1. Ange de svars alternativ som du vill ge den begär Ande när du besvarar frågan i rutan **svars värden** .
    1. Skriv in så många svar som du behöver och klicka sedan på **Spara**.
    
    ![Åtkomst paket-princip – ange flera alternativ](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. Om du vill kräva att beställare besvarar den här frågan när du begär åtkomst till ett Access-paket, klickar du på kryss rutan under **obligatorisk**.

1. Fyll i de återstående flikarna (t. ex. livs cykel) utifrån dina behov.

När du har konfigurerat information om beställaren i din princip för åtkomst paket, kan visa svars svar på frågorna. Vägledning om hur du kan se information om beställare finns i [Visa svar på frågor om beställare (för hands version)](entitlement-management-request-approve.md#view-requestors-answers-to-questions-preview).

## <a name="next-steps"></a>Nästa steg
- [Ändra livs cykel inställningar för ett Access-paket](entitlement-management-access-package-lifecycle-policy.md)
- [Visa begär Anden för ett Access-paket](entitlement-management-access-package-requests.md)
