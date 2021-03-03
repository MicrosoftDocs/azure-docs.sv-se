---
title: Avetablera enheter som har etablerats med Azure IoT Hub Device Provisioning Service
description: Så här avetablerar du enheter som har etablerats med Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 7c91cd8cbb1a927a88f05fcea603c6a4ed6e1c14
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719040"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Så här avetablerar du enheter som tidigare var automatiskt etablerade 

Det kan vara nödvändigt att avetablera enheter som tidigare har etablerats automatiskt genom enhets etablerings tjänsten. En enhet kan till exempel säljas eller flyttas till en annan IoT-hubb, eller så kan den förloras, stjälas eller på annat sätt komprometteras. 

I allmänhet omfattar avetablering av en enhet två steg:

1. Avregistrera enheten från etablerings tjänsten för att förhindra framtida automatisk etablering. Beroende på om du vill återkalla åtkomst tillfälligt eller permanent, kanske du vill inaktivera eller ta bort en registrerings post. För enheter som använder 509-attestering i X. kanske du vill inaktivera/ta bort en post i hierarkin för dina befintliga registrerings grupper.  
 
   - Information om hur du avregistrerar en enhet finns i [så här avregistrerar du en enhet från Azure IoT Hub Device Provisioning service](how-to-revoke-device-access-portal.md).
   - Information om hur du avregistrerar en enhet program mässigt med hjälp av en av tjänst-SDK: erna för etablerings tjänsten finns i [Hantera enhets registreringar med tjänst-SDK](./quick-enroll-device-x509-java.md): er.

2. Avregistrera enheten från IoT Hub för att förhindra framtida kommunikation och data överföring. Återigen kan du tillfälligt inaktivera eller permanent ta bort enhetens post i identitets registret för den IoT Hub där den etablerades. Se [inaktivera enheter](../iot-hub/iot-hub-devguide-identity-registry.md#disable-devices) om du vill veta mer om inaktive ring. Se "enhets hantering/IoT-enheter" för din IoT Hub-resurs i [Azure Portal](https://portal.azure.com).

De exakta steg som du vidtar för att avetablera en enhet beror på dess mekanism för attestering och den tillämpliga registrerings posten med din etablerings tjänst. I följande avsnitt får du en översikt över processen, baserat på registrerings-och attesterings typ.

## <a name="individual-enrollments"></a>Enskilda registreringar
Enheter som använder TPM-attestering eller X. 509-attestering med ett löv certifikat tillhandahålls via en enskild registrerings post. 

Avetablera en enhet som har en enskild registrering: 

1. Avregistrera enheten från etablerings tjänsten:

   - För enheter som använder TPM-attestering tar du bort den enskilda registrerings posten för att permanent återkalla enhetens åtkomst till etablerings tjänsten eller inaktivera posten för att tillfälligt återkalla åtkomsten. 
   - För enheter som använder sig av X. 509-attestering kan du antingen ta bort eller inaktivera posten. Tänk på att om du tar bort en enskild registrering för en enhet som använder X. 509 och en aktive rad registrerings grupp finns för ett signerings certifikat i enhetens certifikat kedja kan enheten omregistreras. För sådana enheter kan det vara säkrare att inaktivera registrerings posten. Detta förhindrar att enheten registreras på nytt, oavsett om det finns en aktive rad registrerings grupp för ett av dess signerings certifikat.

2. Inaktivera eller ta bort enheten i identitets registret i IoT-hubben som den etablerades till. 


## <a name="enrollment-groups"></a>Registrerings grupper
Med X. 509-attestering kan enheter också tillhandahållas via en registrerings grupp. Registrerings grupper konfigureras med ett signerings certifikat, antingen ett mellanliggande certifikat eller ett rotcertifikatutfärdarcertifikat och styr åtkomsten till etablerings tjänsten för enheter med certifikatet i certifikat kedjan. Mer information om registrerings grupper och X. 509-certifikat med etablerings tjänsten finns i avsnittet om [certifikat attestering i X. 509](concepts-x509-attestation.md). 

Om du vill se en lista över enheter som har etablerats via en registrerings grupp kan du Visa information om registrerings gruppen. Det här är ett enkelt sätt att förstå vilken IoT-hubb varje enhet har etablerats till. Så här visar du enhets listan: 

1. Logga in på Azure Portal och klicka på **alla resurser** i den vänstra menyn.
2. Klicka på etablerings tjänsten i listan över resurser.
3. I etablerings tjänsten klickar du på **Hantera registreringar** och väljer sedan fliken **registrerings grupper** .
4. Klicka på registrerings gruppen för att öppna den.

   ![Visa registrerings grupp posten i portalen](./media/how-to-unprovision-devices/view-enrollment-group.png)

Med registrerings grupper finns det två scenarier att tänka på:

- Avetablera alla enheter som har etablerats via en registrerings grupp:
  1. Inaktivera registrerings gruppen om du inte vill att signerings certifikatet ska tillåtas. 
  2. Använd listan över etablerade enheter för registrerings gruppen för att inaktivera eller ta bort varje enhet från identitets registret för respektive IoT Hub. 
  3. När du har inaktiverat eller tagit bort alla enheter från sina respektive IoT-hubbar kan du ta bort registrerings gruppen om du vill. Tänk på att om du tar bort registrerings gruppen och det finns en aktive rad registrerings grupp för ett signerings certifikat som är högre upp i certifikat kedjan för en eller flera av enheterna, kan enheterna omregistreras. 

- Så här avetablerar du en enskild enhet från en registrerings grupp:
  1. Skapa en inaktive rad enskild registrering för sitt löv certifikat (Device). Detta återkallar åtkomsten till etablerings tjänsten för enheten samtidigt som den ger åtkomst till andra enheter som har registrerings gruppens signerings certifikat i kedjan. Ta inte bort den inaktiverade enskilda registreringen för enheten. På så sätt kan enheten omregistrera sig via registrerings gruppen. 
  2. Använd listan över etablerade enheter för registrerings gruppen för att hitta den IoT-hubb som enheten etablerades till och inaktivera eller ta bort den från hubbens identitets register.