---
title: Återställa X. 509-certifikat i Azure IoT Hub Device Provisioning Service
description: Så här återställer du X. 509-certifikat med Device Provisioning service-instansen (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: bf8b1e04e11dee4e636826430838a467fe034e3f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94951136"
---
# <a name="how-to-roll-x509-device-certificates"></a>Så här återställer du X. 509 enhets certifikat

Under livs cykeln för din IoT-lösning måste du registrera certifikat. Två av de största orsakerna till rullande certifikat skulle vara en säkerhets överträdelse och förfallo datum för certifikat. 

Rullande certifikat är en säker säkerhets metod för att skydda systemet i händelse av en överträdelse. Som en del av den här [metoden](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)är det viktigt att du behöver ha återaktiva säkerhets processer på plats tillsammans med förebyggande åtgärder. Att återställa enhetens certifikat bör ingå som en del av de här säkerhets processerna. Hur ofta du kan återställa dina certifikat beror på lösningens säkerhets behov. Kunder med lösningar som har mycket känsliga data kan förnya certifikat dagligen, medan andra registrerar sina certifikat varje år.

Rullande enhets certifikat innebär att uppdatera det certifikat som lagras på enheten och IoT Hub. Därefter kan enheten reetablera sig själv med IoT Hub med hjälp av normal [etablering](about-iot-dps.md#provisioning-process) med enhets etablerings tjänsten (DPS).


## <a name="obtain-new-certificates"></a>Hämta nya certifikat

Det finns många sätt att hämta nya certifikat för dina IoT-enheter. Dessa omfattar att hämta certifikat från enhets fabriken, skapa egna certifikat och låta en tredje part hantera certifikat som skapas åt dig. 

Certifikat signeras av varandra för att bilda en kedja av förtroende från ett rot certifikat från en rot certifikat utfärdare till ett [löv certifikat](concepts-x509-attestation.md#end-entity-leaf-certificate). Ett signerings certifikat är det certifikat som används för att signera löv certifikatet i slutet av förtroende kedjan. Ett signerings certifikat kan vara ett certifikat från en rot certifikat utfärdare eller ett mellanliggande certifikat i förtroende kedjan. Mer information finns i avsnittet om [X. 509-certifikat](concepts-x509-attestation.md#x509-certificates).
 
Det finns två olika sätt att hämta ett signerings certifikat. Det första sättet, som rekommenderas för produktions system, är att köpa ett signerings certifikat från en rot certifikat utfärdare (CA). På så sätt går säkerhetserna vidare till en betrodd källa. 

Det andra sättet är att skapa egna X. 509-certifikat med hjälp av ett verktyg som OpenSSL. Den här metoden är bra för att testa X. 509-certifikat men ger några garantier kring säkerheten. Vi rekommenderar att du bara använder den här metoden för testning om du inte har för berett att fungera som din egen CA-Provider.
 

## <a name="roll-the-certificate-on-the-device"></a>Återställa certifikatet på enheten

Certifikat på en enhet bör alltid lagras på en säker plats som en [maskinvaru-säkerhetsmodul (HSM)](concepts-service.md#hardware-security-module). Hur du registrerar enhets certifikat beror på hur de har skapats och installerats i enheterna på den första platsen. 

Om du fick dina certifikat från en tredje part måste du se hur de registrerar sina certifikat. Processen kan ingå i din ordning, eller så kan det vara en separat tjänst som de erbjuder. 

Om du hanterar dina egna enhets certifikat måste du bygga din egen pipeline för att uppdatera certifikat. Se till att både gamla och nya löv certifikat har samma egna namn (CN). Genom att ha samma CN kan enheten reetablera sig själv utan att skapa en dubblett av registreringen. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Rulla certifikatet i IoT Hub

Enhets certifikatet kan läggas till manuellt i en IoT-hubb. Certifikatet kan också automatiseras med hjälp av en enhets etablerings tjänst instans. I den här artikeln förutsätter vi att en enhets etablerings tjänst instans används som stöd för automatisk etablering.

När en enhet tillhandahålls första gången via automatisk etablering, startas den och kontaktar etablerings tjänsten. Etablerings tjänsten svarar genom att utföra en identitets kontroll innan du skapar en enhets identitet i en IoT-hubb med enhetens löv certifikat som autentiseringsuppgifter. Etablerings tjänsten meddelar sedan den enhet som IoT Hub är tilldelad till och enheten använder sedan sitt löv certifikat för att autentisera och ansluta till IoT-hubben. 

När ett nytt löv certifikat har registrerats på enheten kan det inte längre ansluta till IoT Hub eftersom det använder ett nytt certifikat för att ansluta. IoT Hub känner bara igen enheten med det gamla certifikatet. Resultatet av enhetens anslutnings försök kommer att vara ett "obehörig" anslutnings fel. För att lösa det här felet måste du uppdatera registrerings posten för enheten för att kunna hantera enhetens nya löv certifikat. Etablerings tjänsten kan sedan uppdatera IoT Hub enhetens register information vid behov när enheten har etablerats. 

Ett möjligt undantag till det här anslutnings felet är ett scenario där du har skapat en [registrerings grupp](concepts-service.md#enrollment-group) för enheten i etablerings tjänsten. I det här fallet, om du inte rullar rot-eller mellanliggande certifikat i enhetens certifikat kedja, kommer enheten att identifieras om det nya certifikatet är en del av förtroende kedjan som definierats i registrerings gruppen. Om det här scenariot inträffar som en reaktion på en säkerhets överträdelse bör du minst tillåta att de angivna enhets certifikaten i gruppen anses vara överträdelse. Mer information finns i [Tillåt inte vissa enheter i en registrerings grupp](./how-to-revoke-device-access-portal.md#disallow-specific-devices-in-an-enrollment-group).

Uppdatering av registrerings poster för registrerade certifikat utförs på sidan **Hantera registreringar** . Följ dessa steg för att få åtkomst till sidan:

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till IoT Hub Device Provisioning service-instansen som innehåller registrerings posten för din enhet.

2. Klicka på **Hantera registreringar**.

    ![Hantera registreringar](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Hur du hanterar uppdatering av registreringen beror på om du använder enskilda registreringar eller grupp registreringar. De rekommenderade metoderna skiljer sig även beroende på om du har rullande certifikat på grund av en säkerhets överträdelse eller om certifikatet upphör att gälla. I följande avsnitt beskrivs hur du hanterar dessa uppdateringar.


## <a name="individual-enrollments-and-security-breaches"></a>Enskilda registreringar och säkerhets överträdelser

Om du rullar certifikat som svar på en säkerhets överträdelse bör du använda följande metod för att ta bort det aktuella certifikatet omedelbart:

1. Klicka på **enskilda** registreringar och klicka på posten registrerings-ID i listan. 

2. Klicka på knappen **ta bort aktuellt certifikat** och klicka sedan på mappikonen för att välja det nya certifikat som ska överföras för registrerings posten. Klicka på **Spara** när du är färdig.

    De här stegen bör utföras för det primära och sekundära certifikatet, om båda komprometteras.

    ![Hantera enskilda registreringar med en säkerhets överträdelse](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. När det komprometterade certifikatet har tagits bort från etablerings tjänsten kan certifikatet fortfarande användas för att göra enhets anslutningar till IoT-hubben så länge en enhets registrering finns där. Du kan hantera följande två sätt: 

    Det första sättet är att manuellt navigera till din IoT-hubb och genast ta bort enhets registreringen som är associerad med det komprometterade certifikatet. När enheten sedan etablerar igen med ett uppdaterat certifikat skapas en ny enhets registrering.     

    ![Ta bort enhets registrering för IoT Hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Det andra sättet är att använda stöd för att etablera om enheten till samma IoT-hubb. Den här metoden kan användas för att ersätta certifikatet för enhets registrering på IoT Hub. Mer information finns i [så här reetablerar du enheter](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Enskilda registreringar och förfallo datum för certifikat

Om du rullar certifikat för att hantera certifikat upphör ande bör du använda den sekundära certifikat konfigurationen på följande sätt för att minska stillestånds tiden för enheter som försöker etablera.

Senare när det sekundära certifikatet också snart upphör att gälla och måste återställas, kan du rotera till att använda den primära konfigurationen. Att rotera mellan de primära och sekundära certifikaten på det här sättet minskar stillestånds tiden för enheter som försöker etablera.


1. Klicka på **enskilda** registreringar och klicka på posten registrerings-ID i listan. 

2. Klicka på **sekundärt certifikat** och klicka sedan på mappikonen för att välja det nya certifikat som ska överföras för registrerings posten. Klicka på **Spara**.

    ![Hantera enskilda registreringar med förfallo datum för sekundärt certifikat](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Senare när det primära certifikatet har upphört att gälla, kom tillbaka och ta bort det primära certifikatet genom att klicka på knappen **ta bort aktuellt certifikat** .

## <a name="enrollment-groups-and-security-breaches"></a>Registrerings grupper och säkerhets överträdelser

Om du vill uppdatera en grupp registrering som svar på en säkerhets överträdelse bör du använda en av följande metoder som tar bort den aktuella rot certifikat utfärdaren eller mellanliggande certifikat omedelbart.

#### <a name="update-compromised-root-ca-certificates"></a>Uppdatera komprometterade rot certifikat för certifikat utfärdare

1. Klicka på fliken **certifikat** för din enhets etablerings tjänst instans.

2. Klicka på det komprometterade certifikatet i listan och klicka sedan på knappen **ta bort** . Bekräfta borttagningen genom att ange certifikat namnet och klicka på **OK**. Upprepa processen för alla komprometterade certifikat.

    ![Ta bort certifikat från rot certifikat utfärdare](./media/how-to-roll-certificates/delete-root-cert.png)

3. Följ de steg som beskrivs i [Konfigurera verifierade certifikat utfärdare](how-to-verify-certificates.md) för att lägga till och kontrol lera nya certifikat från rot certifikat utfärdaren.

4. Klicka på fliken **Hantera registreringar** för din enhets etablerings tjänst instans och klicka på listan **registrerings grupper** . Klicka på namnet på din registrerings grupp i listan.

5. Klicka på **ca-certifikat** och välj ditt nya rot certifikat för certifikat utfärdare. Klicka sedan på **Spara**. 

    ![Välj det nya rot certifikat utfärdarens certifikat för ett komprometterat certifikat](./media/how-to-roll-certificates/select-new-root-cert.png)

6. När det komprometterade certifikatet har tagits bort från etablerings tjänsten kan certifikatet fortfarande användas för att göra enhets anslutningar till IoT-hubben så länge enhets registreringar finns där. Du kan hantera följande två sätt: 

    Det första sättet är att manuellt navigera till din IoT-hubb och genast ta bort enhets registreringen som är associerad med det komprometterade certifikatet. När enheterna etablerar igen med uppdaterade certifikat skapas en ny enhets registrering för var och en.     

    ![Ta bort enhets registrering för IoT Hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Det andra sättet är att använda stöd för att etablera om dina enheter till samma IoT-hubb. Den här metoden kan användas för att ersätta certifikat för enhets registreringar på IoT Hub. Mer information finns i [så här reetablerar du enheter](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Uppdatera komprometterade mellanliggande certifikat

1. Klicka på **registrerings grupper** och klicka sedan på grupp namnet i listan. 

2. Klicka på **mellanliggande certifikat** och **ta bort aktuellt certifikat**. Klicka på mappikonen för att navigera till det nya mellanliggande certifikatet som ska överföras för registrerings gruppen. Klicka på **Spara** när du är klar. De här stegen bör utföras för både det primära och sekundära certifikatet, om båda komprometteras.

    Det nya mellanliggande certifikatet bör signeras av ett verifierat rot certifikat för certifikat utfärdare som redan har lagts till i etablerings tjänsten. Mer information finns i avsnittet om [X. 509-certifikat](concepts-x509-attestation.md#x509-certificates).

    ![Hantera enskilda registreringar för en komprometterad mellanliggande](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. När det komprometterade certifikatet har tagits bort från etablerings tjänsten kan certifikatet fortfarande användas för att göra enhets anslutningar till IoT-hubben så länge enhets registreringar finns där. Du kan hantera följande två sätt: 

    Det första sättet är att manuellt navigera till din IoT-hubb och genast ta bort enhets registreringen som är associerad med det komprometterade certifikatet. När enheterna etablerar igen med uppdaterade certifikat skapas en ny enhets registrering för var och en.     

    ![Ta bort enhets registrering för IoT Hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Det andra sättet är att använda stöd för att etablera om dina enheter till samma IoT-hubb. Den här metoden kan användas för att ersätta certifikat för enhets registreringar på IoT Hub. Mer information finns i [så här reetablerar du enheter](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Registrerings grupper och förfallo datum för certifikat

Om du rullar certifikat för att hantera certifikat upphör ande bör du använda den sekundära certifikat konfigurationen på följande sätt för att se till att ingen stillestånds tid för enheter som försöker etableras.

Senare när det sekundära certifikatet också snart upphör att gälla och måste återställas, kan du rotera till att använda den primära konfigurationen. Om du roterar mellan primära och sekundära certifikat på det här sättet ser du till att ingen stillestånds tid för enheter försöker etablera. 

#### <a name="update-expiring-root-ca-certificates"></a>Uppdatering förfaller rot certifikat utfärdarens certifikat

1. Följ de steg som beskrivs i [Konfigurera verifierade certifikat utfärdare](how-to-verify-certificates.md) för att lägga till och kontrol lera nya certifikat från rot certifikat utfärdaren.

2. Klicka på fliken **Hantera registreringar** för din enhets etablerings tjänst instans och klicka på listan **registrerings grupper** . Klicka på namnet på din registrerings grupp i listan.

3. Klicka på **ca-certifikat** och välj ditt nya rot certifikat för certifikat utfärdare under den **sekundära certifikat** konfigurationen. Klicka sedan på **Spara**. 

    ![Välj det nya rot certifikat utfärdarens certifikat för förfallo datum](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Senare när det primära certifikatet har upphört att gälla klickar du på fliken **certifikat** för din enhets etablerings tjänst instans. Klicka på certifikatet har förfallit i listan och klicka sedan på knappen **ta bort** . Bekräfta borttagningen genom att ange certifikat namnet och klicka på **OK**.

    ![Ta bort certifikat från rot certifikat utfärdare](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Uppdatera utgångna mellanliggande certifikat


1. Klicka på **registrerings grupper** och klicka på grupp namnet i listan. 

2. Klicka på **sekundärt certifikat** och klicka sedan på mappikonen för att välja det nya certifikat som ska överföras för registrerings posten. Klicka på **Spara**.

    Det nya mellanliggande certifikatet bör signeras av ett verifierat rot certifikat för certifikat utfärdare som redan har lagts till i etablerings tjänsten. Mer information finns i avsnittet om [X. 509-certifikat](concepts-x509-attestation.md#x509-certificates).

   ![Hantera registrerings grupper med det sekundära certifikatet som upphör att gälla](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Senare när det primära certifikatet har upphört att gälla, kom tillbaka och ta bort det primära certifikatet genom att klicka på knappen **ta bort aktuellt certifikat** .


## <a name="reprovision-the-device"></a>Reetablera enheten

När certifikatet har registrerats på både enheten och enhets etablerings tjänsten kan enheten reetablera sig själv genom att kontakta enhets etablerings tjänsten. 

Ett enkelt sätt att tillhandahålla programmerings enheter är att program mera enheten för att kontakta etablerings tjänsten för att gå igenom etablerings flödet om enheten får ett "obehörigt" fel vid försök att ansluta till IoT Hub.

Ett annat sätt är att både de gamla och de nya certifikaten ska vara giltiga för en kort överlappning och att använda IoT Hub för att skicka ett kommando till enheter för att de ska registreras på nytt via etablerings tjänsten för att uppdatera IoT Hub anslutnings information. Eftersom varje enhet kan bearbeta kommandon på olika sätt måste du program mera enheten för att veta vad du ska göra när kommandot anropas. Det finns flera sätt som du kan använda för att styra enheten via IoT Hub och vi rekommenderar att du startar processen med hjälp av [direkta metoder](../iot-hub/iot-hub-devguide-direct-methods.md) eller [jobb](../iot-hub/iot-hub-devguide-jobs.md) .

När etableringen är klar kommer enheterna att kunna ansluta till IoT Hub med hjälp av de nya certifikaten.


## <a name="disallow-certificates"></a>Tillåt inte certifikat

Som svar på en säkerhets överträdelse kan du behöva inaktivera ett enhets certifikat. Inaktivera registrerings posten för mål enheten/certifikatet om du inte vill tillåta ett enhets certifikat. Mer information finns i avsnittet om att inte tillåta enheter i artikeln [Hantera avregistrering](how-to-revoke-device-access-portal.md) .

När ett certifikat ingår som en del av en inaktive rad registrerings post kommer alla försök att registrera sig med en IoT-hubb som använder certifikaten att Miss lyckas även om det är aktiverat som en del av en annan registrerings post.
 



## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om X. 509-certifikat i Device Provisioning-tjänsten, se [X. 509 certifikat attestering](concepts-x509-attestation.md) 
- Läs mer om hur du kan använda certifikat för X. 509 CA-certifikat med Azure IoT Hub Device Provisioning Service i [så här verifierar du certifikat](how-to-verify-certificates.md)
- Information om hur du använder portalen för att skapa en registrerings grupp finns i [Hantera enhets registreringar med Azure Portal](how-to-manage-enrollments.md).