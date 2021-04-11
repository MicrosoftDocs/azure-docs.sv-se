---
title: Så här förbereder du HTML-innehåll för avancerad läsare
titleSuffix: Azure Cognitive Services
description: Lär dig hur du startar den fördjupade läsaren med HTML, Java Script, python, Android eller iOS. Avancerad läsare använder beprövade tekniker för att förbättra förståelsen av förståelse för språk studerande, nya läsare och studenter med inlärnings skillnader.
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 40aab9510eb90c405f92be49a3b4c0a3f756c872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620164"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>Så här förbereder du HTML-innehåll för avancerad läsare

Den här artikeln visar hur du strukturerar din HTML och hämtar innehållet, så att det kan användas av fördjupad läsare.

## <a name="prepare-the-html-content"></a>Förbereda HTML-innehållet

Placera det innehåll som du vill återge i den fördjupade läsaren i ett behållar element. Se till att behållar elementet har ett unikt `id` . Den fördjupade läsaren tillhandahåller stöd för grundläggande HTML-element, se [referensen](reference.md#html-support) för mer information.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Hämta HTML-innehåll i Java Script

Använd `id` elementet container för att hämta HTML-innehållet i JavaScript-koden.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Starta den fördjupade läsaren med ditt HTML-innehåll

När du anropar `ImmersiveReader.launchAsync` ställer du in segmentets `mimeType` egenskap till `text/html` för att aktivera åter givning av HTML.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK-referensen för avancerad läsare](reference.md)