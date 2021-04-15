---
title: Felmeddelandet visas på appsidan när du har loggat in | Microsoft Docs
description: Så här löser du problem med Azure AD-inloggning när appen returnerar ett felmeddelande.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ddebc4778d923bc3a002f14fc4b4db1b7bb730d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379306"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>En appsida visar ett felmeddelande när användaren har loggat in

I det här Azure Active Directory (Azure AD) användaren. Men programmet visar ett felmeddelande och låter inte användaren slutföra inloggningsflödet. Problemet är att appen inte accepterar det svar som Azure AD utfärdade.

Det finns flera möjliga orsaker till varför appen inte accepterar svaret från Azure AD. Om felmeddelandet inte tydligt identifierar vad som saknas i svaret kan du prova följande:

-   Om appen är Azure AD-galleriet kontrollerar du att du har följt stegen i Så här felsöker du [SAML-baserad](./debug-saml-sso-issues.md)enkel inloggning till program i Azure AD.

-   Använd ett verktyg som [Fiddler för](https://www.telerik.com/fiddler) att samla in SAML-begäran, svar och token.

-   Skicka SAML-svaret till appleverantören och fråga dem vad som saknas.

## <a name="attributes-are-missing-from-the-saml-response"></a>Attribut saknas i SAML-svaret

Följ dessa steg om du vill lägga till ett attribut i Azure AD-konfigurationen som ska skickas i Azure AD-svaret:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Längst upp i navigeringsfönstret till vänster väljer du Alla tjänster **för att** öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i filtersökrutan och välj sedan **Azure Active Directory**.

4. Välj **Företagsprogram** i Azure AD-navigeringsfönstret.

5. Välj **Alla program** för att visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app som du vill använda kan du använda **filterkontrollen** överst i **listan Med alla program.** Ange alternativet **Visa** till "Alla program".

6. Välj det program som du vill konfigurera för enkel inloggning.

7. När appen har laddats **väljer du Enkel inloggning** i navigeringsfönstret.

8. I avsnittet **Användarattribut** väljer du **Visa och redigerar alla andra användarattribut.** Här kan du ändra vilka attribut som ska skickas till appen i SAML-token när användare loggar in.

   Så här lägger du till ett attribut:

   1. Välj **Lägg till attribut.** Ange **Namn** och välj **Värde** i listrutan.

   1.  Välj **Spara**. Du ser det nya attributet i tabellen.

9. Spara konfigurationen.

   Nästa gång användaren loggar in på appen skickar Azure AD det nya attributet i SAML-svaret.

## <a name="the-app-doesnt-identify-the-user"></a>Appen identifierar inte användaren

Det går inte att logga in i appen eftersom SAML-svaret saknar ett attribut, till exempel en roll. Eller så misslyckas det eftersom appen förväntar sig ett annat format eller värde för **attributet NameID** (användaridentifierare).

Om du använder [automatisk användareablering](../app-provisioning/user-provisioning.md) i Azure AD för att skapa, underhålla och ta bort användare i appen kontrollerar du att användaren har etablerats i SaaS-appen. Mer information finns i [Inga användare etableras till ett Azure AD-galleriprogram.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Lägga till ett attribut i Azure AD-appkonfigurationen

Följ dessa steg om du vill ändra värdet för Användaridentifierare:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Välj **Alla tjänster** överst i navigeringsfönstret till vänster för att öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i filtersökrutan och välj **sedan Azure Active Directory**.

4. Välj **Företagsprogram** i Azure AD-navigeringsfönstret.

5. Välj **Alla program** för att visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app som du vill använda kan du använda **filterkontrollen** överst i **listan Med alla program.** Ange alternativet **Visa** till "Alla program".

6. Välj den app som du vill konfigurera för enkel inloggning.

7. När appen har laddats **väljer du Enkel inloggning** i navigeringsfönstret.

8. Under **Användarattribut** väljer du den unika identifieraren för användaren **i** listrutan Användaridentifierare.

## <a name="change-the-nameid-format"></a>Ändra NameID-format

Om programmet förväntar sig ett annat format för **attributet NameID** (Användaridentifierare) kan du gå till [Redigera nameID](../develop/active-directory-saml-claims-customization.md#editing-nameid) för att ändra NameID-formatet.

Azure AD väljer formatet för **NameID-attributet** (användaridentifierare) baserat på det värde som har valts eller det format som begärs av appen i SAML AuthRequest. Mer information finns i avsnittet "NameIDPolicy" i [SAML-protokollet för enkel inloggning.](../develop/single-sign-on-saml-protocol.md#nameidpolicy)

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Appen förväntar sig en annan signaturmetod för SAML-svaret

Följ dessa steg om du vill ändra vilka delar av SAML-token som signeras digitalt av Azure AD:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Välj **Alla tjänster** överst i navigeringsfönstret till vänster för att öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i filtersökrutan och välj sedan **Azure Active Directory**.

4. Välj **Företagsprogram** i Azure AD-navigeringsfönstret.

5. Välj **Alla program** för att visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser det program som du vill använda kan du använda **filterkontrollen** överst i **listan Med alla program.** Ange alternativet **Visa** till "Alla program".

6. Välj det program som du vill konfigurera för enkel inloggning.

7. När programmet har laddats **väljer du Enkel inloggning** i navigeringsfönstret.

8. Under **SAML-signeringscertifikat** väljer du **Visa avancerade inställningar för certifikatsignering.**

9. Välj **signeringsalternativ** som appen förväntar sig bland följande alternativ:

   * **Sign SAML response (Signera SAML-svar)**
   * **Signera SAML-svar och försäkran**
   * **Signera SAML-försäkran**

   Nästa gång användaren loggar in i appen signerar Azure AD den del av SAML-svaret som du har valt.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Appen förväntar sig SHA-1-signeringsalgoritmen

Som standard signerar Azure AD SAML-token med hjälp av den säkraste algoritmen. Vi rekommenderar att du inte ändrar signeringsalgoritmen till *SHA-1* om inte appen kräver SHA-1.

Följ dessa steg om du vill ändra signeringsalgoritmen:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Välj **Alla tjänster** överst i navigeringsfönstret till vänster för att öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i filtersökrutan och välj **sedan Azure Active Directory**.

4. Välj **Företagsprogram** i Azure AD-navigeringsfönstret.

5. Välj **Alla program** för att visa en lista över dina program.

   > [!NOTE]
   > Om du inte ser det program som du vill använda använder du **filterkontrollen** överst i **listan Alla program.** Ange alternativet **Visa** till "Alla program".

6. Välj den app som du vill konfigurera för enkel inloggning.

7. När appen har laddats **väljer du Enkel** inloggning i navigeringsfönstret till vänster i appen.

8. Under **SAML-signeringscertifikat** väljer du **Visa avancerade inställningar för certifikatsignering.**

9. Välj **SHA-1** som **Signeringsalgoritm.**

   Nästa gång användaren loggar in på appen signerar Azure AD SAML-token med hjälp av SHA-1-algoritmen.

## <a name="next-steps"></a>Nästa steg
[Så här felsöker du SAML-baserad enkel inloggning till program i Azure AD.](./debug-saml-sso-issues.md)