---
title: Lösen ords återställning via självbetjäning – Azure Active Directory
description: Hur fungerar lösen ords återställning via självbetjäning
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
ms.openlocfilehash: 496a8dba9b9ea7fb82ad9016479154d6a61cb767
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703283"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Så här fungerar det: Självbetjäning av lösenordsåterställning i Azure AD

Azure Active Directory (Azure AD) självbetjäning för återställning av lösen ord (SSPR) ger användare möjlighet att ändra eller återställa sitt lösen ord, utan någon administratör eller support vid inblandning. Om ett användar konto är låst eller om det glömmer sitt lösen ord, kan de följa prompter för att avblockera sig själva och komma tillbaka till arbetet. Detta minskar risken för support och produktivitets förlust när en användare inte kan logga in på sin enhet eller ett program.

> [!IMPORTANT]
> I den här konceptuella artikeln beskrivs en administratör Hur lösen ords återställning via självbetjäning fungerar. Om du är en slutanvändare som redan är registrerad för lösen ords återställning via självbetjäning och behöver gå tillbaka till ditt konto, går du till [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Om IT-teamet inte har aktiverat möjligheten att återställa ditt eget lösen ord kan du kontakta supportavdelningen för ytterligare hjälp.

## <a name="how-does-the-password-reset-process-work"></a>Hur fungerar lösen ords återställnings processen?

En användare kan återställa eller ändra sitt lösen ord med hjälp av [SSPR-portalen](https://aka.ms/sspr). De måste först ha registrerat sina önskade autentiseringsmetoder. När en användare kommer åt SSPR-portalen, beaktar Azure-plattformen följande faktorer:

* Hur ska sidan lokaliseras?
* Är användar kontot giltigt?
* Vilken organisation tillhör användaren?
* Var hanteras användarens lösen ord?
* Är användaren licensierad för att använda funktionen?

När en användare väljer länken **kan inte komma åt ditt konto** från ett program eller en sida, eller går direkt till [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) , baseras språket som används i SSPR-portalen på följande alternativ:

* Som standard används webbläsarens nationella inställningar för att Visa SSPR på rätt språk. Lösen ords återställnings upplevelsen lokaliseras till samma språk som [Microsoft 365 stöder](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec).
* Om du vill länka till SSPR på ett särskilt lokaliserat språk lägger du till i `?mkt=` slutet av URL: en för lösen ords återställning tillsammans med de språk som krävs.
    * Om du till exempel vill ange den spanska *es-USA-* språkvarianten använder du `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

När SSPR-portalen visas på det språk som krävs uppmanas användaren att ange ett användar-ID och skicka en captcha. Nu verifierar Azure AD att användaren kan använda SSPR genom att göra följande:

* Kontrollerar att användaren har SSPR aktiverat och tilldelats en Azure AD-licens.
  * Om användaren inte är aktive rad för SSPR eller inte har någon tilldelad licens, uppmanas användaren att kontakta sin administratör för att återställa sitt lösen ord.
* Kontrollerar att användaren har rätt autentiseringsmetoder definierade för sitt konto i enlighet med administratörs principen.
  * Om principen bara kräver en metod kontrollerar du att användaren har rätt data definierad för minst en av de autentiseringsmetoder som Aktiver ATS av administratörs principen.
    * Om autentiseringsmetoderna inte har kon figurer ATS, uppmanas användaren att kontakta sin administratör för att återställa sitt lösen ord.
  * Om principen kräver två metoder kontrollerar du att användaren har rätt data definierad för minst två av de autentiseringsmetoder som har Aktiver ATS av administratörs principen.
    * Om autentiseringsmetoderna inte har kon figurer ATS, uppmanas användaren att kontakta sin administratör för att återställa sitt lösen ord.
  * Om en Azure-administratör är tilldelad användaren, tillämpas den starka lösen ords principen på två-grind. Mer information finns i [princip skillnader för återställning av administratörer](concept-sspr-policy.md#administrator-reset-policy-differences).
* Kontrollerar om användarens lösen ord hanteras lokalt, till exempel om Azure AD-klienten använder federerad, direktautentisering eller hash-synkronisering av lösen ord:
  * Om SSPR tillbakaskrivning har kon figurer ATS och användarens lösen ord hanteras lokalt, tillåts användaren att fortsätta att autentisera och återställa sitt lösen ord.
  * Om SSPR tillbakaskrivning inte distribueras och användarens lösen ord hanteras lokalt, uppmanas användaren att kontakta sin administratör för att återställa sitt lösen ord.

Om alla tidigare kontroller har slutförts guidas användaren genom processen att återställa eller ändra lösen ordet.

> [!NOTE]
> SSPR kan skicka e-postmeddelanden till användare som en del av processen för lösen ords återställning. Dessa e-postmeddelanden skickas med SMTP-tjänsten för vidarebefordran, som fungerar i ett aktivt-aktivt läge i flera regioner.
>
> SMTP Relay-tjänster tar emot och bearbetar e-postmeddelandet, men lagrar det inte. Bröd texten i SSPR-e-postmeddelandet som potentiellt kan innehålla information om den angivna kunden lagras inte i SMTP Relay service-loggarna. Loggarna innehåller bara protokollets metadata.

Kom igång med SSPR genom att följa de här självstudierna:

> [!div class="nextstepaction"]
> [Självstudie: Aktivera självbetjäning för återställning av lösen ord (SSPR)](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>Kräv att användare registrerar sig när de loggar in

Du kan aktivera alternativet för att kräva att en användare slutför SSPR-registreringen om de loggar in på alla program som använder Azure AD. Det här arbets flödet innehåller följande program:

* Microsoft 365
* Azure Portal
* Åtkomstpanel
* Federerade program
* Anpassade program med hjälp av Azure AD

När du inte behöver registrering uppmanas användarna inte att logga in, men de kan registrera sig manuellt. Användarna kan antingen besöka [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) eller välja länken **Registrera för lösen ords återställning** under fliken **profil** på åtkomst panelen.

![Registrerings alternativ för SSPR i Azure Portal][Registration]

> [!NOTE]
> Användare kan stänga av SSPR-registrerings portalen genom att välja **Avbryt** eller stänga fönstret. De uppmanas dock att registrera sig varje gången de loggar in tills de har slutfört registreringen.
>
> Detta avbrott i registreringen för SSPR bryter inte användarens anslutning om de redan är inloggade.

## <a name="reconfirm-authentication-information"></a>Bekräfta autentiseringsinformation

För att se till att autentiseringsmetoderna är korrekta när de behövs för att återställa eller ändra lösen ordet, kan du kräva att användarna bekräftar sin information som registrerats efter en viss tids period. Det här alternativet är bara tillgängligt om du aktiverar alternativet **Kräv att användare registrerar sig vid inloggning** .

Giltiga värden för att uppmana en användare att bekräfta sina registrerade metoder är mellan *0* och *730* dagar. Om det här värdet anges till *0* så uppmanas användarna aldrig att bekräfta sin autentiseringsinformation.

## <a name="authentication-methods"></a>Autentiseringsmetoder

När en användare är aktive rad för SSPR måste han eller hon registrera minst en autentiseringsmetod. Vi rekommenderar starkt att du väljer två eller fler autentiseringsmetoder så att användarna har större flexibilitet om de inte kan komma åt en metod när de behöver den. Mer information finns i [Vad är autentiseringsmetoder?](concept-authentication-methods.md).

Följande autentiseringsmetoder är tillgängliga för SSPR:

* Meddelanden via mobilapp
* Kod för mobilapp
* E-post
* Mobiltelefon
* Arbetstelefon
* Säkerhetsfrågor

Användare kan bara återställa sina lösen ord om de har registrerat en autentiseringsmetod som administratören har aktiverat.

> [!WARNING]
> Konton som har tilldelats Azure- *Administratörs* roller krävs för att använda metoder som definieras i avsnittet [återställa princip skillnader](concept-sspr-policy.md#administrator-reset-policy-differences).

![Val av autentiseringsmetoder i Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Antal autentiseringsmetoder som krävs

Du kan konfigurera antalet tillgängliga autentiseringsmetoder som en användare måste ange för att återställa eller låsa upp sina lösen ord. Värdet kan anges till antingen *en* eller *två*.

Användare kan, och bör registrera flera autentiseringsmetoder. Vi rekommenderar att användarna registrerar två eller fler autentiseringsmetoder så att de har större flexibilitet om de inte kan komma åt en metod när de behöver den.

Om en användare inte har det minsta antalet obligatoriska metoder som registreras när de försöker använda SSPR, visas en felsida som uppmanar dem att begära att en administratör återställer sitt lösen ord. Ta hand om du ökar antalet metoder som krävs från en till två om du har befintliga användare som är registrerade för SSPR och inte kan använda funktionen. Mer information finns i följande avsnitt för att [ändra autentiseringsmetoder](#change-authentication-methods).

#### <a name="mobile-app-and-sspr"></a>Mobilapp och SSPR

När du använder en mobilapp som en metod för lösen ords återställning, t. ex. Microsoft Authenticator-appen, gäller följande:

* När administratörer behöver en metod för att återställa ett lösen ord är verifierings koden det enda tillgängliga alternativet.
* När administratörer kräver att två metoder används för att återställa ett lösen ord kan användarna använda meddelande- **eller** verifierings kod utöver andra aktiverade metoder.

| Antal metoder som krävs för att återställa | En | Två |
| :---: | :---: | :---: |
| Mobile app-funktioner som är tillgängliga | Kod | Kod eller meddelande |

Användarna har inte möjlighet att registrera sin mobilapp när de registrerar sig för lösen ords återställning via självbetjäning från [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Användare kan registrera sina mobilappar på [https://aka.ms/mfasetup](https://aka.ms/mfasetup) eller i den kombinerade registreringen av säkerhets information på [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> Det går inte att välja Authenticator-appen som enda autentiseringsmetod när endast en metod krävs. På samma sätt kan inte Authenticator-appen och endast en ytterligare metod väljas när du kräver två metoder.
>
> När du konfigurerar SSPR-principer som inkluderar Authenticator-appen som en metod måste du välja minst en ytterligare metod när en metod krävs, och minst två ytterligare metoder måste väljas när du konfigurerar två metoder.
>
> Detta krav beror på att den aktuella SSPR-registreringen inte innehåller alternativet för att registrera Authenticator-appen. Alternativet att registrera Authenticator-appen ingår i den nya [kombinerade registrerings upplevelsen](./concept-registration-mfa-sspr-combined.md).
>
> Att tillåta principer som bara använder Authenticator-appen (när en metod krävs), eller Authenticator-appen och endast en ytterligare metod (när två metoder krävs), kan leda till att användare blockeras från att registrera sig för SSPR tills de har kon figurer ATS för att använda den nya kombinerade registrerings upplevelsen.

### <a name="change-authentication-methods"></a>Ändra autentiseringsmetoder

Vad händer om du börjar med en princip som bara har en obligatorisk autentiseringsmetod för återställning eller upplåsning av registreras och du ändrar den till två metoder?

| Antal registrerade metoder | Antal metoder som krävs | Resultat |
| :---: | :---: | :---: |
| 1 eller flera | 1 | **Kan** återställa eller låsa upp |
| 1 | 2 | **Det går inte** att återställa eller låsa upp |
| 2 eller flera | 2 | **Kan** återställa eller låsa upp |

Att ändra tillgängliga autentiseringsmetoder kan också orsaka problem för användarna. Om du ändrar de typer av autentiseringsmetoder som en användare kan använda kan du oavsiktligt hindra användare från att kunna använda SSPR om de inte har den minsta mängden data som är tillgängliga.

Tänk på följande exempel scenario:

1. Den ursprungliga principen konfigureras med två autentiseringsmetoder som krävs. Den använder bara arbets telefonnumret och säkerhets frågorna.
1. Administratören ändrar principen till att inte längre använda säkerhets frågorna, men kan använda en mobil telefon och ett alternativt e-postmeddelande.
1. Användare utan mobil telefon eller alternativa e-postfält som inte fyllts i kan nu återställa sina lösen ord.

## <a name="notifications"></a>Meddelanden

För att förbättra medvetenheten om lösen ords händelser kan SSPR Konfigurera meddelanden för både användare och identitets administratörer.

### <a name="notify-users-on-password-resets"></a>Meddela användare om lösenordsåterställning

Om det här alternativet är inställt på **Ja** får användare som återställer sina lösen ord ett e-postmeddelande om att deras lösen ord har ändrats. E-postmeddelandet skickas via SSPR-portalen till sina primära och alternativa e-postadresser som lagras i Azure AD. Ingen annan får ett meddelande om reset-händelsen.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Meddela alla administratörer när andra administratörer återställer sina lösen ord

Om det här alternativet är inställt på **Ja** får alla andra Azure-administratörer ett e-postmeddelande till sin primära e-postadress som lagras i Azure AD. E-postmeddelandet meddelar att en annan administratör har ändrat sitt lösen ord med hjälp av SSPR.

Tänk på följande exempel scenario:

* Det finns fyra administratörer i en miljö.
* Administratör *A* återställer lösen ordet med hjälp av SSPR.
* Administratörer *B*, *C* och *D* får en e-postavisering om att lösen ordet återställs.

## <a name="on-premises-integration"></a>Lokal integration

Om du har en hybrid miljö kan du konfigurera Azure AD Connect att skriva lösen ords ändrings händelser tillbaka från Azure AD till en lokal katalog.

![Validering av tillbakaskrivning av lösen ord är aktiverat och fungerar][Writeback]

Azure AD kontrollerar din aktuella hybrid anslutning och tillhandahåller något av följande meddelanden i Azure Portal:

* Din lokala tillbakaskrivning-klient är igång.
* Azure AD är online och är ansluten till din lokala tillbakaskrivning-klient. Det ser dock ut som att den installerade versionen av Azure AD Connect är inaktuell. Överväg att [uppgradera Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) för att säkerställa att du har de senaste anslutnings funktionerna och viktiga fel korrigeringar.
* Tyvärr kan vi inte kontrol lera din lokala tillbakaskrivning av tillbakaskrivning-klientens status eftersom den installerade versionen av Azure AD Connect är inaktuell. [Uppgradera Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) för att kunna kontrol lera anslutnings status.
* Tyvärr ser det ut som att vi inte kan ansluta till din lokala tillbakaskrivning-klient just nu. [Felsök Azure AD Connect](./troubleshoot-sspr-writeback.md) för att återställa anslutningen.
* Tyvärr kan vi inte ansluta till din lokala tillbakaskrivning-klient eftersom tillbakaskrivning av lösen ord inte har kon figurer ATS korrekt. [Konfigurera tillbakaskrivning av lösen ord](./tutorial-enable-sspr-writeback.md) för att återställa anslutningen.
* Tyvärr ser det ut som att vi inte kan ansluta till din lokala tillbakaskrivning-klient just nu. Detta kan bero på tillfälliga problem på vår sida. Om problemet kvarstår [felsöker du Azure AD Connect](./troubleshoot-sspr-writeback.md) för att återställa anslutningen.

Kom igång med SSPR tillbakaskrivning genom att följa de här självstudierna:

> [!div class="nextstepaction"]
> [Självstudie: Aktivera tillbakaskrivning av lösen ord för självbetjäning (SSPR)](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Skriv tillbaka lösen ord till din lokala katalog

Du kan aktivera tillbakaskrivning av lösen ord med hjälp av Azure Portal. Du kan också inaktivera tillbakaskrivning av lösen ord tillfälligt utan att behöva konfigurera om Azure AD Connect.

* Om alternativet är inställt på **Ja** är tillbakaskrivning aktiverat. Federerad, direktautentisering eller lösen ords-hash-synkroniserade användare kan återställa sina lösen ord.
* Om alternativet är inställt på **Nej**, inaktive ras tillbakaskrivning. Federerad autentisering, direktautentisering eller lösen ords-hash-synkroniserade användare kan inte återställa sina lösen ord.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Tillåt att användare låser upp konton utan att återställa sina lösen ord

Som standard låser Azure AD upp konton när en lösen ords återställning utförs. För att ge flexibilitet kan du välja att tillåta att användarna låser upp sina lokala konton utan att behöva återställa sina lösen ord. Använd den här inställningen för att avgränsa de två åtgärderna.

* Om det är inställt på **Ja** får användarna möjlighet att återställa sina lösen ord och låsa upp kontot eller för att låsa upp kontot utan att behöva återställa lösen ordet.
* Om det är inställt på **Nej** kan användarna bara utföra en kombinerad lösen ords återställning och konto upplåsning.

### <a name="on-premises-active-directory-password-filters"></a>Lösen ords filter för lokala Active Directory

SSPR utför motsvarigheten till en administratörs initierad lösen ords återställning i Active Directory. Om du använder ett lösen ords filter från tredje part för att tillämpa anpassade lösen ords regler och du kräver att det här lösen ords filtret kontrol leras under återställning av lösen ord i Azure AD, kontrollerar du att den tredje partens lösen ords filter-lösning är konfigurerad att tillämpas i scenariot för lösen ords återställning för administratörer. [Azure AD Password Protection för Active Directory Domain Services](concept-password-ban-bad-on-premises.md) stöds som standard.

## <a name="password-reset-for-b2b-users"></a>Lösen ords återställning för B2B-användare

Återställning av lösen ord och ändringar stöds helt och hållet i alla konfigurationer för Business-to-Business (B2B). B2B-återställning av användar lösen ord stöds i följande tre fall:

* **Användare från en partner organisation med en befintlig Azure AD-klient**: om organisationen som du samarbetar med har en befintlig Azure AD-klient ser vi hur principerna för återställning av lösen ord är aktiverade på klienten. För att lösen ords återställning ska fungera måste partner organisationen bara se till att Azure AD SSPR har Aktiver ATS. Det kostar inget extra att Microsoft 365 kunder.
* **Användare som registrerar sig via** självbetjänings registrering: om den organisation som du samarbetar med använde funktionen för [självbetjänings registrering](../enterprise-users/directory-self-service-signup.md) för att ansluta till en klient kan vi återställa lösen ordet med det e-postmeddelande som de har registrerat.
* **B2B-användare**: alla nya B2B-användare som skapats med hjälp av de nya [Azure AD B2B-funktionerna](../external-identities/what-is-b2b.md) kan också återställa sina lösen ord med det e-postmeddelande som de registrerade under den inbjudna processen.

För att testa det här scenariot går du till https://passwordreset.microsoftonline.com med någon av dessa partner användare. Om en annan e-postadress eller ett e-postmeddelande har definierats fungerar lösen ords återställning som förväntat.

> [!NOTE]
> Microsoft-konton som har beviljats gäst åtkomst till din Azure AD-klient, t. ex. från Hotmail.com, Outlook.com eller andra personliga e-postadresser, kan inte använda Azure AD SSPR. De behöver återställa sina lösen ord med hjälp av informationen i [när du inte kan logga in på Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) -artikeln.

## <a name="next-steps"></a>Nästa steg

Kom igång med SSPR genom att följa de här självstudierna:

> [!div class="nextstepaction"]
> [Självstudie: Aktivera självbetjäning för återställning av lösen ord (SSPR)](tutorial-enable-sspr.md)

Följande artiklar ger ytterligare information om lösenordsåterställning via Azure AD:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Azure AD-autentiseringsmetoder som är tillgängliga och kvantitet som krävs"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Konfigurera SSPR-registrerings alternativ i Azure Portal"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Lokal integrering för SSPR i Azure Portal"
