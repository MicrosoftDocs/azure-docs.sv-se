---
title: Riskfylldt användar inloggnings skydd i Azure Active Directory
description: I den här självstudien får du lära dig att aktivera Azure Identity Protection för att skydda användare när riskfyllda inloggnings beteenden upptäcks på deras konto.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97aabfa39954aa6ba937166eb54c05ac4874ea7e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741346"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-ad-multi-factor-authentication-or-password-changes"></a>Självstudie: använda risk identifieringar för användar inloggningar för att utlösa Azure AD-Multi-Factor Authentication eller lösen ords ändringar

För att skydda dina användare kan du konfigurera riskfyllda principer i Azure Active Directory (Azure AD) som automatiskt svarar på riskfyllda beteenden. Azure AD Identity Protection principer kan automatiskt blockera ett inloggnings försök eller kräva ytterligare åtgärder, till exempel kräva en lösen ords ändring eller en uppvarning för Azure AD Multi-Factor Authentication. Dessa principer fungerar med befintliga principer för villkorlig åtkomst i Azure AD som ett extra skydds lager för din organisation. Användare får aldrig utlösa ett riskfylldt beteende i någon av dessa principer, men din organisation skyddas om ett försök att skada din säkerhet görs.

> [!IMPORTANT]
> I den här självstudien visas en administratör för att aktivera riskfyllda Azure AD-Multi-Factor Authentication.
>
> Om ditt IT-team inte har aktiverat möjligheten att använda Azure AD Multi-Factor Authentication eller om du har problem under inloggningen kan du kontakta supportavdelningen för ytterligare hjälp.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förstå de tillgängliga principerna för Azure AD Identity Protection
> * Aktivera registrering av Azure AD-Multi-Factor Authentication
> * Aktivera riskbaserade lösenordsändringar
> * Aktivera riskbaserad multifaktorautentisering
> * Testa riskfyllda principer för användar inloggnings försök

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En fungerande Azure AD-klient med minst en Azure AD Premium P2-eller utvärderings licens aktive rad.
    * Om det behövs kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett konto med *Global administratörs* behörighet.
