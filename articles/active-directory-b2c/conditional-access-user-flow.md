---
title: Lägg till villkorlig åtkomst till ett användar flöde i Azure AD B2C
description: Lär dig hur du lägger till villkorlig åtkomst till dina Azure AD B2C användar flöden. Konfigurera Multi-Factor Authentication-inställningar (MFA) och principer för villkorlig åtkomst i dina användar flöden för att genomdriva principer och åtgärda riskfyllda inloggningar.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c120f343ec539783f04fe35e96891c5372c5d39
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109087"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Lägg till villkorlig åtkomst till användar flöden i Azure Active Directory B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Villkorlig åtkomst kan läggas till i Azure Active Directory B2C användar flöden för att hantera riskfyllda inloggningar till dina program. Genom att integrera identitets skydd och villkorlig åtkomst i Azure AD B2C kan du konfigurera principer som identifierar riskfyllda inloggnings beteenden och tillämpa principer som kräver ytterligare åtgärder från användaren eller administratören. Detta är de komponenter som möjliggör villkorlig åtkomst i Azure AD B2C användar flöden:

- **Användar flöde**. Skapa ett användar flöde som guidar användaren genom inloggnings-och registrerings processen. I användar flödes inställningarna anger du om du vill aktivera principer för villkorlig åtkomst när en användare följer användar flödet.
- **Program som dirigerar användare till användar flödet**. Konfigurera din app för att dirigera användare till rätt användar flöde för registrering och inloggning genom att ange användar flödets slut punkt i appen.
- **Princip för villkorlig åtkomst**. [Skapa en princip för villkorlig åtkomst](conditional-access-identity-protection-setup.md) och ange de appar som du vill att principen ska tillämpas på. När användaren följer inloggnings-eller registrerings användar flödet för din app använder den villkorliga åtkomst principen identitets skydds signaler för att identifiera riskfyllda inloggningar och visar lämplig åtgärd vid behov.

Villkorlig åtkomst stöds i de senaste versionerna av användar flöden. Du kan lägga till principer för villkorlig åtkomst för nya användar flöden när du skapar dem, eller så kan du lägga till dem i befintliga användar flöden så länge versionen stöder villkorlig åtkomst. När du lägger till villkorlig åtkomst till ett befintligt användar flöde måste du gå igenom två inställningar:

- **Multi-Factor Authentication (MFA)**: användare kan nu använda en engångs kod via SMS eller Voice, eller ett eng ång slö sen ord via e-post för Multi-Factor Authentication. MFA-inställningar är oberoende av inställningarna för villkorlig åtkomst. Du kan ställa in MFA **så att MFA alltid krävs** , oavsett konfigurationen för villkorlig åtkomst. Du kan också ange MFA till **villkorligt** så att MFA endast krävs när en aktiv princip för villkorlig åtkomst kräver det.

- **Villkorlig åtkomst**: den här inställningen bör alltid vara **aktive** ras. Normalt aktiverar du bara den **här inställningen vid** fel sökning eller migrering, eller för äldre implementeringar.

Läs mer om [identitets skydd och villkorlig åtkomst](conditional-access-identity-protection-overview.md) i Azure AD B2C eller i [så här konfigurerar du det](conditional-access-identity-protection-setup.md).

## <a name="add-conditional-access-to-a-new-user-flow"></a>Lägg till villkorlig åtkomst till ett nytt användar flöde

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **principer** väljer du **användar flöden** och väljer sedan **nytt användar flöde**.
1. På sidan **skapa ett användar flöde** väljer du typ av användar flöde.
1. Under **Välj en version** väljer du **rekommenderas** och väljer sedan **skapa**. ([Läs mer](user-flow-versions.md) om användar flödes versioner.)

    ![Sidan skapa användar flöde i Azure Portal med egenskaper markerat](./media/tutorial-create-user-flows/select-version.png)

1. Ange ett **Namn** för användarflödet. Till exempel *signupsignin1*.
1. I avsnittet **identitets leverantörer** väljer du de identitets leverantörer som du vill tillåta för det här användar flödet.
2. I avsnittet **multifaktorautentisering** väljer du önskad **MFA-metod** och under **MFA Enforcement** Select **villkorlig (rekommenderas)**.
 
   ![Konfigurera multifaktorautentisering](media/conditional-access-user-flow/configure-mfa.png)

