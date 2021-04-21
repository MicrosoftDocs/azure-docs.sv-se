---
title: Djupdykning i lösenordsåterställning via självbetjäning – Azure Active Directory
description: Hur fungerar självbetjäning av lösenordsåterställning?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94077a1c6329aa1fecf9593f2df41fa77afc8a44
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765925"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Så här fungerar det: Självbetjäning av lösenordsåterställning i Azure AD

Azure Active Directory (Azure AD) med självbetjäning av lösenordsåterställning (SSPR) ger användarna möjlighet att ändra eller återställa sina lösenord, utan inblandning av administratörer eller supportavdelningen. Om ett användarkonto är låst eller om de glömmer sitt lösenord kan de följa anvisningarna för att avblockera sig själva och komma tillbaka till arbetet. Den här möjligheten minskar supportsamtal och produktivitetsförlust när en användare inte kan logga in på sin enhet eller i ett program.

> [!IMPORTANT]
> I den här konceptuella artikeln förklaras hur självbetjäning av lösenordsåterställning fungerar för en administratör. Om du är en slutanvändare som redan har registrerats för lösenordsåterställning via självbetjäning och behöver komma tillbaka till ditt konto går du till [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Om IT-teamet inte har aktiverat möjligheten att återställa ditt eget lösenord kan du kontakta supporten för ytterligare hjälp.

## <a name="how-does-the-password-reset-process-work"></a>Hur fungerar processen för lösenordsåterställning?

En användare kan återställa eller ändra sitt lösenord med hjälp av [SSPR-portalen](https://aka.ms/sspr). De måste först ha registrerat sina önskade autentiseringsmetoder. När en användare kommer åt SSPR-portalen tar Azure-plattformen hänsyn till följande faktorer:

* Hur ska sidan lokaliseras?
* Är användarkontot giltigt?
* Vilken organisation tillhör användaren?
* Var hanteras användarens lösenord?
* Är användaren licensierad för att använda funktionen?

När en användare  väljer länken Det går inte att komma åt ditt konto från ett program eller en sida, eller går direkt till , baseras språket som används i [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) SSPR-portalen på följande alternativ:

* Som standard används webbläsarens språkinställning för att visa SSPR på lämpligt språk. Lösenordsåterställningen är lokaliserad till samma språk som Microsoft 365 [stöder](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec).
* Om du vill länka till SSPR på ett specifikt lokaliserat språk lägger du till i slutet av url:en för lösenordsåterställning tillsammans med de `?mkt=` språk som krävs.
    * Om du till exempel vill ange det spanska *språket es-us* använder du `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

När SSPR-portalen visas på det språk som krävs uppmanas användaren att ange ett användar-ID och skicka en captcha. Azure AD verifierar nu att användaren kan använda SSPR genom att göra följande kontroller:

* Kontrollerar att användaren har SSPR aktiverat och har tilldelats en Azure AD-licens.
  * Om användaren inte är aktiverad för SSPR eller inte har någon tilldelad licens uppmanas användaren att kontakta administratören för att återställa sitt lösenord.
* Kontrollerar att användaren har rätt autentiseringsmetoder definierade för sitt konto i enlighet med administratörsprincipen.
  * Om principen bara kräver en metod kontrollerar du att användaren har rätt data definierade för minst en av de autentiseringsmetoder som aktiverats av administratörsprincipen.
    * Om autentiseringsmetoderna inte har konfigurerats uppmanas användaren att kontakta administratören för att återställa sitt lösenord.
  * Om principen kräver två metoder kontrollerar du att användaren har rätt data definierade för minst två av de autentiseringsmetoder som aktiverats av administratörsprincipen.
    * Om autentiseringsmetoderna inte har konfigurerats uppmanas användaren att kontakta administratören för att återställa sitt lösenord.
  * Om en Azure-administratörsroll tilldelas användaren tillämpas den starka lösenordsprincipen med två grindar. Mer information finns i Skillnader [i återställningsprincip för administratörer.](concept-sspr-policy.md#administrator-reset-policy-differences)
* Kontrollerar om användarens lösenord hanteras lokalt, till exempel om Azure AD-klienten använder federerad, direktautentisering eller synkronisering av lösenordshashar:
  * Om SSPR-tillbakaskrivning har konfigurerats och användarens lösenord hanteras lokalt kan användaren fortsätta att autentisera och återställa sitt lösenord.
  * Om SSPR-tillbakaskrivning inte har distribuerats och användarens lösenord hanteras lokalt uppmanas användaren att kontakta administratören för att återställa sitt lösenord.

Om alla tidigare kontroller har slutförts vägleds användaren genom processen för att återställa eller ändra sitt lösenord.

> [!NOTE]
> SSPR kan skicka e-postaviseringar till användare som en del av processen för lösenordsåterställning. Dessa e-postmeddelanden skickas med SMTP-relätjänsten, som fungerar i ett aktivt-aktivt läge i flera regioner.
>
> SMTP-relätjänsterna tar emot och bearbetar e-postmeddelandet, men lagrar den inte. Brödtexten i SSPR-e-postmeddelandet som potentiellt kan innehålla kundinformation lagras inte i SMTP-relätjänstens loggar. Loggarna innehåller bara protokollmetadata.

Kom igång med SSPR genom att slutföra följande självstudie:

> [!div class="nextstepaction"]
> [Självstudie: Aktivera lösenordsåterställning via självbetjäning (SSPR)](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>Kräv att användarna registrerar sig när de loggar in

Du kan aktivera alternativet för att kräva att en användare slutför SSPR-registreringen om de använder modern autentisering eller webbläsare för att logga in på program som använder Azure AD. Det här arbetsflödet innehåller följande program:

* Microsoft 365
* Azure Portal
* Åtkomstpanel
* Federerade program
* Anpassade program med Azure AD

När du inte kräver registrering uppmanas användarna inte att göra det under inloggningen, men de kan registrera sig manuellt. Användarna kan antingen [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) besöka eller välja länken Registrera dig **för** lösenordsåterställning **på** fliken Profil i Åtkomstpanelen.

![Registreringsalternativ för SSPR i Azure Portal][Registration]

> [!NOTE]
> Användare kan stänga SSPR-registreringsportalen genom att **välja Avbryt** eller genom att stänga fönstret. De uppmanas dock att registrera sig varje gång de loggar in tills registreringen är klar.
>
> Det här avbrottet vid registreringen för SSPR bryter inte användarens anslutning om användaren redan är inloggad.

## <a name="reconfirm-authentication-information"></a>Bekräfta autentiseringsinformation

För att säkerställa att autentiseringsmetoderna är korrekta när de behöver återställas eller ändra sina lösenord kan du kräva att användarna bekräftar sin information som registrerats efter en viss tidsperiod. Det här alternativet är endast tillgängligt om du aktiverar **alternativet Kräv att användare registrerar sig vid inloggning.**

Giltiga värden för att uppmana en användare att bekräfta att deras registrerade metoder är *mellan 0* och *730* dagar. Om det här värdet *är 0* uppmanas användarna aldrig att bekräfta sin autentiseringsinformation. När du använder den kombinerade registreringsupplevelsen måste användarna bekräfta sin identitet innan de bekräftar sin information.

## <a name="authentication-methods"></a>Autentiseringsmetoder

När en användare har aktiverats för SSPR måste de registrera minst en autentiseringsmetod. Vi rekommenderar starkt att du väljer två eller flera autentiseringsmetoder så att användarna får mer flexibilitet om de inte kan komma åt en metod när de behöver den. Mer information finns i [Vad är autentiseringsmetoder?](concept-authentication-methods.md).

Följande autentiseringsmetoder är tillgängliga för SSPR:

* Meddelanden via mobilapp
* Kod för mobilapp
* E-post
* Mobiltelefon
* Arbetstelefon
* Säkerhetsfrågor

Användare kan bara återställa sina lösenord om de har registrerat en autentiseringsmetod som administratören har aktiverat.

> [!WARNING]
> Konton som *tilldelats* Azure-administratörsroller krävs för att använda metoder som definieras i avsnittet Skillnader i [återställningsprincip för administratörer.](concept-sspr-policy.md#administrator-reset-policy-differences)

![Val av autentiseringsmetoder i Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Antal autentiseringsmetoder som krävs

Du kan konfigurera antalet tillgängliga autentiseringsmetoder som en användare måste ange för att återställa eller låsa upp sina lösenord. Det här värdet kan anges till *antingen en* eller *två*.

Användare kan och bör registrera flera autentiseringsmetoder. Vi rekommenderar återigen starkt att användarna registrerar två eller flera autentiseringsmetoder så att de får mer flexibilitet om de inte kan komma åt en metod när de behöver den.

Om en användare inte har det minsta antalet obligatoriska metoder som registrerats när de försöker använda SSPR, visas en felsida som leder till att de begär att en administratör återställer sitt lösenord. Var noga om du ökar antalet metoder som krävs från en till två om du har befintliga användare registrerade för SSPR och de sedan inte kan använda funktionen. Mer information finns i avsnittet Ändra [autentiseringsmetoder i följande avsnitt.](#change-authentication-methods)

#### <a name="mobile-app-and-sspr"></a>Mobilapp och SSPR

När du använder en mobilapp som en metod för lösenordsåterställning, till exempel Microsoft Authenticator-appen, gäller följande:

* När administratörer kräver att en metod används för att återställa ett lösenord är verifieringskoden det enda tillgängliga alternativet.
* När administratörer kräver att två metoder används för att återställa ett lösenord kan användarna använda meddelande- **eller** verifieringskod utöver andra aktiverade metoder.

| Antal metoder som krävs för återställning | En | Två |
| :---: | :---: | :---: |
| Tillgängliga funktioner för mobilappar | Kod | Kod eller meddelande |

Användarna har inte möjlighet att registrera sin mobilapp när de registrerar sig för lösenordsåterställning via självbetjäning från [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Användare kan registrera sin mobilapp på [https://aka.ms/mfasetup](https://aka.ms/mfasetup) eller i den kombinerade registreringen av säkerhetsinformation på [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> Authenticator-appen kan inte väljas som den enda autentiseringsmetoden när endast en metod krävs. På samma sätt går det inte att välja Authenticator-appen och bara en ytterligare metod när två metoder krävs.
>
> När du konfigurerar SSPR-principer som inkluderar Authenticator-appen som en metod bör minst en ytterligare metod väljas när en metod krävs och minst två ytterligare metoder ska väljas när du konfigurerar två metoder.
>
> Det här kravet beror på att den aktuella SSPR-registreringsupplevelsen inte innehåller alternativet att registrera autentiseringsappen. Alternativet för att registrera autentiseringsappen ingår i den nya [kombinerade registreringsupplevelsen](./concept-registration-mfa-sspr-combined.md).
>
> Om du tillåter principer som endast använder Authenticator-appen (när en metod krävs) eller Authenticator-appen och endast en ytterligare metod (när två metoder krävs) kan det leda till att användarna blockeras från att registrera sig för SSPR tills de har konfigurerats för att använda den nya kombinerade registreringsupplevelsen.

### <a name="change-authentication-methods"></a>Ändra autentiseringsmetoder

Vad händer om du börjar med en princip som bara har en autentiseringsmetod som krävs för att återställa eller låsa upp registrerad och du ändrar den till två metoder?

| Antal registrerade metoder | Antal metoder som krävs | Resultat |
| :---: | :---: | :---: |
| 1 eller fler | 1 | **Kan** återställa eller låsa upp |
| 1 | 2 | **Det går** inte att återställa eller låsa upp |
| 2 eller fler | 2 | **Kan** återställa eller låsa upp |

Att ändra de tillgängliga autentiseringsmetoderna kan också orsaka problem för användarna. Om du ändrar de typer av autentiseringsmetoder som en användare kan använda kan du oavsiktligt hindra användare från att kunna använda SSPR om de inte har den minsta mängden tillgängliga data.

Tänk dig följande exempelscenario:

1. Den ursprungliga principen konfigureras med två autentiseringsmetoder som krävs. Den använder bara kontorets telefonnummer och säkerhetsfrågorna.
1. Administratören ändrar principen så att säkerhetsfrågorna inte längre används, men tillåter att en mobiltelefon och ett alternativt e-postmeddelande används.
1. Användare utan mobiltelefonen eller alternativa e-postfält som fyllts i kan inte återställa sina lösenord.

## <a name="notifications"></a>Meddelanden

För att förbättra medvetenheten om lösenordshändelser kan du med SSPR konfigurera meddelanden för både användare och identitetsadministratörer.

### <a name="notify-users-on-password-resets"></a>Meddela användare om lösenordsåterställning

Om det här alternativet är inställt **på Ja** får användare som återställer sina lösenord ett e-postmeddelande om att deras lösenord har ändrats. E-postmeddelandet skickas via SSPR-portalen till deras primära och alternativa e-postadresser som lagras i Azure AD. Ingen annan meddelas om återställningshändelsen.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Meddela alla administratörer när andra administratörer återställer sina lösenord

Om det här alternativet är inställt **på Ja** får alla andra Azure-administratörer ett e-postmeddelande till sin primära e-postadress som lagras i Azure AD. E-postmeddelandet meddelar dem att en annan administratör har ändrat sitt lösenord med hjälp av SSPR.

Tänk dig följande exempelscenario:

* Det finns fyra administratörer i en miljö.
* Administratör *A* återställer sitt lösenord med hjälp av SSPR.
* Administratörerna *B,* *C* och *D får ett* e-postmeddelande som varnar dem om lösenordsåterställningen.

## <a name="on-premises-integration"></a>Lokal integration

Om du har en hybridmiljö kan du konfigurera Azure AD Connect att skriva tillbaka händelser för lösenordsändring från Azure AD till en lokal katalog.

![Validering av tillbakaskrivning av lösenord är aktiverat och fungerar][Writeback]

Azure AD kontrollerar din aktuella hybridanslutning och tillhandahåller något av följande meddelanden i Azure Portal:

* Den lokala tillbakaskrivningsklienten är igång.
* Azure AD är online och är anslutet till din lokala tillbakaskrivningsklient. Det verkar dock som om den installerade versionen Azure AD Connect är in-date. Överväg [att Azure AD Connect för](../hybrid/how-to-upgrade-previous-version.md) att se till att du har de senaste anslutningsfunktionerna och viktiga felkorrigeringar.
* Tyvärr kan vi inte kontrollera klientstatusen för din lokala tillbakaskrivning eftersom den installerade versionen av Azure AD Connect är in föråldrad. [Uppgradera Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) för att kunna kontrollera anslutningsstatusen.
* Tyvärr verkar det som om vi inte kan ansluta till din lokala tillbakaskrivningsklient just nu. [Felsök Azure AD Connect](./troubleshoot-sspr-writeback.md) för att återställa anslutningen.
* Tyvärr kan vi inte ansluta till din lokala återskrivningsklient eftersom tillbakaskrivning av lösenord inte har konfigurerats korrekt. [Konfigurera tillbakaskrivning av lösenord](./tutorial-enable-sspr-writeback.md) för att återställa anslutningen.
* Tyvärr verkar det som att vi inte kan ansluta till din lokala tillbakaskrivningsklient just nu. Detta kan bero på tillfälliga problem i slutet. Om problemet kvarstår kan [du Azure AD Connect för](./troubleshoot-sspr-writeback.md) att återställa anslutningen.

Kom igång med SSPR-tillbakaskrivning genom att slutföra följande självstudie:

> [!div class="nextstepaction"]
> [Självstudie: Aktivera tillbakaskrivning av lösenord via självbetjäning (SSPR)](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Skriva tillbaka lösenord till din lokala katalog

Du kan aktivera tillbakaskrivning av lösenord med hjälp av Azure Portal. Du kan också tillfälligt inaktivera tillbakaskrivning av lösenord utan att behöva konfigurera om Azure AD Connect.

* Om alternativet är inställt **på Ja** är tillbakaskrivning aktiverat. Federerade användare, direktautentisering eller synkroniserade lösenordshashar kan återställa sina lösenord.
* Om alternativet är inställt på **Nej** är tillbakaskrivning inaktiverat. Federerade användare, direktautentisering eller synkroniserade lösenordshashar kan inte återställa sina lösenord.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Tillåt användare att låsa upp konton utan att återställa sitt lösenord

Som standard låser Azure AD upp konton när en lösenordsåterställning utförs. För att ge flexibilitet kan du välja att tillåta användare att låsa upp sina lokala konton utan att behöva återställa sina lösenord. Använd den här inställningen för att avgränsa dessa två åtgärder.

* Om det är **inställt** på Ja kan användarna återställa sina lösenord och låsa upp kontot, eller låsa upp sitt konto utan att behöva återställa lösenordet.
* Om det här **är inställt** på Nej kan användarna bara utföra en kombinerad lösenordsåterställning och kontoupplåsning.

### <a name="on-premises-active-directory-password-filters"></a>Lokala Active Directory-lösenordsfilter

SSPR utför motsvarigheten till en administratörsinitierad lösenordsåterställning i Active Directory. Om du använder ett lösenordsfilter från tredje part för att framtvinga anpassade lösenordsregler och du kräver att det här lösenordsfiltret kontrolleras under självbetjäning av lösenordsåterställning i Azure AD, ser du till att lösenordsfilterlösningen från tredje part är konfigurerad för att tillämpas i scenariot med administratörslösenordsåterställning. [Azure AD-lösenordsskydd för Active Directory Domain Services](concept-password-ban-bad-on-premises.md) stöds som standard.

## <a name="password-reset-for-b2b-users"></a>Lösenordsåterställning för B2B-användare

Återställning och ändring av lösenord stöds fullt ut i alla B2B-konfigurationer (business-to-business). Återställning av B2B-användarlösenord stöds i följande tre fall:

* **Användare från en partnerorganisation** med en befintlig Azure AD-klientorganisation: Om den organisation som du samarbetar med har en befintlig Azure AD-klientorganisation respekterar vi de principer för lösenordsåterställning som är aktiverade för den klientorganisationen. För att lösenordsåterställning ska fungera behöver partnerorganisationen bara se till att Azure AD SSPR är aktiverat. Det tillkommer ingen extra kostnad för Microsoft 365 kunder.
* **Användare som** registrerar sig via självbetjäning: Om organisationen du samarbetar med använde registreringsfunktionen för självbetjäning för att komma in i en klientorganisation låter vi dem återställa lösenordet med den [e-postadress](../enterprise-users/directory-self-service-signup.md) som de registrerade.
* **B2B-användare:** Alla nya B2B-användare som skapats med hjälp av de nya Funktionerna i [Azure AD B2B](../external-identities/what-is-b2b.md) kan också återställa sina lösenord med den e-postadress som de registrerade under inbjudan.

Om du vill testa det här scenariot https://passwordreset.microsoftonline.com går du till med någon av dessa partneranvändare. Om en alternativ e-postadress eller e-postadress för autentisering har definierats fungerar lösenordsåterställningen som förväntat.

> [!NOTE]
> Microsoft-konton som har beviljats gäståtkomst till din Azure AD-klientorganisation, till exempel från Hotmail.com, Outlook.com eller andra personliga e-postadresser, kan inte använda Azure AD SSPR. De måste återställa sina lösenord med hjälp av informationen i artikeln [When you can't sign in to your Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) (När du inte kan logga in på Microsoft-konto).

## <a name="next-steps"></a>Nästa steg

Kom igång med SSPR genom att slutföra följande självstudie:

> [!div class="nextstepaction"]
> [Självstudie: Aktivera lösenordsåterställning via självbetjäning (SSPR)](tutorial-enable-sspr.md)

Följande artiklar ger ytterligare information om lösenordsåterställning via Azure AD:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Azure AD-autentiseringsmetoder som är tillgängliga och kvantitet som krävs"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Konfigurera SSPR-registreringsalternativ i Azure Portal"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Lokal integrering för SSPR i Azure Portal"
