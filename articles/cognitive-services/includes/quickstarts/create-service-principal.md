---
title: Skapa Azure-tjänstens huvudnamn
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 2f60759fbbae2c0f712a475ef397ca260e1f0415
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473779"
---
## <a name="create-an-azure-service-principal"></a>Skapa ett huvud namn för Azure-tjänsten

För att ditt program ska interagera med ditt Azure-konto behöver du ett Azure-tjänstens huvud namn för att hantera behörigheter. Följ instruktionerna i [skapa ett huvud namn för Azure-tjänsten](/powershell/azure/create-azure-service-principal-azureps).

När du skapar ett huvud namn för tjänsten visas det ett hemligt värde, ett ID och ett program-ID. Spara program-ID och hemlighet till en tillfällig plats för senare steg.
