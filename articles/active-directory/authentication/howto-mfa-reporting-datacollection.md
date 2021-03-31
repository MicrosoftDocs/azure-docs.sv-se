---
title: Insamling av Azure AD MFA-användar data – Azure Active Directory
description: Vilken information används för att autentisera användare av Azure AD Multi-Factor Authentication?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2124ca9fae76d87a695a2c453f7b222e7a08794
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742801"
---
# <a name="azure-ad-multi-factor-authentication-user-data-collection"></a>Azure AD Multi-Factor Authentication användar data insamling

Det här dokumentet beskriver hur du hittar användar information som samlats in av Azure Multi-Factor Authentication-server (MFA Server) och Azure AD MFA (molnbaserad) i händelse av att du vill ta bort den.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Information som samlas in

MFA Server, NPS-tillägget och Windows Server 2016 Azure AD MFA AD FS adapter samla in och lagra följande information i 90 dagar.

Autentiseringsförsök (används för rapportering och fel sökning):

- Timestamp
- Användarnamn
- Förnamn
- Efternamn
- E-postadress
- Användar grupp
- Autentiseringsmetod (telefonsamtal, textmeddelande, mobilapp, OATH-token)
- Telefonsamtals läge (standard, PIN-kod)
- Text meddelande riktning (envägs, tvåvägs)
- SMS-läge (text meddelande) (eng ång slö sen ord)
- Mobilt app-läge (standard, PIN)
- OATH-token-läge (standard, PIN)
- Autentiseringstyp
- Programnamn
- Lands nummer för primär samtal
- Telefonnummer för primär samtal
- Primärt anrops tillägg
- Primärt anrop autentiserat
- Primärt anrops resultat
- Landskod för säkerhets kopierings nummer
- Telefonnummer till säkerhets kopierings samtal
- Säkerhetskopiera samtals tillägg
- Säkerhets kopierings anrop autentiserat
- Resultat av säkerhets kopierings anrop
- Totalt autentiserat
- Övergripande resultat
- Resultat
- Autentiserad
- Resultat
- Initierar IP-adress
- Enheter
- Enhets-token
- Enhetstyp
- Mobil program version
- OS-version
- Resultat
- Använd kontroll för meddelande

Aktiveringar (försöker aktivera ett konto i Microsoft Authenticator mobilappen):
- Användarnamn
- Account Name
- Timestamp
- Hämta aktiverings kod resultat
- Aktiveringen lyckades
- Aktivera fel
- Aktiverings status resultat
- Enhets namn
- Enhetstyp
- Appversion
- OATH-token har Aktiver ATS

Block (används för att fastställa blockerat tillstånd och för rapportering):

- Blockera tidsstämpel
- Blockera efter användar namn
- Användarnamn
- Landskod
- Telefonnummer
- Telefonnummer formaterat
- Anknytning
- Rensa tillägg
- Blockerad
- Orsak till blockering
- Tidsstämpel för slut för ande
- Orsak till slut för ande
- Kontoutelåsning
- Bedrägeri avisering
- Bedrägeri varning blockeras inte
- Språk

Kringgås (används för rapportering):

- Kringgå tidsstämpel
- Kringgå sekunder
- Kringgå användar namn
- Användarnamn
- Landskod
- Telefonnummer
- Telefonnummer formaterat
- Anknytning
- Rensa tillägg
- Kringgå orsak
- Tidsstämpel för slut för ande
- Orsak till slut för ande
- Förbikoppling används

Ändringar (används för att synkronisera användar ändringar till MFA Server eller Azure AD):

- Ändra tidsstämpel
- Användarnamn
- Ny landskod
- Nytt telefonnummer
- Nytt tillägg
- Ny kod för säkerhets kopierings land
- Nytt reserv telefonnummer
- Nytt säkerhets kopierings tillägg
- Ny PIN-kod
- PIN-koden måste ändras
- Gammal enhets-token
- Ny enhets-token

## <a name="gather-data-from-mfa-server"></a>Samla in data från MFA Server

För MFA Server version 8,0 eller senare kan administratörer exportera alla data för användare med hjälp av följande process:

