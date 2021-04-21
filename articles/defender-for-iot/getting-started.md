---
title: 'Snabbstart: Komma igång'
description: I den här snabbstarten lär du dig hur du kommer igång med att förstå det grundläggande arbetsflödet för Defender for IoT-distribution.
ms.topic: quickstart
ms.date: 04/17/2021
ms.openlocfilehash: b1e7686e1d68d5a3f239320930d69f22c78e13cb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750454"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Snabbstart: Kom igång med Defender for IoT

Den här artikeln innehåller en översikt över de steg du ska vidta för att konfigurera Azure Defender for IoT. Processen kräver att du:

- Registrera din prenumeration och dina sensorer på Azure Defender for IoT portalen.
- Installera programvaran för sensorn och den lokala hanteringskonsolen.
- Utför inledande aktivering av sensorn och hanteringskonsolen.

## <a name="permission-requirements"></a>Behörighetskrav

Vissa av installationsstegen kräver specifika användarbehörigheter.

Administrativa användarbehörigheter krävs för att aktivera sensorn och hanteringskonsolen, ladda upp SSL/TLS-certifikat och generera nya lösenord.

I följande tabell beskrivs behörigheter för användaråtkomst till Azure Defender for IoT portalverktyg:

| Behörighet | Säkerhetsläsare | Säkerhetsadministratör | Prenumerationsdeltagare | Prenumerationsägare |
|--|--|--|--|--|
| Visa information och åtkomst till programvara, aktiveringsfiler och hotinformationspaket  | ✓ | ✓ | ✓ | ✓ |
| Registrera en sensor  |  |  ✓ | ✓ | ✓ |
| Uppdateringspriser  |  |  ✓ | ✓ | ✓ |
| Återställa lösenord  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>Identifiera lösningsinfrastrukturen

**Tydliggöra behoven för nätverksinstallation**

Utforska din nätverksarkitektur, övervakad bandbredd och annan nätverksinformation. Mer information finns i [Om Azure Defender for IoT nätverkskonfiguration.](how-to-set-up-your-network.md)

**Tydliggöra vilka sensorer och hanteringskonsoler som krävs för att hantera nätverksbelastningen**

Azure Defender for IoT stöder både fysiska och virtuella distributioner. För de fysiska distributionerna kan du köpa olika certifierade installationer. Mer information finns i [Identifiera nödvändiga apparater.](how-to-identify-required-appliances.md)

Vi rekommenderar att du beräknar det ungefärliga antalet enheter som ska övervakas. Senare, när du registrerar din Azure-prenumeration på portalen, uppmanas du att ange det här numret. Tal kan läggas till i intervall på 1 000 sekunder. Antalet övervakade enheter kallas *indeade enheter*.

## <a name="register-with-azure-defender-for-iot"></a>Registrera med Azure Defender for IoT

Registreringen omfattar:

- Registrera dina Azure-prenumerationer i Defender for IoT.
- Definiera indeade enheter.
- Ladda ned en aktiveringsfil för den lokala hanteringskonsolen.

Så här registrerar du dig:

1. Gå till Azure Defender for IoT portalen.

1. Välj **Publicera prenumeration.**

1. På sidan **Prissättning** väljer du en prenumeration eller skapar en ny och lägger till antalet intiga enheter.

1. Välj fliken **Ladda ned den lokala hanteringskonsolen** och spara den nedladdade aktiveringsfilen. Den här filen innehåller de sammanställda enheter som du har definierat. Filen laddas upp till hanteringskonsolen efter den första inloggningen.

Information om hur du avboardar en prenumeration finns i [Avanteckning av en prenumeration.](how-to-manage-subscriptions.md#offboard-a-subscription)

## <a name="install-and-set-up-the-on-premises-management-console"></a>Installera och konfigurera den lokala hanteringskonsolen

När du har skaffat den lokala hanteringskonsolinstallationen:

- Ladda ned ISO-paketet från Azure Defender for IoT portalen.
- Installera programvaran.
- Aktivera och utför den första installationen av hanteringskonsolen.

Installera och konfigurera:

1. Välj **Komma igång** från Defender for IoT-portalen.
1. Välj **fliken Lokal hanteringskonsol.**
1. Välj en version och välj Ladda **ned.**
1. Installera programvaran för den lokala hanteringskonsolen. Mer information finns i [Defender for IoT-installation](how-to-install-software.md).
1. Aktivera och konfigurera hanteringskonsolen. Mer information finns i [Aktivera och konfigurera din lokala hanteringskonsol.](how-to-activate-and-set-up-your-on-premises-management-console.md)

## <a name="onboard-a-sensor"></a>Registrera en sensor ##

Registrera en sensor genom att registrera den Azure Defender for IoT och ladda ned en sensoraktiveringsfil:

1. Definiera ett sensornamn och associera det med en prenumeration.
1. Välj ett sensoranslutningsläge:

   - **Molnanslutna sensorer:** Information som sensorerna identifierar visas i sensorkonsolen. Dessutom levereras aviseringsinformation via en IoT-hubb och kan delas med andra Azure-tjänster, till exempel Azure Sentinel.  Du kan också välja att automatiskt skicka hotinformationspaket från Azure Defender for IoT-portalen till dina sensorer. Mer information finns i [Hotinformationsforskning och paket.](how-to-work-with-threat-intelligence-packages.md)

   - **Lokalt hanterade sensorer:** Information som sensorer identifierar visas i sensorkonsolen. Om du arbetar i ett trådlöst nätverk och vill ha en enhetlig vy över all information som identifieras av flera lokalt hanterade sensorer kan du arbeta med den lokala hanteringskonsolen.

1. Ladda ned en sensoraktiveringsfil.

Mer information om registrering finns i [Publicera och hantera sensorer i Defender for IoT-portalen.](how-to-manage-sensors-on-the-cloud.md)

## <a name="install-and-set-up-the-sensor"></a>Installera och konfigurera sensorn

Ladda ned ISO-paketet från Azure Defender for IoT-portalen, installera programvaran och konfigurera sensorn.

1. Välj **Komma igång** från Defender for IoT-portalen.

1. Välj **Konfigurera sensorn**.

1. Välj en version och välj **Ladda ned.**

1. Installera sensorprogramvaran. Mer information finns i [Defender for IoT-installation](how-to-install-software.md).

1. Aktivera och konfigurera din sensor. Mer information finns i [Logga in och aktivera en sensor.](how-to-activate-and-set-up-your-sensor.md)

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Ansluta sensorer till en lokal hanteringskonsol

Anslut sensorer till hanteringskonsolen för att säkerställa att:

- Sensorer skickar aviserings- och enhetsinventeringsinformation till den lokala hanteringskonsolen.

- Den lokala hanteringskonsolen kan utföra sensorsäkerhetskopior, hantera aviseringar som sensorer identifierar, undersöka sensoravkopplingar och utföra annan aktivitet på anslutna sensorer.

Vi rekommenderar att du grupperar flera sensorer som övervakar samma nätverk i en zon. Om du gör detta sammanskar du information som samlas in av flera sensorer.

Mer information finns i [Ansluta sensorer till den lokala hanteringskonsolen.](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>Fyll Azure Sentinel med aviseringsinformation (valfritt)

Skicka aviseringsinformation till Azure Sentinel genom att konfigurera Azure Sentinel. Se [Ansluta dina data från Defender för IoT till Azure Sentinel](how-to-configure-with-sentinel.md).

## <a name="next-steps"></a>Nästa steg ##

[Välkommen till Azure Defender for IoT](overview.md)

[Azure Defender for IoT arkitektur](architecture.md)
