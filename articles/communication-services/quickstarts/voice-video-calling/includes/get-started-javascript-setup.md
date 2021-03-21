---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 33212f14faa0e7c201c13474af981790c01284f3
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "104598823"
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

Använd `npm install` kommandot för att installera Azure Communication Services som anropar klient bibliotek för Java Script.

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
