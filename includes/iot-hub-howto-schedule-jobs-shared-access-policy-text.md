---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "70049015"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

I den här artikeln skapar du en backend-tjänst som schemalägger ett jobb för att anropa en direkt metod på en enhet, schemalägger ett jobb för att uppdatera enheten med dubbla och övervakar förloppet för varje jobb. För att utföra dessa åtgärder behöver tjänsten **register Läs** -och **Skriv** behörighet för registret. Varje IoT-hubb skapas som standard med en delad åtkomst princip med namnet **registryReadWrite** som ger behörighet.
