---
title: Fel sökning av tal enheter SDK-Speech service
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder tal enheter SDK.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "74815574"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Felsöka Speech Devices SDK

Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder tal enheter SDK.

## <a name="certificate-failures"></a>Certifikat haverier

Om du får certifikat fel när du använder tal tjänsten kontrollerar du att enheten har rätt datum och tid:

1. Gå till **Inställningar**. Under **system** väljer du **datum & tid**.

    ![Under Inställningar väljer du datum & tid](media/speech-devices-sdk/qsg-12.png)

1. Låt alternativet **automatiskt datum &s tid** vara markerat. Under **Välj** tidszon väljer du aktuell tidszon.

    ![Välj alternativ för datum och tidszon](media/speech-devices-sdk/qsg-13.png)

    När du ser att dev kit-tiden matchar tiden på din dator är dev-paketet anslutet till Internet.

## <a name="next-steps"></a>Nästa steg

* [Läs viktig information](devices-sdk-release-notes.md)
