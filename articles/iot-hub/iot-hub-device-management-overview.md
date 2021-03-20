---
title: Översikt över enhets hantering med Azure IoT Hub | Microsoft Docs
description: Översikt över enhets hantering i Azure IoT hu--Enterprise-enhetens livs cykel och enhets hanterings mönster som, omstart, fabriks återställning, uppdatering av inbyggd program vara, konfiguration, enhets uppstarter, frågor, jobb.
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 9ff8ebeaf7c4a219bc403c81dfea62f4657a1fd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148345"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Översikt över enhetshantering med IoT Hub

Azure IoT Hub innehåller funktionerna och en modell för utökningsbarhet som gör det möjligt enhets- och backend-utvecklare att bygga robusta lösningar för enhetshantering. Enheter är allt från begränsade sensorer och enskilda mikrostyrenheter för ett särskilt ändamål till kraftfulla gateways som dirigerar kommunikationen för grupper av enheter.  Dessutom varierar användningsfall och krav för IoT-operatörer avsevärt mellan olika branscher.  Trots variationen ger enhetshantering med IoT Hub funktioner, mönster och kodbibliotek för att serva en mängd olika enheter och slutanvändare.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

En viktig del av att skapa en lyckad IoT-lösning för företaget är att tillhandahålla en strategi för hur operatörer kontinuerligt hanterar sin samling med enheter. IoT-operatörer behöver enkla och tillförlitliga verktyg och program som låter dem fokusera på mer strategiska aspekter av sina arbetsuppgifter. Den här artikeln innehåller:

* En kort översikt över metoden för enhetshantering i Azure IoT Hub.
* En beskrivning av gemensamma principer för hantering av enheten.
* En beskrivning av enhetens livscykel.
* En översikt över vanliga mönster för hantering av enheten.

## <a name="device-management-principles"></a>Principer för enhetshantering

IoT medför en unik uppsättning utmaningar för enhetshantering, och en lösning i företagsklass måste ta hänsyn till följande principer:

![Bild över principer för enhetshantering](media/iot-hub-device-management-overview/image4.png)

* **Skala och automatisering**: IoT-lösningar kräver enkla verktyg som kan automatisera rutiner och som låter en mycket liten personalstyrka hantera miljontals enheter. I det dagliga arbetet förväntas operatörer fjärrhantera enhetsåtgärder gruppvis och endast få aviseringar när problem som kräver direkta åtgärder uppstår.

* **Insyn och kompatibilitet**: Enhetsmiljön är ovanligt mångfaldig. Hanteringsverktygen måste skräddarsys för att hantera en mängd olika enhetsklasser, plattformar och protokoll. Operatörer måste ha stöd för många typer av enheter, från de mest begränsade inbäddade enkelprocesschippen till kraftfulla och helt funktionella datorer.

* **Kontextmedvetenhet**: IoT-miljöer är dynamiska och föränderliga. Tjänstens tillförlitlighet är avgörande. Enhetshanteringsåtgärder måste ha följande faktorer i åtanke så att driftavbrotten inte påverkar viktiga företagsrutiner eller skapar farliga förutsättningar:

    * SLA-underhållsfönster
    * Nätverks- och krafttillstånd
    * Användningsförutsättningar
    * Enhetens geografiska plats

* **Stöd för många roller**: stöd för IoT-åtgärdernas unika arbetsflöden och processer är avgörande. Driftpersonalen måste arbeta harmoniskt med de givna begränsningarna för interna IT-avdelningar.  De måste också hitta hållbara sätt för att exponera information om enhetsåtgärder i realtid för chefer och andra företagsledarroller. 

## <a name="device-lifecycle"></a>Enhetslivscykel
Det finns en uppsättning av allmänna enhetshanteringsfaser som är gemensamma för alla företags IoT-projekt. Livscykeln för enheter i Azure IoT består av fem faser:

![De fem Azure IoT-livscyklerna är: planera, etablera, konfigurera, övervaka, dra tillbaka](./media/iot-hub-device-management-overview/image5.png)

I var och en av dessa fem faser finns det flera enhetsoperatorskrav som måste uppfyllas för att tillhandahålla en komplett lösning:

* **Planera**: Operatörer kan skapa ett schema med enhetens metadata. Schemat kan användas till att snabbt och korrekt skicka frågor till eller fokusera på en enhetsgrupp för massåtgärder. Du kan använda enhetstvillingen för att lagra dessa enhetsmetadata i form av taggar och egenskaper.
  
    *Mer läsning*: 
    * [Kom igång med enhetstvillingar](iot-hub-node-node-twin-getstarted.md)
    * [Förstå enhetstvillingar](iot-hub-devguide-device-twins.md)
    * [Använda enhetens dubbla egenskaper](tutorial-device-twins.md)
    * [Metod tips för enhets konfiguration i en IoT-lösning](iot-hub-configuration-best-practices.md)

