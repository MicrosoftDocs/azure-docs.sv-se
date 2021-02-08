---
title: Skapa en Defender IoT Micro agent-modul, delad (för hands version)
titleSuffix: Azure Defender for IoT
description: Lär dig hur du skapar en enskild DefenderIotMicroAgent-modul för nya enheter.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: f14f253960b628f2bf8052e92dbec3c897a205db
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820848"
---
# <a name="create-a-defender-iot-micro-agent-module-twin-preview"></a>Skapa en Defender IoT Micro agent-modul, delad (för hands version)

Du kan skapa en enskild **DefenderIotMicroAgent** -modul för nya enheter. Du kan också skapa en modul med dubbla för alla enheter i en IoT Hub. 

## <a name="device-twins"></a>Enhets dubbla 

För IoT-lösningar som är inbyggda i Azure spelar enheten upp en viktig roll i både enhets hantering och process automatisering. 

Defender för IoT kan integreras fullständigt med din befintliga plattform för IoT-enhets hantering. Fullständig integrering, gör att du kan hantera enhetens säkerhets status och gör att du kan använda alla befintliga funktioner för enhets kontroll. Integreringen uppnås genom att använda den IoT Hub dubbla mekanismen. 

Lär dig mer om begreppet [enhets enheter](../iot-hub/iot-hub-devguide-device-twins.md)   i Azure IoT Hub. 

## <a name="security-module-twins"></a>Säkerhetsmodul, dubbla 

Defender for IoT använder en säkerhetsmodul som är dubbel för varje enhet. Säkerhetsmodulen är dubbelt innehåller all information som är relevant för enhets säkerhet, för varje enskild enhet i din lösning. Egenskaper för enhets säkerhet konfigureras via en dedikerad säkerhetsmodul för säkrare kommunikation, för att möjliggöra uppdateringar och underhåll som kräver färre resurser. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Att förstå DefenderIotMicroAgent-modulen är dubbla 

Enheten är byggd med att spela en viktig roll i både enhets hantering och process automatisering, för IoT-lösningar som är inbyggda i Azure.

Defender for IoT erbjuder möjlighet att helt integrera din befintliga plattform för IoT-enhets hantering, så att du kan hantera enhetens säkerhets status och använda de befintliga enhets kontroll funktionerna. Du kan integrera din Defender för IoT med hjälp av IoT Hub dubbla mekanismen.  

Om du vill veta mer om det allmänna konceptet moduls dubbla i Azure IoT Hub, se [IoT Hub modul, dubbla](../iot-hub/iot-hub-devguide-module-twins.md).

Defender for IoT använder modulens dubbla mekanism och underhåller en säkerhetsmodul med dubbla namn `DefenderIotMicroAgent` för var och en av dina enheter. 

För att kunna utnyttja alla funktioner i Defender för IoT måste du skapa, konfigurera och använda säkerhetsmodulen för alla enheter i tjänsten. 

## <a name="create-defenderiotmicroagent-module-twin"></a>Skapa DefenderIotMicroAgent-modul, dubbla 

Du kan skapa **DefenderIotMicroAgent** -modulens dubblare genom att manuellt redigera varje modul så att den innehåller vissa konfigurationer för varje enhet. 

Så här skapar du en ny **DefenderIotMicroAgent** -modul för en enhet manuellt: 

1. Leta upp och välj den enhet som du vill skapa en säkerhetsmodul för i IoT Hub. 

1. Välj **Lägg till modulens identitet**. 

1. I fältet **namn på modulens identitet**   och ange  `DefenderIotMicroAgent` . 

1. Välj **Spara**. 

## <a name="verify-the-creation-of-a-module-twin"></a>Kontrol lera att en modul har skapats 

Så här kontrollerar du om det finns en säkerhetsmodul för en speciell enhet: 

1. I Azure IoT Hub väljer du **IoT**   -enheter på menyn **Explorer**   . 

1. Ange enhets-ID eller Välj ett alternativ i fältet **fråga enhet** och välj **fråga enheter**.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Välj fråga enheter om du vill hämta en lista över dina enheter.":::

1. Välj enheten och öppna sidan med **enhets information** . 

1. Välj menyn **modul identiteter**   och bekräfta att **DefenderIotMicroAgent** -modulen finns i listan över modul identiteter som är associerade med enheten.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Välj modul identiteter på fliken.":::

## <a name="next-steps"></a>Nästa steg 

Gå vidare till nästa artikel och lär dig hur du [undersöker säkerhets rekommendationer](quickstart-investigate-security-recommendations.md).
