---
title: Frågor & svar om Microsoft Authenticator app – Azure AD
description: Vanliga frågor och svar (FAQ) om Microsoft Authentication-appen och tvåstegsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 8ddc02075d2cdeba4a4fa83a2475ec20c3f18d6e
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418087"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>Vanliga frågor och svar om Microsoft Authenticator-appen

I den här artikeln besvaras vanliga frågor om Microsoft Authenticator-appen. Om du inte ser något svar på din fråga går du till [Microsoft Authenticator app-forumet](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp).

Microsoft Authenticator-appen ersatte Azure Authenticator-appen och det är den rekommenderade appen när du använder Azure AD-Multi-Factor Authentication. Microsoft Authenticator-appen finns för [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) och [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="registering-a-device"></a>Registrera en enhet

**F**: registreras en enhet som kan ge företaget eller tjänst åtkomst till min enhet?

S **: när** du registrerar en enhet får enheten åtkomst till din organisations tjänster och ger inte din organisation åtkomst till din enhet.

### <a name="error-adding-account"></a>Fel vid tillägg av konto

**F**: när jag försöker lägga till mitt konto visas ett fel meddelande om att kontot som du försöker lägga till inte är giltigt för tillfället. Kontakta administratören för att åtgärda det här problemet (verifiering av unikhet). " Vad ska jag göra?

**A**: kontakta administratören och meddela att du inte kan lägga till ditt konto till autentisering på grund av ett verifierings problem. Du måste ange ditt inloggnings namn så att administratören kan se dig i din organisation.

### <a name="legacy-apns-support-deprecated"></a>Äldre APN-stöd är inaktuellt

**F**: eftersom det äldre binära gränssnittet för Apple Push Notification Service föråldras i november 2020, hur kan jag fortsätta att använda Microsoft Authenticator/telefon faktor för att logga in?

**A**: [Apple presenterade utfasning](https://developer.apple.com/news/?id=11042019a) av push-meddelanden som använder dess binära gränssnitt för iOS-enheter, till exempel de som används av telefon faktorn. För att fortsätta att ta emot push-meddelanden rekommenderar vi att användarna uppdaterar sin Authenticator-app till den senaste versionen av appen. Under tiden kan du kringgå det genom att manuellt söka efter meddelanden i Authenticator-appen.

### <a name="app-lock-feature"></a>Lås funktion för app

**F**: Vad är app lock och hur kan jag använda det för att hjälpa mig att skydda mig?

**A**: app lock hjälper till att hålla dina verifierings koder, appdata och appinställningar säkrare. När app lock är aktiverat uppmanas du att autentisera med enhetens PIN-kod eller bio mått varje gång du öppnar autentiseraren. Med appens lås kan du också se till att du är den enda som kan godkänna meddelanden genom att uppmana dig att ange din PIN-kod eller bio metrisk när du godkänner inloggnings aviseringen. Du kan aktivera eller inaktivera app lock på sidan autentisera inställningar. Som standard aktive ras app-låset när du ställer in en PIN-kod eller bio metrisk på enheten.<br><br>Tyvärr finns det ingen garanti för att app lock kommer att hindra någon från att komma åt autentiseraren. Det beror på att enhets registrering kan ske på andra platser utanför autentiseraren, t. ex. i Android-konto inställningar eller i Företagsportal-appen.

### <a name="windows-mobile-retired"></a>Windows Mobile har dragits tillbaka

**F**: Jag har en Windows Mobile-enhet och Microsoft Authenticator på Windows Mobile är inaktuell. Kan jag fortsätta autentisera med appen?

**A**: alla autentiseringar med Microsoft Authenticator på Windows Mobile kommer att dras tillbaka efter den 15 juli 2020. Vi rekommenderar starkt att du använder en alternativ autentiseringsmetod för att undvika att vara utelåst från dina konton.<br>Alternativa alternativ för företags användare är:<br><ul><li>Konfigurera Microsoft Authenticator för [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) eller [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>Konfigurera [SMS](multi-factor-authentication-setup-phone-number.md) för att ta emot verifierings koder.</li><li>Konfigurera telefonnumret för att ta emot [telefonsamtal för att verifiera deras identitet](multi-factor-authentication-setup-office-phone.md).</li></ul><br>Alternativa alternativ för personliga Microsoft-konto användare är:<br><ul><li>Konfigurera Microsoft Authenticator för [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) eller [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>Konfigurera en alternativ inloggnings metod (SMS eller e-post) genom att uppdatera din säkerhets information på [sidan Microsoft konto säkerhet](https://account.microsoft.com/security/).</li></ul>

### <a name="android-screenshots"></a>Android-skärm bilder

**F**: kan jag ta skärm bilder av mina eng ång slö sen ord (eng ång slö sen ord) på Android-autentiseraren?

**A**: från och med version 6.2003.1704 av Authenticator Android, som standard, döljs alla eng ång slö sen ord varje gång en skärm bild av autentiseraren tas. Om du vill se dina koder för eng ång slö sen ord i skärm bilder eller tillåta andra appar att avbilda verifierings skärmen kan du. Aktivera bara inställningen **skärmdump** i autentiseraren och starta om appen.

### <a name="delete-stored-data"></a>Ta bort lagrade data

**F**: vilken data autentiserar autentiseraren för mig och hur kan jag ta bort det?

**A**: Authenticator-appen samlar in tre typer av information:

- Konto information som du anger när du lägger till ditt konto. Du kan ta bort dessa data genom att ta bort ditt konto.
- Diagnostiska loggdata som bara finns i appen tills du **skickar kommentarer** i appens översta meny för att skicka loggar till Microsoft. Dessa loggar kan innehålla personliga data, till exempel e-postadresser, Server adresser eller IP-adresser. De kan också innehålla enhets data, till exempel enhets namn och operativ system version. Alla personliga data som samlas in är begränsade till information som behövs för att felsöka problem med appar. Du kan när som helst bläddra bland loggfilerna i appen för att se information som samlas in. Om du skickar dina loggfiler använder Authentication app-tekniker bara dem för att felsöka problem som rapporter ATS av kunden.
- Icke-personligt identifierbara användnings data, t. ex. "starta Lägg till konto flöde/lade till konto", "eller" meddelandet har godkänts. " Dessa data är en viktig del av våra tekniska beslut. Användningen hjälper oss att avgöra var vi kan förbättra apparna på ett sätt som är viktigt för dig. Du ser ett meddelande om den här data insamlingen när du använder appen för första gången. Det informerar dig om att det kan inaktive ras på appens **inställnings**   sida. Du kan aktivera eller inaktivera den här inställningen när du vill.

### <a name="codes-in-the-app"></a>Koder i appen

**F**: vilka är koderna i appen?

**A**: när du öppnar autentiseraren visas de konton som har lagts till som paneler. Dina arbets-eller skol konton och dina personliga Microsoft-konton har sex eller åtta siffror som visas i hel skärms läge för kontot (nås genom att trycka på konto panelen). För andra konton visas ett sex eller åtta siffer nummer på sidan **konton** i appen.<br>Du använder dessa koder som lösen ord för enkel användning för att kontrol lera att du är den du är. När du har loggat in med ditt användar namn och lösen ord skriver du in verifierings koden som är kopplad till det kontot. Om du t. ex. Katy loggar in på ditt Contoso-konto trycker du på konto panelen och använder sedan verifierings koden 895823. För Outlook-kontot följer du samma steg.<br>Tryck på panelen Contoso-konto.<br>![Konto paneler i Authenticator-appen](media/user-help-auth-app-faq/katy-signin.png)<br>När du har tryckt på panelen Contoso-konto visas verifierings koden i hel skärms läge.<br>![Verifierings kod i konto panelen i autentiseraren](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>Tids gräns för nedräkning

**F**: Varför ska talet bredvid koden fortsätta att räknas ned?

S **: det** kan hända att du ser en 30-sekunds uppräknings intervall bredvid din aktiva verifierings kod. Den här timern är så att du aldrig loggar in med samma kod två gånger. Till skillnad från ett lösen ord vill vi inte att du kommer ihåg det här numret. Idén är att bara någon med åtkomst till din telefon känner till din kod.

### <a name="grayed-account-tile"></a>Panel för grått konto

**F**: Varför är min konto panel grå?

**A**: vissa organisationer kräver att autentiseraren arbetar med enkel inloggning och för att skydda organisationens resurser. I den här situationen används inte kontot för tvåstegsverifiering och visas som grått eller inaktivt. Den här typen av konto kallas ofta ett "Broker"-konto.

### <a name="device-registration"></a>Enhetsregistrering

**F**: Vad är enhets registrering?

**A**: din organisation kan kräva att du registrerar enheten för att spåra åtkomsten till skyddade resurser, t. ex. filer och appar. De kan också aktivera villkorlig åtkomst för att minska risken för oönskad åtkomst till dessa resurser. Du kan avregistrera enheten i **Inställningar**, men du kan förlora åtkomsten till e-postmeddelanden i Outlook, filer i OneDrive och du förlorar möjligheten att använda telefonin loggning.

### <a name="verification-codes-when-connected"></a>Verifierings koder vid anslutning

**F**: måste jag vara ansluten till Internet eller mitt nätverk för att hämta och använda verifierings koderna?

**A**: koderna kräver inte att du är ansluten till Internet eller är ansluten till data, så du behöver inte använda telefon tjänsten för att logga in. Dessutom, eftersom appen slutar köras så fort du stänger den, kommer den inte att tömma batteriet.

### <a name="no-notifications-when-app-is-closed"></a>Inga meddelanden när appen är stängd

**F**: Varför får jag bara meddelanden när appen är öppen? När appen är stängd får jag inga meddelanden.

**A**: om du får meddelanden, men inte en avisering, även om du har ringt på, bör du kontrol lera inställningarna för appen. Kontrol lera att appen är aktive rad för att använda ljud eller för att vibrera meddelanden. Om du inte får meddelanden alls bör du kontrol lera följande villkor:<ul><li>Är din telefon i stör inte eller tyst läge? Dessa lägen kan förhindra att appar skickar meddelanden.</li><li>Kan du få meddelanden från andra appar? Om inte, kan det vara problem med nätverks anslutningarna på din telefon, eller meddelande kanalen från Android eller Apple. Du kan försöka lösa dina nätverks anslutningar via dina telefon inställningar. Du kan behöva prata med din tjänst leverantör för att hjälpa till med Android-eller Apple Notifications-kanalen.</li><li>Kan du få aviseringar för vissa konton i appen, men inte andra? Om ja, ta bort det problematiska kontot från appen, Lägg till det igen och se om det löser problemet.</li></ul>Om du har provat alla dessa steg och fortfarande har problem, rekommenderar vi att du skickar dina loggfiler för diagnostik. Öppna appen, gå till appens översta meny och välj sedan **Skicka feedback**. Därefter går du till [Microsoft Authenticator app-forumet](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) och ber dig att se vad du har för problem med Microsoft.

### <a name="switch-to-push-notifications"></a>Växla till push-meddelanden

**F**: Jag använder verifierings koderna i appen, men hur växlar jag till push-meddelanden?

**A**: du kan ställa in aviseringar för ditt arbets-eller skol konto (om det tillåts av administratören) eller för din personliga Microsoft-konto. Aviseringar fungerar inte för konton från tredje part, som Google eller Facebook.<br>Om du vill växla ditt personliga konto till aviseringar måste du registrera enheten på nytt med kontot. Gå till **Lägg till konto**, Välj **personligt Microsoft-konto** och logga sedan in med ditt användar namn och lösen ord.<br>För ditt arbets-eller skol konto bestämmer din organisation om du vill tillåta ett meddelande med en klickning.

### <a name="notifications-for-other-accounts"></a>Aviseringar för andra konton

**F**: fungerar aviseringar för konton som inte kommer från Microsoft?

**A**: Nej, meddelanden fungerar bara med Microsoft-konton och Azure Active Directory-konton. Om ditt arbete eller din skola använder Azure AD-konton kan de stänga av den här funktionen.

### <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

**F**: Jag har fått en ny enhet eller återställt enheten från en säkerhets kopia. Hur gör jag för att konfigurerar du Mina konton i autentiseraren igen?

**A**: om du har aktiverat **säkerhets kopiering av molnet** på din gamla enhet kan du använda din gamla säkerhets kopia för att återställa dina autentiseringsuppgifter på din nya iOS-eller Android-enhet. Mer information finns i artikeln [säkerhetskopiera och återställa autentiseringsuppgifter för konto med autentiseraren](user-help-auth-app-backup-recovery.md) .

### <a name="lost-device"></a>Borttappad enhet

**F**: Jag förlorade enheten eller flyttade den till en ny enhet. Hur gör jag för att se till att meddelanden inte fortsätter att gå till min gamla enhet?

**A**: om du lägger till autentiseraren till den nya enheten tas appen inte bort automatiskt från den gamla enheten. Det räcker inte att ta bort appen från den gamla enheten. Du måste både ta bort appen från din gamla enhet och be Microsoft eller din organisation att glömma och avregistrera den gamla enheten.<ul><li>**Ta bort appen från en enhet med hjälp av en personlig Microsoft-konto.** Gå till avsnittet tvåstegsverifiering på din [konto säkerhets](https://account.microsoft.com/security)   sida och välj att inaktivera verifiering för den gamla enheten.</li><li>**Ta bort appen från en enhet med ett arbets-eller skol Microsoft-konto.** Gå till avsnittet tvåstegsverifiering på [sidan Mina appar](https://myapps.microsoft.com/) eller din organisations anpassade Portal för att stänga av verifieringen av den gamla enheten.</li></ul>

### <a name="remove-account-from-app"></a>Ta bort kontot från appen

**F**: Hur gör jag för att ta bort ett konto från appen?

S **: Tryck** på konto panelen för det konto som du vill ta bort från appen för att Visa kontots fulla skärm. Tryck på **ta bort konto** för att ta bort kontot från appen.<br>Om du har en enhet som har registrerats i din organisation kan du behöva ett extra steg för att ta bort ditt konto. På dessa enheter registreras autentiseraren automatiskt som enhets administratör. Om du vill avinstallera appen fullständigt måste du först avregistrera appen i appens inställningar.

### <a name="too-many-permissions"></a>För många behörigheter

**F**: Varför begär appen så många behörigheter?

**A**: här är en fullständig lista över behörigheter som kan tillfrågas och hur de används av appen. Vilka behörigheter som visas beror på vilken typ av telefon du har.<ul><li>**Plats**. Ibland vill din organisation veta din plats innan du får åtkomst till vissa resurser. Appen kommer att begära den här behörigheten endast om din organisation har en princip som kräver plats.</li><li>**Använd bio metrisk maskin vara.** Vissa arbets-och skol konton kräver ytterligare en PIN-kod när du verifierar din identitet. Appen kräver ditt medgivande för att använda Bio metrisk eller ansikts igenkänning i stället för att ange PIN-koden.</li><li>**Digitalkameror.** Används för att skanna QR-koder när du lägger till en arbets plats, skola eller icke-Microsoft-konto.</li><li>**Kontakter och telefon.** Appen kräver den här behörigheten för att kunna söka efter arbets-eller skol Microsoft-konton på din telefon och lägga till dem i appen åt dig.</li><li>**Statusmeddelanden.** Används för att se till att ditt telefonnummer matchar numret på posten när du loggar in med din personliga Microsoft-konto för första gången. Vi skickar ett SMS till den telefon där du installerade appen som innehåller en 6-8-siffrig verifierings kod. Du behöver inte hitta den här koden och ange den eftersom autentiseraren hittar den automatiskt i SMS-meddelandet.</li><li>**Rita över andra appar.** Det meddelande du får som verifierar din identitet visas också i alla andra appar som körs.</li><li>**Ta emot data från Internet.** Den här behörigheten krävs för att skicka meddelanden.</li><li>**Förhindra att telefonen är i ström spar läge.** Om du registrerar din enhet i din organisation kan din organisation ändra den här principen på din telefon.</li><li>**Kontrol lera vibrationer.** Du kan välja om du vill ha en vibration när du får ett meddelande om att verifiera din identitet.</li><li>**Använd finger avtrycks maskin vara.** Vissa arbets-och skol konton kräver ytterligare en PIN-kod när du verifierar din identitet. För att göra processen enklare kan du använda ditt finger avtryck i stället för att ange PIN-koden.</li><li> **Visa nätverks anslutningar.** När du lägger till en Microsoft-konto kräver appen nätverks-/Internet anslutning.</li><li>**Läs innehållet i lagringen**. Den här behörigheten används bara när du rapporterar ett tekniskt problem via appens inställningar. Viss information från din lagring samlas in för att diagnostisera problemet.</li><li>**Fullständig nätverks åtkomst.** Den här behörigheten krävs för att skicka meddelanden för att verifiera din identitet.</li><li>**Körs vid start.** Om du startar om din telefon säkerställer den här behörigheten att du fortsätter att få meddelanden om att verifiera din identitet.</li></ul>

### <a name="approve-requests-without-unlocking"></a>Godkänn begär Anden utan att låsa upp

**F**: Varför kan autentiseraren godkänna en begäran utan att låsa upp enheten?

**A**: du behöver inte låsa upp enheten för att godkänna verifierings förfrågningar eftersom allt du behöver för att bevisa är att du har din telefon med dig. Tvåstegsverifiering kräver att du bevisar två saker – en sak som du känner till och ett som du har. Det du känner till är ditt lösen ord. Det du har är din telefon (Konfigurera med autentiserare och registrerad som ett Multi-Factor Authentication-bevis). Därför uppfyller din telefon och godkännande av begäran villkoren för den andra faktorn för autentisering.

### <a name="activity-notifications"></a>Aktivitets aviseringar

**F**: Varför får jag meddelanden om min konto aktivitet?

**A**: aktivitets aviseringar skickas till autentiseraren omedelbart när en ändring görs i dina personliga Microsoft-konton, vilket hjälper dig att skydda dig. Vi har tidigare skickat dessa meddelanden endast via e-post och SMS. Mer information om dessa aktivitets aviseringar finns i [Vad händer om det finns en ovanlig inloggning till ditt konto](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in). Om du vill ändra var du får dina meddelanden loggar du in på sidan [där kan vi kontakta dig med aviseringar om icke-kritiska konton](https://account.live.com/SecurityNotifications/Update) för ditt konto.

### <a name="one-time-passcodes"></a>Eng ång slö sen ord

**F**: min eng ång slö sen ord fungerar inte. Vad ska jag göra?

**A**: kontrol lera att datum och tid på enheten är korrekta och synkroniseras automatiskt. Om datumet och tiden är fel eller är osynkroniserade fungerar inte koden.

### <a name="windows-10-mobile"></a>Windows 10 Mobil

**F**: Windows 10 mobil operativ systemet var föråldrat december 2019. Kommer Microsoft Authenticator på Windows Mobile-operativsystem att vara inaktuellt också?

**A**: autentiseraren på alla Windows Mobile-operativsystem kommer inte att stödjas efter 28 feb 2020. Användarna kommer inte att kunna ta emot några nya uppdateringar av appen efter det datum som anges ovan. Efter den 28 feb 2020 kommer Microsoft-tjänster som för närvarande stöder autentisering med hjälp av Microsoft Authenticator på alla Windows Mobile-operativsystem att börja dra tillbaka sin support. För att kunna autentisera till Microsoft-tjänster uppmuntrar vi starkt att alla våra användare byter till en alternativ mekanism innan detta datum.

### <a name="default-mail-app"></a>Standard app för e-post

**F**: när du loggar in på mitt arbets-eller skol konto med hjälp av standard-e-postappen som medföljer iOS, uppmanas jag att ange autentiseraren för min säkerhets verifierings information. När jag har angett informationen och återgår till e-postappen får jag ett fel meddelande. Vad kan jag göra?

**A**: detta inträffar förmodligen eftersom din inloggning och ditt e-postprogram sker i två olika appar, vilket gör att den inledande bakgrunds inloggnings processen slutar fungera och inte fungerar. För att försöka åtgärda detta rekommenderar vi att du väljer **Safari** -ikonen längst ned till höger på skärmen när du loggar in på e-postappen. Genom att flytta till Safari sker hela inloggnings processen i en enda app, så att du kan logga in på appen.

### <a name="apple-watch-watchos-7"></a>Apple Watch-Watch 7

**F**: varför jag har problem med Apple Watch på klockan 7?

**A**: det finns ett problem med att godkänna meddelanden på klockan 7 och vi arbetar med Apple för att få detta åtgärdat. Under tiden ska alla meddelanden som kräver Microsoft Authenticator bevaka app godkännas på din telefon i stället.

### <a name="apple-watch-doesnt-show-accounts"></a>Apple Watch visar inte konton

**F**: Varför visas inte alla mina konton när jag öppnar autentiseraren på min Apple Watch?

**A**: autentiseraren stöder endast Microsoft personal-eller skol-eller arbets konton med push-meddelanden i Apple Watch Companion-appen. För dina andra konton, som Google eller Facebook, måste du öppna Authenticator-appen på din telefon för att se dina verifierings koder.

### <a name="apple-watch-notifications"></a>Apple Watch-meddelanden

**F**: Varför kan jag inte godkänna eller neka meddelanden på min Apple Watch?

**A**: kontrol lera först att du har uppgraderat till Authenticator version 6.0.0 eller högre på din iPhone. Sedan öppnar du appen Microsoft Authenticator Companion på din Apple Watch och letar efter alla konton med en **inställnings** knapp under dem. Slutför installations processen för att godkänna meddelanden för dessa konton.

### <a name="apple-watch-communication-error"></a>Apple Watch-kommunikations fel

**F**: Jag får ett kommunikations fel mellan Apple Watch och min telefon. Vad kan jag göra för att felsöka?

**A**: det här felet uppstår när din Watch-skärm försätts i vilo läge innan den har slutfört kommunikationen med din telefon.<br><b>Om felet inträffar under installationen:</b><br>Försök att köra installations programmet igen och se till att se till att du är aktiv tills processen är klar. Öppna appen på din telefon och svara på eventuella prompter som visas.<br>Om din telefon och Watch fortfarande inte kommunicerar kan du prova med följande åtgärder:<ol><li>Tvinga att avsluta Microsoft Authenticator Phone-appen och öppna den igen på din iPhone.</li><li>Tvinga att avsluta Companion-appen på din Apple Watch.<ol><li> Öppna appen Microsoft Authenticator Companion i klockan</li><li>Håll ned sido knappen tills skärmen **stängs** visas.</li><li>Släpp sido knappen och håll ned den digitala Kronheten för att framtvinga avslut av den aktiva appen.</li></ol></li><li>Stäng av både Bluetooth och Wi-Fi för både din telefon och din Watch, och slå sedan på dem igen.</li><li>Starta om iPhone och titta på.</li></ol><b>Om felet inträffar när du försöker godkänna ett meddelande:</b><br>Nästa gång du försöker godkänna ett meddelande på din Apple Watch ska du hålla skärmen aktiv tills förfrågan är klar och du hör ljudet som indikerar att det lyckades.

### <a name="apple-watch-companion-app-not-syncing"></a>Apple Watch Companion-appen synkroniserar inte

**F**: Varför är inte Microsoft Authenticator Companion-appen för Apple Watch-synkronisering eller visas i min Watch?

**A**: om appen inte visas i klockan kan du prova följande: <ol><li>Se till att klockan kör klockan 4,0 eller senare.</li><li>Synkronisera klockan igen.</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>Apple Watch Companion-appen kraschade

**F**: min Apple Watch Companion-app kraschade. Kan jag skicka mina krasch loggar så att du kan undersöka?

S **: du** måste först se till att du har valt att dela din analys med oss. Om du är en TestFlight-användare är du redan registrerad. Annars kan du gå till **inställningar > sekretess > analys** och välja båda alternativen **dela iPhone-& se analys** och **dela med Apps** .<br>När du har registrerat dig kan du försöka återskapa din krasch så att dina krasch loggar skickas automatiskt till oss för att undersöka detta. Men om du inte kan återskapa din krasch kan du manuellt kopiera dina loggfiler och skicka dem till oss.<ol><li>Öppna appen titta på din telefon, gå till **inställningar > allmänt** och klicka sedan på **Kopiera bevaknings analys**.</li><li>Hitta motsvarande krasch under **inställningar > sekretess > analys > analys data** och kopiera sedan hela texten manuellt.</li><li>Vill du öppna autentiseraren på din telefon och klistra in den kopierade texten i rutan  **Beskriv problemet som du står inför** under **har du problem?** på sidan  **Skicka feedback** . </li></ol>

## <a name="autofill-with-authenticator"></a>Autofyll med autentiserare

**F**: Vad är Autofyll med autentiseraren?

**A**: Authentication-appen lagrar och fyller på lösen ord på ett säkert sätt för appar och webbplatser som du besöker på din telefon. Du kan använda Autofyll för att synkronisera och Autofyll dina lösen ord på dina iOS-och Android-enheter. När du har konfigurerat Authenticator-appen som en Autofyll-Provider på din telefon, kan du spara dina lösen ord när du anger dem på en plats eller på en inloggnings sida för appen. Lösen orden sparas som en del av [din personliga Microsoft-konto](https://account.microsoft.com/account) och är också tillgängliga när du loggar in på Microsoft Edge med din personliga Microsoft-konto.

**F**: vilken information kan autoautentiseras för mig?

**A**: autentiseraren kan fylla i användar namn och lösen ord på webbplatser och appar som du besöker på din telefon.

**F**: Hur gör jag för att aktivera Autofyll för lösen ord i autentiseraren på min telefon?

**A**: Följ dessa steg:

1. Öppna Authenticator-appen.
1. På fliken **lösen ord** i autentiserare väljer du **Logga in med Microsoft** och loggar in med [ditt Microsoft-konto](https://account.microsoft.com/account). Den här funktionen har för närvarande endast stöd för Microsoft-konton och har ännu inte stöd för arbets-eller skol konton.

**F**: Hur gör jag för att göra autentiseraren till standardprovidern för Autofyll på min telefon?

**A**: Följ dessa steg:

1. Öppna Authenticator-appen.
1. På fliken **lösen ord** i appen väljer du **Logga in med Microsoft** och loggar in med [ditt Microsoft-konto](https://account.microsoft.com/account).
1. Gör något av följande:

   - Under **Inställningar** på iOS väljer du **hur du vill aktivera Autofyll** i avsnittet Inställningar för Autofyll för att lära dig hur du ställer in Authenticator som standardprovider för Autofyll.
   - På Android under **Inställningar** väljer du **Ange som Autofyll Provider** i avsnittet Inställningar för Autofyll.

**F**: Vad händer om **Autofyll** inte är tillgängligt för mig i inställningar?

**A**: om Autofyll inte är tillgängligt för dig i autentiseraren kan det bero på att Autofyll ännu inte har tillåtits för din organisation eller konto typ. Du kan använda den här funktionen på en enhet där ditt arbets-eller skol konto inte har lagts till. Mer information om hur du tillåter Autofyll för din organisation finns i [Autofyll för IT-administratörer](#autofill-for-it-admins).

**F**: Hur gör jag för att stoppa synkroniseringen av lösen ord?

**A**: om du vill stoppa synkroniseringen av lösen ord i Authenticator-appen öppnar du **Inställningar**  >  **Autofyll inställningar**  >  **Sync-konto**. På nästa skärm kan du välja **stoppa synkronisering och ta bort alla Autofyll-data**. Detta tar bort lösen ord och andra Autofyll-data från enheten. Att ta bort Autofyll-data påverkar inte Multi-Factor Authentication.

**F**: Hur skyddas mina lösen ord av Authenticator-appen?

**A**: Authenticator-appen tillhandahåller redan en hög säkerhets nivå för Multi-Factor Authentication och konto hantering, och samma höga säkerhets fält utökas också för att hantera dina lösen ord.

- **Stark autentisering krävs av Authenticator-appen**: inloggning till autentisering kräver en andra faktor. Det innebär att dina lösen ord i Authenticator-appen skyddas även om någon har Microsoft-konto lösen ord.
- **Autofyll-data skyddas med biometrik och lösen ord**: innan du kan fylla i lösen ord på en app eller på en webbplats kräver autentiseraren bio metrisk eller enhets lösen ord. På så sätt kan du lägga till extra säkerhet så att även om någon annan har åtkomst till din enhet, kan de inte fylla eller se ditt lösen ord eftersom de inte kan tillhandahålla bio metrisk eller PIN-kod för enheten. Dessutom kan en användare inte öppna sidan lösen ord om de inte anger bio metrisk eller PIN-kod, även om de inaktiverar app lock i appinställningar.
- **Krypterade lösen ord på enheten**: lösen ord på enheten är krypterade och krypterings-/dekrypterings nycklar lagras aldrig och genereras alltid vid behov. Lösen ord dekrypteras endast när användaren vill, det vill säga under Autofyll eller när användaren vill se lösen ordet, som kräver bio metrisk eller PIN-kod.
- **Moln-och nätverks säkerhet**: dina lösen ord i molnet krypteras och dekrypteras bara när de når enheten. Lösen orden synkroniseras via en SSL-skyddad HTTPS-anslutning, som hjälper till att förhindra att en angripare åsidosätter känsliga data när de synkroniseras. Vi garanterar också att vi kontrollerar Sanity för data som synkroniseras över nätverket med hjälp av krypterade hash-funktioner (specifikt hash-baserad meddelande kod).

## <a name="autofill-for-it-admins"></a>Autofyll för IT-administratörer

**F**: kommer mina anställda eller studenter att få använda lösen ord Autofyll i Authenticator-appen?

**A**: Ja, Autofyll för dina [personliga Microsoft-konton](https://go.microsoft.com/fwlink/?linkid=2144423) fungerar nu för de flesta företags användare även när ett arbets-eller skol konto läggs till i Authenticator-appen. Du kan fylla i ett formulär för att tillåta eller neka Autofyll för din organisation och [skicka det till Authenticator-teamet](https://aka.ms/ConfigureAutofillInAuthenticator). Autofyll är för närvarande inte tillgängligt för arbets-eller skol konton.

**F**: kommer mina användares lösen ord för arbets-eller skol kontot att synkroniseras automatiskt?

**A**: Nej. Lösen ord för Autofyll synkroniserar inte lösen ord för arbets-eller skol konton för dina användare. När användare besöker en webbplats eller en app, erbjuder autentiseraren att spara lösen ordet för platsen eller appen, och lösen ordet sparas bara när användaren väljer.
  
**F**: kan jag endast tillåten vissa användare av min organisation för Autofyll?

**A**: Nej. Företag kan bara aktivera lösen ord Autofyll för alla eller ingen av sina anställda för tillfället.

**F**: Vad händer om min medarbetare eller student har flera arbets-eller skol konton? Min medarbetare har till exempel konton från flera företag eller skolor i sin Microsoft Authenticator.

**A**: alla företag eller skolor som har lagts till i Authenticator-appen måste vara tillåtna – listat för Autofyll i Authenticator för att appens ägare ska kunna använda den. Ett undantag till den här begränsningen är när din medarbetare eller student lägger till sitt arbets-eller skol konto i Microsoft Cloud-baserad Multi-Factor Authentication som ett [externt konto eller ett konto från tredje part](user-help-auth-app-add-non-ms-account.md).

## <a name="next-steps"></a>Nästa steg

- Om du har problem med att hämta din verifierings kod för din personliga Microsoft-konto kan du läsa avsnittet **fel sökning av verifierings kod problem** i artikeln [Microsoft-konto säkerhets information & verifierings koder](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- Om du vill ha mer information om tvåstegsverifiering kan du läsa Konfigurera [mitt konto för](multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering

- Om du vill ha mer information om säkerhets information, se [Översikt över säkerhets information (för hands version)](./security-info-setup-signin.md)

- Om din fråga inte besvarades här, vill vi gärna höra från dig. Gå till [Microsoft Authenticator app-forumet](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) för att publicera din fråga och få hjälp från communityn eller lämna en kommentar på den här sidan.
