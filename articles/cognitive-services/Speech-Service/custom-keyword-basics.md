---
title: Skapa snabb start för nyckelord – tal tjänst
titleSuffix: Azure Cognitive Services
description: Enheten lyssnar alltid efter ett nyckelord (eller en fras). När användaren säger nyckelordet skickar enheten allt efterföljande ljud till molnet tills användaren slutar att tala. Att anpassa ditt nyckelord är ett effektivt sätt att särskilja din enhet och förbättra ditt varumärke.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 49ac70b6881085f48c8bc3a12e31e4a1aa220c6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021957"
---
# <a name="get-started-with-custom-keyword"></a>Komma igång med Anpassat nyckelord

I den här snabb starten lär du dig grunderna för att arbeta med anpassade nyckelord, med tal Studio och tal-SDK. Ett nyckelord är ett ord eller en kort fras som gör att produkten röst aktive rad. Du skapar nyckelords modeller i tal Studio och exporterar sedan en modell fil som du använder med talet SDK i dina program.

## <a name="prerequisites"></a>Förutsättningar

Stegen i den här artikeln kräver en tal prenumeration och talet SDK. Om du inte redan har en prenumeration kan du [prova röst tjänsten kostnads fritt](overview.md#try-the-speech-service-for-free). Information om hur du hämtar SDK finns i [installations guiden](quickstarts/setup-platform.md) för din plattform.

## <a name="create-a-keyword-in-speech-studio"></a>Skapa ett nyckelord i tal Studio

Innan du kan använda ett anpassat nyckelord måste du skapa ett nyckelord med hjälp av sidan [anpassat nyckelord](https://aka.ms/sdsdk-wakewordportal) i [tal Studio](https://aka.ms/sdsdk-speechportal). När du har angett ett nyckelord genererar det en `.table` fil som du kan använda med talet SDK.

> [!IMPORTANT]
> Anpassade nyckelords modeller och resulterande `.table` filer kan **bara** skapas i tal Studio.
> Du kan inte skapa anpassade nyckelord från SDK eller med REST-anrop.

1. Gå till [tal Studio](https://aka.ms/sdsdk-speechportal) och **Logga** in, eller Välj [**skapa en prenumeration**](https://go.microsoft.com/fwlink/?linkid=2086754)om du inte har en tal prenumeration än.

1. Skapa ett **nytt projekt** på sidan [anpassat nyckelord](https://aka.ms/sdsdk-wakewordportal) . 

1. Ange ett **namn**, en valfri **Beskrivning** och välj språket. Du behöver ett projekt per språk och supporten är för närvarande begränsad till `en-US` språket.

    ![Beskriv ditt nyckelords projekt](media/custom-keyword/custom-kws-portal-new-project.png)

1. Välj ditt projekt i listan. 

    ![Välj ditt nyckelords projekt](media/custom-keyword/custom-kws-portal-project-list.png)

1. Om du vill skapa en ny nyckelords modell klickar du på **träna modell**.

1. Ange ett **namn** för modellen, en valfri **Beskrivning** och valfritt **nyckelord** , och klicka sedan på **Nästa**. Se [rikt linjerna](./custom-keyword-overview.md#choose-an-effective-keyword) för att välja ett effektivt nyckelord.

    ![Ange ditt nyckelord](media/custom-keyword/custom-kws-portal-new-model.png)

1. Portalen skapar kandidat uttal för ditt nyckelord. Lyssna på varje kandidat genom att klicka på uppspelnings knapparna och ta bort kontrollerna bredvid eventuella uttal som är felaktiga. När endast väluttal är markerat klickar du på **träna** för att börja generera nyckelords modellen. 

    ![Skärm bild som visar var du väljer rätt pronounciations.](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Det kan ta upp till trettio minuter innan modellen genereras. Nyckelords listan ändras från **bearbetning** till **lyckades** när modellen har slutförts. Sedan kan du hämta filen.

    ![Granska ditt nyckelord](media/custom-keyword/custom-kws-portal-download-model.png)

1. Den nedladdade filen är ett `.zip` Arkiv. Extrahera arkivet så ser du en fil med `.table` tillägget. Detta är den fil som du använder med SDK i nästa avsnitt, så se till att anteckna sökvägen. fil namnet speglar nyckelordets namn, till exempel om en nyckelords **aktiverings enhet** har fil namnet `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Använda en nyckelords modell med SDK

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>Nästa steg

Testa ditt anpassade nyckelord med [tal enheter SDK snabb start](./speech-devices-sdk-quickstart.md?pivots=platform-android).