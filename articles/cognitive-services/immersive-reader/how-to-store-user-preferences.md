---
title: Spara användarinställningar
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du lagrar användarens inställningar.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 16ecd2166604d29fbc2242229f625b30ffd684e5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617700"
---
# <a name="how-to-store-user-preferences"></a>Så här lagrar du användar inställningar

Den här artikeln visar hur du lagrar användarens GRÄNSSNITTs inställningar, formellt känt som **användar inställningar**, via alternativen [-Preferences](./reference.md#options) och [-onPreferencesChanged](./reference.md#options) avancerad läsare SDK.

När SDK-alternativet [CookiePolicy](./reference.md#cookiepolicy-options) är inställt på *aktive rad* lagrar det fördjupade läsar programmet **användar inställningarna** (text storlek, temafärg, teckensnitt och så vidare) i cookies, som är lokala för en specifik webbläsare och enhet. Varje gången användaren startar den fördjupade läsaren på samma webbläsare och enhet öppnas den med användarens inställningar från den senaste sessionen på den enheten. Men om användaren öppnar den fördjupade läsaren på en annan webbläsare eller enhet konfigureras inställningarna från början med standardinställningarna för avancerad läsare och användaren måste ange sina inställningar igen och så vidare för varje enhet de använder. `-preferences` `-onPreferencesChanged` SDK-alternativen för och avancerad läsare är ett sätt för program att växla till en användares inställningar i olika webbläsare och enheter, så att användaren får en enhetlig upplevelse oavsett var de använder programmet.

Först, genom att tillhandahålla `-onPreferencesChanged` SDK-alternativet för motringning när du startar programmet för avancerad läsare, skickar den fördjupade läsaren en `-preferences` sträng tillbaka till värd programmet varje gång användaren ändrar sina inställningar under den fördjupade läsaren. Värd programmet ansvarar sedan för att lagra användar inställningarna i det egna systemet. När samma användare startar den fördjupade läsaren igen, kan värd programmet hämta användarens inställningar från lagringen och ange dem som `-preferences` sträng-SDK-alternativ när du startar ett fördjupat läsar program, så att användarens inställningar återställs.

Den här funktionen kan användas som ett alternativ för att lagra **användar inställningar** om det inte är önskvärt eller genomförbart att använda cookies.

> [!CAUTION]
> **Viktigt** Försök inte att program mässigt ändra värdena för den `-preferences` sträng som skickas till och från det fördjupade läsar programmet, eftersom detta kan orsaka oväntade beteenden som resulterar i en försämrad användar upplevelse för dina kunder. Värd program ska aldrig tilldela ett anpassat värde till eller ändra `-preferences` strängen. När du använder `-preferences` alternativet sträng använder du bara det exakta värdet som returnerades från `-onPreferencesChanged` alternativet motringning.

## <a name="how-to-enable-storing-user-preferences"></a>Så här aktiverar du lagring av användar inställningar

[launchAsync](./reference.md#launchasync) -parametern för avancerad läsare `options` innehåller `-onPreferencesChanged` återanropet. Den här funktionen kommer att anropas när användaren ändrar sina inställningar. `value`Parametern innehåller en sträng som representerar användarens aktuella inställningar. Den här strängen lagras sedan för den användaren av värd programmet.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Så här läser du in användar inställningar i den fördjupade läsaren

Skicka in användarens inställningar till den fördjupade läsaren med `-preferences` alternativet. Ett enkelt exempel på att lagra och läsa in användar inställningarna är följande:

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK-referensen för avancerad läsare](./reference.md)