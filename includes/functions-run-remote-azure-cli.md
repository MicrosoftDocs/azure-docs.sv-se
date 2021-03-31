---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93425198"
---
## <a name="invoke-the-function-on-azure"></a>Anropa funktionen på Azure

Eftersom din funktion använder en HTTP-utlösare anropar du den genom att göra en HTTP-begäran till dess URL i webbläsaren eller med ett verktyg som vändning. 

# <a name="browser"></a>[Webbläsare](#tab/browser)

Kopiera den fullständiga **anrops-URL: en** som visas i utdata från kommandot Publicera till ett webbläsarens Adress fält, som lägger till Frågeparametern `&name=Functions` . Webbläsaren bör visa liknande utdata som när du körde funktionen lokalt.

![Resultatet av funktionen körs på Azure i en webbläsare](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Kör [`curl`](https://curl.haxx.se/) med **anrops-URL: en** och Lägg till parametern `&name=Functions` . Kommandots utdata ska vara texten, "Hello Functions".

![Resultatet av funktionen körs på Azure med hjälp av sväng](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