* Azure AD har kon figurer ATS för självbetjäning för återställning av lösen ord och Azure AD Multi-Factor Authentication
    * Om det behövs [Slutför du självstudien för att aktivera Azure AD SSPR](tutorial-enable-sspr.md).
    * Om det behövs [Slutför du självstudien för att aktivera Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

## <a name="overview-of-azure-ad-identity-protection"></a>Översikt över Azure AD Identity Protection

Varje dag samlar Microsoft in och analyserar biljoner anonymiserats-signaler som en del av användarnas inloggnings försök. Dessa signaler hjälper till att bygga mönster av välsamma användar inloggnings beteende och identifiera potentiella inloggnings försök. Azure AD Identity Protection kan granska användar inloggnings försök och vidta ytterligare åtgärder om det finns något misstänkt beteende:

En del av följande åtgärder kan utlösa Azure AD Identity Protection risk identifiering:

* Användare med läckta autentiseringsuppgifter.
* Inloggningar från anonyma IP-adresser.
* Omöjlig resa till ovanlig platser.
* Inloggningar från angripna enheter.
* Inloggningar från IP-adresser med misstänkt aktivitet.
* Inloggningar från okända platser.

Följande tre principer är tillgängliga i Azure AD Identity Protection för att skydda användare och reagera på misstänkt aktivitet. Du kan välja att aktivera eller inaktivera princip tvång, välja användare eller grupper som principen ska tillämpas på och bestämma om du vill blockera åtkomst vid inloggning eller om du vill få ytterligare åtgärder.

* Princip för användarrisk
    * Identifierar och svarar på användar konton som kan ha komprometterade autentiseringsuppgifter. Kan begära att användaren skapar ett nytt lösen ord.
* Logga in risk princip
    * Identifierar och svarar på misstänkta inloggnings försök. Kan begära att användaren ger ytterligare former av verifiering med hjälp av Azure AD Multi-Factor Authentication.
* Registreringsprincip för multifaktorautentisering
    * Kontrollerar att användare har registrerats för Azure AD Multi-Factor Authentication. Om en inloggnings risk princip begärs för MFA måste användaren redan vara registrerad för Azure AD-Multi-Factor Authentication.

När du aktiverar en princip användare eller loggar in risk policy, kan du också välja tröskelvärdet för risk nivå – *låg och över*, *medel och över* eller *hög*. Den här flexibiliteten gör det möjligt för dig att bestämma hur aggressiv du vill ska vara i tvinga alla kontroller för misstänkta inloggnings händelser.

Mer information om Azure AD Identity Protection finns i [Vad är Azure AD Identity Protection?](../identity-protection/overview-identity-protection.md)

## <a name="enable-mfa-registration-policy"></a>Aktivera MFA-registrerings princip

Azure AD Identity Protection innehåller en standard princip som kan hjälpa dig att få användare registrerade för Azure AD Multi-Factor Authentication. Om du använder ytterligare principer för att skydda inloggnings händelser måste användarna redan ha registrerats för MFA. När du aktiverar den här principen kräver den inte att användare utför MFA vid varje inloggnings händelse. Principen kontrollerar bara registrerings statusen för en användare och ber dem att förregistrera sig vid behov.

Vi rekommenderar att du aktiverar MFA-registrerings principen för användare som ska aktive ras för ytterligare Azure AD Identity Protection-principer. Utför följande steg för att aktivera den här principen:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett globalt administratörs konto.
1. Sök efter och välj **Azure Active Directory**, Välj **säkerhet** och välj sedan **identitets skydd** under menyn *skydda* .
1. Välj **principen för MFA-registrering** på menyn till vänster.
1. Som standard gäller principen för *alla användare*. Om du vill väljer du **tilldelningar** och väljer sedan de användare eller grupper som du vill tillämpa principen på.
1. Under *kontroller* väljer du **åtkomst**. Kontrol lera att alternativet för att *kräva Azure AD MFA-registrering* är markerat och välj sedan **Välj**.
1. Ange **tillämpa principen** på *på* och välj sedan **Spara**.

    ![Skärm bild av hur du kräver att användare registrerar sig för MFA i Azure Portal](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>Aktivera användar risk princip för lösen ords ändring

Microsoft samarbetar med forskare, polis och rättsväsende, flera säkerhetsteam på Microsoft och andra betrodda källor för att hitta par av användarnamn och lösenord. När ett av dessa par matchar ett konto i din miljö kan du begära en riskfylld ändring av lösen ord. Den här principen och åtgärden kräver att användaren uppdaterar sitt lösen ord innan de kan logga in för att se till att alla tidigare utsatta autentiseringsuppgifter inte längre fungerar.

Utför följande steg för att aktivera den här principen:

1. Välj **principen för användar risk** på menyn till vänster.
1. Som standard gäller principen för *alla användare*. Om du vill väljer du **tilldelningar** och väljer sedan de användare eller grupper som du vill tillämpa principen på.
1. Under *villkor* väljer du  **Välj villkor > väljer en risk nivå** och väljer sedan *medel och över*.
1. Välj **Välj** och sedan **Slutför**.
1. Under *åtkomst* väljer du **åtkomst**. Kontrol lera att alternativet **Tillåt åtkomst** och *Kräv lösen ords ändring* är markerat och välj sedan **Välj**.
1. Ange **tillämpa principen** på *på* och välj sedan **Spara**.

    ![Skärm bild av hur du aktiverar användar risk principen i Azure Portal](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>Aktivera inloggnings risk princip för MFA

De flesta användare har ett normalt beteende som kan spåras. När de faller utanför denna norm kan det vara riskfylldt att tillåta att de lyckas logga in. I stället kanske du vill blockera användaren eller be dem att utföra Multi-Factor Authentication. Om användaren slutför MFA-utmaningen kan du betrakta den som ett giltigt inloggnings försök och bevilja åtkomst till programmet eller tjänsten.

Utför följande steg för att aktivera den här principen:

1. Välj **principen för inloggnings risk** på menyn till vänster.
1. Som standard gäller principen för *alla användare*. Om du vill väljer du **tilldelningar** och väljer sedan de användare eller grupper som du vill tillämpa principen på.
1. Under *villkor* väljer du  **Välj villkor > väljer en risk nivå** och väljer sedan *medel och över*.
1. Välj **Välj** och sedan **Slutför**.
1. Under *åtkomst* väljer du **Välj en kontroll**. Kontrol lera att alternativet **Tillåt åtkomst** och *Kräv Multi-Factor Authentication* är markerat och välj sedan **Välj**.
1. Ange **tillämpa principen** på *på* och välj sedan **Spara**.

    ![Skärm bild av hur du aktiverar inloggnings risk principen i Azure Portal](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>Testa riskfyllda inloggnings händelser

De flesta användar inloggnings händelser utlöser inte de riskhanterings principer som kon figurer ATS i föregående steg. En användare kan aldrig se en fråga om ytterligare MFA eller återställa sina lösen ord. Om autentiseringsuppgifterna förblir skyddade och deras beteende är konsekventa, skulle deras inloggnings händelser lyckas.

Om du vill testa Azure AD Identity Protection principer som skapats i föregående steg, behöver du ett sätt att simulera riskfyllda beteenden eller potentiella attacker. Stegen för att utföra dessa tester varierar beroende på den Azure AD Identity Protections princip som du vill validera. Mer information om scenarier och steg finns i [simulera risk identifieringar i Azure AD Identity Protection](../identity-protection/howto-identity-protection-simulate-risk.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du har slutfört tester och inte längre vill ha de riskbaserade principerna aktiverade, går du tillbaka till varje princip som du vill inaktivera och ange **tvingande policy** till *av*.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat riskfyllda användar principer för Azure AD Identity Protection. Du har lärt dig att:

> [!div class="checklist"]
> * Förstå de tillgängliga principerna för Azure AD Identity Protection
> * Aktivera registrering av Azure AD-Multi-Factor Authentication
> * Aktivera riskbaserade lösenordsändringar
> * Aktivera riskbaserad multifaktorautentisering
> * Testa riskfyllda principer för användar inloggnings försök

> [!div class="nextstepaction"]
> [Läs mer om Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)
