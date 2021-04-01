---
title: Referens för nätverks växlings inställningar i Windows 10 – Azure Active Directory
description: Inställningar som kommer att roamas eller säkerhets kopie ras i Windows 10 med ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6048ee9237640799b7bec37083e607fc74ffb8e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85252975"
---
# <a name="windows-10-roaming-settings-reference"></a>Referens för Windows 10-växlingsinställningar

Följande är en lista över de inställningar som kommer att roamas eller säkerhets kopie ras i Windows 10. 

## <a name="devices-and-endpoints"></a>Enheter och slut punkter

I följande tabell finns en sammanfattning av de enheter och konto typer som stöds av ramverket för synkronisering, säkerhets kopiering och återställning i Windows 10.

| Konto typ och åtgärd | Skrivbord | Mobilt |
| --- | --- | --- |
| Azure Active Directory: synkronisera |Ja |Inga |
| Azure Active Directory: säkerhets kopiering/återställning |Inga |Inga |
| Microsoft-konto: synkronisera |Ja |Ja |
| Microsoft-konto: säkerhets kopiering/återställning |Inga |Ja |

## <a name="what-is-backup"></a>Vad är säkerhets kopiering?

Windows-inställningar synkroniseras vanligt vis som standard, men vissa inställningar säkerhets kopie ras bara, till exempel listan över installerade program på en enhet. Backup är endast för mobila enheter och är för närvarande inte tillgängligt för Enterprise State Roaming användare. Säkerhets kopiering använder en Microsoft-konto och lagrar inställningar och program data till OneDrive. Om en användare inaktiverar synkronisering på enheten med appen Inställningar blir program data som normalt synkroniseras endast säkerhets kopia. Säkerhets kopierings data kan bara nås via återställnings åtgärden under den första körnings upplevelsen av en ny enhet. Säkerhets kopieringar kan inaktive ras via enhets inställningarna och kan hanteras och tas bort via användarens OneDrive-konto.

## <a name="windows-settings-overview"></a>Översikt över Windows-inställningar

Följande inställnings grupper är tillgängliga för slutanvändare för att aktivera/inaktivera inställningar synkronisera på Windows 10-enheter.

* Tema: Skriv bords bakgrund, användar panel, position i aktivitets fältet osv. 
* Internet Explorer-inställningar: webb läsar historik, skrivna URL: er, favoriter osv. 
* Lösen ord: Windows Autentiseringshanteraren, inklusive Wi-Fi profiler 
* Språk inställningar: stavnings ord lista, system språk inställningar 
* Enkel åtkomst: skärm läsaren, skärm tangent bordet, skärm förstoraren 
* Andra Windows-inställningar: se information om Windows-inställningar
* Inställning för Microsoft Edge-webbläsare: Microsoft Edge-favoriter, Läslista och andra inställningar

