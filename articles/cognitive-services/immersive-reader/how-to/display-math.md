---
title: Visa matematik i den fördjupade läsaren
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du visar matematik i den fördjupade läsaren.
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 75b748604e8faa502970ac71e3fec4fae2774c19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91334522"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Så här visar du matematik i den fördjupade läsaren

Den fördjupade läsaren kan visa matematik när den anges i form av[mathml](https://developer.mozilla.org/docs/Web/MathML)(matematiskt Markup Language).
MIME-typen kan ställas in via [segmentet](../reference.md#chunk)för avancerad läsare. Mer information finns i [MIME-typer som stöds](../reference.md#supported-mime-types) .

## <a name="send-math-to-the-immersive-reader"></a>Skicka matematik till den fördjupade läsaren
För att skicka matematik till den fördjupade läsaren, anger du ett segment som innehåller MathML och anger MIME-typen till ```application/mathml+xml``` .

Om ditt innehåll exempelvis var följande:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Sedan kan du Visa ditt innehåll med hjälp av följande Java Script.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

När du startar den fördjupade läsaren bör du se:

![Matematik i fördjupad läsare](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Nästa steg

* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](../reference.md)