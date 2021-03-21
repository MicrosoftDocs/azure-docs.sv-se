---
title: Viktig information för avancerad läsare SDK
titleSuffix: Azure Cognitive Services
description: Läs mer om vad som är nytt i programmerings gränssnittet för avancerad läsare.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93133723"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>Viktig information om SDK SDK SDK för avancerad läsare

## <a name="version-110"></a>Version 1.1.0

Den här versionen innehåller nya funktioner, säkerhets problem korrigeringar, fel korrigeringar, uppdateringar av kod exempel och konfigurations alternativ.

#### <a name="new-features"></a>Nya funktioner

* Aktivera sparande och inläsning av användar inställningar i olika webbläsare och enheter
* Aktivera konfigurering av standard visnings alternativ
* Lägg till alternativ för att ange översättnings språket, aktivera Word-översättning och aktivera dokument översättning vid start av fördjupad läsare
* Lägg till stöd för att konfigurera läsa upp högt via alternativ
* Lägg till möjlighet att inaktivera första körnings upplevelsen
* Lägg till ImmersiveReaderView för UWP

#### <a name="improvements"></a>Bättre

* Uppdatera exempel-HTML för Android-koden så att den fungerar med den senaste SDK: n
* Uppdatera start svar för att returnera antalet bearbetade tecken
* Uppdatera kod exempel för att använda v-1.1.0
* Tillåt inte att launchAsync anropas när den redan läses in
* Sök efter ogiltigt innehåll genom att ignorera meddelanden där data inte är en sträng
* Bryt upp anrop till fönster i en if-sats för att kontrol lera webbläsarens stöd för löfte

#### <a name="fixes"></a>Korrigeringar

* Korrigera dependabot genom att ta bort garn. lock från gitignore
* Åtgärda säkerhets problem genom att uppgradera pug till v 3.0.0 i snabb start – NodeJS kod exempel
* Åtgärda flera säkerhets risker genom att uppgradera Jest-och TypeScript-paket
* Åtgärda säkerhets risker genom att uppgradera Microsoft. IdentityModel. clients. ActiveDirectory till v 5.2.0

<br>

## <a name="version-100"></a>Version 1.0.0

Den här versionen innehåller viktiga ändringar, nya funktioner, kod exempel förbättringar och fel korrigeringar.

#### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

* Kräv Azure AD-token och under domän, och de inaktuella token som används i tidigare versioner.
* Ange CookiePolicy till inaktive rad. Kvarhållning av användar inställningar är inaktiverat som standard. Läsaren startar med standardinställningar varje gång, om inte CookiePolicy är inställt på aktive rad.

#### <a name="new-features"></a>Nya funktioner

* Lägg till stöd för att aktivera eller inaktivera cookies
* Lägg till Android-Kotlin snabb start kod exempel
* Lägg till Android Java snabb start kod exempel
* Lägg till Node.js snabb starts kod exempel

#### <a name="improvements"></a>Bättre

* Uppdatera Node.js avancerade README.md
* Ändra exempel på python-kod från Avancerat till snabb start
* Flytta kod exempel för iOS SWIFT till JS/samples
* Uppdatera kod exempel för att använda v-1.0.0

#### <a name="fixes"></a>Korrigeringar

* Korrigering för Node.js avancerad kod exempel
* Lägg till saknade filer för Advanced-csharp-Multiple-Resources
* Ta bort en-oss från hyperlänkar

<br>

## <a name="version-003"></a>Version 0.0.3

Den här versionen innehåller nya funktioner, förbättringar av kod exempel, säkerhets sårbarhets korrigeringar och fel korrigeringar.

#### <a name="new-features"></a>Nya funktioner

* Lägg till kod exempel för iOS Swift
* Lägg till exempel på avancerad kod i C# demonstrera användningen av flera resurser 
* Lägg till stöd för att inaktivera hel skärms växlings funktion
* Lägg till stöd för att dölja fördjupad läsar programmets avslutnings knapp
* Lägg till en callback-funktion som kan användas av värd programmet när du avslutar den fördjupade läsaren
* Uppdatera kod exempel för att använda Azure Active Directory autentisering

#### <a name="improvements"></a>Bättre

* Uppdatera C# Advanced Code-exemplet för att inkludera Word-dokument
* Uppdatera kod exempel för att använda v-0.0.3

#### <a name="fixes"></a>Korrigeringar

* Uppgradera lodash till version 4.17.14 för att åtgärda säkerhets problem
* Uppdatera C# MSAL-bibliotek för att åtgärda säkerhets problem
* Uppgradera Mixin-djup till version 1.3.2 för att lösa säkerhets risker
* Uppgradera Jest, WebPack och WebPack-CLI som använder sårbara versioner av Set-Value och Mixin-djup för att åtgärda säkerhets risker

<br>

## <a name="version-002"></a>Version 0.0.2

Den här versionen innehåller nya funktioner, förbättringar av kod exempel, säkerhets sårbarhets korrigeringar och fel korrigeringar.

#### <a name="new-features"></a>Nya funktioner

* Lägg till avancerad python-kod exempel
* Lägg till kod exempel för Java-snabb start
* Lägg till enkelt kod exempel

#### <a name="improvements"></a>Bättre

* Byt namn på resourceName till cogSvcsSubdomain
* Flytta hemligheterna bort från kod och Använd miljövariabler
* Uppdatera kod exempel för att använda v-0.0.2

#### <a name="fixes"></a>Korrigeringar

* Åtgärda den fördjupade läsar knappens funktions fel
* Åtgärda bruten rullning
* Uppgradera styr skydds paketet till version 4.1.2 för att åtgärda säkerhets risker
* Korrigerar buggar i test av SDK-enhet
* Korrigerar JavaScript-fel i Internet Explorer 11-kompatibilitet
* SDK-URL: er för uppdateringar

<br>

## <a name="version-001"></a>Version 0.0.1

Den första versionen av SDK SDK för avancerad läsare.

* Lägg till avancerad läsares JavaScript SDK
* Lägg till stöd för att ange GRÄNSSNITTs språk
* Lägg till en tids gräns för att fastställa när launchAsync-funktionen ska kunna köras med ett tids gräns fel
* Lägg till stöd för att ange z-index för den integrerande läsarens iframe
* Lägg till stöd för att använda en WebView-tagg i stället för en iframe för kompatibilitet med Chrome-appar
* Lägg till test för SDK-enhet
* Lägg till Node.js avancerad kod exempel
* Lägg till exempel på avancerad kod i C#
* Lägg till snabb starts kod exempel för C#
* Lägg till paket konfiguration, garn och andra build-filer
* Lägga till git-konfigurationsfiler
* Lägga till README.md-filer i kod exempel och SDK
* Lägg till MIT-licens
* Lägg till deltagar instruktioner
* Lägg till knappen för statiska Symbols SVG-tillgångar

## <a name="next-steps"></a>Nästa steg

Kom igång med Avancerad läsare:

* Läs [klient biblioteks referensen för avancerad läsare](./reference.md)
* Utforska [klient biblioteket för fördjupade läsare på GitHub](https://github.com/microsoft/immersive-reader-sdk)
