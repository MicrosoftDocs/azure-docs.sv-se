---
title: Azure-certifierad enhets program | Microsoft Docs
description: Förstå programmet Azure Certified Device.
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1fcfc7a9e632e5db1fb809dba7a938c8641c9ddc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92048108"
---
# <a name="what-is-the-azure-certified-device-program"></a>Vad är Azure Certified Device-programmet?

Programmet Azure Certified Device säkerställer att kund lösningar fungerar sömlöst med dina Azure-tjänster. Programmet använder verktyg, tjänster och en marknads plats för att dela bransch kunskap och bästa praxis med en community för enhets-och lösnings byggare.

Det här programmet är utformat för att:

- **Ge kunderna förtroende:** Kunder kan köpa enheter som certifierats av Microsoft för användning med Azure-tjänster på ett säkert sätt.

- **Hjälp kunderna att hitta rätt enheter för lösningen:** Enhets byggare kan publicera de unika funktionerna i sina enheter som en del av certifierings processen. Kunder kan enkelt hitta de produkter som passar deras behov.

- **Marknadsför certifierade enheter:** Enhets byggare får ökad insyn, kontakt med kunder och användning av Azure-certifierad enhets märke.

Den här artikeln beskriver hur du:

- Publicera ditt företag till programmet Azure Certified Device.
- Bestäm vilken certifiering som gäller för din enhet.
- Hitta program krav och andra resurser för att komma igång med testningen.
- Använd Azure-certifierad enhets Portal för att verifiera din enhet.

## <a name="onboarding"></a>Publicering

