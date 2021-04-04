---
title: Placering för kund information i Azure IoT Central | Microsoft Docs
description: I den här artikeln beskrivs placering för kund information i Azure IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280708"
---
# <a name="azure-iot-central-customer-data-residency"></a>Placering för Azure IoT Central kund information

IoT Central lagrar inte kund information utanför den angivna geografien, förutom i följande scenarier:

- När en ny användare läggs till i ett befintligt IoT Central-program kan användarens e-post-ID lagras utanför geografien tills den inbjudna användaren har åtkomst till programmet för första gången.

- IoT Central instrument panelens kart paneler använder [Azure Maps](../../azure-maps/about-azure-maps.md). När du lägger till en kart panel till ett befintligt IoT Central-program kan plats data bearbetas eller lagras i enlighet med Azure Maps tjänstens regler för geolokalisering.
