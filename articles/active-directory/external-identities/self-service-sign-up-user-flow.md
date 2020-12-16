---
title: Lägga till ett användar flöde för självbetjänings registrering – Azure AD
description: Skapa användar flöden för appar som har skapats av din organisation. Sedan kan användare som besöker appen få ett gäst konto med hjälp av de alternativ som kon figurer ATS i användar flödet.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f76f4a3e5fc87420c242c693e3c48a91244641e0
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560041"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app-preview"></a>Lägga till ett användar flöde för självbetjänings registrering i en app (för hands version)
> [!NOTE]
> Självbetjänings registrering är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

Du kan skapa användar flöden för appar som har skapats av din organisation. Genom att associera ditt användar flöde med ett program kan du aktivera registrering i appen. Du kan välja mer än ett program som ska associeras med användar flödet. När du associerar användar flödet med ett eller flera program kommer användare som besöker appen kunna registrera sig och få ett gäst konto med hjälp av de alternativ som kon figurer ATS i användar flödet.

> [!NOTE]
> Du kan koppla användar flöden till appar som skapats av din organisation. Användar flöden kan inte användas för Microsoft-appar, t. ex. SharePoint eller team.

## <a name="before-you-begin"></a>Innan du börjar

### <a name="add-social-identity-providers-optional"></a>Lägg till providrar för sociala identiteter (valfritt)

Azure AD är standard identitets leverantören för självbetjänings registrering. Det innebär att användarna kan registrera sig som standard med ett Azure AD-konto. Sociala identitets leverantörer kan också ingå i dessa registrerings flöden för att stödja Google-och Facebook-konton.

- [Lägg till Facebook i listan över sociala identitets leverantörer](facebook-federation.md)
- [Lägg till Google i din lista över sociala identitets leverantörer](google-federation.md)

> [!NOTE]
> Om ett självbetjänings registrerings användar flöde är associerat med en app i den aktuella för hands versionen och du skickar en användare en inbjudan till appen, kommer användaren inte att kunna använda ett Gmail-konto för att lösa in inbjudan. Som en lösning kan användaren gå igenom registrerings processen för självbetjäning. Eller så kan de lösa in inbjudan genom att komma åt en annan app eller genom att använda deras mina apps-Portal på https://myapps.microsoft.com .

### <a name="define-custom-attributes-optional"></a>Definiera anpassade attribut (valfritt)

Användarattribut är värden som samlas in från användaren under självbetjänings registrering. Azure AD levereras med en inbyggd uppsättning attribut, men du kan skapa anpassade attribut för användning i ditt användar flöde. Du kan också läsa och skriva attributen med hjälp av Microsoft Graph-API: et. Se [definiera anpassade attribut för användar flöden](user-flow-add-custom-attributes.md).

## <a name="enable-self-service-sign-up-for-your-tenant"></a>Aktivera självbetjänings registrering för din klient organisation

Innan du kan lägga till ett användar flöde för självbetjänings registrering i dina program måste du aktivera funktionen för din klient. När den är aktive rad blir kontrollerna tillgängliga i det användar flöde som gör att du kan koppla användar flödet till ett program.

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Under **Azure-tjänster** väljer du **Azure Active Directory**.
3. Välj **användar inställningar** och välj **Hantera inställningar för externt samarbete** under **externa användare**.
4. Ange alternativet för att aktivera självbetjäning för **gäst registrering via användar flöden (förhands granskning)** till **Ja**.

   ![Aktivera självbetjänings registrering för gäst](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)
5. Välj **Spara**.
## <a name="create-the-user-flow-for-self-service-sign-up"></a>Skapa användar flödet för självbetjänings registrering

Därefter skapar du användar flödet för självbetjänings registrering och lägger till det i ett program.

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Under **Azure-tjänster** väljer du **Azure Active Directory**.
3. På den vänstra menyn väljer du **externa identiteter**.
4. Välj **användar flöden (förhands granskning)** och välj sedan **nytt användar flöde**.

   ![Knappen Lägg till ett nytt användar flöde](media/self-service-sign-up-user-flow/new-user-flow.png)

5. På sidan **skapa** anger du ett **namn** för användar flödet. Observera att namnet föregås automatiskt av **B2X_1_**.
6. I listan **identitets leverantörer** väljer du en eller flera identitets leverantörer som dina externa användare kan använda för att logga in i ditt program. **Azure Active Directory registreringen** är markerad som standard. (Mer information om hur du lägger till identitets leverantörer finns i [innan du börjar](#before-you-begin) tidigare i den här artikeln.)
7. Under **användarattribut** väljer du de attribut som du vill samla in från användaren. För ytterligare attribut väljer du **Visa fler**. Välj till exempel **Visa mer** och välj sedan attribut och anspråk för **land/region**, **visnings namn** och **post nummer**. Välj **OK**.

   ![Sidan skapa ett nytt användar flöde](media/self-service-sign-up-user-flow/create-user-flow.png)

8. Välj **Skapa**.
9. Det nya användar flödet visas i listan **användar flöden (för hands version)** . Om det behövs uppdaterar du sidan.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>Välj layout för attributet samlings formulär

Du kan välja i vilken ordning attributen ska visas på registrerings sidan. 

1. Välj **Azure Active Directory** i [Azure-portalen](https://portal.azure.com).
2. Välj **externa identiteter**, Välj **användar flöden (förhands granskning)**.
3. Välj användar flödet för självbetjänings registrering i listan.
4. Under **Anpassa** väljer du **sidlayouter.**
5. De attribut som du väljer att samla in visas. Om du vill ändra visnings ordningen väljer du ett attribut och väljer sedan **Flytta upp**, **Flytta ned**, **Flytta till början** eller **Flytta längst ned**.
6. Välj **Spara**.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>Lägga till program till användar flödet för självbetjänings registrering

Nu kan du koppla program till användar flödet.

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Under **Azure-tjänster** väljer du **Azure Active Directory**.
3. På den vänstra menyn väljer du **externa identiteter**.
4. Under självbetjänings **registrering** väljer du **användar flöden (för hands version)**.
5. Välj användar flödet för självbetjänings registrering i listan.
6. På den vänstra menyn väljer du **program** under **Använd**.
7. Välj **Lägg till program**.

   ![Tilldela ett program till användar flödet](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. Välj programmet i listan. Eller Använd sökrutan för att hitta programmet och markera det.
9. Klicka på **Välj**.

## <a name="next-steps"></a>Nästa steg

- [Lägg till Google i din lista över sociala identitets leverantörer](google-federation.md)
- [Lägg till Facebook i listan över sociala identitets leverantörer](facebook-federation.md)
- [Använd API-kopplingar för att anpassa och utöka dina användar flöden via webb-API: er](api-connectors-overview.md)
- [Lägg till anpassat godkännande arbets flöde i ditt användar flöde](self-service-sign-up-add-approvals.md)
