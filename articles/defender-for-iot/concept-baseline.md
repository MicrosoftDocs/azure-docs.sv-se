---
title: Bas linje och anpassade kontroller
description: Lär dig mer om konceptet för Azure Defender för IoT-bas linje.
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1b8b9d62918e40262da6b3df48d0fece842e050f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779366"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>Azure Defender för IoT-bas linje och anpassade kontroller

Den här artikeln beskriver Defender för IoT-bas linje och sammanfattar alla tillhör ande egenskaper för anpassade bas linje kontroller.

## <a name="baseline"></a>Baslinje

En bas linje fastställer standard beteendet för varje enhet och gör det lättare att fastställa ovanliga beteenden eller avvikelser från förväntade normer.

## <a name="baseline-custom-checks"></a>Bas linje anpassade kontroller

Med anpassade bas kontroller upprättas en anpassad lista över kontroller för varje enhets bas linje med hjälp av **modulens identitet** på enhets nivå.

## <a name="setting-baseline-properties"></a>Ange egenskaper för bas linje

1. Leta upp och välj den enhet som du vill ändra i IoT Hub.

1. Klicka på enheten och klicka sedan på **azureiotsecurity** -modulen.

1. Klicka på **modul identitet, dubbel**.

1. Ladda upp **bas linje filen för anpassade kontroller** till enheten.

1. Lägg till egenskaper för bas linje i Defender-IoT-Micro-agenten och klicka på **Spara**.

### <a name="baseline-custom-check-file-example"></a>Exempel på anpassad kontroll fil för bas linje

Så här konfigurerar du anpassade bas kontroller:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Egenskaper för anpassad kontroll bas linje

| Name| Status | Giltiga värden| Standardvärden| Beskrivning |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|Krävs: sant |Giltiga värden: **Boolean** |Standardvärde: **falskt** |Max tidsintervall innan meddelanden med hög prioritet skickas.|
|baselineCustomChecksFilePath |Krävs: sant|Giltiga värden: **sträng**, **Null** |Standardvärde: **Null** |Fullständig sökväg till bas linje XML-konfigurationen|
|baselineCustomChecksFileHash |Krävs: sant|Giltiga värden: **sträng**, **Null** |Standardvärde: **Null** |`sha256sum` i XML-konfigurationsfilen. Använd [sha256sum-referensen](https://linux.die.net/man/1/sha256sum) om du vill ha mer information. |

Om du vill granska fler bas linje exempel, se [anpassade bas linje exempel-1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) och [anpassad bas linje exempel-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Nästa steg

- Få åtkomst till dina [rå säkerhets data](how-to-security-data-access.md)
- [Undersöka en enhet](how-to-investigate-device.md)
- Förstå och utforska [säkerhets rekommendationer](concept-recommendations.md)
- Förstå och utforska [säkerhets aviseringar](concept-security-alerts.md)
