---
title: Azure Marketplace IoT Edge-modulen erbjuder
description: Lär dig mer om hur du publicerar IoT Edge modul erbjudanden i Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 08/18/2020
ms.openlocfilehash: 492571ac76acfcf388954a4b714b6603aced7338
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107677"
---
# <a name="iot-edge-modules"></a>IoT Edge-moduler

[Azure IoT Edges](https://azure.microsoft.com/services/iot-edge/) plattformen backas upp av Microsoft Azure.  Den här plattformen gör det möjligt för användare att distribuera moln arbets belastningar för att köra direkt på IoT-enheter.  En IoT Edge-modul kan köra offline-arbetsbelastningar och utföra data analys lokalt. Med den här typen av erbjudande kan du spara bandbredd, skydda lokala och känsliga data och erbjuda svars tid med låg latens.  Nu har du möjlighet att dra nytta av dessa förbyggda arbets belastningar. Fram till nu var bara en fåtal av lösningar från första part från Microsoft tillgängliga.  Du var tvungen att investera tiden och resurserna för att skapa dina egna anpassade IoT-lösningar.

Med [IoT Edge moduler på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)har vi nu ett enda mål för utgivare att exponera och sälja sina lösningar till IoT-publiken. IoT-utvecklare kan i slut ända hitta och köpa funktioner för att påskynda utvecklingen av lösningen.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Viktiga fördelar med IoT Edge moduler på Azure Marketplace

| **För utgivare**    | **För kunder (IoT-utvecklare)**  |
| :------------------- | :-------------------|
| Uppnå miljon tals utvecklare som vill bygga och distribuera IoT Edge lösningar.  | Skapa en IoT Edge-lösning med förtroende för att använda säkra och testade komponenter. |
| Publicera en gång och kör på alla IoT Edge maskin vara som har stöd för behållare. | Minska tiden till marknaden genom att söka efter och distribuera 1 och tredje parts IoT Edge moduler för speciella behov. |
| Betala med flexibla fakturerings alternativ <ul> <li> Gratis och ta med din egen licens (BYOL). </li> </ul> | Gör inköp med ditt val av fakturerings modeller. <ul> <li> Gratis och ta med din egen licens (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Vad är en IoT Edge modul?

Med Azure IoT Edge kan du distribuera och hantera affärs logik på kanten i form av moduler. Azure IoT Edge moduler är de minsta beräknings enheter som hanteras av IoT Edge och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster från tredje part eller din egen lösnings-specifika kod. Mer information om IoT Edge moduler finns i [förstå Azure IoT Edge moduler](../iot-edge/iot-edge-modules.md).

**Vad är skillnaden mellan en behållar erbjudande typ och en IoT Edge moduls erbjudande typ?**

Typen IoT Edge module-erbjudande är en bestämd typ av behållare som körs på en IoT Edge enhet. Den innehåller standard konfigurations inställningar som ska köras i IoT Edge-kontexten, och kan också använda SDK för IoT Edge modul för att integreras med IoT Edge Runtime.

## <a name="publishing-your-iot-edge-module"></a>Publicera IoT Edge-modulen

**Välja den högra onlinebutiken**

IoT Edge moduler publiceras bara på Azure Marketplace. AppSource gäller inte. Mer information om skillnaderna i onlinebutiker finns i [avgöra publicerings alternativet](determine-your-listing-type.md).

**Betalningsalternativ**

Marketplace har för närvarande stöd för **kostnads fria** och **använder dina egna licens alternativ för BYOL-** fakturering för IoT Edge moduler.

### <a name="publishing-options"></a>Publicerings alternativ

I samtliga fall bör IoT Edge moduler välja alternativet för **Transact** -publicering.  Se [Välj ett publicerings alternativ](determine-your-listing-type.md) för mer information om publicerings alternativ.  

## <a name="eligibility-criteria"></a>Villkor för berättigande

Alla villkor för Microsoft Azure Marketplace-avtal och-principer gäller för IoT Edge modul-erbjudanden.  Dessutom finns det krav och tekniska krav för IoT Edge moduler.  

### <a name="prerequisites"></a>Förutsättningar

Om du vill publicera en IoT Edge-modul på Azure Marketplace måste du uppfylla följande krav:

- Åtkomst till Partner Center. Mer information finns i [skapa ett kommersiellt Marketplace-konto i Partner Center](create-account.md).
- Placera din IoT Edge-modul i en Azure Container Registry.
- Låt din IoT Edge modul metadata vara klara som (ej uttömmande lista):
    - En rubrik
    - En beskrivning (i HTML-format)
    - En logo typ bild (i storlekar på 48 x 48 (valfritt), 90 x 90 (valfritt) och från 216 x 216 till 350 x 350 BPT, allt i PNG-format)
    - Ett användnings villkor och en sekretess policy
    - Standardkonfiguration av modul (Route, dubbla önskade egenskaper, createOptions, miljövariabler)
    - Dokumentation
    - Supportkontakter

### <a name="technical-requirements"></a>Tekniska krav

De primära tekniska kraven för en IoT Edge modul, för att det ska bli certifierat och publicerat på Azure Marketplace, beskrivs i [förbereda din IoT Edge-modul tekniska till gångar](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="next-steps"></a>Nästa steg

- Logga in på [partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
- [Skapa ett IoT Edge modul-erbjudande](./partner-center-portal/azure-iot-edge-module-creation.md) i Partner Center.
