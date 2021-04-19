---
title: Distribuera X.509-certifikat Azure IoT Central
description: Så här rullar du X.509-certifikat med ditt IoT Central program
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c25af944b4c748307f4f974ca8616ecc9f7b28c3
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714532"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>Så här rullar du X.509-enhetscertifikat i IoT Central program

Under livscykeln för din IoT-lösning måste du distribuera certifikat. Två av de främsta orsakerna till att distribuera certifikat är ett säkerhetsintrång och att certifikatet upphör att gälla.

Om du har ett säkerhetsintrång är löpande certifikat en säkerhetsmetod som hjälper dig att skydda systemet. Som en del [av Metoden anta intrång](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)rekommenderar Microsoft behovet av att ha reaktiva säkerhetsprocesser på plats tillsammans med förebyggande åtgärder. Du bör ta med dina enhetscertifikat som en del av dessa säkerhetsprocesser. Hur ofta du distribuerar certifikaten beror på lösningens säkerhetsbehov. Kunder med lösningar som rör mycket känsliga data kan varje dag distribuera certifikat, medan andra rullar sina certifikat vart par år.


## <a name="obtain-new-x509-certificates"></a>Hämta nya X.509-certifikat

Du kan skapa egna X.509-certifikat med ett verktyg som OpenSSL. Den här metoden är bra för att testa X.509-certifikat, men ger få säkerhetsgarantier. Använd bara den här metoden för testning om du inte är beredd att agera som din egen CA-provider.

## <a name="enrollment-groups-and-security-breaches"></a>Registreringsgrupper och säkerhetsöverträdelser

Om du vill uppdatera en gruppregistrering som svar på ett säkerhetsintrång bör du använda följande metod som uppdaterar det aktuella certifikatet omedelbart:

1. Gå till **Administration** i det vänstra fönstret och välj **Enhetsanslutning.**

2. Välj **Registreringsgrupper** och välj gruppnamnet i listan.

3. För certifikatuppdatering väljer du **Hantera primär** eller **Hantera sekundär**.

4. Lägg till och verifiera X.509-rotcertifikatet i registreringsgruppen.

   Utför de här stegen för de primära och sekundära certifikaten, om båda komprometteras.

## <a name="enrollment-groups-and-certificate-expiration"></a>Registreringsgrupper och förfallodatum för certifikat

Om du rullar certifikat för att hantera förfallodatum för certifikat använder du följande metod för att uppdatera det aktuella certifikatet omedelbart:

1. Gå till **Administration** i det vänstra fönstret och välj **Enhetsanslutning.**

2. Välj **Registreringsgrupper** och välj gruppnamnet i listan.

3. För certifikatuppdatering väljer du **Hantera primär**.

4. Lägg till och verifiera X.509-rotcertifikatet i registreringsgruppen.

5. Senare när det sekundära certifikatet har upphört att gälla, kommer du tillbaka och uppdaterar det sekundära certifikatet.

## <a name="individual-enrollments-and-security-breaches"></a>Enskilda registreringar och säkerhetsöverträdelser

Om du distribuerar certifikat som svar på ett säkerhetsintrång använder du följande metod för att uppdatera det aktuella certifikatet omedelbart:

1. Välj **Enheter** och välj enheten.

2. Välj **Anslut** och välj anslut metod som **Enskild registrering**

3. Välj **Certifikat (X.509)** som mekanism.

    ![Hantera enskilda registreringar](./media/how-to-roll-x509-certificates/certificate-update.png)

4. För certifikatuppdatering väljer du mappikonen för att välja det nya certifikatet som ska laddas upp för registreringsposten. Välj **Spara**.

    Utför de här stegen för de primära och sekundära certifikaten om båda har komprometterats

## <a name="individual-enrollments-and-certificate-expiration"></a>Enskilda registreringar och förfallodatum för certifikat

Om du rullar certifikat för att hantera förfallodatum för certifikat bör du använda den sekundära certifikatkonfigurationen på följande sätt för att minska stilleståndstiden för enheter som försöker etablera.

När det sekundära certifikatet snart upphör att gälla och behöver distribueras kan du rotera till med hjälp av den primära konfigurationen. Om du roterar mellan de primära och sekundära certifikaten på det här sättet minskar avbrottstiden för enheter som försöker etablera.

1. Välj **Enheter** och välj enheten.

2. Välj **Anslut** och välj anslut metod som **Enskild registrering**

3. Välj **Certifikat (X.509)** som mekanism.

    ![Hantera enskilda registreringar](./media/how-to-roll-x509-certificates/certificate-update.png)

4. För sekundär certifikatuppdatering väljer du mappikonen för att välja det nya certifikatet som ska laddas upp för registreringsposten. Välj **Spara**.

5. Senare när det primära certifikatet har upphört att gälla kommer du tillbaka och uppdaterar det primära certifikatet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar X.509-certifikat i ditt Azure IoT Central-program kan du ansluta [till Azure IoT Central](concepts-get-connected.md).


