---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: a3345ff9a6cc1d5f8e2fbc78c2a76ba6f183aeb6
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620200"
---
## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* En fördjupad läsar resurs som kon figurer ATS för Azure Active Directory autentisering. Följ [dessa instruktioner](../../how-to-create-immersive-reader.md) för att konfigurera.  Du behöver några av de värden som skapas här när du konfigurerar miljö egenskaperna. Spara utdata från sessionen i en textfil för framtida bruk.
* [MacOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [SDK för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Konfigurera autentiseringsuppgifter för autentisering

1. Öppna Xcode och öppna **Immersive-Reader-SDK/JS/samples/iOS/QuickStart-Swift/QuickStart-Swift. XCODEPROJ**.
1. Välj   >    >  **Redigera schema** för produkt schema på den översta menyn.
1. I vyn **Kör** väljer du fliken **argument** .
1. I avsnittet **miljövariabler** lägger du till följande namn och värden. Ange de värden som anges när du skapade din fördjupade läsare-resurs.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Genomför inte den här ändringen i käll kontrollen eftersom den innehåller hemligheter som inte ska publiceras.

## <a name="start-the-immersive-reader-with-sample-content"></a>Starta den fördjupade läsaren med exempel innehåll

I Xcode väljer du **CTRL + R** för att köra projektet.

## <a name="next-steps"></a>Nästa steg

* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](../../reference.md).
* Visa kod exempel på [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
