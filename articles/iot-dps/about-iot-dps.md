---
title: Översikt över Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Beskriver enhets etablering i Azure med enhets etablerings tjänsten (DPS) och IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e58028495da409df00db8e25adf68945a008bc9e
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228307"
---
# <a name="what-is-azure-iot-hub-device-provisioning-service"></a>Vad är IoT Hub Device Provisioning Service?
Microsoft Azure har en stor uppsättning integrerade offentliga molntjänster för alla dina IoT-lösningsbehov. IoT Hub Device Provisioning Service (DPS) är en hjälp tjänst för IoT Hub som möjliggör Zero-Touch och just-in-Time-etablering till rätt IoT Hub utan mänsklig inblandning. DPS möjliggör etablering av miljon tals enheter på ett säkert och skalbart sätt.

## <a name="when-to-use-device-provisioning-service"></a>När Device Provisioning Service ska användas
Det finns många etablerings scenarier där DPS är ett utmärkt alternativ för att hämta enheter som är anslutna och konfigurerade att IoT Hub, till exempel:

* Zero touch-etablering till en enda IoT-lösning utan att hårdkoda IoT Hub-anslutningsinformationen som fabriksinställning (första konfigurationen)
* Belastnings Utjämnings enheter över flera hubbar
* Ansluta enheter till ägarens IoT-lösning baserat på försäljnings transaktions data (flera innehavare)
* Anslutning av enheter till en viss IoT-lösning beroende på användningsfall (lösningsisolering)
* Anslutning av en enhet till den IoT-hubb som har lägst fördröjning (geografisk horisontell partitionering)
* Ometablering baserat på en ändring i enheten
* Rotera de nycklar som används av enheten för att ansluta till IoT Hub (när X.509-certifikat inte används för att ansluta)

## <a name="behind-the-scenes"></a>I bakgrunden
Alla scenarier som anges i föregående avsnitt kan utföras med hjälp av DPS för noll-touch-etablering med samma flöde. Många av de manuella stegen som traditionellt ingår i etableringen automatiseras med DPS för att minska tiden för att distribuera IoT-enheter och minska risken för manuella fel. I följande avsnitt beskrivs vad som händer i bakgrunden när en enhet etableras. Det första steget är manuellt; alla följande steg utförs automatiskt.

![Grundläggande etableringsflöde](./media/about-iot-dps/dps-provisioning-flow.png)

1. Enhetstillverkaren lägger till enhetsregistreringsinformation i registreringslistan i Azure-portalen.
2. Enheten kontaktar DPS-slutpunkten som ställts in på fabriken. Enheten skickar identifierings informationen till DPS för att bevisa sin identitet.
3. DPS verifierar enhetens identitet genom att verifiera registrerings-ID: t och nyckeln mot registrerings List posten med antingen en nonce-utmaning ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) eller standard x. 509-verifiering (x. 509).
4. DPS registrerar enheten med en IoT-hubb och fyller enhetens [önskade dubbla tillstånd](../iot-hub/iot-hub-devguide-device-twins.md).
5. IoT-hubben returnerar information om enhets-ID för DPS.
6. DPS returnerar anslutnings informationen för IoT Hub till enheten. Enheten kan nu börja skicka data direkt till IoT-hubben.
7. Enheten ansluter till IoT-hubben.
8. Enheten får önskad status från sin tvillingenhet i IoT-hubben.

## <a name="provisioning-process"></a>Etableringsprocessen
Det finns två olika steg i distributions processen för en enhet där DPS tar en del som kan göras oberoende av varandra:

* **Tillverkningssteget** där enheten skapas och förberedas på fabriken samt
* **Molnkonfigurationssteget** där Device Provisioning Service konfigureras för automatisk etablering.

Båda dessa steg passar smidigt in i befintliga tillverknings- och etableringsprocesser. DPS fören klar vissa distributions processer som omfattar manuellt arbete för att få anslutnings information till enheten.

### <a name="manufacturing-step"></a>Tillverkningssteg
Det här steget handlar om vad som händer vid tillverkningen. De roller som ingår i det här steget innefattar kiseldesigner, kiseltillverkare, integrerare och sluttillverkare av enheten. Det här steget handlar om tillverkningen av själva maskinvaran.

DPS introducerar inte ett nytt steg i tillverknings processen. i stället är den i det befintliga steget som installerar den ursprungliga program varan och (helst) HSM på enheten. I stället för att ett enhets-ID skapas i det här steget programmeras enheten med information om etableringstjänsten, vilket gör att den kan anropa etableringstjänsten för att få sin tilldelning för anslutningsinformation/IoT-lösning när den aktiveras.

I det här steget ger också tillverkaren enhetsdistributören/-operatören identifierande nyckelinformation. Att tillhandahålla den informationen kan vara så enkelt som att bekräfta att alla enheter har ett X.509-certifikat som genereras från ett signeringscertifikat som tillhandahålls av enhetsdistributören/-operatören eller så komplicerat som att extrahera den offentliga delen av en TPM-bekräftelsenyckel från varje TPM-enhet. Dessa tjänster erbjuds av många kiseltillverkare i dag.

### <a name="cloud-setup-step"></a>Molnkonfigurationssteg
Det här steget handlar om att konfigurera molnet för korrekt automatisk etablering. Generellt finns det två typer av användare som ingår i molnkonfigurationssteget: någon som vet hur enheter måste konfigureras i början (en enhetsoperatör) och någon som vet hur enheter ska delas upp mellan IoT-hubbar (en lösningsoperatör).

Det finns en första engångskonfiguration av etableringen som måste utföras. Detta hanteras vanligtvis av lösningsoperatören. När etableringstjänsten har konfigurerats behöver den inte ändras såvida inte användningsfallet ändras.

