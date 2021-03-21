---
title: Information om inloggnings händelser för Azure AD Multi-Factor Authentication-Azure Active Directory
description: Lär dig hur du visar inloggnings aktivitet för Azure AD Multi-Factor Authentication händelser och status meddelanden.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5f78b70599d6d0ae8825accf4cc55cdc1c01d9ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861246"
---
# <a name="use-the-sign-ins-report-to-review-azure-ad-multi-factor-authentication-events"></a>Använd inloggnings rapporten för att granska Azure AD Multi-Factor Authentication-händelser

Om du vill granska och förstå Azure AD Multi-Factor Authentication-händelser kan du använda inloggnings rapporten Azure Active Directory (Azure AD). Den här rapporten innehåller information om autentisering när en användare uppmanas att använda Multi-Factor Authentication och om några principer för villkorlig åtkomst används. Detaljerad information om inloggnings rapporten finns i [Översikt över rapporter om inloggnings aktiviteter i Azure AD](../reports-monitoring/concept-sign-ins.md).

Den här artikeln visar hur du visar rapporten Azure AD-inloggningar i Azure Portal och sedan MSOnline v1 PowerShell-modulen.

## <a name="view-the-azure-ad-sign-ins-report"></a>Visa rapporten Azure AD-inloggningar

Inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter, som innehåller information om Multi-Factor Authentication (MFA) användning. MFA-data ger dig information om hur MFA fungerar i din organisation. Du kan besvara frågor som följande:

- Kontrollerades inloggningen med MFA?
- Hur slutförde användaren MFA?
- Varför kunde användaren inte slutföra MFA?
- Hur många användare kontrolleras av MFA?
- Hur många användare kan inte slutföra MFA-kontrollen?
- Vilka vanliga MFA-relaterade problem råkar slutanvändarna ut för?

