---
title: Utvärdering av kontinuerlig åtkomst i Azure AD
description: Svara på ändringar i användartillståndet snabbare med kontinuerlig utvärdering av åtkomst i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74009759bb9ca2a0516148fc1387b150b67452ab
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387912"
---
# <a name="continuous-access-evaluation"></a>Utvärdering av kontinuerlig åtkomst

Förfallodatum för token och uppdatering är en standardmekanism i branschen. När ett klientprogram som Outlook ansluter till en tjänst som Exchange Online auktoriserats API-begäranden med hjälp av OAuth 2.0-åtkomsttoken. Som standard är dessa åtkomsttoken giltiga i en timme. När de upphör att gälla omdirigeras klienten tillbaka till Azure AD för att uppdatera dem. Uppdateringsperioden ger möjlighet att omvärdera principer för användaråtkomst. Vi kan till exempel välja att inte uppdatera token på grund av en princip för villkorlig åtkomst eller eftersom användaren har inaktiverats i katalogen. 

Kunder har uttryckt oro över fördröjningen mellan när villkoren ändras för användaren, till exempel nätverksplats eller stöld av autentiseringsuppgifter, och när principer kan tillämpas relaterade till ändringen. Vi har experimenterat med metoden med "objekt mot objekt" med kortare livslängd för token, men upptäckt att de kan försämra användarupplevelser och tillförlitlighet utan att eliminera risker.

Snabba svar på principöverträdelser eller säkerhetsproblem kräver verkligen en "konversation" mellan tokenutfärdaren, till exempel Azure AD, och den förlitande parten, som Exchange Online. Den här tvåvägskonversationen ger oss två viktiga funktioner. Den förlitande parten kan se när saker har ändrats, till exempel en klient som kommer från en ny plats, och meddela tokenutfärdaren. Det ger också tokenutfärdaren ett sätt att be den förlitande parten att sluta respektera token för en viss användare på grund av kontokompromettering, inaktivering eller andra problem. Mekanismen för den här konversationen är utvärdering av kontinuerlig åtkomst (CAE). Målet är att svaret ska vara nära realtid, men i vissa fall kan svarstider på upp till 15 minuter observeras på grund av händelsens spridningstid.

Den inledande implementeringen av kontinuerlig utvärdering av åtkomst fokuserar på Exchange, Teams och SharePoint Online.

Information om hur du förbereder dina program för att använda CAE finns [i Använda Kontinuerlig tillgänglighetskontroll-aktiverade API:er i dina program.](../develop/app-resilience-continuous-access-evaluation.md)

### <a name="key-benefits"></a>Viktiga fördelar

- Ändring/återställning av användaravslut eller lösenord: Återkallelse av användarsessioner tillämpas nästan i realtid.
- Ändring av nätverksplats: Platsprinciper för villkorlig åtkomst tillämpas nästan i realtid.
- Tokenexport till en dator utanför ett betrott nätverk kan förhindras med platsprinciper för villkorlig åtkomst.

## <a name="scenarios"></a>Scenarier 

Det finns två scenarier som utgör kontinuerlig utvärdering av åtkomst, utvärdering av kritiska händelser och utvärdering av principer för villkorlig åtkomst.

### <a name="critical-event-evaluation"></a>Utvärdering av kritiska händelser

Kontinuerlig utvärdering av åtkomst implementeras genom att aktivera tjänster som Exchange Online, SharePoint Online och Teams för att prenumerera på kritiska händelser i Azure AD så att dessa händelser kan utvärderas och framtvingas nästan i realtid. Utvärdering av kritiska händelser förlitar sig inte på principer för villkorsstyrd åtkomst, så den är tillgänglig i alla klienter. Följande händelser utvärderas för närvarande:

- Användarkontot tas bort eller inaktiveras
- Lösenordet för en användare ändras eller återställs
- Multifaktorautentisering har aktiverats för användaren
- Administratören återkallar uttryckligen alla uppdateringstoken för en användare
- Hög användarrisk identifieras av Azure AD Identity Protection

Den här processen möjliggör ett scenario där användare förlorar åtkomst till organisationens SharePoint Online-filer, e-post, kalender eller uppgifter och Teams från Microsoft 365-klientappar inom några minuter efter någon av dessa kritiska händelser. 

