---
title: Avancerad läsare SDK-referens
titleSuffix: Azure Cognitive Services
description: 'SDK: n för avancerad läsare innehåller ett JavaScript-bibliotek som gör att du kan integrera den fördjupade läsaren i ditt program.'
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metang
ms.openlocfilehash: f2f5c8193454a3b7fa6be1cea7a1236b613d6c8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92636535"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>Avancerad läsares Java Script SDK-referens (v 1.1)

SDK: n för avancerad läsare innehåller ett JavaScript-bibliotek som gör att du kan integrera den fördjupade läsaren i ditt program.

## <a name="functions"></a>Functions

SDK: n visar funktionerna:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>launchAsync

Startar den fördjupade läsaren i en `iframe` i ditt webb program. Observera att storleken på innehållet är begränsad till högst 50 MB.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>launchAsync-parametrar

| Namn | Typ | Description |
| ---- | ---- |------------ |
| `token` | sträng | Azure AD-autentiseringstoken. Se [anvisningar för att skapa en fördjupad läsar resurs](./how-to-create-immersive-reader.md) för mer information. |
| `subdomain` | sträng | Den anpassade under domänen för den fördjupade läsar resursen i Azure. Se [anvisningar för att skapa en fördjupad läsar resurs](./how-to-create-immersive-reader.md) för mer information. |
| `content` | [Innehåll](#content) | Ett objekt som innehåller det innehåll som ska visas i den fördjupade läsaren. |
| `options` | [Alternativ](#options) | Alternativ för att konfigurera vissa beteenden för den fördjupade läsaren. Valfritt. |

#### <a name="returns"></a>Returer

Returnerar en `Promise<LaunchResponse>` , som löses när den fördjupade läsaren läses in. `Promise`Matchar ett- [`LaunchResponse`](#launchresponse) objekt.

#### <a name="exceptions"></a>Undantag

Den returnerade filen `Promise` avvisas med ett [`Error`](#error) objekt om den fördjupade läsaren inte kan läsas in. Mer information finns i [fel koderna](#error-codes).

<br>

## <a name="close"></a>stäng

Stänger den fördjupade läsaren.

Ett exempel på användnings fall för den här funktionen är om knappen Avsluta är dold genom inställning ```hideExitButton: true``` i [alternativ](#options). Sedan kan en annan knapp (till exempel ett mobil huvuds bakre pil) anropa den här ```close``` funktionen när den klickas.

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>Start knapp för avancerad läsare

SDK tillhandahåller standard formatet för knappen för att starta den fördjupade läsaren. Använd `immersive-reader-button` Class-attributet för att aktivera det här formatet. Mer information finns i [så här anpassar du knappen för avancerad läsare](./how-to-customize-launch-button.md) .

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>Valfria attribut

Använd följande attribut för att konfigurera utseendet och utseendet på knappen.

| Attribut | Beskrivning |
| --------- | ----------- |
| `data-button-style` | Anger formatet för knappen. Kan vara `icon` , `text` eller `iconAndText` . Standardvärdet är `icon` . |
| `data-locale` | Anger språkvarianten. Exempel: `en-US` eller `fr-FR`. Standardvärdet är engelska `en` . |
| `data-icon-px-size` | Anger storleken på ikonen i bild punkter. Standardvärdet är 20px. |

<br>

## <a name="renderbuttons"></a>renderButtons

```renderButtons```Funktionen är inte nödvändig om du använder guiden [så här anpassar](./how-to-customize-launch-button.md) du vägledningen för avancerad läsare.

Den här funktionen formaterar och uppdaterar dokumentets fördjupade läsa knapp element. Om ```options.elements``` har angetts återges knapparna i varje element som anges i ```options.elements``` . Att använda- ```options.elements``` parametern är användbart när du har flera avsnitt i ditt dokument där du kan starta den fördjupade läsaren och vill ha ett förenklat sätt att återge flera knappar med samma format, eller vill återge knapparna med ett enkelt och enhetligt design mönster. Om du vill använda den här funktionen med parametern [renderButtons alternativ](#renderbuttons-options) , anropar ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);``` du sid inläsning som visas i kodfragmentet nedan. Annars återges knapparna i dokumentets element som har klassen ```immersive-reader-button``` som visas i [How-to customize The avancerad Reader-knappen](./how-to-customize-launch-button.md) .

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

Se ovanstående [valfria attribut](#optional-attributes) för fler åter givnings alternativ. Om du vill använda de här alternativen lägger du till alla alternativ-attribut till varje ```HTMLDivElement``` på sidans HTML.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>renderButtons-parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| `options` | [renderButtons-alternativ](#renderbuttons-options) | Alternativ för att konfigurera vissa beteenden för funktionen renderButtons. Valfritt. |

### <a name="renderbuttons-options"></a>renderButtons-alternativ

Alternativ för att återge knapparna för avancerad läsare.

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>Parametrar för renderButtons-alternativ

| Inställningen | Typ | Beskrivning |
| ------- | ---- | ----------- |
| ämnen | HTMLDivElement[] | Element som återger de fördjupade Reader-knapparna i. |

##### `elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

Innehåller svaret från anropet till `ImmersiveReader.launchAsync` . Observera att en referens till `iframe` som innehåller den fördjupade läsaren kan nås via `container.firstChild` .

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>LaunchResponse-parametrar

| Inställningen | Typ | Beskrivning |
| ------- | ---- | ----------- |
| container | HTMLDivElement | HTML-element som innehåller IFRAME för avancerad läsare. |
| sessionId | Sträng | Globalt unik identifierare för den här sessionen som används för fel sökning. |
 
## <a name="error"></a>Fel

Innehåller information om ett fel.

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>Fel parametrar

| Inställningen | Typ | Beskrivning |
| ------- | ---- | ----------- |
| kod | Sträng | En av en uppsättning felkoder. Se [Felkoder](#error-codes). |
| meddelande | Sträng | Mänskligt åter givning av felet. |

#### <a name="error-codes"></a>Felkoder

| Kod | Beskrivning |
| ---- | ----------- |
| BadArgument | Det angivna argumentet är ogiltigt, se `message` parameter för [felet](#error). |
| Tidsgräns | Det gick inte att läsa in den fördjupade läsaren inom den angivna tids gränsen. |
| TokenExpired | Angiven token har upphört att gälla. |
| Begränsas | Anrops frekvens gränsen har överskridits. |

<br>

## <a name="types"></a>Typer

### <a name="content"></a>Innehåll

Innehåller det innehåll som ska visas i den fördjupade läsaren.

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>Innehålls parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| title | Sträng | Rubrik text som visas överst i den fördjupade läsaren (valfritt) |
| segment | [Segment []](#chunk) | Matris med segment |

##### `title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>Segment

Ett enda data segment som skickas till innehållet i den fördjupade läsaren.

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>Segment parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| innehåll | Sträng | Den sträng som innehåller det innehåll som skickas till den fördjupade läsaren. |
| lang | Sträng | Språk för texten, värdet är i IETF BCP 47 language tag-format, t. ex. en, es-ES. Språket identifieras automatiskt om det inte anges. Se [Språk som stöds](#supported-languages). |
| mimeType | sträng | Oformaterad text, MathML, HTML-& Microsoft Word DOCX-format stöds. Mer information finns i [MIME-typer som stöds](#supported-mime-types) . |

##### `content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>MIME-typer som stöds

| MIME-typ | Beskrivning |
| --------- | ----------- |
| text/ren | Oformaterad text. |
| text/html | HTML-innehåll. [Läs mer](#html-support)|
| program/mathml + XML | MathML (matematiskt Markup Language). [Läs mer](./how-to/display-math.md).
| ument för program/vnd.openxmlformats-officedocument.wordprocessingml.doc | Dokument för Microsoft Word. docx-format.


<br>

## <a name="options"></a>Alternativ

Innehåller egenskaper som konfigurerar vissa beteenden för den fördjupade läsaren.

```typescript
{
    uiLang?: string;
    timeout?: number;
    uiZIndex?: number;
    useWebview?: boolean;
    onExit?: () => any;
    allowFullscreen?: boolean;
    hideExitButton?: boolean;
    cookiePolicy?: CookiePolicy;
    disableFirstRun?: boolean;
    readAloudOptions?: ReadAloudOptions;
    translationOptions?: TranslationOptions;
    displayOptions?: DisplayOptions;
    preferences?: string;
    onPreferencesChanged?: (value: string) => any;
    customDomain?: string;
}
```

#### <a name="options-parameters"></a>Alternativ parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| uiLang | Sträng | Språk i användar gränssnittet, värdet är i IETF BCP 47 language tag-format, t. ex. en, es-ES. Standardvärdet för webb läsar språk om inget anges. |
| timeout | Antal | Varaktighet (i millisekunder) innan [launchAsync](#launchasync) Miss lyckas med ett tids gräns fel (Standardvärdet är 15000 MS). Denna timeout gäller endast för den första starten av läsar sidan, där lyckad observeras när läsar sidan öppnas och rotations rutan startar. Justeringen av tids gränsen bör inte vara nödvändig. |
| uiZIndex | Antal | Z-index för den iframe som ska skapas (Standardvärdet är 1000). |
| useWebview | Boolesk| Använd en WebView-tagg i stället för en iframe för kompatibilitet med Chrome-appar (Standardvärdet är falskt). |
| onExit | Funktion | Körs när den fördjupade läsaren avslutas. |
| allowFullscreen | Boolesk | Möjligheten att växla helskärm (Standardvärdet är sant). |
| hideExitButton | Boolesk | Huruvida fördjupad läsares avslutnings knapp pil (standard är falskt). Detta bör bara vara sant om det finns en alternativ mekanism för att avsluta den fördjupade läsaren (t. ex. ett mobilt verktygsfälts bakre pil). |
| cookiePolicy | [CookiePolicy](#cookiepolicy-options) | Inställning för avancerad läsares cookie-användning (Standardvärdet är *CookiePolicy. Disable*). Det är värd programmets ansvar för att erhålla nödvändiga användar medgivande i enlighet med EU: s policy för cookie-kompatibilitet. Se [alternativ för cookie-princip](#cookiepolicy-options). |
| disableFirstRun | Boolesk | Inaktivera den första körnings upplevelsen. |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | Alternativ för att konfigurera Läs högt. |
| translationOptions | [TranslationOptions](#translationoptions) | Alternativ för att konfigurera översättning. |
| displayOptions | [DisplayOptions](#displayoptions) | Alternativ för att konfigurera text storlek, teckensnitt osv. |
| inställningarna | Sträng | Strängen som returneras från onPreferencesChanged som representerar användarens inställningar i den fördjupade läsaren. Mer information finns i [inställnings parametrar](#settings-parameters) och [anvisningar om hur du lagrar användar inställningar](./how-to-store-user-preferences.md) . |
| onPreferencesChanged | Funktion | Körs när användarens inställningar har ändrats. Se [anvisningar för att lagra användar inställningar](./how-to-store-user-preferences.md) för mer information. |
| customDomain | Sträng | Reserverat för internt bruk. Anpassad domän där den fördjupade läsaren webapp är värdbaserad (Standardvärdet är null). |

##### `uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `preferences`

> [!CAUTION]
> **Viktigt** Försök inte att program mässigt ändra värdena för den `-preferences` sträng som skickas till och från det fördjupade läsar programmet, eftersom detta kan orsaka oväntade beteenden som resulterar i en försämrad användar upplevelse för dina kunder. Värd program ska aldrig tilldela ett anpassat värde till eller ändra `-preferences` strängen. När du använder `-preferences` alternativet sträng använder du bara det exakta värdet som returnerades från `-onPreferencesChanged` alternativet motringning.

```Parameters
Type: String
Required: false
Default value: null
```

##### `onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `customDomain`
```Parameters
Type: String
Required: false
Default value: null
```

<br>

## <a name="readaloudoptions"></a>ReadAloudOptions

```typescript
type ReadAloudOptions = {
    voice?: string;
    speed?: number;
    autoplay?: boolean;
};
```

#### <a name="readaloudoptions-parameters"></a>ReadAloudOptions-parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| röst | Sträng | Röst, antingen "hona" eller "hane". Observera att inte alla språk stöder båda kön. |
| hastighet | Antal | Uppspelnings hastigheten måste vara mellan 0,5 och 2,5. |
| Spela | Boolesk | Starta automatiskt Läs högt när den fördjupade läsaren läser in. |

##### `voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> På grund av begränsningar i webbläsaren stöds inte automatisk uppspelning i Safari.

<br>

## <a name="translationoptions"></a>TranslationOptions

```typescript
type TranslationOptions = {
    language: string;
    autoEnableDocumentTranslation?: boolean;
    autoEnableWordTranslation?: boolean;
};
```

#### <a name="translationoptions-parameters"></a>TranslationOptions-parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| language | Sträng | Anger översättnings språket, värdet är i IETF BCP 47 language tag-format, t. ex. fr-FR, ES-MX, zh-hans-CN. Krävs för att automatiskt aktivera Word-eller dokument översättning. |
| autoEnableDocumentTranslation | Boolesk | Översätt hela dokumentet automatiskt. |
| autoEnableWordTranslation | Boolesk | Aktivera ord översättning automatiskt. |

##### `language`
```Parameters
Type: String
Required: true
Default value: null 
Values available: See the Supported Languages section
```

<br>

## <a name="displayoptions"></a>DisplayOptions

```typescript
type DisplayOptions = {
    textSize?: number;
    increaseSpacing?: boolean;
    fontFamily?: string;
};
```

#### <a name="displayoptions-parameters"></a>DisplayOptions-parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| textSize | Antal | Anger den valda text storleken. |
| increaseSpacing | Boolesk | Anger om text avstånd ska växlas eller inaktive ras. |
| fontFamily | Sträng | Anger det valda teckensnittet ("Calibri", "ComicSans" eller "Sitka"). |

##### `textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `fontFamily`
```Parameters
Type: String
Required: false
Default value: "Calibri"
Values available: "Calibri", "Sitka", "ComicSans"
```

<br>

## <a name="cookiepolicy-options"></a>CookiePolicy-alternativ

```typescript
enum CookiePolicy { Disable, Enable }
```

**Inställningarna i listan nedan är endast i informations syfte**. Den fördjupade läsaren lagrar sina inställningar eller användar inställningar i cookies. Med det här *cookiePolicy* -alternativet **inaktive ras** användningen av cookies som standard för att efterleva EU: s bestämmelser om cookie-efterlevnad. Om du vill återaktivera cookies och återställa standard funktionerna för användar inställningarna för avancerad läsare måste du se till att din webbplats eller ditt program får rätt medgivande från användaren för att aktivera cookies. Om du sedan vill återaktivera cookies i den fördjupade läsaren måste du uttryckligen ange alternativet *cookiePolicy* till *cookiePolicy. enable* när du startar den fördjupade läsaren. I tabellen nedan beskrivs vilka inställningar som fördjupar läsaren lagrar i sin cookie när alternativet *cookiePolicy* är aktiverat.

#### <a name="settings-parameters"></a>Inställnings parametrar

| Inställningen | Typ | Beskrivning |
| ------- | ---- | ----------- |
| textSize | Antal | Anger den valda text storleken. |
| fontFamily | Sträng | Anger det valda teckensnittet ("Calibri", "ComicSans" eller "Sitka"). |
| textSpacing | Antal | Anger om text avstånd ska växlas eller inaktive ras. |
| formattingEnabled | Boolesk | Anger om HTML-formateringen ska växlas eller inaktive ras. |
| visst | Sträng | Anger det valda temat (t. ex. "Light", "mörk"...). |
| syllabificationEnabled | Boolesk | Anger om syllabification ska växlas eller inaktive ras. |
| nounHighlightingEnabled | Boolesk | Anger om Substantiv markering ska växlas eller inaktive ras. |
| nounHighlightingColor | Sträng | Anger vald markerings färg för substantiv. |
| verbHighlightingEnabled | Boolesk | Anger om markering av verb ska växlas eller inaktive ras. |
| verbHighlightingColor | Sträng | Anger markerings färg för vald verb. |
| adjectiveHighlightingEnabled | Boolesk | Anger om adjektiv markeringar är aktiverade eller inaktiverade. |
| adjectiveHighlightingColor | Sträng | Anger den valda färgen för adjektiv markering. |
| adverbHighlightingEnabled | Boolesk | Anger om markering av adverb ska växlas på eller av. |
| adverbHighlightingColor | Sträng | Anger den valda färgen för adverb-markering. |
| pictureDictionaryEnabled | Boolesk | Anger om bild ord listan ska växlas eller inaktive ras. |
| posLabelsEnabled | Boolesk | Anger om upphöjda text etiketter för varje markerad del av tal är aktiverade eller inaktiverade.  |

<br>

## <a name="supported-languages"></a>Språk som stöds

Översättnings funktionen i avancerad läsare har stöd för många språk. Mer information finns i [språk stöd](./language-support.md) .

<br>

## <a name="html-support"></a>HTML-stöd

När formateringen är aktive rad kommer följande innehåll att återges som HTML i den fördjupade läsaren.

| HTML | Innehåll som stöds |
| --------- | ----------- |
| Teckensnitts format | Fet, kursiv, understruken, kod, genomstruken, upphöjd, nedsänkt |
| Osorterade listor | Skiva, cirkel, kvadrat |
| Ordnade listor | Decimal, övre-alfa, lägre – alfa, övre roman Lower-Roman |

Taggar som inte stöds kommer att återges jämförbart. Bilder och tabeller stöds inte för närvarande.

<br>

## <a name="browser-support"></a>Stöd för webbläsare

Använd de senaste versionerna av följande webbläsare för bästa möjliga upplevelse med avancerad läsare.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK för avancerad läsare på GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Snabb start: skapa en webbapp som startar den fördjupade läsaren (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