Om du vill visa inloggnings aktivitets rapporten i [Azure Portal](https://portal.azure.com)utför du följande steg. Du kan också fråga efter data med hjälp av [rapporterings-API: et](../reports-monitoring/concept-reporting-api.md).

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto med *globala administratörs* behörigheter.
1. Sök efter och välj **Azure Active Directory** och välj sedan **användare** på menyn till vänster.
1. Under *aktivitet* på menyn på vänster sida väljer du **inloggningar**.
1. En lista över inloggnings händelser visas, inklusive status. Du kan välja en händelse om du vill visa mer information.

    På fliken *autentiseringsinformation* eller *villkorlig åtkomst* i händelse informationen visas en status kod eller vilken princip som utlöste MFA-prompten.

    [![Skärm bild av exempel Azure Active Directory inloggnings rapport i Azure Portal](media/howto-mfa-reporting/sign-in-report-cropped.png)](media/howto-mfa-reporting/sign-in-report.png#lightbox)

Om det är tillgängligt visas autentiseringen, till exempel textmeddelande, Microsoft Authenticator app-avisering eller telefonsamtal.

Följande information visas i fönstret *autentiseringsinformation* för en inloggnings händelse som visar om MFA-begäran har uppfyllts eller nekats:

* Om MFA uppfylldes tillhandahåller den här kolumnen mer information om hur MFA uppfylldes.
   * slutfört i molnet
   * har upphört på grund av de principer som konfigurerats på klienten
   * uppmaning om registrering
   * uppfyllt av anspråk i token
   * uppfyllt av anspråk som tillhandahållits av en extern provider
   * uppfyllt av stark autentisering
   * hoppades över eftersom flödet som utnyttjades var flödet för inloggning av asynkron Windows-meddelandekö
   * hoppades över på grund av applösenord
   * hoppades över på grund av plats
   * hoppades över på grund av en registrerad enhet
   * hoppades över på grund av en sparad enhet
   * slutfördes

* Om MFA nekades innehåller den här kolumnen orsaken till nekandet.
   * autentisering pågår
   * duplicera autentiseringsförsök
   * angett felaktig kod för många gånger
   * ogiltig autentisering
   * ogiltig verifieringskod från mobilapp
   * felaktig konfiguration
   * telefonsamtalet dirigerades till röstmeddelanden
   * telefonnumret har ett ogiltigt format
   * tjänstfel
   * Det gick inte att komma åt användarens telefon
   * kunde inte skicka meddelande via mobilapp till enheten
   * kunde inte skicka meddelande via mobilapp
   * användaren nekade autentiseringen
   * användaren svarade inte på meddelandet via mobilappen
   * användaren har inte några verifieringsmetoder registrerade
   * användaren har angett en felaktig kod
   * användaren har angett en felaktig PIN-kod
   * användaren avslutade telefonsamtalet utan att ha slutfört autentiseringen
   * användaren har blockerats
   * användaren angav aldrig verifieringskoden
   * användaren kunde inte hittas
   * verifieringskoden har redan använts en gång

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell-rapportering för användare som registrerats för MFA

Se först till att du har installerat [MSOnline v1 PowerShell-modulen](/powershell/azure/active-directory/overview) .

Identifiera användare som har registrerat sig för MFA med hjälp av PowerShell som följer. Den här uppsättningen kommandon utesluter inaktiverade användare eftersom dessa konton inte kan autentiseras mot Azure AD:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifiera användare som inte har registrerat sig för MFA med hjälp av PowerShell som följer. Den här uppsättningen kommandon utesluter inaktiverade användare eftersom dessa konton inte kan autentiseras mot Azure AD:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifiera användare och utmatnings metoder som registrerats:

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="downloaded-activity-reports-result-codes"></a>Hämtade aktivitets rapporter resultat koder

Följande tabell kan användas för att felsöka händelser med den nedladdade versionen av aktivitets rapporten från föregående Portal steg eller PowerShell-kommandon. Dessa resultat koder visas inte direkt i Azure Portal.

| Anrops resultat | Beskrivning | Bred beskrivning |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN-kod angiven | Användaren angav en PIN-kod.  Om autentiseringen lyckades angavs rätt PIN-kod.  Om autentisering nekas anges en felaktig PIN-kod eller användaren är inställd på standard läge. |
| SUCCESS_NO_PIN | Endast antal angivna | Om användaren är inställd på PIN-läge och autentiseringen nekas innebär det att användaren inte angav sin PIN-kod och bara angav #.  Om användaren är inställd på standard läge och autentiseringen lyckas innebär det att användaren bara angav # vilket är rätt att göra i standard läge. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Har inte tryckts ned efter posten | Användaren skickade inga DTMF-siffror eftersom # inte angavs.  Andra siffror som anges skickas inte om # anges i slutet av posten. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Ingen telefonin text-tids gränsen uppnåddes | Anropet besvarades, men det fanns inget svar.  Detta indikerar vanligt vis att samtalet hämtades av röst brev. |
| SUCCESS_PIN_EXPIRED | PIN-koden har upphört och har inte ändrats | Användarens PIN-kod har upphört att gälla och du uppmanas att ändra den, men PIN-koden har ändrats. |
| SUCCESS_USED_CACHE | Använt cacheminne | Autentiseringen lyckades utan ett Multi-Factor Authentication-anrop eftersom en tidigare lyckad autentisering för samma användar namn inträffade inom den konfigurerade cache-tidsramen. |
| SUCCESS_BYPASSED_AUTH | Förhoppad autentisering | Autentiseringen lyckades med ett One-Time bypass initierades för användaren.  Se rapporten över ignorerade användar historik för mer information om att kringgå. |
| SUCCESS_USED_IP_BASED_CACHE | Använd IP-baserad cache | Autentiseringen lyckades utan ett Multi-Factor Authentication-anrop sedan en tidigare lyckad autentisering för samma användar namn, autentiseringstyp, program namn och IP inträffade inom den konfigurerade cache-tidsramen. |
| SUCCESS_USED_APP_BASED_CACHE | Använd app-baserad cache | Autentiseringen lyckades utan ett Multi-Factor Authentication-anrop sedan en tidigare lyckad autentisering för samma användar namn, autentiseringstyp och program namn inom den konfigurerade cache-tidsramen. |
| SUCCESS_INVALID_INPUT | Ogiltig telefonin ingång | Svaret som skickas från telefonen är inte giltigt.  Detta kan vara från en fax maskin eller ett modem, eller så kan användaren ha angett * som en del av PIN-koden. |
| SUCCESS_USER_BLOCKED | Användaren är blockerad | Användarens telefonnummer är blockerat.  Ett blockerat nummer kan initieras av användaren under ett autentiserings anrop eller av en administratör med hjälp av Azure Portal. <br> Obs: ett blockerat nummer är också en byproduct av en bedrägeri avisering. |
| SUCCESS_SMS_AUTHENTICATED | Textmeddelande autentiserat | För tvåvägs test meddelande svarar användaren korrekt med eng ång slö sen ord (eng ång slö sen ord) eller eng ång slö sen ord + PIN-kod. |
| SUCCESS_SMS_SENT | Textmeddelande har skickats | SMS-meddelandet som innehåller eng ång slö sen ordet (eng ång slö sen ord) har skickats.  Användaren kommer att ange eng ång slö sen ord eller eng ång slö sen ord i programmet för att slutföra autentiseringen. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobilapp autentiserad | Användaren har autentiserats via mobilappen. |
| SUCCESS_OATH_CODE_PENDING | Väntande OATH-kod | Användaren uppmanades att ange sin OATH-kod men svarade inte. |
| SUCCESS_OATH_CODE_VERIFIED | OATH-kod verifierad | Användaren angav en giltig OATH-kod när den uppmanas till det. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Återställnings-OATH-kod verifierad | Användaren nekades autentisering med hjälp av den primära Multi-Factor Authentication metoden och angav sedan en giltig OATH-kod för återställning. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Svar på reserv säkerhets frågor | Användaren nekades autentisering med hjälp av den primära Multi-Factor Authentication metoden och besvarade sedan sina säkerhets frågor korrekt för reserven. |
| FAILED_PHONE_BUSY | Autentisering pågår redan | Multi-Factor Authentication bearbetar redan en autentisering för den här användaren.  Detta orsakas ofta av RADIUS-klienter som skickar flera autentiseringsbegäranden vid samma inloggning. |
| CONFIG_ISSUE | Det går inte att komma åt telefonen | Anropet gjordes, men kunde inte placeras eller besvarades inte.  Detta omfattar upptaget signal, snabb, upptaget signal (frånkopplad), tre toner (antalet finns inte längre i tjänsten), tids gränsen nåddes vid ringning osv. |
| FAILED_INVALID_PHONENUMBER | Ogiltigt telefonnummer format | Telefonnumret har ett ogiltigt format.  Telefonnummer måste vara numeriska och måste vara 10 siffror för lands kod + 1 (USA & Kanada). |
| FAILED_USER_HUNGUP_ON_US | Användaren lade på telefonen | Användaren besvarade telefonen, men stannade sedan utan att trycka på några knappar. |
| FAILED_INVALID_EXTENSION | Ogiltigt tillägg | Tillägget innehåller ogiltiga tecken.  Endast siffror, kommatecken, * och # tillåts.  Ett @-prefix kan också användas. |
| FAILED_FRAUD_CODE_ENTERED | Bedrägeri kod har angetts | Användaren valde att rapportera bedrägerier under anropet, vilket resulterade i en nekad autentisering och ett blockerat telefonnummer.| 
| FAILED_SERVER_ERROR | Det gick inte att ringa | Det gick inte att placera anropet i Multi-Factor Authentications tjänsten. |
| FAILED_SMS_NOT_SENT | Det gick inte att skicka textmeddelande | Det gick inte att skicka textmeddelandet.  Autentiseringen nekas. |
| FAILED_SMS_OTP_INCORRECT | Felaktigt eng ång slö sen ord | Användaren angav ett felaktigt lösen ord (eng ång slö sen ord) från det textmeddelande som de fick.  Autentiseringen nekas. |
| FAILED_SMS_OTP_PIN_INCORRECT | Textmeddelandets eng ång slö sen ord + PIN-kod | Användaren angav ett felaktigt lösen ord (eng ång slö sen ord) och/eller en felaktig PIN-kod för användaren.  Autentiseringen nekas. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Max antal försök för eng ång slö sen ord | Användaren har överskridit det maximala antalet eng ång slö sen ord (eng ång slö sen ord). |
| FAILED_PHONE_APP_DENIED | Nekad mobilapp | Användaren nekade autentiseringen i mobilappen genom att trycka på neka-knappen. |
| FAILED_PHONE_APP_INVALID_PIN | Ogiltig PIN-kod för mobilapp | Användaren angav en ogiltig PIN-kod vid autentisering i mobilappen. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Mobil Programets PIN-kod har inte ändrats | Användaren kunde inte slutföra en nödvändig PIN-ändring i mobilappen. |
| FAILED_FRAUD_REPORTED | Bedrägeri har rapporter ATS | Användaren rapporterade bedrägerier i mobilappen. |
| FAILED_PHONE_APP_NO_RESPONSE | Mobilapp saknar svar | Användaren svarade inte på autentiseringsbegäran för mobilappen. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Alla enheter har blockerats av mobilappen | Mobile App-enheter för den här användaren svarar inte längre på meddelanden och har blockerats. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Avisering om mobilapp misslyckades | Ett fel uppstod vid försök att skicka ett meddelande till mobilappen på användarens enhet. |
| FAILED_PHONE_APP_INVALID_RESULT | Ogiltigt resultat för mobilapp | Mobilappen returnerade ett ogiltigt resultat. |
| FAILED_OATH_CODE_INCORRECT | Felaktig OATH-kod | Användaren angav en felaktig OATH-kod.  Autentiseringen nekas. |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH-kod + PIN-kod felaktigt | Användaren angav en felaktig OATH-kod och/eller en felaktig PIN-kod för användaren.  Autentiseringen nekas. |
| FAILED_OATH_CODE_DUPLICATE | Duplicera OATH-kod | Användaren angav en OATH-kod som tidigare har använts.  Autentiseringen nekas. |
| FAILED_OATH_CODE_OLD | OATH-koden är inaktuell | Användaren angav en OATH-kod som föregår en OATH-kod som tidigare har använts.  Autentiseringen nekas. |
| FAILED_OATH_TOKEN_TIMEOUT | Timeout för OATH-kods resultat | Användaren tog för lång tid att ange OATH-koden och Multi-Factor Authentication försök hade redan nått tids gränsen. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Timeout för säkerhets frågor | Användaren tog för lång tid att ange svar på säkerhets frågor och det Multi-Factor Authentication försöket hade redan nått tids gränsen. |
| FAILED_AUTH_RESULT_TIMEOUT | Timeout för autentiserings resultat | Användaren tog för lång tid att slutföra Multi-Factor Authentication försöket. |
| FAILED_AUTHENTICATION_THROTTLED | Begränsad autentisering | Multi-Factor Authentication-försöket begränsades av tjänsten. |

## <a name="additional-mfa-reports"></a>Ytterligare MFA-rapporter

Följande ytterligare information och rapporter är tillgängliga för MFA-händelser, inklusive de för MFA-servern:

| Rapport | Location | Beskrivning |
|:--- |:--- |:--- |
| Blockerad användar historik | Azure AD >-säkerhet > MFA > blockera/avblockera användare | Visar historiken för förfrågningar om att blockera eller avblockera användare. |
| Användning för lokala komponenter | Azure AD > säkerhets > MFA > aktivitets rapport | Innehåller information om den övergripande användningen av MFA server via NPS-tillägget, ADFS-och MFA-servern. |
| Förhoppad användar historik | Azure AD >-säkerhet > MFA-> eng ång slö tiden | Innehåller en historik över MFA Server-begäranden för att kringgå MFA för en användare. |
| Server status | Azure AD > säkerhets > MFA > Server status | Visar status för MFA-servrar som är kopplade till ditt konto. |

## <a name="next-steps"></a>Nästa steg

I den här artikeln ges en översikt över inloggnings aktivitets rapporten. Mer detaljerad information om vad den här rapporten innehåller och förstår data finns i [rapporter om inloggnings aktiviteter i Azure AD](../reports-monitoring/concept-sign-ins.md).
