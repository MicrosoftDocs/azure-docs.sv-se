---
title: Övervaka hälso tillståndet för ett Azure IoT Central-program | Microsoft Docs
description: Som operatör eller administratör övervakar du övergripande hälso tillstånd för de enheter som är anslutna till ditt IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5e462397196d2fd0fd716801d9106929a8cb6a6b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061459"
---
# <a name="monitor-the-overall-health-of-an-iot-central-application"></a>Övervaka den övergripande hälsan för ett IoT Central program

> [!NOTE]
> Mått är bara tillgängliga för version 3 IoT Central-program. Information om hur du kontrollerar program versionen finns i [om ditt program](./howto-get-app-info.md).

*Den här artikeln gäller för operatörer och administratörer.*

I den här artikeln får du lära dig hur du använder den uppsättning mått som tillhandahålls av IoT Central för att utvärdera hälso tillståndet för enheter som är anslutna till ditt IoT Central program och hälsan för dina data exporter som körs.

Mått är aktiverade som standard för ditt IoT Central program och du kommer åt dem från [Azure Portal](https://portal.azure.com/). [Azure Monitor data plattformen visar dessa mått](../../azure-monitor/essentials/data-platform-metrics.md) och ger dig flera olika sätt att interagera med dem. Du kan till exempel använda diagram i Azure Portal, en REST API eller frågor i PowerShell eller Azure CLI.

### <a name="trial-applications"></a>Utvärderings program

Program som använder den kostnads fria utvärderings planen har ingen tillhör ande Azure-prenumeration och stöder inte Azure Monitor mått. Du kan [konvertera ett program till en standard pris plan](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) och få till gång till dessa mått.

## <a name="view-metrics-in-the-azure-portal"></a>Visa mått i Azure Portal

Följande steg förutsätter att du har ett [IoT Central-program](./quick-deploy-iot-central.md) med vissa [anslutna enheter](./tutorial-connect-device.md) eller en pågående [data export](howto-export-data.md).

Visa IoT Central mått i portalen:

1. Navigera till din IoT Central program resurs i portalen. IoT Central-resurser finns som standard i en resurs grupp med namnet **IOTC**.
1. Om du vill skapa ett diagram från ditt programs mått väljer du **mått** i avsnittet **övervakning** .

![Azure-mått](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure Portal behörigheter

Åtkomst till mått i Azure Portal hanteras av rollbaserad [åtkomst kontroll i Azure](../../role-based-access-control/overview.md). Använd Azure Portal för att lägga till användare i IoT Central program/resurs grupp/prenumeration för att ge åtkomst till dem. Du måste lägga till en användare i portalen även om de redan har lagts till i IoT Central programmet. Använd [inbyggda Azure-roller](../../role-based-access-control/built-in-roles.md) för att få bättre kornig åtkomst kontroll.

## <a name="iot-central-metrics"></a>IoT Central mått

En lista över de mått som för närvarande är tillgängliga för IoT Central finns i mått som [stöds med Azure Monitor](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps).

### <a name="metrics-and-invoices"></a>Mått och fakturor

Måtten kan skilja sig från de tal som visas på din Azure IoT Central-faktura. Den här situationen uppstår på grund av ett antal orsaker, till exempel:

- IoT Central [standard pris planeraren](https://azure.microsoft.com/pricing/details/iot-central/) omfattar två enheter och varierande meddelande kvoter kostnads fritt. Även om de kostnads fria objekten exkluderas från faktureringen räknas de fortfarande i måtten.

- IoT Central automatiskt genererar ett test enhets-ID för varje enhets mall i programmet. Detta enhets-ID visas på sidan **Hantera test enhet** för en enhets mall. Lösnings verktyg kan välja att [Verifiera sina enhets mallar](./overview-iot-central.md#create-device-templates) innan du publicerar dem genom att generera kod som använder dessa test enhets-ID: n. Även om enheterna exkluderas från faktureringen räknas de fortfarande i måtten.

- Även om mått kan visa en delmängd av kommunikation mellan enheter och molnet räknas all kommunikation mellan enheten och molnet [som ett meddelande för fakturering](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder programmallar är det föreslagna nästa steg att lära dig hur du [hanterar IoT Central från Azure Portal](howto-manage-iot-central-from-portal.md).