När tjänsten har konfigurerats för automatisk etablering måste den förberedas inför att registrera enheter. Det här steget utförs av enhetsoperatören, som känner till önskad konfiguration för enheten och är ansvarig för att se till att etableringstjänsten kan garantera enhetens identitet korrekt när enheten söker efter sin IoT-hubb. Enhetsoperatören tar emot den identifierande nyckelinformationen från tillverkaren och lägger till den i registreringslistan. Det kan förekomma efterföljande uppdateringar av registreringslistan allt eftersom nya poster läggs till eller befintliga poster uppdateras med den senaste informationen om enheterna.

## <a name="registration-and-provisioning"></a>Registrering och etablering
*Etablering* innebär olika saker beroende på vilken bransch termen används i. I samband med etablering av IoT-enheter till sina molnlösningar är etablering en tvåstegsprocess:

1. Den första delen är att upprätta den första anslutningen mellan enheten och IoT-lösningen genom att registrera enheten.
2. Den andra delen är att tillämpa korrekt konfiguration på enheten baserat på de särskilda kraven i den lösning som enheten registrerats för.

När båda av dessa två steg har slutförts kan enheten anses ha etablerats fullständigt. Vissa molntjänster tillhandahåller endast det första steget i etableringsprocessen genom att registrera enheter till IoT-lösningens slutpunkt men utan att ge den inledande konfigurationen. DPS automatiserar båda stegen för att tillhandahålla en sömlös etablerings upplevelse för enheten.

## <a name="features-of-the-device-provisioning-service"></a>Funktion i Device Provisioning Service
DPS har många funktioner, vilket gör det idealiskt för att konfigurera enheter.

* Stöd för **säker attestering** för både X.509- och TPM-baserade identiteter.
* **Registreringslista** som innehåller fullständiga uppgifter för enheter eller grupper av enheter som kan komma att registreras. Registreringslistan innehåller information om önskad konfiguration för enheten när den registreras, och den kan uppdateras när som helst.
* **Flera allokeringsregler** för att kontrol lera hur DPS tilldelar enheter till IoT-hubbar som stöder dina scenarier: lägsta latens, jämnt viktad distribution (standard) och statisk konfiguration via registrerings listan. Svars tiden fastställs med samma metod som [Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#performance).
* **Övervaknings- och diagnostikloggning** för att kontrollera att allt fungerar korrekt.
* **Stöd för flera hubbar** gör att DPS kan tilldela enheter till fler än en IoT Hub. DPS kan prata med hubbar i flera Azure-prenumerationer.
* **Stöd för flera regioner** gör att DPS kan tilldela enheter till IoT-hubbar i andra regioner.
* **Kryptering av data i vila** gör att data i DPS krypteras och dekrypteras transparent med hjälp av 256-bitars AES-kryptering, en av de starkaste block krypteringarna som är tillgängliga och är FIPS 140-2-kompatibla.


Du kan lära dig mer om de koncept och funktioner som ingår i enhets etableringen genom att läsa avsnittet om [DPS-terminologi](concepts-service.md) tillsammans med andra konceptuella ämnen i samma avsnitt.

## <a name="cross-platform-support"></a>Stöd för alla plattformar
Precis som med alla Azure IoT-tjänster arbetar DPS mellan olika plattformar med olika operativ system. Azure erbjuder SDK:er med öppen källkod i en mängd [språk](https://github.com/Azure/azure-iot-sdks), vilket underlättar anslutning av enheter och hantering av tjänsten. DPS stöder följande protokoll för att ansluta enheter:

* HTTPS
* AMQP
* AMQP över WebSockets
* MQTT
* MQTT över WebSockets

DPS stöder bara HTTPS-anslutningar för tjänst åtgärder.

## <a name="regions"></a>Regioner
DPS är tillgängligt i många regioner. En uppdaterad lista över befintliga och nyligen tillkännagivna regioner för alla tjänster finns på [Azure Regions](https://azure.microsoft.com/regions/) (Azure-regioner). Du kan kontrollera tillgängligheten för Device Provisioning Service på sidan [Azure Status](https://azure.microsoft.com/status/) (Azure-status).

> [!NOTE]
> DPS är globalt och är inte kopplat till någon plats. Du måste dock ange en region där de metadata som är kopplade till din DPS-profil kommer att finnas.

## <a name="availability"></a>Tillgänglighet
Det finns 99,9% Serviceavtal för DPS och du kan [läsa service avtalet](https://azure.microsoft.com/support/legal/sla/iot-hub/). I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/) förklaras den garanterade tillgängligheten för Azure som helhet.

## <a name="quotas"></a>Kvoter
Varje Azure-prenumeration har standardkvotgränser som kan påverka IoT-lösningens omfång. Den aktuella gränsen på per prenumerationsbasis är 10 Device Provisioning Services per prenumeration.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

Mer information om kvotgränser:
* [Tjänstbegränsningar för Azure-prenumeration](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Relaterade Azure-komponenter
DPS automatiserar enhets etablering med Azure IoT Hub. Läs mer om [IoT Hub](../iot-hub/index.yml).

## <a name="next-steps"></a>Nästa steg
Nu har du en översikt över etablering av IoT-enheter i Azure. Nästa steg är att testa ett IoT-scenario från slutpunkt till slutpunkt.

[Konfigurera IoT Hub Device Provisioning Service med Azure Portal](quick-setup-auto-provision.md)

[Skapa och etablera en simulerad enhet](quick-create-simulated-device.md)

[Konfigurera enhet för etablering](tutorial-set-up-device.md)