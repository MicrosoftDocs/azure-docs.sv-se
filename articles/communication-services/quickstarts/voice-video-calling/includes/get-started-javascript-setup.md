---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 4a493d5f0d34cd4621d55c0371036c03e267c466
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108313"
---
## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Öppna terminalen eller kommando fönstret Skapa en ny katalog för din app och navigera till den.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Kör `npm init -y` för att skapa en **package.jspå** en fil med standardinställningar.

```console
npm init -y
```

### <a name="install-the-package"></a>Installera paketet

Använd `npm install` kommandot för att installera Azure Communication Services som anropar SDK för Java Script.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Följande versioner av WebPack rekommenderas för den här snabb starten:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

I det här `--save` alternativet visas biblioteket som ett beroende i **package.jsi** filen.

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

I den här snabb starten används WebPack för att paketera program till gångarna. Kör följande kommando för att installera WebPack-, WebPack-CLI-och WebPack-dev-Server NPM-paket och lista dem som utvecklings beroenden i din **package.jspå**:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Skapa en **index.html** -fil i projektets rot Katalog. Vi kommer att använda den här filen för att konfigurera en grundläggande layout som gör det möjligt för användaren att placera ett samtal.