- Logga in på MFA-servern, navigera till fliken **användare** , Välj användaren i fråga och klicka på knappen **Redigera** . Ta skärm bilder (ALT-PrintScrn) på varje flik för att ge användaren sina aktuella MFA-inställningar.
- Kör följande kommando från kommando raden för MFA-servern och ändra sökvägen enligt din installation `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` för att skapa en JSON-formaterad fil.
- Administratörer kan också använda GetUserGdpr-åtgärden för webbtjänst-SDK som ett alternativ för att exportera all information om MFA-molntjänster som samlats in för en specifik användare eller inkludera i en större rapporterings lösning.
- Sök `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` och eventuella säkerhets kopior för " \<username> " (inklusive citat tecken i sökningen) för att hitta alla instanser av användar posten som läggs till eller ändras.
   - Dessa poster kan begränsas (men inte elimineras) genom att avmarkera **Logga användar ändringar** i avsnittet MFA Server UX, loggning, fliken loggfiler.
   - Om syslog är konfigurerat och **"Logga användar ändringar"** är markerat i avsnittet MFA Server UX, loggning, fliken syslog och sedan kan logg posterna samlas in från syslog i stället.
- Andra förekomster av användar namnet i MultiFactorAuthSvc. log och andra MFA Server-loggfiler som hör till autentiseringsförsök anses vara operativa och duplicerad till den information som anges med hjälp av MultiFactorAuthGdpr.exe export-eller webbtjänst-SDK-GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Ta bort data från MFA Server

På kommando raden för MFA-servern kör du följande kommando för att ändra sökvägen enligt din installation `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` för att ta bort all MFA Cloud service-information som samlas in för den här användaren.

- Data som ingår i exporten tas bort i real tid, men det kan ta upp till 30 dagar innan operativ-eller duplicerade data tas bort helt.
- Administratörer kan också använda DeleteUserGdpr-åtgärden för webbtjänst-SDK som ett alternativ för att ta bort all information om MFA-molntjänster som samlats in för en specifik användare eller inkludera i en större rapporterings lösning.

## <a name="gather-data-from-nps-extension"></a>Samla in data från NPS-tillägg

Använd [Microsofts sekretess Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) för att göra en begäran om export.

- MFA-information ingår i exporten, vilket kan ta flera timmar eller dagar att slutföra.
- Förekomster av användar namnet i händelse loggarna AzureMfa/authn/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh och AzureMfa/AuthZ/AuthZOptCh betraktas som operativa och duplicerad till den information som anges i exporten.

## <a name="delete-data-from-nps-extension"></a>Ta bort data från NPS-tillägg

Använd [Microsofts sekretess Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) för att göra en begäran om konto nära borttagning av all information om MFA-molntjänster som samlas in för den här användaren.

- Det kan ta upp till 30 dagar innan data tas bort helt.

## <a name="gather-data-from-windows-server-2016-azure-ad-mfa-ad-fs-adapter"></a>Samla in data från Windows Server 2016 Azure AD MFA AD FS adapter

Använd [Microsofts sekretess Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) för att göra en begäran om export. 

- MFA-information ingår i exporten, vilket kan ta flera timmar eller dagar att slutföra.
- Förekomster av användar namnet i AD FS spårnings-eller fel söknings händelse loggar (om de är aktiverade) betraktas som operativa och duplicerat till den information som anges i exporten.

## <a name="delete-data-from-windows-server-2016-azure-ad-mfa-ad-fs-adapter"></a>Ta bort data från Windows Server 2016 Azure AD MFA AD FS adapter

Använd [Microsofts sekretess Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) för att göra en begäran om konto nära borttagning av all information om MFA-molntjänster som samlas in för den här användaren.

- Det kan ta upp till 30 dagar innan data tas bort helt.

## <a name="gather-data-for-azure-ad-mfa"></a>Samla in data för Azure AD MFA

Använd [Microsofts sekretess Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) för att göra en begäran om export.

- MFA-information ingår i exporten, vilket kan ta flera timmar eller dagar att slutföra.

## <a name="delete-data-for-azure-ad-mfa"></a>Ta bort data för Azure AD MFA

Använd [Microsofts sekretess Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) för att göra en begäran om konto nära borttagning av all information om MFA-molntjänster som samlas in för den här användaren.

- Det kan ta upp till 30 dagar innan data tas bort helt.

## <a name="next-steps"></a>Nästa steg

[MFA Server repor ting](howto-mfa-reporting.md)
