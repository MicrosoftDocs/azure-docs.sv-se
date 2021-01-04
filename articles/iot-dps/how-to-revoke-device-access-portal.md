---
title: Avregistrera enheten från Azure IoT Hub Device Provisioning Service
description: Så här avregistrerar du en enhet för att förhindra etablering via Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c75fcd1fd20e41df5018fcaa07fe83051d7e5f1a
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740376"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Så här avregistrerar du en enhet från Azure IoT Hub Device Provisioning Service

Korrekt hantering av autentiseringsuppgifter för enheten är avgörande för system med hög profil som IoT-lösningar. Bästa praxis för sådana system är att ha en tydlig plan för hur du återkallar åtkomst för enheter när deras autentiseringsuppgifter, om en SAS-token (signatur för delad åtkomst) eller ett X. 509-certifikat kan ha komprometterats. 

Registrering i enhets etablerings tjänsten gör det möjligt [att tillhandahålla en enhet.](about-iot-dps.md#provisioning-process) En etablerad enhet är en som har registrerats med IoT Hub, vilket gör att den kan ta emot sin ursprungliga [enhet med dubbla](~/articles/iot-hub/iot-hub-devguide-device-twins.md) tillstånd och börja rapportera telemetridata. Den här artikeln beskriver hur du avregistrerar en enhet från din etablerings tjänst instans, vilket gör att den inte kan tillhandahållas igen i framtiden.

> [!NOTE] 
> Var medveten om principen för återförsök för enheter som du återkallar åtkomst för. Till exempel kan en enhet som har en oändlig återförsöks princip kontinuerligt försöka registrera med etablerings tjänsten. Den situationen förbrukar tjänst resurser och kan påverka prestandan.

## <a name="disallow-devices-by-using-an-individual-enrollment-entry"></a>Tillåt inte enheter genom att använda en enskild registrerings post

Enskilda registreringar gäller för en enskild enhet och kan använda X. 509-certifikat, TPM-bekräftelse nycklar (i en verklig eller virtuell TPM) eller SAS-token som mekanism för attestering. Om du vill tillåta en enhet som har en enskild registrering kan du antingen inaktivera eller ta bort registrerings posten. 

För att tillfälligt neka enheten genom att inaktivera registrerings posten: 

1. Logga in på Azure Portal och välj **alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du den etablerings tjänst som du vill inaktivera enheten från.
3. I etablerings tjänsten väljer du **Hantera registreringar** och väljer sedan fliken **enskilda registreringar** .
4. Välj registrerings posten för den enhet som du vill inaktivera. 

    ![Välj en individuell registrering](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Rulla längst ned på sidan registrering och välj **inaktivera** för växeln **Aktivera post** och välj sedan **Spara**.  

   ![Inaktivera enskilda registrerings poster i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

För att permanent tillåta enheten genom att ta bort dess registrerings post:

1. Logga in på Azure Portal och välj **alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du den etablerings tjänst som du vill inaktivera enheten från.
3. I etablerings tjänsten väljer du **Hantera registreringar** och väljer sedan fliken **enskilda registreringar** .
4. Markera kryss rutan bredvid registrerings posten för den enhet som du vill inaktivera. 
5. Välj **ta bort** överst i fönstret och välj sedan **Ja** för att bekräfta att du vill ta bort registreringen. 

   ![Ta bort enskilda registrerings poster i portalen](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


När du har slutfört proceduren bör du se att posten tas bort från listan över enskilda registreringar.  

## <a name="disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Tillåt inte ett X. 509-mellanliggande certifikat eller rotcertifikatutfärdarcertifikat med hjälp av en registrerings grupp

X. 509-certifikat ordnas vanligt vis i en certifikat kedja med förtroende. Om ett certifikat vid något Stadium i en kedja blir komprometterat bryts förtroendet. Certifikatet måste vara förhindrat att enhets etablerings tjänsten ska förhindras från etablering av enheter i en kedja som innehåller certifikatet. Mer information om X. 509-certifikat och hur de används med etablerings tjänsten finns i [X. 509-certifikat](./concepts-x509-attestation.md#x509-certificates). 

En registrerings grupp är en post för enheter som delar en gemensam mekanism för attestering av X. 509-certifikat signerade av samma mellanliggande eller rot certifikat utfärdare. Posten för registrerings gruppen har kon figurer ATS med det X. 509-certifikat som är associerat med mellanliggande eller rot certifikat utfärdare. Posten konfigureras också med konfigurations värden, till exempel dubbla tillstånd och IoT Hub-anslutning, som delas av enheter med certifikatet i certifikat kedjan. Om du inte vill tillåta certifikatet kan du antingen inaktivera eller ta bort registrerings gruppen.

För att tillfälligt neka certifikatet genom att inaktivera registrerings gruppen: 

1. Logga in på Azure Portal och välj **alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du den etablerings tjänst som du vill tillåta signerings certifikatet från.
3. I etablerings tjänsten väljer du **Hantera registreringar** och väljer sedan fliken **registrerings grupper** .
4. Välj registrerings gruppen med det certifikat som du vill inaktivera.
5. Välj **inaktivera** på växeln **Aktivera post** och välj sedan **Spara**.  

   ![Inaktivera registreringen av registrerings gruppen i portalen](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
För att permanent tillåta certifikatet genom att ta bort dess registrerings grupp:

1. Logga in på Azure Portal och välj **alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du den etablerings tjänst som du vill inaktivera enheten från.
3. I etablerings tjänsten väljer du **Hantera registreringar** och väljer sedan fliken **registrerings grupper** .
4. Markera kryss rutan bredvid registrerings gruppen för det certifikat som du vill tillåta. 
5. Välj **ta bort** överst i fönstret och välj sedan **Ja** för att bekräfta att du vill ta bort registrerings gruppen. 

   ![Ta bort posten för registrerings gruppen i portalen](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

När du har slutfört proceduren bör du se att posten tas bort från listan över registrerings grupper.  

> [!NOTE]
> Om du tar bort en registrerings grupp för ett certifikat kan enheter som har certifikatet i certifikat kedjan fortfarande kunna registrera om en aktive rad registrerings grupp för rot certifikatet eller ett annat mellanliggande certifikat högre upp i certifikat kedjan finns.

## <a name="disallow-specific-devices-in-an-enrollment-group"></a>Tillåt inte vissa enheter i en registrerings grupp

Enheter som implementerar mekanismen för 509-attestering använder enhetens certifikat kedja och privat nyckel för att autentisera. När en enhet ansluter och autentiserar med enhets etablerings tjänsten söker tjänsten först efter en enskild registrering som matchar enhetens autentiseringsuppgifter. Tjänsten söker sedan efter registrerings grupper för att avgöra om enheten kan tillhandahållas. Om tjänsten hittar en inaktive rad enskild registrering för enheten förhindrar den att enheten ansluter. Tjänsten förhindrar anslutningen även om en aktive rad registrerings grupp för en mellanliggande eller rot certifikat utfärdare finns i enhetens certifikat kedja. 

Följ dessa steg om du vill tillåta en enskild enhet i en registrerings grupp:

1. Logga in på Azure Portal och välj **alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du etablerings tjänsten som innehåller registrerings gruppen för den enhet som du vill inaktivera.
3. I etablerings tjänsten väljer du **Hantera registreringar** och väljer sedan fliken **enskilda registreringar** .
4. Välj knappen **Lägg till individuell registrering** överst. 
5. På sidan **Lägg till registrering** väljer du **X. 509** som **mekanism** för attestering för enheten.

    Ladda upp enhets certifikatet och ange enhets-ID för den enhet som ska vara otillåten. För certifikatet använder du det signerade slutenhets certifikatet som är installerat på enheten. Enheten använder det signerade slut enhets certifikatet för autentisering.

    ![Ange enhets egenskaper för den otillåtna enheten](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Rulla längst ned på sidan **Lägg till registrering** och välj **inaktivera** på växeln **Aktivera post** och välj sedan **Spara**. 

    [![Använd inaktiverad individuell registrerings post för att inaktivera enhet från grupp registrering i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

När du har skapat registreringen bör du se din inaktiverade enhets registrering på fliken **enskilda registreringar** . 

## <a name="next-steps"></a>Nästa steg

Avregistrering är också en del av den större avetablerings processen. Avetablering av en enhet omfattar både avregistrering från etablerings tjänsten och avregistrering från IoT Hub. Läs mer om den fullständiga processen i [så här avetablerar du enheter som tidigare var automatiskt etablerade](how-to-unprovision-devices.md)
