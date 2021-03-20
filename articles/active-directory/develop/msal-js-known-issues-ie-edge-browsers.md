---
title: Problem i Internet Explorer & Microsoft Edge (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Läs om kända problem när du använder Microsoft Authentication Library för Java Script (MSAL.js) med Internet Explorer och Microsoft Edge-webbläsare.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/18/2020
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2a471504b88791b5bfb6ce6cc7c81d60bfbe5028
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83772088"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Kända problem i Internet Explorer och Microsoft Edge-webbläsare (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Problem på grund av säkerhets zoner
Vi hade flera rapporter om problem med autentisering i IE och Microsoft Edge (sedan uppdateringen av *Microsoft Edge browser-versionen till 40.15063.0.0*). Vi spårar dessa och har informerat Microsoft Edge-teamet. Microsoft Edge arbetar med en lösning, men här är en beskrivning av de ofta förekommande problemen och de möjliga lösningarna som kan implementeras.

### <a name="cause"></a>Orsak
Orsaken till de flesta av de här problemen är som följer. Session Storage och Local Storage partitioneras av säkerhets zoner i Microsoft Edge-webbläsaren. I den här specifika versionen av Microsoft Edge, när programmet omdirigeras mellan zoner, rensas sessionens lagring och lokala lagrings enheter. Mer specifikt rensas sessionens lagrings utrymme i webbläsarens vanliga navigering, och både sessionen och den lokala lagringen rensas i webbläsarens InPrivate-läge. MSAL.js sparar ett visst tillstånd i sessionens lagrings utrymme och förlitar sig på att kontrol lera det här läget under autentiseringen. När sessionen har rensats går det inte att spara det här läget och därför resulterar det i trasiga upplevelser.

### <a name="issues"></a>Ärenden

- **Oändliga omdirigerings-slingor och hämtning av sidor vid autentisering**. När användarna loggar in på programmet i Microsoft Edge, omdirigeras de tillbaka från inloggnings sidan för AAD och fastnar i en oändlig omdirigerings slinga som resulterar i upprepade inläsningar av sidor. Detta åtföljs vanligt vis av ett `invalid_state` fel i session Storage.

- **Oändligt antal hämtningar av token och AADSTS50058-fel**. När ett program som körs på Microsoft Edge försöker hämta en token för en resurs, kan det hända att programmet fastnar i en oändlig loop av anropet för inhämta token tillsammans med följande fel från AAD i nätverks spårningen:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Popup-fönstret stängs eller fastnar inte när du använder inloggning via popup för att autentisera**. När du autentiserar via popup-fönstret i Microsoft Edge eller IE (InPrivate) efter att ha angett autentiseringsuppgifter och loggat in, om flera domäner i säkerhets zoner är involverade i navigeringen, stängs inte popup-fönstret eftersom MSAL.js förlorar handtaget till popup-fönstret.  

### <a name="update-fix-available-in-msaljs-023"></a>Uppdatering: korrigeringen är tillgänglig i MSAL.js 0.2.3
Korrigeringar för problem med att omdirigera loopar för autentisering har släppts i [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Aktivera flaggan `storeAuthStateInCookie` i MSAL.js config för att dra nytta av den här korrigeringen. Som standard är den här flaggan inställd på falskt.

När `storeAuthStateInCookie` flaggan är aktive rad använder MSAL.js webbläsarens cookies för att lagra det begär ande tillstånd som krävs för validering av auth-flöden.

> [!NOTE]
> Den här korrigeringen är ännu inte tillgänglig för msal-omslutningar och msal-AngularJS. Den här korrigeringen löser inte problemet med popup-fönster.

Använd lösningarna nedan.

#### <a name="other-workarounds"></a>Andra lösningar
Se till att testa att ditt problem bara uppstår på den aktuella versionen av Microsoft Edge-webbläsaren och fungerar i de andra webbläsarna innan du inför dessa lösningar.  
1. Som ett första steg för att lösa dessa problem bör du se till att program domänen och andra platser som ingår i omdirigeringen av autentiseringsschemat läggs till som betrodda platser i webbläsarens säkerhets inställningar så att de tillhör samma säkerhets zon.
Det gör du på följande sätt:
    - Öppna **Internet Explorer** och klicka på **inställningarna** (kugg hjuls ikonen) i det övre högra hörnet
    - Välj **Internet alternativ**
    - Välj fliken **säkerhet**
    - Under alternativet **Betrodda platser** klickar du på knappen **platser** och lägger till URL: erna i dialog rutan som öppnas.

2. Som tidigare nämnts, eftersom bara session Storage rensas under den vanliga navigeringen, kan du konfigurera MSAL.js att använda den lokala lagringen i stället. Detta kan anges som config- `cacheLocation` parameter vid initiering av MSAL.

Observera att detta inte löser problemet med InPrivate-surfning eftersom både sessionen och den lokala lagringen har rensats.

## <a name="issues-due-to-popup-blockers"></a>Problem som beror på popup-Blocker

Det finns fall då popup-fönster blockeras i IE eller Microsoft Edge, till exempel när ett andra popup-fönster inträffar under [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md). Du får en avisering i webbläsaren så att popup-fönstret kan visas en gång eller alltid. Om du väljer att tillåta öppnar webbläsaren automatiskt popup-fönstret och returnerar en `null` referens för det. Därför har biblioteket ingen referens för fönstret och det finns inget sätt att stänga popup-fönstret. Samma problem inträffar inte i Chrome när du blir ombedd att tillåta popup-fönster eftersom det inte automatiskt öppnar ett popup-fönster.

Som en **lösning** måste utvecklare tillåta popup-fönster i IE och Microsoft Edge innan de börjar använda appen för att undvika det här problemet.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [använder MSAL.js i Internet Explorer](msal-js-use-ie-browser.md).