Om du vill certifiera dina enheter på [Azure-certifierad enhets Portal](https://aka.ms/acdp)måste du utföra följande steg:

1. Se till att företaget har ett Azure Active Directory-konto med en arbets-eller skol klient.
2. Anslut [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) med ditt konto.
3. Logga in på [Azure-certifierad enhets Portal](https://aka.ms/acdp) när du har anslutit till MPN.
4. Granska och signera [program avtalet](https://aka.ms/acdagreement) på sidan företags profil

### <a name="company-profile"></a>Företags profil

Välj **företags profil** för att hantera företagets profil på Azure-certifierad enhets Portal. Företags profilen innehåller företagets URL, e-postadress och logo typ. Godkänn program avtalet på den här sidan innan du fortsätter med eventuella certifierings åtgärder.

Sidan enhets beskrivning i Azure-certifierad enhets katalog använder företags profil informationen.

## <a name="choose-the-certification"></a>Välj certifikat

Det finns tre olika certifieringar, som var och en fokuserar på att leverera ett annat kund värde. Beroende på vilken typ av enhet du skapar och mål gruppen kan du välja de certifieringar eller certifieringar som är mest tillämpliga:

### <a name="azure-certified-device"></a>Azure-certifierad enhet

_Azure-certifierad enhets certifiering_ verifierar att en enhet kan ansluta till Azure IoT Hub och på ett säkert sätt etablera genom enhets etablerings tjänsten (DPS). Den här certifieringen visar en enhets funktioner och interoperabilitet som är en nödvändig bas linje för mer avancerade certifieringar.

- Mer information finns i [certifierings kraven](https://aka.ms/acdrequirements).
- Mer information om hur du använder DPS för att ansluta enheten till Azure IoT Hub finns i [Översikt över etablerings enheter](../iot-dps/about-iot-dps.md).

### <a name="iot-plug-and-play"></a>Plug and Play för IoT

_IoT plug and Play-certifiering_, en stegvis certifiering till Azure-certifierad enhets certifiering, fören klar processen med att skapa enheter utan anpassad enhets kod. IoT Plug and Play gör det möjligt för maskin varu partner att bygga enheter som enkelt kan integreras med moln lösningar baserade på Azure IoT Central och lösningar från tredje part.

- Mer information finns i [certifierings kraven](https://aka.ms/acdiotpnprequirements).
- Mer information om hur du förbereder en enhet för IoT-Plug and Play testning finns i [så här certifierar du IoT plug and Play-enheter](howto-certify-device.md).

### <a name="edge-managed"></a>Edge-hanterad

_Edge-hanterad certifiering_, en stegvis certifiering till Azure-certifierad enhets certifiering, fokuserar på enhets hanterings standarder för Azure IoT-enheter som kör Windows, Linux eller återställnings tider. För närvarande fokuserar program certifieringen på Edge runtime-kompatibilitet för distribution och hantering av modulen.

> [!TIP]
> Det här programmet kallades tidigare för _IoT Edge certifierings program_.

- Mer information finns i [certifierings kraven](https://aka.ms/acdedgemanagedrequirements).
- Mer information om IoT Edge finns i [IoT Edge översikt](../iot-edge/about-iot-edge.md).
- Mer information om operativ system och behållare som stöds finns i [IoT Edge system som stöds](../iot-edge/support.md).

## <a name="use-the-azure-certified-device-portal"></a>Använd Azure-certifierad enhets Portal

I det här avsnittet sammanfattas hur du använder [Azure-certifierad enhets Portal](https://certify.azure.com). Mer information finns i [komma igång med Portal guiden](https://aka.ms/acdhelp).

För att certifiera en enhet i programmet Azure Certified Device, utför följande fyra steg:

1. Ange enhets information
2. Testa enheten
3. Skicka och Slutför granskningen
4. Publicera till Azure-certifierad enhets katalog (valfritt)

### <a name="provide-device-details"></a>Ange enhets information

För varje enhet som du vill certifiera använder du formulären i certifierings portalen för att registrera information om enhetens maskin vara, installations anvisningar och marknadsförings material:

- **Enhets information:** Samlar in information om enheten, till exempel namn, beskrivning, maskin varu information och operativ system.
- **Kom igång-guide**: ett PDF-dokument som en kund kan använda för att snabbt använda produkten. [Exempel på mallar](https://aka.ms/GSTemplate) är tillgängliga.
- **Marknadsförings information:** Tillhandahåll kund färdig marknadsförings information för din enhet, till exempel en bild-och distributörs information.
- **Ytterligare bransch certifieringar:** Ett valfritt avsnitt där du kan ange information om andra certifieringar som enheten har.

### <a name="test-the-device"></a>Testa enheten

Den här fasen interagerar med din enhet och kör en serie tester efter att enheten använder DPS för att ansluta till IoT Hub. När du är klar kan du Visa en uppsättning loggfiler med dina enhets test resultat.

Certifierings portalen innehåller instruktioner om hur du ansluter till den IoT Hub-instans som används för testning. Du kan upprätta DPS-anslutningen genom någon av de [metoder som stöds](../iot-dps/concepts-service.md#attestation-mechanism).

Azure Certified Device-teamet kan kontakta enhets verktyget för att få ytterligare manuell verifiering av enheten.

### <a name="submit-and-publish"></a>Skicka och publicera

Den slutliga nödvändiga fasen är att skicka projektet för granskning. Det här steget meddelar en Azure-certifierad enhets grupp medlem att granska ditt projekt, inklusive enhets-och marknadsförings information och guiden kom igång. En grupp medlem kan kontakta dig på företagets e-postadress som tidigare medföljde frågor eller redigera begär Anden innan godkännande.

Om enheten kräver ytterligare manuell verifiering som en del av certifieringen får du ett meddelande för tillfället.

När en enhet är certifierad kan du välja att publicera din produkt information till Azure-certifierad enhets katalog med hjälp av funktionen **Publicera till katalog** på sidan produkt Sammanfattning.

## <a name="if-you-have-questions"></a>Om du har frågor

Kontakta teamet på [iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question) om du har frågor om certifierings programmen, certifierings portalen eller Azure-certifierad enhets katalog.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över programmet Azure Certified Device är ett föreslaget nästa steg att lära dig [hur du certifierar IoT plug and Play-enheter](howto-certify-device.md).