---
title: Övervaka hälsotillståndet för ett Azure IoT Central-| Microsoft Docs
description: Som operatör eller administratör övervakar du den övergripande hälsan för de enheter som är anslutna till IoT Central program.
author: dominicbetts
ms.author: dobett
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a9abd00035ccd779fcbe5dcf29b90f47758ff403
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588657"
---
# <a name="monitor-the-overall-health-of-an-iot-central-application"></a>Övervaka den övergripande hälsan för ett IoT Central program

> [!NOTE]
> Mått är endast tillgängliga för version 3 IoT Central program. Information om hur du kontrollerar programversionen finns i [Om ditt program](./howto-get-app-info.md).

*Den här artikeln gäller för operatörer och administratörer.*

I den här artikeln får du lära dig hur du använder den uppsättning mått som tillhandahålls av IoT Central för att utvärdera hälsotillståndet för enheter som är anslutna till ditt IoT Central-program och hälsotillståndet för dina dataexporter som körs.

Mått är aktiverade som standard för ditt IoT Central program och du kommer åt dem från [Azure Portal](https://portal.azure.com/). [Dataplattformen Azure Monitor exponerar dessa mått](../../azure-monitor/essentials/data-platform-metrics.md) och erbjuder flera olika sätt att interagera med dem. Du kan till exempel använda diagram i Azure Portal, en REST API eller frågor i PowerShell eller Azure CLI.

### <a name="trial-applications"></a>Utvärderingsprogram

Program som använder den kostnadsfria utvärderingsplanen har ingen associerad Azure-prenumeration och stöder därför inte Azure Monitor mått. Du kan [konvertera ett program till en standardprisplan](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) och få åtkomst till dessa mått.

## <a name="view-metrics-in-the-azure-portal"></a>Visa mått i Azure Portal

Följande steg förutsätter att du har [ett IoT Central-program](./quick-deploy-iot-central.md) med vissa [anslutna enheter](./tutorial-connect-device.md) eller en [dataexport som körs.](howto-export-data.md)

Så här visar IoT Central mått i portalen:

1. Gå till IoT Central programresurs i portalen. Som standard finns IoT Central i en resursgrupp som heter **IOTC**.
1. Om du vill skapa ett diagram från programmets mått väljer **du Mått** i **avsnittet** Övervakning.

![Azure-mått](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure Portal behörigheter

Åtkomsten till mått i Azure Portal hanteras av [rollbaserad åtkomstkontroll i Azure.](../../role-based-access-control/overview.md) Använd Azure Portal att lägga till användare i IoT Central program/resursgrupp/prenumeration för att ge dem åtkomst. Du måste lägga till en användare i portalen även om de redan har lagts till i IoT Central program. Använd [inbyggda Azure-roller](../../role-based-access-control/built-in-roles.md) för mer begränsad åtkomstkontroll.

## <a name="iot-central-metrics"></a>IoT Central mått

En lista över mått som för närvarande är tillgängliga för IoT Central finns i [Mått som stöds med Azure Monitor](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps).

### <a name="metrics-and-invoices"></a>Mått och fakturor

Mått kan skilja sig från de siffror som visas på Azure IoT Central fakturan. Den här situationen inträffar av ett antal orsaker, till exempel:

- IoT Central [standardprisplaner](https://azure.microsoft.com/pricing/details/iot-central/) omfattar två enheter och varierande meddelandekvoter utan kostnad. Även om de kostnadsfria objekten undantas från faktureringen räknas de fortfarande i måtten.

- IoT Central automatiskt ett testenhets-ID för varje enhetsmall i programmet. Det här enhets-ID:t visas **på sidan Hantera testenhet** för en enhetsmall. Lösningsbyggare kan välja att verifiera [sina enhetsmallar innan de](./overview-iot-central.md#connect-devices) publicerar dem genom att generera kod som använder dessa testenhets-ID:er. Även om dessa enheter undantas från faktureringen räknas de fortfarande i måtten.

- Mått kan visa en delmängd av kommunikation från enhet till moln, men all kommunikation mellan enheten och molnet räknas som [ett meddelande för fakturering.](https://azure.microsoft.com/pricing/details/iot-central/)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder programmallar föreslår vi att du går vidare med att lära dig hur du [hanterar IoT Central från Azure Portal](howto-manage-iot-central-from-portal.md).