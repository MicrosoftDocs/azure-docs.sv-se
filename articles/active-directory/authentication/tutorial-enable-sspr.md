---
title: Aktivera Azure Active Directory lösen ords återställning via självbetjäning
description: I den här självstudien får du lära dig att aktivera Azure Active Directory självbetjäning för återställning av lösen ord för en grupp användare och testa lösen ords återställnings processen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 03/25/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39eec4fb6e9907b36908a87c09aceabd0dd1a678
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075174"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Självstudie: gör det möjligt för användare att låsa upp kontot eller återställa lösen ord med hjälp av Azure Active Directory självbetjäning för återställning av lösen ord

Azure Active Directory (Azure AD) självbetjäning för återställning av lösen ord (SSPR) ger användare möjlighet att ändra eller återställa sitt lösen ord, utan någon administratör eller support vid inblandning. Om Azure AD låser ett användar konto eller om de glömmer bort sitt lösen ord, kan de följa prompter för att avblockera sig själva och komma tillbaka till arbetet. Detta minskar risken för support och produktivitets förlust när en användare inte kan logga in på sin enhet eller ett program. Vi rekommenderar den här videon om [hur du aktiverar och konfigurerar SSPR i Azure AD](https://www.youtube.com/watch?v=rA8TvhNcCvQ). Vi har också en video för IT-administratörer för [att lösa de sex vanligaste slut användar fel meddelandena med SSPR](https://www.youtube.com/watch?v=9RPrNVLzT8I).

> [!IMPORTANT]
> I den här självstudien visas en administratör för att aktivera återställning av lösen ord för självbetjäning. Om du redan har registrerat dig för lösen ords återställning via självbetjäning och du behöver gå tillbaka till ditt konto går du till sidan [Microsoft Online Password Reset](https://passwordreset.microsoftonline.com/) .
>
> Om IT-teamet inte har aktiverat möjligheten att återställa ditt eget lösen ord kan du kontakta supportavdelningen för ytterligare hjälp.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Aktivera självbetjäning för återställning av lösen ord för en grupp med Azure AD-användare
> * Konfigurera autentiseringsmetoder och registrerings alternativ
> * Testa SSPR-processen som en användare

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En fungerande Azure AD-klient med minst en licens för kostnads fri Azure AD eller utvärdering aktive rad. På den kostnads fria nivån fungerar SSPR endast för moln användare i Azure AD.
    * För senare självstudier i den här serien behöver du en Azure AD Premium P1-eller utvärderings licens för tillbakaskrivning av lösen ord.
    * Om det behövs kan du [skapa ett Azure-konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett konto med *Global administratörs* behörighet.
* En icke-administratörs användare med ett lösen ord som du känner till, t. ex. *testuser*. Du testar slutanvändarens SSPR-upplevelse med det här kontot i den här självstudien.
    * Om du behöver skapa en användare, se [snabb start: Lägg till nya användare i Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* En grupp som inte är administratörs användare som är medlem i, gillar *SSPR-test-Group*. Du kommer att aktivera SSPR för den här gruppen i den här självstudien.
    * Om du behöver skapa en grupp kan du läsa [skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Med Azure AD kan du aktivera SSPR för *inga*, *markerade* eller *alla* användare. Med den här detaljerade möjligheten kan du välja en delmängd av användare för att testa registrerings processen och arbets flödet för SSPR. När du är nöjd med processen och tiden är rätt att förmedla kraven med en bredare uppsättning användare kan du välja en grupp med användare som ska aktive ras för SSPR. Du kan också aktivera SSPR för alla i Azure AD-klienten.

> [!NOTE]
> För närvarande kan du bara aktivera en Azure AD-grupp för SSPR med hjälp av Azure Portal. Som en del av en större distribution av SSPR stöder Azure AD kapslade grupper. Se till att de användare i gruppen som du väljer har rätt tilldelade licenser. Det finns för närvarande ingen validerings process för dessa licensierings krav.

I den här självstudien ställer du in SSPR för en uppsättning användare i en test grupp. Använd *SSPR-test-Group* och ange din egen Azure AD-grupp efter behov:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto med *globala administratörs* behörigheter.
1. Sök efter och välj **Azure Active Directory** och välj sedan **Återställ lösen ord** på menyn på vänster sida.
1. På sidan **Egenskaper** , under alternativet Återställning av *lösen ord* för självbetjäning aktiverat, väljer du **Välj grupp**
1. Bläddra efter och välj din Azure AD-grupp, t. ex. *SSPR-test-Group*, och välj sedan *Välj*.

    [![Välj en grupp i Azure Portal för att aktivera lösen ords återställning via självbetjäning](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Om du vill aktivera SSPR för Välj användare väljer du **Spara**.

## <a name="select-authentication-methods-and-registration-options"></a>Välj autentiseringsmetoder och registrerings alternativ

När användarna behöver låsa upp sitt konto eller återställa sitt lösen ord, uppmanas de att ange en annan bekräftelse metod. Den här extra autentiseringsmetoden ser till att Azure AD endast har godkänt godkända SSPR-händelser. Du kan välja vilka autentiseringsmetoder som ska tillåtas, baserat på den registrerings information som användaren tillhandahåller.

1. Från menyn till vänster på sidan **autentiseringsmetoder** anger du **antalet metoder som krävs för att återställa** till *1*.

    För att förbättra säkerheten kan du öka antalet autentiseringsmetoder som krävs för SSPR.

1. Välj de **metoder som är tillgängliga för användare** som din organisation vill tillåta. I den här självstudien markerar du rutorna för att aktivera följande metoder:

    * *Meddelande om mobilapp*
    * *Kod för mobilapp*
    * *E-post*
    * *Mobiltelefon*

    Du kan aktivera andra autentiseringsmetoder, som *arbets telefon* eller *säkerhets frågor*, efter behov för att passa dina affärs behov.

1. Om du vill använda autentiseringsmetoder väljer du **Spara**.

Innan användarna kan låsa upp sitt konto eller återställa ett lösen ord måste de registrera sin kontakt information. Azure AD använder den här kontakt informationen för de olika autentiseringsmetoderna som anges i föregående steg.

En administratör kan ange kontakt information manuellt eller så kan användarna gå till en registrerings Portal för att tillhandahålla själva informationen. I den här självstudien ställer du in Azure AD så att användarna uppmanas att registrera sig nästa gång de loggar in.

1. Från menyn till vänster på **registrerings** sidan väljer du *Ja* för **Kräv att användare registrerar sig vid inloggning**.
1. Ställ in **Antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation** på *180*.

    Det är viktigt att hålla kontakt informationen uppdaterad. Om inaktuell kontakt information finns när en SSPR-händelse startar kanske inte användaren kan låsa upp sitt konto eller återställa sitt lösen ord.

1. Om du vill använda registrerings inställningarna väljer du **Spara**.

## <a name="set-up-notifications-and-customizations"></a>Konfigurera meddelanden och anpassningar

För att hålla användare informerade om konto aktivitet kan du konfigurera Azure AD för att skicka e-postaviseringar när en SSPR-händelse inträffar. Dessa meddelanden kan avse både vanliga användar konton och administratörs konton. För administratörs konton ger detta meddelande ett annat informations lager när lösen ordet för privilegierade administratörs konton återställs med hjälp av SSPR. Azure AD kommer att meddela alla globala administratörer när någon använder SSPR på ett administratörs konto.

1. På menyn till vänster på sidan **meddelanden** anger du följande alternativ:

   * Ställ in alternativet **Meddela användare om lösenordsåterställning** på *Ja*.
   * Ställ in **Meddela alla administratörer när andra administratörer återställer sina lösenord** på *Ja*.

1. Om du vill använda meddelande inställningarna väljer du **Spara**.

Om användarna behöver mer hjälp med SSPR-processen kan du anpassa länken "kontakta din administratör". Användaren kan välja den här länken i registrerings processen för SSPR och när de låser upp kontot eller återställer sitt lösen ord. För att se till att användarna får support, rekommenderar vi starkt att du tillhandahåller en anpassad e-postadress eller URL för supportavdelningen.

1. På menyn till vänster på sidan **anpassning** anger du **länken anpassa supportavdelningen** till *Ja*.
1. Ange en e-postadress eller URL för webb sidan där användarna kan få mer hjälp från din organisation, t. ex. *https: \/ /support.contoso.com/* , i fältet **anpassad e-post eller URL för supportavdelningen** .
1. Om du vill använda den anpassade länken väljer du **Spara**.

## <a name="test-self-service-password-reset"></a>Testa självåterställning av lösenord

Med SSPR aktiverat och konfigurerat, testa SSPR-processen med en användare som är en del av gruppen som du valde i föregående avsnitt, till exempel *test-SSPR-Group*. I följande exempel används *testuser* -kontot. Ange ditt eget användar konto. Den är en del av gruppen som du aktiverade för SSPR i det första avsnittet av den här självstudien.

> [!NOTE]
> Använd ett konto som inte är administratör när du testar lösen ords återställning via självbetjäning. Som standard aktiverar Azure AD lösen ords återställning via självbetjäning för administratörer. De måste använda två autentiseringsmetoder för att återställa sina lösen ord. Mer information finns i [princip skillnader för återställning av administratörer](concept-sspr-policy.md#administrator-reset-policy-differences).

1. Om du vill se den manuella registrerings processen öppnar du ett nytt webbläsarfönster i InPrivate-eller Incognito-läge och bläddrar till *https: \/ /aka.MS/ssprsetup*. Azure AD dirigerar användare till den här registrerings portalen när de loggar in nästa gång.
1. Logga in med en icke-administratörs test användare, t. ex. *testuser*, och registrera dina autentiseringsmetoder för din kontakt information.
1. När du är klar väljer du knappen **ser bra ut** och stänger webbläsarfönstret.
1. Öppna ett nytt webbläsarfönster i InPrivate-eller Incognito-läge och bläddra till *https: \/ /aka.MS/SSPR*.
1. Ange konto information för användare som inte är administratörs test, t. ex. *testuser*, tecknen från captcha och välj sedan **Nästa**.

    ![Ange användar konto information för att återställa lösen ordet](media/tutorial-enable-sspr/password-reset-page.png)

1. Följ verifierings stegen för att återställa lösen ordet. När du är färdig får du ett e-postmeddelande om att lösen ordet har återställts.

## <a name="clean-up-resources"></a>Rensa resurser

I en senare självstudie i den här serien ställer du in tillbakaskrivning av lösen ord. Med den här funktionen skrivs lösen ords ändringar från Azure AD SSPR tillbaka till en lokal AD-miljö. Om du vill fortsätta med den här själv studie serien för att konfigurera tillbakaskrivning av lösen ord ska du inte inaktivera SSPR nu.

Om du inte längre vill använda SSPR-funktionen som du har ställt in som en del av den här självstudien anger du SSPR-status till **ingen** med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj **Azure Active Directory** och välj sedan **Återställ lösen ord** på menyn på vänster sida.
1. På sidan **Egenskaper** under alternativet Återställning av lösen ord för självbetjäning *aktive rad* väljer du **ingen**.
1. Om du vill tillämpa ändringen av SSPR väljer du **Spara**.

## <a name="faqs"></a>Vanliga frågor och svar

I det här avsnittet beskrivs vanliga frågor från administratörer och slutanvändare som testar SSPR:

- Varför väntar federerade användare upp till 2 minuter efter att **lösen ordet har återställts** innan de kan använda lösen ord som synkroniseras lokalt?

  För federerade användare vars lösen ord synkroniseras, är källan till behörighet för lösen orden lokalt. Därför uppdaterar SSPR endast lokala lösen ord. Lösenordssynkronisering tillbaka till Azure AD är schemalagt för varje 2 minuter.

- När en nyligen skapad användare som är förifylld med SSPR-data, t. ex. telefon och e-post besöker SSPR registrerings sidan, **förlorar du inte åtkomsten till ditt konto!** visas som rubrik på sidan. Varför finns det inga andra användare med SSPR data i förväg ifyllda i meddelandet?

  En användare som ser **förlorar inte åtkomst till ditt konto!** är medlem i SSPR/kombinerade registrerings grupper som har kon figurer ATS för klient organisationen. Användare som inte ser **förlorar inte åtkomst till ditt konto!** ingår inte i SSPR/kombinerade registrerings grupperna.

- Varför går det inte att se indikatorn för lösen ords styrka när vissa användare går igenom SSPR-processen och återställer sitt lösen ord?

  Användare som inte ser svag/stark lösen ords styrka har synkroniserat tillbakaskrivning av lösen ord aktiverat. Eftersom SSPR inte kan fastställa lösen ords principen för kundens lokala miljö kan den inte verifiera lösen ords styrka eller svagheter. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat lösen ords återställning via självbetjäning i Azure AD för en viss grupp av användare. Du har lärt dig att:

> [!div class="checklist"]
> * Aktivera självbetjäning för återställning av lösen ord för en grupp med Azure AD-användare
> * Konfigurera autentiseringsmetoder och registrerings alternativ
> * Testa SSPR-processen som en användare

> [!div class="nextstepaction"]
> [Aktivera Azure AD-multifaktorautentisering](./tutorial-enable-azure-mfa.md)
