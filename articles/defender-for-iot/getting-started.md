---
title: Komma igång
description: Kom igång med att förstå det grundläggande arbets flödet för Defender för IoT-distribution.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/18/2021
ms.author: shhazam
ms.openlocfilehash: c224dc3f919471e769717f308aff9ae58630d421
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712767"
---
# <a name="get-started-with-defender-for-iot"></a>Kom igång med Defender för IoT

Den här artikeln innehåller en översikt över de steg du ska vidta för att konfigurera Azure Defender för IoT. Processen kräver att du:

- Registrera din prenumeration och sensorer på Azure Defender för IoT-portalen.
- Installera sensor-och program varan för den lokala hanterings konsolen.
- Utför inledande aktivering av sensor-och hanterings konsolen.

## <a name="permission-requirements"></a>Behörighets krav

Några av installations stegen kräver vissa användar behörigheter.

Administrativa användar behörigheter krävs för att aktivera sensor-och hanterings konsolen, ladda upp SSL/TLS-certifikat och generera nya lösen ord.

I följande tabell beskrivs användar behörighet för Azure Defender för IoT Portal verktyg:

| Behörighet | Säkerhetsläsare | Säkerhetsadministratör | Prenumerations deltagare | Prenumerations ägare |
|--|--|--|--|--|
| Visa alla Defender för IoT-skärmar och data | ✓ | ✓ | ✓ | ✓ |
| Publicera en sensor  |  |  ✓ | ✓ | ✓ |
| Uppdatera prissättning  |  |  ✓ | ✓ | ✓ |
| Återställ lösen ord  | ✓  |  ✓ | ✓ | ✓ |

## <a name="1-identify-the-solution-infrastructure"></a>1. identifiera lösnings infrastrukturen

**Klargör dina nätverks konfigurations behov**

Utforska din nätverks arkitektur, övervakad bandbredd och annan nätverks information. Mer information finns i [om konfiguration av Azure Defender för IoT-nätverk](how-to-set-up-your-network.md).

**Klargör vilka sensorer och hanterings konsol enheter som krävs för att hantera nätverks belastningen**

Azure Defender för IoT stöder både fysiska och virtuella distributioner. För fysiska distributioner kan du köpa olika certifierade apparater. Mer information finns i [identifiera nödvändiga enheter](how-to-identify-required-appliances.md).

Vi rekommenderar att du beräknar det ungefärliga antalet enheter som ska övervakas. När du senare registrerar din Azure-prenumeration på portalen uppmanas du att ange det här numret. Siffror kan läggas till i intervall om 1 000 sekunder. Antalet övervakade enheter kallas *allokerade enheter*.

## <a name="2-register-with-azure-defender-for-iot"></a>2. registrera dig med Azure Defender för IoT

Registreringen omfattar:

- Publicera dina Azure-prenumerationer till Defender för IoT.
- Definiera allokerade enheter.
- Laddar ned en aktiverings fil för den lokala hanterings konsolen.

Så här registrerar du dig:

1. Gå till Azure Defender för IoT-portalen.
1. Välj **onboard-prenumeration**.
1. På sidan **prissättning** väljer du en prenumeration eller skapar en ny och lägger till antalet allokerade enheter.
1. Välj fliken **Ladda ned den lokala hanterings konsolen** och spara den nedladdade aktiverings filen. Den här filen innehåller de aggregerade allokerade enheter som du har definierat. Filen laddas upp till hanterings konsolen efter den första inloggningen.

Information om hur du avpublicera en prenumeration finns i [avpublicera a-prenumeration](how-to-manage-sensors-on-the-cloud.md#offboard-a-subscription).

## <a name="3-install-and-set-up-the-on-premises-management-console"></a>3. Installera och konfigurera den lokala hanterings konsolen

När du har skaffat din lokala hanterings konsol enhet:

- Hämta ISO-paketet från Azure Defender för IoT-portalen.
- Installera program varan.
- Aktivera och utför installations programmet för inledande hanterings konsol.

Installera och konfigurera:

1. Välj **komma igång** från Defender för IoT-portalen.
1. Välj fliken **lokal hanterings konsol** .
1. Välj en version och välj **Hämta**.
1. Installera program varan för den lokala hanterings konsolen. Mer information finns i [Defender för IoT-installation](how-to-install-software.md).
1. Aktivera och konfigurera hanterings konsolen. Mer information finns i [Aktivera och konfigurera den lokala hanterings konsolen](how-to-activate-and-set-up-your-on-premises-management-console.md).

## <a name="4-onboard-a-sensor"></a>4. publicera en sensor

Publicera en sensor genom att registrera den med Azure Defender för IoT och hämta en sensor aktiverings fil:

1. Definiera ett sensor namn och associera det med en prenumeration.
1. Välj ett sensor hanterings läge:

   - **Moln anslutna sensorer**: information som sensorer identifierar visas i sensor konsolen. Dessutom levereras aviserings information via en IoT-hubb och kan delas med andra Azure-tjänster, till exempel Azure Sentinel.

   - **Lokalt hanterade sensorer**: information som sensorer identifierar visas i sensor konsolen. Om du arbetar i ett Air-gapped nätverk och vill ha en enhetlig vy över all information som identifieras av flera lokalt hanterade sensorer, kan du arbeta med den lokala hanterings konsolen. 

1. Ladda ned en sensor aktiverings fil.

Mer information finns i [publicera och hantera sensorer i Defender för IoT-portalen](how-to-manage-sensors-on-the-cloud.md).

## <a name="5-install-and-set-up-the-sensor"></a>5. Installera och konfigurera sensorn

Hämta ISO-paketet från Azure Defender för IoT-portalen, installera program varan och konfigurera sensorn.

1. Välj **komma igång** från Defender för IoT-portalen.
1. Välj **Konfigurera sensor**.
1. Välj en version och välj **Hämta**.
1. Installera sensorprogram varan. Mer information finns i [Defender för IoT-installation](how-to-install-software.md).
1. Aktivera och konfigurera sensorn. Mer information finns i [Logga in och aktivera en sensor](how-to-activate-and-set-up-your-sensor.md).

## <a name="6-connect-sensors-to-an-on-premises-management-console"></a>6. Anslut sensorer till en lokal hanterings konsol

Anslut sensorer till hanterings konsolen för att säkerställa att:

- Sensorer skickar information om avisering och enhets inventering till den lokala hanterings konsolen.

- Den lokala hanterings konsolen kan utföra sensor säkerhets kopieringar, hantera aviseringar som sensorer identifierar, undersöka sensorer från kopplingar och utföra annan aktivitet på anslutna sensorer.

Vi rekommenderar att du grupperar flera sensorer som övervakar samma nätverk i en zon. Om du gör detta samlas sammanslagnings information in av flera sensorer.

Mer information finns i [ansluta sensorer till den lokala hanterings konsolen](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="7-populate-azure-sentinel-with-alert-information-optional"></a>7. Fyll i Azure Sentinel med varnings information (valfritt)

Skicka aviserings information till Azure Sentinel genom att konfigurera Azure Sentinel. Se [Anslut dina data från Defender för IoT till Azure Sentinel](how-to-configure-with-sentinel.md).

## <a name="see-also"></a>Se även

- [Välkommen till Azure Defender för IoT](overview.md)

- [Azure Defender för IoT-arkitektur](architecture.md)