![Synkronisera dina inställningar](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Den här artikeln gäller Microsoft Edge äldre HTML-baserad webbläsare lanserad med Windows 10 i juli 2015. Artikeln gäller inte den nya Microsoft Edge krom-baserade webbläsaren som lanserades den 15 januari 2020. Mer information om hur synkronisering fungerar för den nya Microsoft Edge finns i artikeln [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

Inställnings gruppen för Microsoft Edge-webbläsare (favoriter, läsa lista) kan aktive ras eller inaktive ras av slutanvändare via meny alternativet Microsoft Edge-webbläsar inställningar.

![Konto](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

För Windows 10 version 1803 eller senare kan inställnings gruppen för Internet Explorer (favoriter, skrivna URL: er) aktive ras eller inaktive ras av slutanvändare via meny alternativet för Internet Explorer-inställningar. 

![Inställningar](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Information om Windows-inställningar

I följande tabell refererar andra poster i kolumnen inställnings grupp till inställningar som kan inaktive ras genom att gå till inställningar > konton > synkronisera dina inställningar > andra Windows-inställningar. 

Interna poster i kolumnen Settings Group avser inställningar och appar som bara kan inaktive ras från synkronisering i själva appen eller genom att inaktivera synkronisering för hela enheten med hantering av mobila enheter (MDM) eller grupprincip inställningar.
Inställningar som inte är roaming eller synkronisering tillhör inte någon grupp.

| Inställningar | Skrivbord | Mobilt | Group |
| --- | --- | --- | --- |
| **Konton**: konto bild |sync |X |Tema |
| **Konton**: andra konto inställningar |X |X | |
| **Avancerat mobilt bred band**: nätverks namn för Internet anslutnings delning (aktiverar automatisk identifiering av mobil Wi-Fi hotspots via Bluetooth) |X |X |Lösenord |
| **AppData**: enskilda appar kan synkronisera data |Synkronisera säkerhets kopia |Synkronisera säkerhets kopia |inhemska |
| **Applistan**: lista över installerade appar |X |säkerhetskopiering |Annat |
| **Bluetooth**: alla Bluetooth-inställningar |X |X | |
| **Kommando tolk**: kommando tolkens standardinställningar |sync |X |inhemska |
| **Autentiseringsuppgifter**: Lås för autentiseringsuppgifter |sync |sync |password |
| **Datum, tid och region**: automatisk tid (synkronisering av Internet tid) |sync |sync |language |
| **Datum, tid och region**: 24-timmarsklocka |sync |X |language |
| **Datum, tid och region**: datum och tid |sync |X |language |
| **Datum, tid och region**: tidszon | |X |language |
| **Datum, tid och region**: sommar tid |sync |X |language |
| **Datum, tid och region**: land/region |sync |X |language |
| **Datum, tid och region**: första dagen i veckan |sync |X |language |
| **Datum, tid och region**: regions format (språk) |sync |X |language |
| **Datum, tid och region**: kort datum |sync |X |language |
| **Datum, tid och region**: långt datum |sync |X |language |
| **Datum, tid och region**: kort tid |sync |X |language |
| **Datum, tid och region**: lång tid |sync |X |language |
| **Skriv bords anpassning**: Skriv bords tema (bakgrund, system färg, standard system ljud, skärmsläckare) |sync |X |Tema |
| **Skriv bords anpassning**: bild spels tapet |sync |X |Tema |
| **Skriv bords anpassning**: inställningar för aktivitets fältet (position, Auto-Dölj osv.) |sync |X |Tema |
| **Skriv bords anpassning**: starta skärmlayout |X |säkerhetskopiering | |
| **Enheter**: delade skrivare som du har anslutit till |X |X |other |
| **Microsoft Edge-webbläsare**: läsa lista |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: favoriter |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: översta platser <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: skrivna URL: er <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: inställningar för favorit fält <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: Visa Start knappen <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: blockera popup-fönster <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: fråga mig vad jag ska göra med varje nedladdning <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: erbjudande för att spara lösen ord <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: Skicka Do Not Track-begäranden <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: Spara formulär poster <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: Visa Sök-och webbplats förslag när jag skriver <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: cookies-inställning <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: låt platser Spara skyddade medie licenser på enheten <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Microsoft Edge-webbläsare**: skärm läsar inställningen <sup> [[1]](#footnote-1)</sup> |sync |sync |inhemska |
| **Högkontrast**: på eller av |sync |X |hjälpmedel |
| **Hög kontrast**: Tema inställningar |sync |X |hjälpmedel |
| **Internet Explorer**: öppna flikar (URL och rubrik) |sync |sync |Internet Explorer |
| **Internet Explorer**: läsa lista |sync |sync |Internet Explorer |
| **Internet Explorer**: skrivna URL: er |sync |sync |Internet Explorer |
| **Internet Explorer**: webb historik |sync |sync |Internet Explorer |
| **Internet Explorer**: favoriter |sync |sync |Internet Explorer |
| **Internet Explorer**: undantagna URL: er |sync |sync |Internet Explorer |
| **Internet Explorer**: Start sidor |sync |sync |Internet Explorer |
| **Internet Explorer**: domän förslag |sync |sync |Internet Explorer |
| **Tangent bord**: användare kan aktivera/inaktivera skärm tangent bordet |sync |X |hjälpmedel |
| **Tangent bord**: aktivera Tröga tangenter Ja (inaktiverat som standard) |sync |X |hjälpmedel |
| **Tangent bord**: aktivera filter nycklar (av som standard) |sync |X |hjälpmedel |
| **Tangent bord**: Aktivera växlings tangenter (inaktiverat som standard) |sync |X |hjälpmedel |
| **Internet Explorer**: domän språk: kinesiska (CHS) QWERTY – aktivera själv inlärning |sync |X |Språk |
| **Språk**: CHS QWERTY – aktivera dynamisk kandidat rangordning |sync |X |Språk |
| **Språk**: CHS QWERTY-char-set förenklad kinesiska |sync |X |Språk |
| **Språk**: CHS QWERTY-char-set traditionell kinesiska |sync |X |Språk |
| **Språk**: CHS QWERTY – fuzzy pinyin |sync |säkerhetskopiering |Språk |
| **Språk**: CHS QWERTY – fuzzy-par |sync |säkerhetskopiering |Språk |
| **Språk**: CHS QWERTY-fullständig pinyin |sync |X |Språk |
| **Språk**: CHS QWERTY-dubbel pinyin |sync |X |Språk |
| **Språk**: CHS QWERTY – läsa automatisk korrigering |sync |X |Språk |
| **Språk**: CHS QWERTY-C/E-växel nyckel, Shift |sync |X |Språk |
| **Språk**: CHS QWERTY – C/E switch Key, CTRL |sync |X |Språk |
| **Språk**: CHS Wubi – läge för enstaka Character |sync |X |Språk |
| **Språk**: CHS Wubi – visar den återstående koden för kandidaten |sync |X |Språk |
| **Språk**: CHS Wubi – pip när 4-kod är ogiltig |sync |X |Språk |
| **Språk**: CHT-Bopomofo – inkludera cjk ext-A |sync |X |Språk |
| **Språk**: japansk IME – förutsägande inmatning och anpassade ord |sync |sync |Språk |
| **Språk**: KOREANSK (kor) IME |X |X |Språk |
| **Språk**: hand SKRIFTS igenkänning |X |X |Språk |
| **Språk**: språk profil |sync |säkerhetskopiering |Språk |
| **Språk**: stavnings kontroll – Autokorrigering och markera fel stavningar |sync |säkerhetskopiering |Språk |
| **Språk**: lista över tangent bord |sync |säkerhetskopiering |Språk |
| **Lås skärmen**: alla Lås skärms inställningar |X |X | |
| **Förstorings glas**: on eller off (Master-växling) |X |X |Enkel åtkomst |
| **Förstorings glas**: Aktivera eller inaktivera inversions färg (av som standard) |sync |X |Enkel åtkomst |
| **Förstorings glas**: spårning – Följ tangent bords fokus |sync |X |Enkel åtkomst |
| **Förstorings glas**: spårning – Följ mus markören |sync |X |Enkel åtkomst |
| **Förstorings glas**: starta när användare loggar in (av som standard) |sync |X |Enkel åtkomst |
| **Mus**: ändra storlek på mus markören |sync |X |other |
| **Mus**: ändra färg på mus markören |sync |X |other |
| **Mus**: alla andra inställningar |X |X | |
| **Skärm läsaren**: snabb start |sync |X |Enkel åtkomst |
| **Skärm läsaren**: användare kan ändra skärm läsaren genom att tala i skärm bredd |sync |X |Enkel åtkomst |
| **Skärm läsaren**: användare kan aktivera eller inaktivera skärm läsar tips för vanliga objekt (som standard) |sync |X |Enkel åtkomst |
| **Skärm läsaren**: användare kan aktivera eller inaktivera om de kan höra skrivna tecken (på som standard) |sync |X |Enkel åtkomst |
| **Skärm läsaren**: användare kan aktivera eller inaktivera om de kan höra skrivna ord (på som standard) |sync |X |Enkel åtkomst |
| **Skärm läsaren**: ha insättnings markören efter skärm läsaren (på som standard) |sync |X |Enkel åtkomst |
| **Skärm läsaren**: Aktivera visuell markering av skärm läsar markören (aktiverat som standard) |sync |X |Enkel åtkomst |
| **Skärm läsaren**: spela upp ljud signaler (aktiverat som standard) |sync |X |Enkel åtkomst |
| **Skärm läsaren**: Aktivera nycklar på pektangentbordet när du lyfter fingret (av som standard) |sync |X |Enkel åtkomst |
| **Enkel åtkomst**: Ange den blinkande markörens tjocklek |sync |X |Enkel åtkomst |
| **Enkel åtkomst**: ta bort bakgrunds bilder (av som standard) |sync |X |Enkel åtkomst |
| **Ström och ström spar läge**: alla inställningar |X |X | |
| **Start skärms anpassning**: accentfärg (endast mobil) |X |sync |Tema |
| **Skriva**: stavnings ord lista |sync |säkerhetskopiering |Språk |
| **Skriva**: autokorrigering felstavat ord |sync |säkerhetskopiering |Språk |
| **Skriva**: Markera felstavade ord |sync |säkerhetskopiering |Språk |
| **Skriva**: Visa text förslag medan jag skriver |sync |säkerhetskopiering |Språk |
| **Skriva**: Lägg till ett blank steg efter att jag har valt ett text förslag |sync |säkerhetskopiering |Språk |
| **Skriva**: Lägg till en punkt efter att jag dubbelknackar på blank steg |sync |säkerhetskopiering |Språk |
| **Skriva**: inled den första bokstaven i varje mening |sync |säkerhetskopiering |Språk |
| **Skriva**: Använd enbart versaler när jag dubbelknackar på SKIFT-tangenten |sync |säkerhetskopiering |Språk |
| **Skriva**: spela upp nyckel ljud när jag skriver |sync |säkerhetskopiering |Språk |
| **Skriva**: anpassnings data för pektangentbordet |sync |säkerhetskopiering |Språk |
| **Wi-Fi**: Wi-Fi profiler (endast WPA) |sync |sync |Lösenord |

###### <a name="footnote-1"></a>Fotnot 1

Lägsta operativ system version som stöds av Windows Creators Update (version 15063). 

## <a name="next-steps"></a>Nästa steg

En översikt finns i [Översikt över företags tillstånds växling](enterprise-state-roaming-overview.md).
