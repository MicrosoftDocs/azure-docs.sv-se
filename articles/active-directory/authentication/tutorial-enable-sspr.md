---
title: Aktivera Azure Active Directory lösenordsåterställning via självbetjäning
description: I den här självstudien lär du dig att Azure Active Directory lösenordsåterställning via självbetjäning för en grupp användare och testar processen för lösenordsåterställning.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c18dd231a708030e3a454ab8708e3f0f11dbecf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861831"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Självstudie: Gör det möjligt för användare att låsa upp sitt konto eller återställa lösenord Azure Active Directory med självbetjäning av lösenordsåterställning

Azure Active Directory (Azure AD) med självbetjäning av lösenordsåterställning (SSPR) ger användarna möjlighet att ändra eller återställa sina lösenord, utan inblandning av administratörer eller supportavdelningen. Om Azure AD låser ett användarkonto eller om de glömmer sitt lösenord kan de följa anvisningarna för att avblockera sig själva och komma tillbaka till arbetet. Den här möjligheten minskar supportsamtal och produktivitetsförlust när en användare inte kan logga in på sin enhet eller i ett program. Vi rekommenderar den här videon [om hur du aktiverar och konfigurerar SSPR i Azure AD.](https://www.youtube.com/watch?v=rA8TvhNcCvQ) Vi har också en video för IT-administratörer om att lösa de sex vanligaste [felmeddelandena för slutanvändare med SSPR](https://www.youtube.com/watch?v=9RPrNVLzT8I).

> [!IMPORTANT]
> Den här självstudien visar en administratör hur du aktiverar lösenordsåterställning via självbetjäning. Om du är en slutanvändare som redan har registrerats för lösenordsåterställning via självbetjäning och behöver komma tillbaka till ditt konto går du till [sidan för lösenordsåterställning i Microsoft Online.](https://passwordreset.microsoftonline.com/)
>
> Om IT-teamet inte har aktiverat möjligheten att återställa ditt eget lösenord kan du kontakta supporten för ytterligare hjälp.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Aktivera lösenordsåterställning via självbetjäning för en grupp Azure AD-användare
> * Konfigurera autentiseringsmetoder och registreringsalternativ
> * Testa SSPR-processen som användare

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure AD-klientorganisation med minst en kostnadsfri licens eller utvärderingslicens för Azure AD aktiverad. På den kostnadsfria nivån fungerar SSPR endast för molnanvändare i Azure AD. Lösenordsändring stöds på den kostnadsfria nivån, men lösenordsåterställning är det inte. 
    * För senare självstudier i den här serien behöver du en Azure AD Premium P1- eller utvärderingslicens för tillbakaskrivning av lokala lösenord.
    * Om det behövs [skapar du ett Azure-konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Ett konto med *behörigheter som* global administratör.
* En icke-administratörsanvändare med ett lösenord som du känner till, *t.ex. testuser*. Du testar slutanvändarens SSPR-upplevelse med det här kontot i den här självstudien.
    * Om du behöver skapa en användare kan du gå till [Snabbstart: Lägga till nya användare i Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* En grupp som icke-administratörsanvändaren är medlem i, liknar *SSPR-Test-Group*. Du aktiverar SSPR för den här gruppen i den här självstudien.
    * Om du behöver skapa en grupp kan du gå till [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Med Azure AD kan du aktivera SSPR *för Ingen,* *Valda* eller *Alla* användare. Med den här detaljerade funktionen kan du välja en delmängd av användarna för att testa SSPR-registreringsprocessen och arbetsflödet. När du är nöjd med processen och det är rätt tid att kommunicera kraven med en bredare uppsättning användare kan du välja en grupp användare som ska aktiveras för SSPR. Eller så kan du aktivera SSPR för alla i Azure AD-klientorganisationen.

> [!NOTE]
> För närvarande kan du bara aktivera en Azure AD-grupp för SSPR med hjälp av Azure Portal. Som en del av en bredare distribution av SSPR stöder Azure AD kapslade grupper. Kontrollera att de användare i de grupper som du väljer har rätt tilldelade licenser. Det finns för närvarande ingen valideringsprocess för dessa licensieringskrav.

I den här självstudien ställer du in SSPR för en uppsättning användare i en testgrupp. Använd *SSPR-Test-Group och* ange din egen Azure AD-grupp efter behov:

1. Logga in på Azure Portal [med](https://portal.azure.com) ett konto med behörigheter *som global* administratör.
1. Sök efter och **välj Azure Active Directory** och välj sedan **Återställning av** lösenord på menyn till vänster.
1. På sidan **Egenskaper går** du till alternativet Återställning av lösenord *via självbetjäning har aktiverats* och väljer **Välj grupp**
1. Bläddra efter och välj din Azure AD-grupp, till *exempel SSPR-Test-Group,* och välj *sedan Välj*.

    [![Välj en grupp i Azure Portal att aktivera lösenordsåterställning via självbetjäning](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Om du vill aktivera SSPR för de valda användarna väljer du **Spara**.

## <a name="select-authentication-methods-and-registration-options"></a>Välj autentiseringsmetoder och registreringsalternativ

När användarna behöver låsa upp sitt konto eller återställa sitt lösenord uppmanas de att ange en annan bekräftelsemetod. Den här extra autentiseringsfaktorn ser till att Azure AD endast slutförde godkända SSPR-händelser. Du kan välja vilka autentiseringsmetoder som ska tillåtas, baserat på den registreringsinformation som användaren tillhandahåller.

1. På menyn till vänster på sidan Autentiseringsmetoder **anger du** Antal metoder som krävs för **att återställa** till *1*.

    För att förbättra säkerheten kan du öka antalet autentiseringsmetoder som krävs för SSPR.

1. Välj de **metoder som är tillgängliga för** användare som din organisation vill tillåta. För den här självstudien markerar du kryssrutorna för att aktivera följande metoder:

    * *Meddelande från mobilapp*
    * *Kod för mobilapp*
    * *E-post*
    * *Mobiltelefon*

    Du kan aktivera andra autentiseringsmetoder, t.ex. Telefon- eller *säkerhetsfrågor för* *Office,* efter behov för att passa dina affärsbehov.

1. Om du vill tillämpa autentiseringsmetoderna väljer du **Spara**.

Innan användarna kan låsa upp sitt konto eller återställa ett lösenord måste de registrera sina kontaktuppgifter. Azure AD använder den här kontaktinformationen för de olika autentiseringsmetoder som konfigureras i föregående steg.

En administratör kan ange den här kontaktinformationen manuellt, eller så kan användarna gå till en registreringsportal för att ange själva informationen. I den här självstudien ställer du in Azure AD så att användarna uppmanas att registrera sig nästa gång de loggar in.

1. På menyn till vänster på sidan Registrering väljer **du Ja** för *Kräv* att **användare registrerar sig vid inloggning.**
1. Ställ in **Antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation** på *180*.

    Det är viktigt att hålla kontaktinformationen uppdaterad. Om det finns inaktuell kontaktinformation när en SSPR-händelse startar kanske användaren inte kan låsa upp sitt konto eller återställa sitt lösenord.

1. Om du vill tillämpa registreringsinställningarna väljer du **Spara**.

## <a name="set-up-notifications-and-customizations"></a>Konfigurera meddelanden och anpassningar

Om du vill hålla användarna informerade om kontoaktivitet kan du konfigurera Azure AD att skicka e-postmeddelanden när en SSPR-händelse inträffar. Dessa meddelanden kan omfatta både vanliga användarkonton och administratörskonton. För administratörskonton ger det här meddelandet ytterligare ett lager av medvetenhet när lösenordet för ett privilegierat administratörskonto återställs med hjälp av SSPR. Azure AD meddelar alla globala administratörer när någon använder SSPR på ett administratörskonto.

1. Konfigurera följande alternativ på menyn till **vänster på** sidan Meddelanden:

   * Ställ in alternativet **Meddela användare om lösenordsåterställning** på *Ja*.
   * Ställ in **Meddela alla administratörer när andra administratörer återställer sina lösenord** på *Ja*.

1. Om du vill tillämpa meddelandeinställningarna väljer du **Spara**.

Om användarna behöver mer hjälp med SSPR-processen kan du anpassa länken "Kontakta administratören". Användaren kan välja den här länken i SSPR-registreringsprocessen och när de låser upp sitt konto eller återställer sitt lösenord. För att se till att användarna får den support som behövs rekommenderar vi starkt att du anger en anpassad e-postadress eller URL för supporten.

1. På menyn till vänster på sidan **Anpassning anger** du **Anpassa supportlänken till** *Ja.*
1. I fältet **Anpassad e-postadress** eller URL för supporten anger du en e-postadress eller webbadress där användarna kan få mer hjälp från din organisation, t.ex. *https: \/ /support.contoso.com/*
1. Om du vill tillämpa den anpassade länken väljer du **Spara.**

## <a name="test-self-service-password-reset"></a>Testa självåterställning av lösenord

När SSPR har aktiverats och ställts in testar du SSPR-processen med en användare som ingår i den grupp som du valde i föregående avsnitt, t.ex. *Test-SSPR-Group*. I följande exempel används *testuser-kontot.* Ange ditt eget användarkonto. Det är en del av gruppen som du aktiverade för SSPR i det första avsnittet av den här självstudien.

> [!NOTE]
> När du testar självbetjäning av lösenordsåterställning använder du ett icke-administratörskonto. Som standard aktiverar Azure AD lösenordsåterställning via självbetjäning för administratörer. De måste använda två autentiseringsmetoder för att återställa sina lösenord. Mer information finns i Skillnader [i återställningsprincip för administratörer.](concept-sspr-policy.md#administrator-reset-policy-differences)

1. Om du vill se den manuella registreringsprocessen öppnar du ett nytt webbläsarfönster i InPrivate- eller inkognitoläge och bläddrar till *https: \/ /aka.ms/ssprsetup*. Azure AD dirigerar användarna till den här registreringsportalen när de loggar in nästa gång.
1. Logga in med en testanvändare som inte är administratör, *t.ex. testuser*, och registrera dina kontaktuppgifter för autentiseringsmetoder.
1. När du är klar väljer du knappen **ser bra ut** och stänger webbläsarfönstret.
1. Öppna ett nytt webbläsarfönster i InPrivate- eller inkognitoläge och bläddra *till https: \/ /aka.ms/sspr*.
1. Ange dina icke-administratörstestanvändares kontoinformation, till exempel *testuser*, tecknen från CAPTCHA och välj sedan **Nästa.**

    ![Ange användarkontoinformation för att återställa lösenordet](media/tutorial-enable-sspr/password-reset-page.png)

1. Följ verifieringsstegen för att återställa lösenordet. När du är klar får du ett e-postmeddelande om att lösenordet har återställts.

## <a name="clean-up-resources"></a>Rensa resurser

I en senare självstudie i den här serien ska du konfigurera tillbakaskrivning av lösenord. Den här funktionen skriver lösenordsändringar från Azure AD SSPR tillbaka till en lokal AD-miljö. Om du vill fortsätta med den här självstudieserien för att konfigurera tillbakaskrivning av lösenord ska du inte inaktivera SSPR nu.

Om du inte längre vill använda de SSPR-funktioner som du har ställt in som en del av den här självstudien anger du SSPR-statusen till **Ingen** med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och **välj Azure Active Directory** och välj sedan **Återställning av** lösenord på menyn till vänster.
1. På sidan **Egenskaper** går du till alternativet Återställning av *lösenord via självbetjäning har aktiverats* och väljer **Ingen.**
1. Om du vill tillämpa SSPR-ändringen väljer du **Spara**.

## <a name="faqs"></a>Vanliga frågor och svar

I det här avsnittet beskrivs vanliga frågor från administratörer och slutanvändare som provar SSPR:

- Varför väntar federerade användare i upp till  2 minuter efter att de ser Ditt lösenord har återställts innan de kan använda lösenord som synkroniseras lokalt?

  För federerade användare vars lösenord synkroniseras är källan till utfärdare för lösenorden lokal. Därför uppdaterar SSPR endast lokala lösenord. Synkronisering av lösenordshashar tillbaka till Azure AD schemaläggs varannan minut.

- När en nyligen skapad användare som är förifylld med SSPR-data, till exempel telefon och e-post besöker SSPR-registreringssidan, förlorar du inte **åtkomsten till ditt konto!** visas som rubriken på sidan. Varför visas inte meddelandet för andra användare som har förifyllda SSPR-data?

  En användare som ser **Förlorar inte åtkomsten till ditt konto!** är medlem i SSPR/kombinerade registreringsgrupper som har konfigurerats för klienten. Användare som inte ser **förlorar inte åtkomsten till ditt konto!** ingick inte i SSPR/kombinerade registreringsgrupperna.

- Varför visas inte indikatorn för lösenordsstyrka när vissa användare går igenom SSPR-processen och återställer sina lösenord?

  Användare som inte ser svag/stark lösenordsstyrka har synkroniserad tillbakaskrivning av lösenord aktiverat. Eftersom SSPR inte kan fastställa lösenordsprincipen för kundens lokala miljö kan den inte verifiera lösenordsstyrkan eller svagheten. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat självbetjäning av lösenordsåterställning i Azure AD för en vald grupp användare. Du har lärt dig att:

> [!div class="checklist"]
> * Aktivera lösenordsåterställning via självbetjäning för en grupp Azure AD-användare
> * Konfigurera autentiseringsmetoder och registreringsalternativ
> * Testa SSPR-processen som användare

> [!div class="nextstepaction"]
> [Aktivera Azure AD-multifaktorautentisering](./tutorial-enable-azure-mfa.md)
