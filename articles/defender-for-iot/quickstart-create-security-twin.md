---
title: 'Snabb start: skapa en säkerhetsmodul med dubbla'
description: I den här snabb starten får du lära dig hur du skapar en Defender för IoT-modul med dubbla för användning med Azure Defender för IoT.
ms.topic: quickstart
ms.date: 1/21/2021
ms.openlocfilehash: 2ee88bd23b7d125ef9244f8ff630ee5eb8cdd015
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782681"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Snabb start: skapa en azureiotsecurity-modul med dubbla

I den här snabb starten beskrivs hur du skapar en enskild _azureiotsecurity_ -modul för nya enheter, eller om du skapar en modul med flera enheter i en IoT Hub.

## <a name="prerequisites"></a>Förutsättningar

Inget

## <a name="understanding-azureiotsecurity-module-twins"></a>Att förstå azureiotsecurity-modulen är dubbla

För IoT-lösningar som är inbyggda i Azure spelar enheten upp en viktig roll i både enhets hantering och process automatisering.

Defender för IoT erbjuder fullständig integrering med din befintliga plattform för IoT-enhets hantering, så att du kan hantera enhetens säkerhets status och använda befintliga funktioner för enhets kontroll.
Defender för IoT-integrering uppnås genom att använda den IoT Hub dubbla mekanismen.

Se [IoT Hub moduls dubblare](../iot-hub/iot-hub-devguide-module-twins.md) för att lära dig mer om det allmänna begreppet modul-i Azure IoT Hub.

Defender for IoT använder modulens dubbla mekanism och underhåller en säkerhetsmodul med dubbla namngivna _azureiotsecurity_ för var och en av dina enheter.

Defender-IoT-Micro-agenten innehåller all information som är relevant för enhets säkerhet för var och en av dina enheter.

För att kunna använda Defender för IoT-funktioner måste du skapa, konfigurera och använda den här Defender-IoT-Micro-agenten för varje enhet i tjänsten.

## <a name="create-azureiotsecurity-module-twin"></a>Skapa azureiotsecurity-modul, dubbla

_azureiotsecurity_ -modulens dubblare kan skapas på två sätt:

1. [Modulens batch-skript](https://aka.ms/iot-security-github-create-module) – skapar automatiskt modul dubbla för nya enheter eller enheter utan att en modul är dubbel med standard konfigurationen.
1. Manuellt redigera varje modul, separat med vissa konfigurationer för varje enhet.

>[!NOTE]
> När du använder batch-metoden skrivs inte befintliga azureiotsecurity-moduler över. När du använder batch-metoden skapas bara en ny modul med dubbla objekt för enheter som inte redan har en säkerhetsmodul.

Se [agent konfiguration](how-to-agent-configuration.md) för att lära dig att ändra eller ändra konfigurationen för en befintlig modul med dubbla.

Så här skapar du en ny _azureiotsecurity_ -modul för en enhet manuellt:

1. Leta upp och välj den enhet som du vill skapa en säkerhetsmodul för i IoT Hub.

1. Välj på enheten och sedan på **Lägg till modulens identitet**.

1. Ange **azureiotsecurity** i fältet **namn på modulens identitet** .

1. Välj **Spara**.

## <a name="verify-creation-of-a-module-twin"></a>Verifiera att en modul har skapats

Så här kontrollerar du om det finns en säkerhetsmodul för en speciell enhet:

1. I Azure IoT Hub väljer du **IoT-enheter** på menyn **Explorer** .

1. Ange enhets-ID eller Välj ett alternativ i **fältet fråga enhet** och välj **fråga enheter**.

    :::image type="content" source="./media/quickstart/verify-security-module-twin.png" alt-text="Fråga enheter":::

1. Välj enheten eller dubbelklicka på den för att öppna sidan med enhets information.

1. Välj menyn **modul identiteter** och bekräfta att **azureiotsecurity** -modulen finns i listan över modul identiteter som är associerade med enheten.

    :::image type="content" source="./media/quickstart/verify-security-module-twin-3.png" alt-text="Moduler som är kopplade till en enhet":::

Mer information om hur du anpassar egenskaper för Defender for IoT-modulen finns i [agent konfiguration](how-to-agent-configuration.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel och lär dig hur du undersöker säkerhets rekommendationer...

> [!div class="nextstepaction"]
> [Undersöka säkerhetsrekommendationer](quickstart-investigate-security-recommendations.md)