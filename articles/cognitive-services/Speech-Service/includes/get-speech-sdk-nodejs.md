---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 99be483f67bf5e3f9b27c63a2318df8761c16eff
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434535"
---
:::row:::
    :::column span="3":::
        Tal-SDK för Java Script är tillgängligt som ett NPM-paket, se <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-Speech-SDK </a> och dess Companion GitHub-lagringsplats <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">kognitiv-Services-Speech-SDK-JS </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Även om tal-SDK för Java Script är tillgängligt som ett NPM-paket, så kan både Node.js och klient webbläsare använda IT-överväga de olika arkitektoniska konsekvenserna av varje miljö. <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Dokument objekts modellen (dom)</a> är till exempel inte tillgänglig för program på Server sidan, precis som <a href="https://nodejs.org/api/fs.html" target="_blank">fil systemet</a> inte är tillgängligt för program på klient sidan.

### <a name="nodejs-package-manager-npm"></a>Node.js Package Manager (NPM)

Om du vill installera tal-SDK för Java Script kör du följande `npm install` kommando nedan.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Mer information finns i snabb starten för <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Node.js tal SDK </a>.
