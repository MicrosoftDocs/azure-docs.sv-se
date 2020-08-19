---
title: 'Självstudie: starta den fördjupade läsaren med exemplet Swift iOS Code'
titleSuffix: Azure Cognitive Services
description: I den här självstudien konfigurerar och kör du ett exempel på Swift-program som startar den fördjupade läsaren.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: ce9d3af8f7517e2acae5264197b842d47085279c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516375"
---
# <a name="tutorial-start-the-immersive-reader-using-the-swift-ios-code-sample"></a>Självstudie: starta den fördjupade läsaren med exemplet Swift iOS Code

I [översikten](./overview.md)har du lärt dig om vad den fördjupade läsaren är och hur den implementerar beprövade tekniker för att förbättra läsningen av förståelse för språkstuderande, nya läsare och studenter med inlärnings skillnader. Den här självstudien beskriver hur du skapar ett iOS-program som startar den fördjupade läsaren. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera och kör en SWIFT-app för iOS genom att använda ett exempel projekt.
> * Hämta en åtkomsttoken.
> * Starta den fördjupade läsaren med exempel innehåll.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* En fördjupad läsar resurs som kon figurer ATS för Azure Active Directory autentisering. Följ [dessa instruktioner](./how-to-create-immersive-reader.md) för att konfigurera. Du behöver några av de värden som skapas här när du konfigurerar miljö egenskaperna. Spara utdata från sessionen i en textfil för framtida bruk.
* [MacOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [SDK för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Konfigurera autentiseringsuppgifter för autentisering

1. Öppna Xcode och öppna **Immersive-Reader-SDK/JS/samples/iOS/QuickStart-Swift/QuickStart-Swift. XCODEPROJ**.
1. Välj **Product**  >  **Scheme**  >  **Redigera schema**för produkt schema på den översta menyn.
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

* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](./reference.md).
* Visa kod exempel på [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