* **Etablera**: Etablera säkert nya enheter i IoT Hub där operatörerna kan se enhetens funktioner direkt.  Använd IoT Hub-identitetsregistret för att skapa flexibla enhetsidentiteter och autentiseringsuppgifter och utför åtgärden gruppvis med hjälp av ett jobb. Skapa enheter om du vill rapportera deras kapacitet och villkor via enhetens egenskaper i enhetstvillingen.
  
    *Mer läsning*: 
    * [Hantera enhetsidentiteter](iot-hub-devguide-identity-registry.md)
    * [Mass hantering av enhets identiteter](iot-hub-bulk-identity-mgmt.md)
    * [Använda enhetens dubbla egenskaper](tutorial-device-twins.md)
    * [Metod tips för enhets konfiguration i en IoT-lösning](iot-hub-configuration-best-practices.md)
    * [Azure IoT Hub Device Provisioning-tjänst](https://azure.microsoft.com/documentation/services/iot-dps)

* **Konfigurera**: underlätta massinläsning av konfigurationsändringar och uppdaterad enhetsprogramvara utan att förlora funktion eller säkerhet. Utför dessa åtgärder för enhetshantering gruppvis genom att använda önskade egenskaper eller med direkta metoder och sändningsjobb.
  
    *Mer läsning*:
    * [Använda enhetens dubbla egenskaper](tutorial-device-twins.md)
    * [Konfigurera och övervaka IoT-enheter i stor skala](./iot-hub-automatic-device-management.md)
    * [Metod tips för enhets konfiguration i en IoT-lösning](iot-hub-configuration-best-practices.md)

* **Övervaka**: Övervaka enhetssamlingens allmänna funktion samt statusen för pågående åtgärder så att operatörerna blir medvetna om problem som kräver åtgärder.  Använd enhetstvillingen för att tillåta att enheterna rapporterar driftsförhållanden och status för uppdateringsåtgärder i realtid. Skapa kraftfulla instrumentpanelsrapporter som lyfter fram de mest omedelbara problemen genom att använda enhetstvillingsfrågor.
  
    *Mer läsning*: 
    * [Använda enhetens dubbla egenskaper](tutorial-device-twins.md)
    * [IoT Hub frågespråk för enhets dubbla, jobb och meddelanderoutning](iot-hub-devguide-query-language.md)
    * [Konfigurera och övervaka IoT-enheter i stor skala](./iot-hub-automatic-device-management.md)
    * [Metod tips för enhets konfiguration i en IoT-lösning](iot-hub-configuration-best-practices.md)

* **Dra tillbaka**: Ersätt eller inaktivera enheter efter ett haveri, uppgraderings cykel eller i slutet av tjänstens livs längd.  Använd enhetstvillingen för att underhålla enhetsinformation om den fysiska enheten håller på att ersättas eller arkiveras om den har återkallats. Använd IoT Hub-identitetsregistret för att säkert återkalla enhetsidentiteter och autentiseringsuppgifter.
  
    *Mer läsning*: 
    * [Använda enhetens dubbla egenskaper](tutorial-device-twins.md)
    * [Hantera enhetsidentiteter](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Enhetshanteringsmönster

IoT Hub använder följande uppsättning enhetshanteringsmönster. [Självstudiekursen om enhetshantering](iot-hub-node-node-device-management-get-started.md) beskriver i mer detalj hur du utökar dessa mönster så att de passar just ditt scenario och hur du designar nya mönster utifrån dessa basmallar.

* **Starta om**: backend-appen informerar enheten via en direkt metod om att en omstart har påbörjats.  Enheten använder de rapporterade egenskaperna för att uppdatera enhetens status för omstart.
  
    ![Bild över omstartsmönster för enhetshantering](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Fabriks återställning**: backend-appen informerar enheten via en direkt metod om att en fabriks återställning har påbörjats. Enheten använder de rapporterade egenskaperna för att uppdatera enhetens status för fabriksåterställning.
  
    ![Bilder över fabriksåterställningsmönster för enhetshantering](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Konfiguration**: backend-appen använder önskade egenskaper för att konfigurera program vara som körs på enheten. Enheten använder de rapporterade egenskaperna för att uppdatera enhetens konfigurationsstatus.
  
    ![Bild över konfigurationsmönster för enhetshantering](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Uppdatering av inbyggd program vara**: backend-appen använder en automatisk enhets hanterings konfiguration för att välja vilka enheter som ska ta emot uppdateringen, för att berätta för enheterna var uppdateringen finns och övervaka uppdaterings processen. Enheten initierar en process i flera steg för att ladda ned, verifiera och tillämpa avbildningen av den inbyggda program varan och sedan starta om enheten innan du återansluter till IoT Hubs tjänsten. Enheten använder de rapporterade egenskaperna under hela flerstegsprocessen för att uppdatera enhetens förlopp och status.
  
    ![Bild över mönster för uppdatering av inbyggd programvara för enhetshantering](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **Rapportera förlopp och status**: lösningens Server del kör enheten med dubbla frågor, över en uppsättning enheter, för att rapportera om status och förlopp för åtgärder som körs på enheterna.
  
    ![Bild över mönster för uppdatering av rapporteringsprocess och status](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>Nästa steg

Du kan använda de funktioner, mönster och kodbibliotek som ingår i IoT Hub-enhetshanteringen för att skapa IoT-program som uppfyller företagets IoT-operatörskrav i varje enhets livscykelfas.

Om du vill lära dig mer om enhetshanteringsfunktionerna i IoT Hub går du självstudiekursen [Komma igång med enhetshantering](iot-hub-node-node-device-management-get-started.md).