> [!NOTE] 
> Teams och SharePoint Online stöder inte användarriskhändelser ännu.

### <a name="conditional-access-policy-evaluation-preview"></a>Utvärdering av princip för villkorlig åtkomst (förhandsversion)

Exchange och SharePoint kan synkronisera viktiga principer för villkorsstyrd åtkomst så att de kan utvärderas i själva tjänsten.

Den här processen möjliggör ett scenario där användare förlorar åtkomst till organisationsfiler, e-post, kalender eller uppgifter från Microsoft 365-klientappar eller SharePoint Online direkt efter ändringar av nätverksplats.

> [!NOTE]
> Det är inte alla kombinationer av appar och resursproviderr som stöds. Se tabellen nedan. Office refererar till Word, Excel och PowerPoint.

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Stöds | Stöds | Stöds | Stöds | Stöds |
| **Exchange Online** | Stöds | Stöds | Stöds | Stöds | Stöds |

| | Office-webbappar | Office Win32-appar | Office för iOS | Office för Android | Office för Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Stöds inte | Stöds | Stöds | Stöds | Stöds |
| **Exchange Online** | Stöds inte | Stöds | Stöds | Stöds | Stöds |

| | OneDrive-webb | OneDrive Win32 | OneDrive iOS | OneDrive Android | OneDrive Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Stöds | Stöds | Stöds | Stöds | Stöds |

### <a name="client-side-claim-challenge"></a>Anspråksutmaning på klientsidan

Innan utvärderingen av kontinuerlig åtkomst skulle klienterna alltid försöka spela upp åtkomsttoken från cacheminnet så länge den inte har upphört att gälla. Med CAE introducerar vi ett nytt ärende som en resursprovider kan avvisa en token även om den inte har upphört att gälla. För att informera klienter om att kringgå sin cache trots att cachelagrade  token inte har upphört att gälla, inför vi en mekanism som kallas anspråksfråga för att indikera att token avvisades och att en ny åtkomsttoken måste utfärdas av Azure AD. CAE kräver en klientuppdatering för att förstå anspråksutmaningen. Den senaste versionen av följande program nedan stöder anspråksutmaning:

| | Webb | Win32 | iOS | Android | Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Outlook** | Stöds | Stöds | Stöds | Stöds | Stöds |
| **Teams** | Stöds | Stöds | Stöds | Stöds | Stöds |
| **Office** | Stöds inte | Stöds | Stöds | Stöds | Stöds |
| **OneDrive** | Stöds | Stöds | Stöds | Stöds | Stöds |

### <a name="token-lifetime"></a>Tokenlivslängd

Eftersom risker och principer utvärderas i realtid förlitar sig klienter som förhandlar med utvärderingsmedvetna sessioner på CAE i stället för befintliga livslängdsprinciper för statiska åtkomsttoken, vilket innebär att konfigurerbara livslängdsprinciper för token inte längre respekteras för CAE-kompatibla klienter som förhandlar CAE-medvetna sessioner.

Tokenlivslängden ökar till att vara långlivad, upp till 28 timmar, i CAE-sessioner. Återkallning drivs av kritiska händelser och principutvärdering, inte bara en godtycklig tidsperiod. Den här ändringen ökar stabiliteten för program utan att påverka säkerheten. 

Om du inte använder CAE-kompatibla klienter förblir standardlivslängden för åtkomsttoken 1 timme såvida du inte har konfigurerat livslängden för din åtkomsttoken med förhandsfunktionen [CTL (Configurable Token Lifetime).](../develop/active-directory-configurable-token-lifetimes.md)

## <a name="example-flows"></a>Exempelflöden

### <a name="user-revocation-event-flow"></a>Händelseflöde för användaråterkallning:

