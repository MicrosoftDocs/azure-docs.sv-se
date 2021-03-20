---
title: Återställa X. 509-certifikat i Azure IoT Central
description: Så här återställer du X. 509-certifikat med ditt IoT Central-program
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a9e35c7d4d64279c65971dd512bcd2107dad6594
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92000073"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>Så här återställer du X. 509 enhets certifikat i IoT Central program

Under livs cykeln för din IoT-lösning måste du registrera certifikat. Två av de största orsakerna till rullande certifikat skulle vara en säkerhets överträdelse och förfallo datum för certifikat.

Om du har en säkerhets överträdelse är rullande certifikat en säkerhets metod för att skydda systemet. Som en del av den här [metoden](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)är det viktigt att du behöver ha återaktiva säkerhets processer på plats tillsammans med förebyggande åtgärder. Att återställa enhetens certifikat bör ingå som en del av de här säkerhets processerna. Hur ofta du kan återställa dina certifikat beror på lösningens säkerhets behov. Kunder med lösningar som har mycket känsliga data kan förnya certifikat dagligen, medan andra registrerar sina certifikat varje år.


## <a name="obtain-new-x509-certificates"></a>Hämta nya X. 509-certifikat

Du kan skapa dina egna X. 509-certifikat med hjälp av ett verktyg som OpenSSL. Den här metoden är bra för att testa X. 509-certifikat men ger några garantier kring säkerheten. Använd bara den här metoden för testning om du inte har för berett att fungera som din egen CA-Provider.

## <a name="enrollment-groups-and-security-breaches"></a>Registrerings grupper och säkerhets överträdelser

Om du vill uppdatera en grupp registrering som svar på en säkerhets överträdelse bör du använda följande metod för att uppdatera det aktuella certifikatet omedelbart:

1. Navigera till **Administration**  i det vänstra fönstret och välj **enhets anslutning**.

2. Välj **registrerings grupper** och Välj grupp namnet i listan.

3. För certifikat uppdatering väljer du **Hantera primär** eller **Hantera sekundär**.

4. Lägg till och verifiera rot X. 509-certifikatet i registrerings gruppen.

   Slutför de här stegen för de primära och sekundära certifikaten, om båda har komprometterats.

## <a name="enrollment-groups-and-certificate-expiration"></a>Registrerings grupper och förfallo datum för certifikat

Om du rullar certifikat för att hantera certifikat upphör ande använder du följande metod för att uppdatera det aktuella certifikatet omedelbart:

1. Navigera till **Administration**  i det vänstra fönstret och välj **enhets anslutning**.

2. Välj **registrerings grupper** och Välj grupp namnet i listan.

3. För certifikat uppdatering väljer du **Hantera primär**.

4. Lägg till och verifiera rot X. 509-certifikatet i registrerings gruppen.

5. Senare när det sekundära certifikatet har upphört att gälla, kommer du tillbaka och uppdaterar det sekundära certifikatet.

## <a name="individual-enrollments-and-security-breaches"></a>Enskilda registreringar och säkerhets överträdelser

Om du rullar certifikat som svar på en säkerhets överträdelse kan du använda följande metod för att uppdatera det aktuella certifikatet omedelbart:

1. Välj **enheter** och välj enheten.

2. Välj **Anslut** och Välj Anslut metod som **enskild registrering**

3. Välj **certifikat (X. 509)** som mekanism.

    ![Hantera enskilda registreringar](./media/how-to-roll-x509-certificates/certificate-update.png)

4. För certifikat uppdatering väljer du mappikonen för att välja det nya certifikat som ska överföras för registrerings posten. Välj **Spara**.

    Slutför de här stegen för de primära och sekundära certifikaten, om båda har komprometterats

## <a name="individual-enrollments-and-certificate-expiration"></a>Enskilda registreringar och förfallo datum för certifikat

Om du rullar certifikat för att hantera certifikat upphör ande bör du använda den sekundära certifikat konfigurationen på följande sätt för att minska stillestånds tiden för enheter som försöker etablera.

När det sekundära certifikatet snart upphör att gälla och måste återställas, kan du rotera till att använda den primära konfigurationen. Att rotera mellan de primära och sekundära certifikaten på det här sättet minskar stillestånds tiden för enheter som försöker etablera.

1. Välj **enheter** och välj enheten.

2. Välj **Anslut** och Välj Anslut metod som **enskild registrering**

3. Välj **certifikat (X. 509)** som mekanism.

    ![Hantera enskilda registreringar](./media/how-to-roll-x509-certificates/certificate-update.png)

4. För uppdatering av sekundärt certifikat väljer du mappikonen för att välja det nya certifikat som ska överföras för registrerings posten. Välj **Spara**.

5. Senare när det primära certifikatet har upphört att gälla, kommer du tillbaka och uppdaterar det primära certifikatet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du återställer X. 509-certifikat i ditt Azure IoT Central-program kan du [ansluta till Azure IoT Central](concepts-get-connected.md).


