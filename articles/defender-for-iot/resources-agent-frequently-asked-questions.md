---
title: Vanliga frågor och svar om Azure Defender för IoT-agent
description: Hitta svar på vanliga frågor om Azure Defender för IoT-agenten.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 4bd7d3c9b76dfb37e53cc51e5e16b660545cb7f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778669"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Defender för IoT-agent

Den här artikeln innehåller en lista över vanliga frågor och svar om Defender för IoT-agenten.

## <a name="do-i-have-to-install-an-embedded-security-agent"></a>Måste jag installera en inbäddad säkerhets agent?

Agent installationen på dina IoT-enheter är inte obligatorisk för att aktivera Defender för IoT. Du kan välja mellan följande tre alternativ för att få olika nivåer av säkerhetsövervakning och hanterings funktioner enligt ditt val:

- Passiv, icke-invasiv (utan agent) distribution som använder NTA-sensorer (Network Traffic Analysis) för att övervaka och ge djup insyn i IoT/stor-risk med noll prestanda påverkan på nätverket och enheterna
- Installera säkerhets agenten Defender för IoT Embedded med eller utan ändringar. Det här alternativet ger den högsta nivån av förbättrade säkerhets insikter om enhetens beteende och åtkomst.

- Skapa en egen agent och implementera schemat för säkerhets meddelanden i Defender for IoT. Med det här alternativet aktive ras användning av Defender för IoT Analysis tools ovanpå enhetens säkerhets agent.

- Ingen installation av säkerhets agent på IoT-enheter. Med det här alternativet kan IoT Hub kommunikation övervakas med minskad säkerhetsövervakning och hanterings funktioner.

## <a name="what-does-the-defender-for-iot-agent-do"></a>Vad gör Defender för IoT-agenten?

Defender för IoT-agenten ger hot täckning på enhets nivå för enhets konfiguration, beteende och åtkomst (genom att söka i konfigurationen), bearbeta & anslutning. Defender for IoT-Säkerhetsagenten söker inte efter affärsrelaterade data eller aktiviteter.

Defender for IoT-Säkerhetsagenten är öppen källkod och tillgänglig på GitHub i 32-bitars och 64-bitars Windows-och Linux-versioner: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Vilka är beroendena och kraven för agenten?

Defender for IoT stöder en mängd olika plattformar. Se [vilka enhets plattformar som stöds](how-to-deploy-agent.md) för att verifiera stöd för dina enheter.

## <a name="which-data-is-collected-by-the-agent"></a>Vilka data samlas in av agenten?

Anslutning, åtkomst, brand Väggs konfiguration, process lista & operativ systemets bas linje samlas in av agenten.

## <a name="how-much-data-will-the-agent-generate"></a>Hur mycket data kommer agenten att generera?

Genereringen av agent data drivs av enhets-, program-, Anslutnings typ och konfigurationen av kund agenten. På grund av den höga variationen mellan enheter och IoT-lösningar rekommenderar vi att du först distribuerar agenten i en labb-eller test inställning för att se, lära dig och ange den speciella konfiguration som passar dina behov, samtidigt som du mäter mängden genererade data. När du har startat tjänsten ger Defender for IoT-agenten operativa rekommendationer för att optimera agent-genomflödet för att hjälpa dig med konfigurations-och anpassnings processen.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Använder agent meddelanden kvoten från IoT Hub?

Ja. Agenter som överför data räknas i IoT Hub kvoten.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Vad är nästa steg? Jag har installerat en agent och ser inga aktiviteter eller loggar...

1. Kontrol lera vilken [agent typ som passar din enhets operativ system plattform](how-to-deploy-agent.md)

1. Bekräfta att [agenten körs på enheten](how-to-agent-configuration.md).

1. Kontrol lera att [tjänsten har Aktiver ATS](quickstart-onboard-iot-hub.md) för **säkerhet** i din IoT Hub.

1. Kontrol lera att enheten är [konfigurerad i IoT Hub med Defender för IoT-modulen](quickstart-create-security-twin.md).

Om aktiviteterna eller loggarna fortfarande inte är tillgängliga kontaktar du din Defender for IoT-partner för ytterligare hjälp.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Vad händer när Internet anslutningen slutar fungera?

Sensorer och agenter fortsätter att köra och lagra data så länge enheten körs. Data lagras i cacheminnet för säkerhets meddelanden enligt storleks konfigurationen. När enheten återfår anslutningen återupptas säkerhets meddelanden som skickas.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Kan agenten påverka enhetens prestanda eller annan installerad program vara?

Agenten använder dator resurser som alla andra program/processer och ska inte störa normal enhets aktivitet. Resursförbrukning på den enhet som agenten körs på är kopplad till konfigurationen och konfigurationen. Vi rekommenderar att du testar din agent konfiguration i en innesluten miljö, tillsammans med samverkan med dina andra IoT-program och-funktioner, innan du försöker distribuera i en produktions miljö.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Jag gör vissa underhåll på enheten. Kan jag stänga av agenten?

Agenten kan inte stängas av.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Finns det något sätt att testa om agenten fungerar korrekt?

Om agenten slutar kommunicera eller Miss lyckas med att skicka säkerhets meddelanden genereras en varning om att en **enhet är tyst** .

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kommer igång med Defender för IoT finns i följande artiklar:

- Läs [översikten över](overview.md) Defender för IoT
- Kontrol lera [system kraven](quickstart-system-prerequisites.md)
- Lär dig mer om att [komma igång med Defender för IoT](getting-started.md)
- Förstå [Defender för IoT-säkerhetsaviseringar](concept-security-alerts.md)
