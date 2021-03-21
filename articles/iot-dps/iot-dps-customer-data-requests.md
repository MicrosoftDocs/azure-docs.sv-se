---
title: Funktioner för begäran om kund data för Azure DPS-enheter
description: För enheter som hanteras i Azure Device Provisioning-tjänsten (DPS) som är personliga visar den här artikeln administratörer hur man exporterar eller tar bort personliga data.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 75a2762a0937efbceaa168f8a2d6409e2e3a7ae4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967235"
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av funktioner för begäranden om kunddata

Azure IoT Hub Device Provisioning Service är en REST API-baserad moln tjänst som är riktad till företags kunder och som möjliggör sömlös, automatisk etablering av enheter till Azure IoT Hub med säkerhet som börjar på enheten och slutar med molnet.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Enskilda enheter tilldelas ett registrerings-ID och ett enhets-ID av en klient administratör. Data från och om dessa enheter baseras på dessa ID: n. Microsoft har ingen information och har ingen åtkomst till data som tillåter korrelation av dessa enheter till en individ.

Många av enheterna som hanteras i enhets etablerings tjänsten är inte personliga enheter, till exempel en Office-termostat eller en fabriks robot. Kunder kan dock överväga att vissa enheter ska vara personligt identifierbara och att de kan behålla sina egna till gångar eller inventerings spårnings metoder som knyter enheter till individer. Enhets etablerings tjänsten hanterar och lagrar alla data som är associerade med enheter som om de vore personliga data.

Klient organisations administratörer kan använda antingen Azure Portal eller tjänstens REST-API: er för att uppfylla informations förfrågningar genom att exportera eller ta bort data som är associerade med ett enhets-ID eller registrerings-ID.

> [!NOTE]
> Enheter som har etablerats i Azure IoT Hub via enhets etablerings tjänsten har ytterligare data som lagras i Azure IoT Hub-tjänsten. Se [referens dokumentationen för Azure IoT Hub](../iot-hub/iot-hub-customer-data-requests.md) för att slutföra en fullständig begäran för en specifik enhet.

## <a name="deleting-customer-data"></a>Tar bort kund information

Enhets etablerings tjänsten lagrar registreringar och registrerings poster. Registreringar innehåller information om enheter som har tillåtelse att tillhandahållas och registrerings poster visar vilka enheter som redan har slutfört etablerings processen.

Klient organisations administratörer kan ta bort registreringar från Azure Portal, och detta tar även bort alla associerade registrerings poster.

Mer information finns i [Hantera enhets registreringar](how-to-manage-enrollments.md).

Det går också att utföra borttagnings åtgärder för registreringar och registrerings poster med hjälp av REST API: er:

* Om du vill ta bort registrerings information för en enskild enhet kan du använda [enhets registrering – ta bort](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Om du vill ta bort registrerings information för en grupp av enheter kan du använda [enhets registrerings grupp – ta bort](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Om du vill ta bort information om enheter som har etablerats kan du använda [registrerings tillstånd – ta bort registrerings status](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Exportera kund information

Enhets etablerings tjänsten lagrar registreringar och registrerings poster. Registreringar innehåller information om enheter som har tillåtelse att tillhandahållas och registrerings poster visar vilka enheter som redan har slutfört etablerings processen.

Klient organisations administratörer kan visa registreringar och registrerings poster via Azure Portal och exportera dem med hjälp av kopiera och klistra in.

Mer information om hur du hanterar registreringar finns i [Hantera enhets registreringar](how-to-manage-enrollments.md).

Det går också att utföra export åtgärder för registreringar och registrerings poster med hjälp av REST API: er:

* Om du vill exportera registrerings information för en enskild enhet kan du använda [enhets registrering – Hämta](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Om du vill exportera registrerings information för en grupp av enheter kan du använda [enhets registrerings grupp – Hämta](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Om du vill exportera information om enheter som redan har etablerats kan du använda [registrerings tillstånd – Hämta registrerings status](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> När du använder Microsofts företags tjänster genererar Microsoft viss information, som kallas system genererade loggar. Vissa systemgenererade loggar för enhets etablerings tjänsten är inte tillgängliga eller kan exporteras av klient administratörer. Dessa loggar utgör faktiska åtgärder som utförs i tjänsten och diagnostikdata som är relaterade till enskilda enheter.

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

Fullständig dokumentation för Device Provisioning service-API: er finns på [https://docs.microsoft.com/rest/api/iot-dps](/rest/api/iot-dps) .

Azure IoT Hub [funktioner för kunddata-begäran](../iot-hub/iot-hub-customer-data-requests.md).