![Händelseflöde för användaråterkallning](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. En CAE-kompatibel klient presenterar autentiseringsuppgifter eller en uppdateringstoken till Azure AD som ber om en åtkomsttoken för en viss resurs.
1. En åtkomsttoken returneras tillsammans med andra artefakter till klienten.
1. En administratör [återkallar uttryckligen alla uppdateringstoken för användaren](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). En återkallningshändelse skickas till resursprovidern från Azure AD.
1. En åtkomsttoken visas för resursprovidern. Resursprovidern utvärderar giltigheten för token och kontrollerar om det finns någon återkallningshändelse för användaren. Resursprovidern använder den här informationen för att bestämma sig för att bevilja åtkomst till resursen eller inte.
1. I det här fallet nekar resursprovidern åtkomst och skickar tillbaka en 401+-anspråksutmaning till klienten.
1. Den CAE-kompatibla klienten förstår anspråksutmaningen 401+. Den kringgår cacheminnen och går tillbaka till steg 1 och skickar sin uppdateringstoken tillsammans med anspråksutmaningen tillbaka till Azure AD. Azure AD kommer sedan att omvärdera alla villkor och uppmana användaren att omauktorera i det här fallet.

### <a name="user-condition-change-flow-preview"></a>Ändringsflöde för användarvillkor (förhandsversion):

I följande exempel har en administratör för villkorsstyrd åtkomst konfigurerat en platsbaserad princip för villkorsstyrd åtkomst som endast tillåter åtkomst från specifika IP-intervall:

![Händelseflöde för användarvillkor](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. En CAE-kompatibel klient presenterar autentiseringsuppgifter eller en uppdateringstoken till Azure AD som ber om en åtkomsttoken för en viss resurs.
1. Azure AD utvärderar alla principer för villkorlig åtkomst för att se om användaren och klienten uppfyller villkoren.
1. En åtkomsttoken returneras tillsammans med andra artefakter till klienten.
1. Användaren flyttas utanför ett tillåtet IP-intervall
1. Klienten presenterar en åtkomsttoken för resursprovidern utanför ett tillåtet IP-intervall.
1. Resursprovidern utvärderar giltigheten för token och kontrollerar platsprincipen som synkroniseras från Azure AD.
1. I det här fallet nekar resursprovidern åtkomst och skickar en 401+ anspråksutmaning tillbaka till klienten eftersom den inte kommer från det tillåtna IP-intervallet.
1. Den CAE-kompatibla klienten förstår anspråksutmaningen 401+. Den kringgår cacheminnen och går tillbaka till steg 1 och skickar sin uppdateringstoken tillsammans med anspråksutmaningen tillbaka till Azure AD. Azure AD omvärderar alla villkor och nekar åtkomst i det här fallet.

## <a name="enable-or-disable-cae-preview"></a>Aktivera eller inaktivera CAE (förhandsversion)

1. Logga in på **Azure Portal** administratör för villkorlig åtkomst, säkerhetsadministratör eller global administratör
1. Bläddra till **Azure Active Directory**  >  **Utvärdering av** kontinuerlig åtkomst för  >  **säkerhet.**
1. Välj **Aktivera förhandsgranskning.**

På den här sidan kan du välja att begränsa de användare och grupper som ska omfattas av förhandsversionen.

![Aktivera CAE-förhandsversionen i Azure Portal](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Felsökning

### <a name="supported-location-policies"></a>Platsprinciper som stöds

För CAE har vi bara insikter om namngivna IP-baserade namngivna platser. Vi har inga insikter om andra platsinställningar som [betrodda IP-adresser](../authentication/howto-mfa-mfasettings.md#trusted-ips) för MFA eller landsbaserade platser. När användaren kommer från en betrodd IP-adress för MFA eller betrodda platser som innehåller betrodda IP-adresser för MFA eller land, tillämpas INTE CAE efter att användaren har flyttat till en annan plats. I dessa fall utfärdar vi en CAE-token på 1 timme utan omedelbar KONTROLL av IP-tvingande.

> [!IMPORTANT]
> När du konfigurerar platser för utvärdering av kontinuerlig åtkomst ska du endast använda det [IP-baserade](../conditional-access/location-condition.md) villkor för platsen för villkorsstyrd åtkomst och konfigurera alla IP-adresser, inklusive **både IPv4 och IPv6,** som kan ses av din identitetsprovider och resursprovider. Använd inte platsvillkor för land eller den betrodda IPS-funktion som är tillgänglig på sidan för tjänstinställningar för Azure AD Multi-Factor Authentication.

### <a name="ip-address-configuration"></a>Konfigurera IP-adress

Din identitetsprovider och resursprovider kan se olika IP-adresser. Det här matchningsfelet kan inträffa på grund av implementeringar av nätverksproxy i din organisation eller felaktiga IPv4/IPv6-konfigurationer mellan identitetsprovidern och resursprovidern. Exempel:

- Identitetsprovidern ser en IP-adress från klienten.
- Resursprovidern ser en annan IP-adress än klienten när den har passerat via en proxyserver.
- IP-adressen som din identitetsprovider ser är en del av ett tillåtet IP-intervall i principen, men IP-adressen från resursprovidern är det inte.

Om det här scenariot finns i din miljö för att undvika oändliga loopar utfärdar Azure AD en timmes CAE-token och kommer inte att framtvinga en ändring av klientens plats. Även i det här fallet förbättras säkerheten jämfört med traditionella entimmetoken eftersom vi fortfarande utvärderar de andra händelserna förutom klientplatsens ändringshändelser. [](#critical-event-evaluation)

### <a name="office-and-web-account-manager-settings"></a>Office- och Webbkontohanterare inställningar

| Office Update-kanal | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Semi-Annual Enterprise Channel | Om det är inställt på aktiverad eller 1 stöds inte CAE. | Om det är inställt på aktiverad eller 1 stöds inte CAE. |
| Aktuell kanal <br> eller <br> Månatlig Företagskanal | CAE stöds oavsett inställning | CAE stöds oavsett inställning |

En förklaring av Office Update-kanaler finns i [Översikt över uppdateringskanaler för Microsoft 365 Apps.](/deployoffice/overview-update-channels) Vi rekommenderar att organisationer inte inaktiverar Webbkontohanterare (WAM).

### <a name="group-membership-and-policy-update-effective-time"></a>Gällande tid för gruppmedlemskap och principuppdatering

Det kan ta upp till en dag innan gruppmedlemskap och principuppdatering som gjorts av administratörer är effektiva. Viss optimering har gjorts för principuppdateringar som minskar fördröjningen till två timmar. Men det omfattar inte alla scenarier ännu. 

Om det finns ett nödfall och du behöver uppdatera dina principer eller ändra gruppmedlemskap så att de tillämpas omedelbart, bör du använda det här [PowerShell-kommandot](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) eller "Återkalla session" på användarprofilsidan för att återkalla användarnas session, vilket ser till att de uppdaterade principerna tillämpas omedelbart.

### <a name="coauthoring-in-office-apps"></a>Kohort i Office-appar

När flera användare samarbetar med samma dokument samtidigt kan det hända att användarens åtkomst till dokumentet inte omedelbart återkallas av CAE baserat på återkallelse- eller principändringshändelser. I det här fallet förlorar användaren åtkomst helt efter, stänger dokumentet, stänger Word, Excel eller PowerPoint eller efter en period på 10 timmar.

Om du vill minska den här tiden kan en SharePoint-administratör välja att minska den maximala livslängden för kohortsessioner för dokument som lagras i SharePoint Online och OneDrive för företag genom att konfigurera en nätverksplatsprincip i [SharePoint Online.](/sharepoint/control-access-based-on-network-location) När den här konfigurationen har ändrats minskas den maximala livslängden för kohortsessioner till 15 minuter och kan justeras ytterligare med hjälp av SharePoint Online PowerShell-kommandot "Set-SPOTenant –IPAddressWACTokenLifetime"

### <a name="enable-after-a-user-is-disabled"></a>Aktivera när en användare har inaktiverats

Om du aktiverar en användare direkt efter att den har inaktiverats. Det blir en viss fördröjning innan kontot kan aktiveras. SPO och Teams har 15 minuters fördröjning. Fördröjningen är 35–40 minuter för EXO.

## <a name="faqs"></a>Vanliga frågor och svar

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Hur fungerar CAE med inloggningsfrekvensen?

Inloggningsfrekvensen respekteras med eller utan CAE.

## <a name="next-steps"></a>Nästa steg

[Meddelande om utvärdering av kontinuerlig åtkomst](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
