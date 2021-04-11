---
title: Använd ett verktyg för att redigera och validera DTDL-modeller | Microsoft Docs
description: Installera DTDL-redigeraren för Visual Studio Code eller Visual Studio 2019 och Använd den för att redigera IoT Plug and Play-modeller.
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 62a89fbb654624e1b1f9242d21d3bbb188140157
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067494"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>Installera och Använd DTDL redigerings verktyg

[DTDL-modeller (Digital Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) är JSON-filer. Du kan använda ett tillägg för Visual Studio Code eller Visual Studio 2019 för att redigera och validera dessa modell filer.

## <a name="install-and-use-the-vs-code-extension"></a>Installera och använda VS Code-tillägget

DTDL-tillägget för VS Code lägger till följande DTDL redigerings funktioner:

- DTDL v2-syntax.
- IntelliSense, inklusive komplettera automatiskt, som hjälper dig med språksyntaxen.
- Möjligheten att skapa gränssnitt från kommando paletten.

Om du vill installera DTDL-tillägget går du till [DTDL-redigeraren för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl). Du kan också söka efter **DTDL** i vyn tillägg i vs Code.

När du har installerat tillägget använder du det för att skapa DTDL-modell-filer i VS Code:

- Tillägget ger DTDL för autentisering och markerar fel som visas på följande skärm bild:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="Modell validering i VS-kod":::

- Använd IntelliSense och Autoavsluta när du redigerar DTDL-modeller:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="Använda IntelliSense för DTDL-modeller i VS Code":::

- Skapa ett nytt DTDL-gränssnitt. Kommandot **DTDL: Create Interface** skapar en JSON-fil med ett nytt gränssnitt. Gränssnittet innehåller exempel på telemetri, egenskaper och kommando definitioner.

## <a name="install-and-use-the-visual-studio-extension"></a>Installera och använda Visual Studio-tillägget

DTDL-tillägget för Visual Studio 2019 lägger till följande DTDL redigerings funktioner:

- DTDL v2-syntax.
- IntelliSense, inklusive komplettera automatiskt, som hjälper dig med språksyntaxen.

Om du vill installera DTDL-tillägget går du till [DTDL språk stöd för VS 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport). Du kan också söka efter **DTDL** i **Hantera tillägg** i Visual Studio.

När du har installerat tillägget kan du använda det för att skapa DTDL-modell-filer i Visual Studio:

- Tillägget ger DTDL för autentisering och markerar fel som visas på följande skärm bild:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Modell validering i Visual Studio":::

- Använd IntelliSense och Autoavsluta när du redigerar DTDL-modeller:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="Använda IntelliSense för DTDL-modeller i Visual Studio":::

## <a name="next-steps"></a>Nästa steg

I den här instruktions artikeln har du lärt dig hur du använder DTDL-tilläggen för Visual Studio Code och Visual Studio 2019 för att skapa och validera DTDL-modellens filer. Ett förslag till nästa steg är att lära dig hur du använder [Azure IoT Explorer med dina modeller och enheter](./howto-use-iot-explorer.md).
