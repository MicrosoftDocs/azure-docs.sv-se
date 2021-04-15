---
title: Felsöka SAML-baserad enkel inloggning i Azure Active Directory
description: Felsöka problem med en Azure AD-app som har konfigurerats för SAML-baserad enkel inloggning.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.openlocfilehash: c4a4f7bfad4254e7c3fa5851e62532ed427ced8b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376434"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>Felsöka SAML-baserad enkel inloggning i Azure Active Directory
Om du stöter på problem när du konfigurerar ett program. Kontrollera att du har följt alla steg i självstudien för programmet. I programmets konfiguration finns det infogade dokumentation om hur du konfigurerar programmet. Dessutom kan du komma åt listan över självstudier om hur du integrerar [SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md) för en detaljerad stegvis vägledning.

## <a name="cant-add-another-instance-of-the-application"></a>Det går inte att lägga till en till instans av programmet
Om du vill lägga till en andra instans av ett program måste du kunna:
-   Konfigurera en unik identifierare för den andra instansen. Du kommer inte att kunna konfigurera samma identifierare som används för den första instansen.
-   Konfigurera ett annat certifikat än det som används för den första instansen.

Om programmet inte stöder något av ovanstående. Sedan kan du inte konfigurera en andra instans.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Det går inte att lägga till identifieraren eller svars-URL:en
Om du inte kan konfigurera identifieraren eller svars-URL:en bekräftar du att värdena identifierare och svars-URL matchar de mönster som är förkonfigurerade för programmet.

Så här känner du till mönstren som är förkonfigurerade för programmet:
1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.** Gå till steg 7. Om du redan är i bladet för programkonfiguration på Azure AD.
2. Öppna tillägget **Azure Active Directory genom** att klicka **på Alla** tjänster längst upp i den vänstra navigeringsmenyn.
3. Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objektet.
4. klicka **på Företagsprogram** Azure Active Directory vänstra navigeringsmenyn.
5. klicka **på Alla program** för att visa en lista över alla dina program.
   * Om du inte ser det program som du vill ska visas  här använder du  **filterkontrollen** överst i listan med alla program och ställer in alternativet Visa **på Alla program.**
6. Välj det program som du vill konfigurera enkel inloggning för.
7. När programmet har laddats **klickar du på Enkel** inloggning på programmets vänstra navigeringsmeny.
8. Välj **SAML-baserad inloggning** i **listrutan** Läge.
9. Gå till **textrutan Identifierare** **eller Svars-URL** under avsnittet Domän och **URL:er.**
10. Det finns tre sätt att känna till de mönster som stöds för programmet:
    * I textrutan visas de mönster som stöds som platshållare *Exempel:* <https://contoso.com> .
    * Om mönstret inte stöds visas ett rött utropstecken när du försöker ange värdet i textrutan. Om du hovrar med musen över det röda utropstecknet visas de mönster som stöds.
    * I självstudien för programmet kan du också få information om de mönster som stöds. Under avsnittet **Konfigurera enkel inloggning för Azure AD.** Gå till steget för konfigurerade värdena under **avsnittet Domän och URL:er.**

Om värdena inte matchar mönstren som är förkonfigurerade i Azure AD. Du kan:
-   Arbeta med programleverantören för att hämta värden som matchar mönstret som är förkonfigurerat i Azure AD
-   Du kan också kontakta Azure AD-teamet på eller lämna en kommentar i självstudien för att begära en uppdatering av de mönster <aadapprequest@microsoft.com> som stöds för programmet

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Var ställer jag in EntityID-format (användaridentifierare)
Du kommer inte att kunna välja det EntityID-format (användaridentifierare) som Azure AD skickar till programmet i svaret efter användarautentisering.

Azure AD väljer formatet för NameID-attributet (användaridentifierare) baserat på det värde som valts eller det format som begärs av programmet i SAML AuthRequest. Mer information finns i artikeln [Single Sign-On SAML protocol](../develop/single-sign-on-saml-protocol.md#authnrequest) under avsnittet NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Det går inte att hitta Azure AD-metadata för att slutföra konfigurationen med programmet
Följ dessa steg om du vill ladda ned programmets metadata eller certifikat från Azure AD:
1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**
2. Öppna tillägget **Azure Active Directory genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.
3. Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objektet.
4. klicka **på Företagsprogram** Azure Active Directory vänstra navigeringsmenyn.
5. klicka **på Alla program** för att visa en lista över alla dina program.
   * Om du inte ser det program som du vill ska visas  här använder du **filterkontrollen** överst i listan med alla program och anger alternativet **Visa** till **Alla program.**
6. Välj det program som du har konfigurerat enkel inloggning för.
7. När programmet har laddats klickar **du på Enkel** inloggning från programmets vänstra navigeringsmeny.
8. Gå till **avsnittet SAML-signeringscertifikat** och klicka sedan **på Hämta** kolumnvärde. Beroende på vad programmet kräver att du konfigurerar enkel inloggning kan du antingen se alternativet för att ladda ned XML-metadata eller certifikatet.

Azure AD tillhandahåller inte någon URL för att hämta metadata. Metadata kan bara hämtas som en XML-fil.

## <a name="customize-saml-claims-sent-to-an-application"></a>Anpassa SAML-anspråk som skickats till ett program
Mer information om hur du anpassar SAML-attributanspråk som skickas till ditt program finns i [Anspråksmappning i Azure Active Directory](../develop/active-directory-claims-mapping.md) för mer information.

## <a name="next-steps"></a>Nästa steg
* [Snabbstartsserie om programhantering](view-applications-portal.md)