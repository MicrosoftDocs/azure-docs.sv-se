---
title: Förhindra innehålls översättning – översättare
titleSuffix: Azure Cognitive Services
description: Förhindra översättning av innehåll med Translator. Med Translator kan du tagga innehåll så att det inte översätts.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: e89ff3f4c1f54d9e137d3dd51e325b908c826b03
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897484"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Förhindra översättning av innehåll med Translator

Med Translator kan du tagga innehåll så att det inte översätts. Det kan till exempel vara bra att tagga kod, ett varumärkesnamn eller ett ord/en fras som inte är begriplig när den lokaliseras.

## <a name="methods-for-preventing-translation"></a>Metoder för att förhindra Översättning

1. Tagga ditt innehåll med `notranslate` . Det är avsiktligt att detta fungerar endast när textType anges som HTML

   Exempel:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Tagga ditt innehåll med `translate="no"` . Detta fungerar bara när textType anges som HTML

   Exempel:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. Använd den [dynamiska ord listan](dynamic-dictionary.md) för att ange en speciell översättning.

4. Skicka inte strängen till Translator för översättning.

5. Anpassad översättare: Använd en [ord lista i en anpassad översättare](custom-translator/what-is-dictionary.md) för att föreskriva översättning av en fras med en sannolikhet på 100%.


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Använd översättnings åtgärden för att översätta text](reference/v3-0-translate.md)
