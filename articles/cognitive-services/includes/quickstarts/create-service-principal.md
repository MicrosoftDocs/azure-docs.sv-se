---
title: Skapa Azure-tjänstens huvudnamn
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: dc5297a3abb913c7bef7c1669fa319d01a0c0203
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947542"
---
## <a name="create-an-azure-service-principal"></a>Skapa ett huvud namn för Azure-tjänsten

För att ditt program ska interagera med ditt Azure-konto behöver du ett Azure-tjänstens huvud namn för att hantera behörigheter. Följ instruktionerna i [skapa ett huvud namn för Azure-tjänsten](/powershell/azure/create-azure-service-principal-azureps?viewFallbackFrom=azps-3.3.0).

När du skapar ett huvud namn för tjänsten visas det ett hemligt värde, ett ID och ett program-ID. Spara program-ID och hemlighet till en tillfällig plats för senare steg.
