---
title: Kontinuerlig övervaknings arkitektur för patient i Azure IoT Central | Microsoft Docs
description: Självstudie – lär dig mer om en kontinuerlig arkitektur för patient övervaknings lösning.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: dad6327acd24fe7642bf1033b1527ef3e40b772e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590111"
---
# <a name="continuous-patient-monitoring-architecture"></a>Arkitektur för kontinuerlig övervakning av patienter

I den här artikeln beskrivs arkitekturen för en lösning som skapats från program mal len **kontinuerlig övervakning av patienter** :

Kontinuerliga övervaknings lösningar för patienter kan skapas med hjälp av den app-mall som anges och med hjälp av den arkitektur som beskrivs nedan som vägledning.

:::image type="content" source="media/cpm-architecture.png" alt-text="Arkitektur för kontinuerlig övervakning av patienter":::

## <a name="details"></a>Information

I det här avsnittet beskrivs varje del av arkitektur diagrammet i detalj:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Medicintekniska enheter för Bluetooth låg energi (Bell)

Många medicinska wearables som används i vård-och sjukvårds lösningar är Bell-enheter. Dessa enheter kan inte kommunicera direkt till molnet och behöver använda en gateway för att utbyta data med din moln lösning. Den här arkitekturen använder ett mobil telefon program som gateway.

### <a name="mobile-phone-gateway"></a>Mobil telefon Gateway

Mobil telefon programmets primära funktion är att samla in tabell data från medicinska enheter och kommunicera med IoT Central. Appen guidar även patienter genom enhets konfigurationen och gör det möjligt för dem att visa sina personliga hälso data. Andra lösningar kan använda en surfplatta gateway eller en statisk gateway i ett sjukhus rum. Ett mobilt exempel mobil program med öppen källkod är tillgängligt för Android och iOS och används som utgångs punkt för program utvecklingen. Mer information finns i mobilappen för [IoT Central kontinuerlig övervakning av patienter](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Exportera till Azure API för FHIR&reg;

Azure IoT Central är HIPAA-kompatibelt och HITRUST- &reg; certifierat. Du kan också skicka patient hälso data till andra tjänster med hjälp av [Azure API för FHIR](../../healthcare-apis/overview.md). Azure API för FHIR är ett standard-baserat API för kliniska hälso data. Med [Azure IoT-anslutningsprogrammet för FHIR](../../healthcare-apis/iot-fhir-portal-quickstart.md) kan du använda Azure API för FHIR som ett kontinuerligt data export mål från IoT Central.

### <a name="machine-learning"></a>Maskininlärning

Använd Machine Learning-modeller med dina FHIR-data för att generera insikter och support av ditt vård team. Mer information finns i [Azure Machine Learning-dokumentationen](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Instrument panel för Provider

Använd Azure-API: et för FHIR-data för att bygga en instrument panel för patient insikter eller integrera den direkt i en elektronisk medicinsk post som används av vård team. Vård team kan använda instrument panelen för att hjälpa patienter och identifiera tidiga varnings tecken på försämringen. Mer information finns i själv studie kursen [bygga en Power BI leverantörs instrument panel](howto-health-data-triage.md) .

## <a name="next-steps"></a>Nästa steg

Det föreslagna nästa steg är att [lära dig hur du distribuerar en kontinuerlig Programmall för patient övervakning](tutorial-continuous-patient-monitoring.md).