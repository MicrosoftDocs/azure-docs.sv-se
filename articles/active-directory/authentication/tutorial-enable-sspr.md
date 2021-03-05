---
title: Aktivera Azure Active Directory lösen ords återställning via självbetjäning
description: I den här självstudien får du lära dig att aktivera Azure Active Directory självbetjäning för återställning av lösen ord för en grupp användare och testa lösen ords återställnings processen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cd1a68b06814d13c386b873ed715f3b03a7b827
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198497"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Självstudie: gör det möjligt för användare att låsa upp kontot eller återställa lösen ord med hjälp av Azure Active Directory självbetjäning för återställning av lösen ord

Azure Active Directory (Azure AD) självbetjäning för återställning av lösen ord (SSPR) ger användare möjlighet att ändra eller återställa sitt lösen ord, utan någon administratör eller support vid inblandning. Om ett användar konto är låst eller om det glömmer sitt lösen ord, kan de följa prompter för att avblockera sig själva och komma tillbaka till arbetet. Detta minskar risken för support och produktivitets förlust när en användare inte kan logga in på sin enhet eller ett program. Här är en video om [hur du konfigurerar och aktiverar lösen ords återställning via självbetjäning i din klient](https://www.youtube.com/watch?v=rA8TvhNcCvQ) (**rekommenderas**). Vi har också en video för IT-administratörer för [att lösa de sex vanligaste slut användar fel meddelandena med SSPR](https://www.youtube.com/watch?v=9RPrNVLzT8I).

> [!IMPORTANT]
> I den här självstudien visas en administratör för att aktivera återställning av lösen ord för självbetjäning. Om du är en slutanvändare som redan är registrerad för lösen ords återställning via självbetjäning och behöver gå tillbaka till ditt konto, går du till https://aka.ms/sspr .
>
> Om IT-teamet inte har aktiverat möjligheten att återställa ditt eget lösen ord kan du kontakta supportavdelningen för ytterligare hjälp.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Aktivera självbetjäning för återställning av lösen ord för en grupp med Azure AD-användare
> * Konfigurera autentiseringsmetoder och registrerings alternativ
> * Testa SSPR-processen som en användare

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En fungerande Azure AD-klient med minst en Azure AD Free-eller utvärderings licens aktive rad. På den kostnads fria nivån fungerar SSPR endast för moln användare i Azure AD.
    * För senare självstudier i den här serien krävs en Azure AD Premium P1-eller utvärderings licens för den lokala tillbakaskrivning av lösen ord.
    * Om det behövs kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett konto med *Global administratörs* behörighet.
* En icke-administratörs användare med ett lösen ord som du känner till, till exempel *testuser*. Du testar slutanvändarens SSPR-upplevelse med det här kontot i den här självstudien.
    * Om du behöver skapa en användare, se [snabb start: Lägg till nya användare i Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* En grupp som inte är administratörs användare som är medlem i, till exempel *SSPR-test-Group*. Du aktiverar SSPR för den här gruppen i den här självstudien.
    * Om du behöver skapa en grupp, se så här [skapar du en grupp och lägger till medlemmar i Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Med Azure AD kan du aktivera SSPR för *inga*, *markerade* eller *alla* användare. Med den här detaljerade möjligheten kan du välja en delmängd av användare för att testa registrerings processen och arbets flödet för SSPR. När du är bekväm med processen och kan kommunicera kraven med en bredare uppsättning användare kan du välja en grupp med användare som ska aktive ras för SSPR. Du kan också aktivera SSPR för alla i Azure AD-klienten.

> [!NOTE]
>
> Endast en Azure AD-grupp kan för närvarande aktive ras för SSPR med hjälp av Azure Portal. Som en del av en större distribution av SSPR stöds kapslade grupper. Se till att de användare i gruppen som du väljer har rätt tilldelade licenser. Det finns för närvarande ingen validerings process för dessa licensierings krav.

I den här självstudien konfigurerar du SSPR för en uppsättning användare i en test grupp. I följande exempel används gruppen *SSPR-test-Group* . Ange din egen Azure AD-grupp efter behov:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto med *globala administratörs* behörigheter.
1. Sök efter och välj **Azure Active Directory** och välj sedan **Återställ lösen ord** på menyn till vänster.
1. På sidan **Egenskaper** , under alternativet Återställning av *lösen ord* för självbetjäning aktiverat, väljer du **Välj grupp**
1. Bläddra efter och välj din Azure AD-grupp, till exempel *SSPR-test-Group* och välj sedan *Välj*.

    [![Välj en grupp i Azure Portal för att aktivera lösen ords återställning ](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png) via självbetjäning](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Om du vill aktivera SSPR för Välj användare väljer du **Spara**.

## <a name="select-authentication-methods-and-registration-options"></a>Välj autentiseringsmetoder och registrerings alternativ

När användarna behöver låsa upp sitt konto eller återställa sitt lösen ord, uppmanas de att ange en ytterligare bekräftelse metod. Denna ytterligare autentiseringsnivå ser till att endast godkända SSPR-händelser har slutförts. Du kan välja vilka autentiseringsmetoder som ska tillåtas, baserat på den registrerings information som användaren tillhandahåller.

1. På sidan **autentiseringsmetoder** från menyn till vänster anger du **antalet metoder som krävs för att återställa** till *1*.

    För att förbättra säkerheten kan du öka antalet autentiseringsmetoder som krävs för SSPR.

1. Välj de **metoder som är tillgängliga för användare** som din organisation vill tillåta. I den här självstudien markerar du rutorna för att aktivera följande metoder:

    * *Meddelande om mobilapp*
    * *Kod för mobilapp*
    * *E-post*
    * *Mobiltelefon*

    Ytterligare autentiseringsmetoder, till exempel *Office Phone* eller *säkerhets frågor*, kan aktive ras efter behov för att passa dina affärs behov.

1. Om du vill använda autentiseringsmetoder väljer du **Spara**.

Innan användarna kan låsa upp sitt konto eller återställa ett lösen ord måste de registrera sin kontakt information. Den här kontakt informationen används för de olika autentiseringsmetoder som kon figurer ATS i föregående steg.

En administratör kan ange kontakt information manuellt eller så kan användarna gå till en registrerings Portal för att tillhandahålla själva informationen. I den här självstudien konfigurerar du användarna så att de uppmanas att registrera sig nästa gång de loggar in.

1. På **registrerings** sidan från menyn till vänster väljer du *Ja* för **Kräv att användare registrerar sig vid inloggning**.
1. Det är viktigt att kontakt information hålls uppdaterad. Om kontakt informationen är föråldrad när en SSPR-händelse startas kanske inte användaren kan låsa upp sitt konto eller återställa sitt lösen ord.

    Ställ in **Antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation** på *180*.
1. Om du vill använda registrerings inställningarna väljer du **Spara**.

## <a name="configure-notifications-and-customizations"></a>Konfigurera meddelanden och anpassningar

Om du vill hålla användare informerade om konto aktivitet kan du konfigurera e-postaviseringar som ska skickas när en SSPR-händelse inträffar. Dessa meddelanden kan avse både vanliga användar konton och administratörs konton. För administratörs konton ger det här meddelandet ytterligare ett informations lager när lösen ordet för privilegierade administratörs konton återställs med hjälp av SSPR. Alla globala administratörer meddelas när SSPR används på ett administratörs konto.

1. På sidan **meddelanden** från menyn till vänster konfigurerar du följande alternativ:

   * Ställ in alternativet **Meddela användare om lösenordsåterställning** på *Ja*.
   * Ställ in **Meddela alla administratörer när andra administratörer återställer sina lösenord** på *Ja*.

1. Om du vill använda meddelande inställningarna väljer du **Spara**.

Om användarna behöver ytterligare hjälp med SSPR-processen kan du anpassa länken för "kontakta administratören". Den här länken används i SSPR registrerings process och när en användare låser upp sitt konto eller återställer sitt lösen ord. För att se till att användarna får support, rekommenderar vi starkt att du tillhandahåller en anpassad e-postadress eller URL för supportavdelningen.

1. På sidan **anpassning** på menyn till vänster väljer du *länken anpassa supportavdelningen* till **Ja**.
1. I fältet **anpassad e-postadress eller URL** för e-post eller URL anger du en e-postadress eller URL för webb sidan där användarna kan få ytterligare hjälp från din organisation, till exempel *`https://support.contoso.com/`*
1. Om du vill använda den anpassade länken väljer du **Spara**.

## <a name="test-self-service-password-reset"></a>Testa självåterställning av lösenord

När SSPR har Aktiver ATS och kon figurer ATS, testa SSPR-processen med en användare som är en del av gruppen som du valde i föregående avsnitt, till exempel *test-SSPR-grupp*. I följande exempel används *testuser* -kontot. Ange ditt eget användar konto som är en del av gruppen som du aktiverade för SSPR i den första delen av den här självstudien.

> [!NOTE]
> Använd ett konto som inte är administratör när du testar lösen ords återställning via självbetjäning. Som standard är administratörer aktiverade för lösen ords återställning via självbetjäning och krävs för att använda två autentiseringsmetoder för att återställa sina lösen ord. Mer information finns i [princip skillnader för återställning av administratörer](concept-sspr-policy.md#administrator-reset-policy-differences).

1. Om du vill se den manuella registrerings processen öppnar du ett nytt webbläsarfönster i InPrivate-eller Incognito-läge och bläddrar till [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Användarna ska dirigeras till den här registrerings portalen vid nästa inloggning.
1. Logga in med en icke-administratörs test användare, till exempel *testuser*, och registrera dina autentiseringsmetoder för din kontakt information.
1. När du är klar väljer du knappen **ser bra ut** och stänger webbläsarfönstret.
1. Öppna ett nytt webbläsarfönster i InPrivate-eller Incognito-läge och bläddra till [https://aka.ms/sspr](https://aka.ms/sspr) .
1. Ange konto informationen för användare som inte är administratörs test, till exempel *testuser*, tecknen från captcha och välj sedan **Nästa**.

    ![Ange användar konto information för att återställa lösen ordet](media/tutorial-enable-sspr/password-reset-page.png)

1. Följ verifierings stegen för att återställa lösen ordet. När du är klar bör du få ett e-postmeddelande om att lösen ordet har återställts.

## <a name="clean-up-resources"></a>Rensa resurser

I en följande självstudie i den här serien konfigurerar du tillbakaskrivning av lösen ord. Med den här funktionen skrivs lösen ords ändringar från Azure AD SSPR tillbaka till en lokal AD-miljö. Om du vill fortsätta med den här själv studie serien för att konfigurera tillbakaskrivning av lösen ord ska du inte inaktivera SSPR nu.

Om du inte längre vill använda SSPR-funktionen som du har konfigurerat som en del av den här självstudien ställer du in SSPR-statusen på **ingen** av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj **Azure Active Directory** och välj sedan **Återställ lösen ord** på menyn till vänster.
1. Välj **ingen** på sidan **Egenskaper** under alternativet Återställning av lösen ord för självbetjäning *aktive rad*.
1. Om du vill tillämpa ändringen av SSPR väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat lösen ords återställning via självbetjäning i Azure AD för en viss grupp av användare. Du har lärt dig att:

> [!div class="checklist"]
> * Aktivera självbetjäning för återställning av lösen ord för en grupp med Azure AD-användare
> * Konfigurera autentiseringsmetoder och registrerings alternativ
> * Testa SSPR-processen som en användare

> [!div class="nextstepaction"]
> [Aktivera Azure AD-multifaktorautentisering](./tutorial-enable-azure-mfa.md)