1. I avsnittet **villkorlig åtkomst** markerar du kryss rutan **tillämpa villkorliga åtkomst principer** .

   ![Konfigurera inställningar för villkorlig åtkomst](media/conditional-access-user-flow/configure-conditional-access.png)

1. I avsnittet **användarattribut och anspråk** väljer du de anspråk och attribut som du vill samla in och skicka från användaren under registreringen. Välj till exempel **Visa mer** och välj sedan attribut och anspråk för **land/region** och **visnings namn**. Välj **OK**.

    ![Sidan för attribut och val av anspråk med tre anspråk valda](./media/conditional-access-user-flow/configure-user-attributes-claims.png)

1. Klicka på **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* anpassningsprefix automatiskt till namnet.

## <a name="add-conditional-access-to-an-existing-user-flow"></a>Lägg till villkorlig åtkomst till ett befintligt användar flöde

> [!NOTE]
> Det befintliga användar flödet måste vara en version som stöder villkorlig åtkomst. Dessa användar flödes versioner är märkta som **rekommenderas**.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

1. I Azure Portal söker du efter och väljer **Azure AD B2C**.

1. Under **principer** väljer du **användar flöden**. Välj sedan användar flödet.

1. Välj **Egenskaper** och kontrol lera att användar flödet stöder villkorlig åtkomst genom att välja **Egenskaper** och leta efter den **villkorliga åtkomst** inställningen.
 
   ![Konfigurera MFA och villkorlig åtkomst i egenskaper](media/conditional-access-user-flow/add-conditional-access.png)

1. I avsnittet **multifaktorautentisering** väljer du önskad **MFA-metod** och under **MFA Enforcement** Select **villkorlig (rekommenderas)**.
 
1. I avsnittet **villkorlig åtkomst** markerar du kryss rutan **tillämpa villkorliga åtkomst principer** .

1. Välj **Spara**.

## <a name="test-the-user-flow"></a>Testa användar flödet

Om du vill testa villkorlig åtkomst i ditt användar flöde [skapar du en princip för villkorlig åtkomst](conditional-access-identity-protection-setup.md) och aktiverar villkorlig åtkomst i ditt användar flöde enligt beskrivningen ovan. 

### <a name="prerequisites"></a>Förutsättningar

- Azure AD B2C Premium 2 krävs för att skapa riskfyllda inloggnings principer. Premium P1-klienter kan skapa plats-, app-eller gruppbaserade principer.
- I test syfte kan du [Registrera test webb programmet](tutorial-register-applications.md) `https://jwt.ms` , som är ett Microsoft-ägda webb program som visar det avkodade innehållet i en token (innehållet i token aldrig lämnar webbläsaren). 
- Om du vill simulera en riskfylld inloggning laddar du ned TOR webbläsaren och försöker logga in på användar flödets slut punkt.
- [Skapa en princip för villkorlig åtkomst](conditional-access-identity-protection-setup.md)med följande inställningar:
   
   - För **användare och grupper** väljer du test användaren (Välj inte **alla användare** eller blockera dig själv från att logga in).
   - För **molnappar eller åtgärder** väljer du **Välj appar** och väljer sedan ditt förlitande parts program.
   - För villkor väljer du **inloggnings risk** , **hög**, **medel** och **låg** risk nivå.
   - För **beviljande** väljer du **blockera åtkomst**.

      ![Riskidentifieringar](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

### <a name="run-the-user-flow"></a>Kör användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**. Under **program** väljer du *webapp1*. **Svars-URL: en** ska visas `https://jwt.ms` .

   ![Sidan kör användar flöde i portalen med knappen Kör användar flöde markerat](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Kopiera URL-adressen under **Kör användar flödets slut punkt**.

1. För att simulera en riskfylld inloggning öppnar du [webbläsaren Tor](https://www.torproject.org/download/) och använder den URL som du kopierade i förhands gransknings steget för att logga in på den registrerade appen.

1. Ange den begärda informationen på inloggnings sidan och försök sedan logga in. Token returneras till `https://jwt.ms` och ska visas för dig. I jwt.ms-kodad token bör du se att inloggningen blockerades:

   ![Testa en blockerad inloggning](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="next-steps"></a>Nästa steg

[Anpassa användar gränssnittet i ett Azure AD B2C användar flöde](customize-ui-with-html.md)
