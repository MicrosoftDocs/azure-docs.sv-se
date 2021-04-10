---
title: Fel meddelande visas på App-sidan efter att du har loggat in | Microsoft Docs
description: Så här löser du problem med Azure AD-inloggning när appen returnerar ett fel meddelande.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c96209f33491645510d8592997c418472d4f227c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258820"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>En appsida visar ett felmeddelande när användaren har loggat in

I det här scenariot signerar Azure Active Directory (Azure AD) användaren i. Men programmet visar ett fel meddelande och tillåter inte att användaren avslutar inloggnings flödet. Problemet är att appen inte accepterade svaret som Azure AD har utfärdat.

Det finns flera möjliga orsaker till varför appen inte accepterade svaret från Azure AD. Om fel meddelandet inte tydligt identifierar vad som saknas i svaret kan du prova följande:

-   Om appen är Azure AD-galleriet kontrollerar du att du har följt stegen i [FELSÖKA SAML-baserad enkel inloggning till program i Azure AD](./debug-saml-sso-issues.md).

-   Använd ett verktyg som [Fiddler](https://www.telerik.com/fiddler) för att avbilda SAML-begäran, svar och token.

-   Skicka SAML-svaret till app-leverantören och fråga vad som saknas.

## <a name="attributes-are-missing-from-the-saml-response"></a>Attribut saknas från SAML-svaret

Följ dessa steg om du vill lägga till ett attribut i Azure AD-konfigurationen som kommer att skickas i Azure AD-svaret:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Längst upp i navigerings fönstret på vänster sida väljer du **alla tjänster** för att öppna tillägget Azure AD.

3. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory**.

4. Välj **företags program** i navigerings fönstret i Azure AD.

5. Välj **alla program** om du vill visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app du vill använda använder du **filter** kontrollen längst upp i **listan Alla program**. Ange alternativet **Visa** som "alla program".

6. Välj det program som du vill konfigurera för enkel inloggning.

7. När appen har lästs in väljer du **enkel inloggning** i navigerings fönstret.

8. I avsnittet **användarattribut** väljer du **Visa och redigera alla andra** användarattribut. Här kan du ändra vilka attribut som ska skickas till appen i SAML-token när användarna loggar in.

   Så här lägger du till ett attribut:

   1. Välj **Lägg till attribut**. Ange **namnet** och välj **värdet** i list rutan.

   1.  Välj **Spara**. Du ser det nya attributet i tabellen.

9. Spara konfigurationen.

   Nästa gången användaren loggar in i appen skickar Azure AD det nya attributet i SAML-svaret.

## <a name="the-app-doesnt-identify-the-user"></a>Appen identifierar inte användaren

Det går inte att logga in på appen eftersom SAML-svaret saknar attribut, till exempel en roll. Eller också Miss lyckas det eftersom appen förväntar sig ett annat format eller värde för attributet **NameID** (användar-ID).

Om du använder [automatiserad användar etablering i Azure AD](../app-provisioning/user-provisioning.md) för att skapa, underhålla och ta bort användare i appen, kontrollerar du att användaren har etablerats till SaaS-appen. Mer information finns i [inga användare som tillhandahålls till ett Azure AD Gallery-program](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Lägg till ett attribut i Azure AD-appens konfiguration

Följ dessa steg om du vill ändra värdet för användar-ID:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Välj **alla tjänster** överst i navigerings fönstret på vänster sida för att öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory**.

4. Välj **företags program** i navigerings fönstret i Azure AD.

5. Välj **alla program** om du vill visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app du vill använda använder du **filter** kontrollen längst upp i **listan Alla program**. Ange alternativet **Visa** som "alla program".

6. Välj den app som du vill konfigurera för SSO.

7. När appen har lästs in väljer du **enkel inloggning** i navigerings fönstret.

8. Under **användarattribut** väljer du den unika identifieraren för användaren i list rutan **användar identifierare** .

## <a name="change-the-nameid-format"></a>Ändra NameID-formatet

Om programmet förväntar sig ett annat format för attributet **NameID** (användar identifierare) läser du [Redigera NameID](../develop/active-directory-saml-claims-customization.md#editing-nameid) för att ändra NameID-formatet.

Azure AD väljer formatet för attributet **NameID** (användar-ID) baserat på det värde som är markerat eller det format som har begärts av appen i SAML-AuthRequest. Mer information finns i avsnittet "NameIDPolicy" i [SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Appen förväntar sig en annan Signaturtyp för SAML-svaret

Följ dessa steg om du vill ändra vilka delar av SAML-token som signerats digitalt av Azure AD:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Välj **alla tjänster** överst i navigerings fönstret på vänster sida för att öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory**.

4. Välj **företags program** i navigerings fönstret i Azure AD.

5. Välj **alla program** om du vill visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser det program som du vill använda använder du **filter** kontrollen längst upp i **listan Alla program**. Ange alternativet **Visa** som "alla program".

6. Välj det program som du vill konfigurera för enkel inloggning.

7. När programmet har lästs in väljer du **enkel inloggning** i navigerings fönstret.

8. Under **SAML-signerings certifikat** väljer du  **Visa avancerade inställningar för certifikat signering**.

9. Välj det **signerings alternativ** som appen förväntar sig bland dessa alternativ:

   * **Signera SAML-svar**
   * **Signera SAML-svar och kontroll**
   * **Signera SAML-kontroll**

   Nästa gången användaren loggar in i appen, kommer Azure AD att signera den del av det SAML-svar som du har valt.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Appen förväntar sig SHA-1-signeringsalgoritmen

Som standard signerar Azure AD SAML-token med den mest säkra algoritmen. Vi rekommenderar att du inte ändrar signeringsalgoritmen till *SHA-1* om inte appen kräver SHA-1.

Följ dessa steg om du vill ändra signeringsalgoritmen:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Välj **alla tjänster** överst i navigerings fönstret på vänster sida för att öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory**.

4. Välj **företags program** i navigerings fönstret i Azure AD.

5. Välj **alla program** om du vill visa en lista över dina program.

   > [!NOTE]
   > Om du inte ser det program som du vill använda använder du **filter** kontrollen längst upp i **listan Alla program**. Ange alternativet **Visa** som "alla program".

6. Välj den app som du vill konfigurera för enkel inloggning.

7. När appen har lästs in väljer du **enkel inloggning** från navigerings fönstret till vänster i appen.

8. Under **SAML-signerings certifikat** väljer du **Visa avancerade inställningar för certifikat signering**.

9. Välj **SHA-1** som **Signeringsalgoritm**.

   Nästa gången användaren loggar in i appen signerar Azure AD SAML-token med hjälp av SHA-1-algoritmen.

## <a name="next-steps"></a>Nästa steg
[FELSÖKA SAML-baserad enkel inloggning till program i Azure AD](./debug-saml-sso-issues.md).