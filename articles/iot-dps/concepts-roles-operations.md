---
title: IoT Hub Device Provisioning Service-roller och åtgärder
description: Den här artikeln innehåller en översikt över de roller och åtgärder som ingår när du utvecklar och IoT-lösningen med IoT Device Provisioning-tjänsten (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: 322d6b590863d8065454c0439c5d899107a6abe7
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784973"
---
# <a name="roles-and-operations"></a>Roller och åtgärder

De olika stegen för att utveckla en IoT-lösning kan omfatta veckor eller månader, på grund av produktions perioder som produktions tid, leverans, tullmyndighet osv. Dessutom kan de omfatta aktiviteter över flera roller och de olika enheterna är inblandade. Det här avsnittet tar en djupare titt på de olika roller och åtgärder som är relaterade till varje fas, och visar sedan flödet i ett sekvensdiagram. 

Etablering innebär också krav på enhets tillverkaren, som är speciell för att möjliggöra [mekanismen för attestering](concepts-service.md#attestation-mechanism). Tillverknings åtgärder kan också ske oberoende av tidsintervallen för automatiska etablerings faser, särskilt i fall där nya enheter har uppfyllts efter det att automatisk etablering redan har upprättats.

En serie snabb starter finns i innehålls förteckningen till vänster för att hjälpa till att förklara automatisk etablering genom praktisk erfarenhet. För att under lätta/förenkla inlärnings processen används program vara för att simulera en fysisk enhet för registrering och registrering. För vissa snabb starter måste du utföra åtgärder för flera roller, inklusive åtgärder för obefintliga roller, på grund av den simulerade typen av snabb starter.

| Roll | Åtgärd | Beskrivning |
|------| --------- | ------------|
| Tillverkare | Koda identitet och registrerings-URL | Baserat på den mekanism för attestering som används ansvarar tillverkaren för att koda enhetens identitets information och registrerings-URL: en för enhets etablerings tjänsten.<br><br>**Snabb starter**: eftersom enheten simuleras finns det ingen tillverkares roll. Se rollen utvecklare för information om hur du hämtar den här informationen, som används för att koda ett exempel registrerings program. |
| | Ange enhets identitet | Som upphovs man till enhetens identitets information ansvarar tillverkaren för att kommunicera med operatören (eller en utsedd agent) eller direkt registrera den till Device Provisioning-tjänsten via API: er.<br><br>**Snabb starter**: eftersom enheten simuleras finns det ingen tillverkares roll. Se operatörs rollen för information om hur du hämtar enhets identiteten, som används för att registrera en simulerad enhet i din enhets etablerings tjänst instans. |
| Operator | Konfigurera automatisk etablering | Den här åtgärden motsvarar den första fasen för automatisk etablering.<br><br>**Snabb starter**: du kan utföra rollen operatör, konfigurera enhets etablerings tjänsten och IoT Hub instanser i din Azure-prenumeration. |
|  | Registrera enhets identitet | Den här åtgärden motsvarar den andra fasen för automatisk etablering.<br><br>**Snabb starter**: du kan utföra operatörs rollen och registrera den simulerade enheten i din enhets etablerings tjänst instans. Enhetens identitet bestäms av den attesterings metod som simuleras i snabb starten (TPM eller X. 509). Se Developer-rollen för attesterings information. |
| Enhets etablerings tjänst,<br>IoT Hub | \<all operations\> | För både en produktions implementering med fysiska enheter och snabb start med simulerade enheter, uppfylls dessa roller via de IoT-tjänster som du konfigurerar i din Azure-prenumeration. Rollerna/åtgärderna fungerar exakt likadant, eftersom IoT-tjänsterna skiljer sig från etablering av fysiska kontra simulerade enheter. |
| Utvecklare | Bygg/distribuera registrerings program vara | Den här åtgärden motsvarar den tredje fasen för automatisk etablering. Utvecklaren ansvarar för att skapa och distribuera registrerings program varan till enheten med hjälp av lämpligt SDK.<br><br>**Snabb starter**: exempel registrerings programmet som du skapar simulerar en riktig enhet, för din plattform/vilket språk du väljer, som körs på din arbets Station (i stället för att distribuera den till en fysisk enhet). Registrerings programmet utför samma åtgärder som en som distribueras till en fysisk enhet. Du anger attesterings metoden (TPM eller X. 509-certifikat), plus registrerings-URL: en och "ID-omfång" för din enhets etablerings tjänst instans. Enhetens identitet bestäms av SDK-hälsologiken vid körning baserat på den metod som du anger: <ul><li>**TPM-attestering** – din utvecklings arbets Station kör ett [TPM Simulator-program](quick-create-simulated-device.md). När du har kört ett separat program används det för att extrahera TPM: ens "bekräftelse nyckel" och "registrerings-ID" för användning vid registrering av enhets identiteten. SDK-logiken för attestering använder också simulatorn under registreringen för att presentera en signerad SAS-token för autentisering och registrering av registreringen.</li><li>**X509-attestering** – du använder ett verktyg för att [skapa ett certifikat](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain). När du har genererat skapar du certifikat filen som krävs för användning i registreringen. SDK-hälsologiken använder även certifikatet under registreringen för att presentera autentiserings-och registrerings verifiering.</li></ul> |
| Enhet | Start och registrera dig | Den här åtgärden motsvarar den tredje fasen av automatisk etablering, uppfyllt av enhetens registrerings program som skapats av utvecklaren. Se rollen utvecklare för mer information. Vid första starten: <ol><li>Programmet ansluter med enhets etablerings tjänst instansen, per den globala URL-adress och det ID-omfång som angavs under utvecklingen.</li><li>När enheten har anslutits autentiseras enheten mot attesterings metoden och identiteten som angavs under registreringen.</li><li>När den har autentiserats registreras enheten med IoT Hub-instansen som anges av etablerings tjänst instansen.</li><li>Vid lyckad registrering returneras ett unikt enhets-ID och IoT Hub slut punkten till registrerings programmet för att kommunicera med IoT Hub.</li><li> Därifrån kan enheten hämta sin ursprungliga [enhets](~/articles/iot-hub/iot-hub-devguide-device-twins.md) konfiguration, och påbörja processen med att rapportera telemetridata.</li></ol>**Snabb starter**: eftersom enheten simuleras körs registrerings programmet på din utvecklings arbets Station.|

I följande diagram sammanfattas rollerna och ordningsföljden för åtgärder under automatisk etablering av enheter:
<br><br>
[![Automatisk etablerings ordning för en enhet](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Tillverkaren kan också utföra åtgärden "Registrera enhets identitet" med hjälp av API: er för enhets etablerings tjänsten (i stället för via-operatorn). En detaljerad beskrivning av denna sekvensering och mer finns i [Zero Touch Device Registration with Azure IoT video](https://youtu.be/cSbDRNg72cU?t=2460) (från markör 41:00)

## <a name="roles-and-azure-accounts"></a>Roller och Azure-konton

Hur varje roll mappas till ett Azure-konto är scenario-beroende och det finns ett stort antal scenarier som kan vara inblandade. De vanliga mönstren nedan bör hjälpa till att ge en allmän förståelse för hur roller vanligt vis mappas till ett Azure-konto.

#### <a name="chip-manufacturer-provides-security-services"></a>Chips tillverkaren tillhandahåller säkerhets tjänster

I det här scenariot hanterar tillverkaren säkerhet för nivå ett-kunder. Det här scenariot kan föredras av den här nivån – en kund eftersom de inte behöver hantera detaljerad säkerhet. 

Tillverkaren introducerar säkerhet i HSM: er (Hardware Security modules). Den här säkerheten kan omfatta tillverkaren som hämtar nycklar, certifikat osv. från potentiella kunder som redan har DPS-instanser och registrerings grupper. Tillverkaren kan också skapa den här säkerhets informationen för sina kunder.

I det här scenariot kan två Azure-konton ingå:

- **Konto #1**: är troligen delat över operatören och utvecklarens roller till viss grad. Den här parten kan köpa HSM-chips från tillverkaren. Dessa kretsar riktas mot DPS-instanser som är kopplade till kontot #1. Med DPS-registreringar kan den här parten låna ut enheter till flera på flera nivåer genom att konfigurera om inställningarna för enhets registrering i DPS. Den här parten kan också ha IoT-hubbar som har allokerats för backend-system för slutanvändare till gränssnitt för att få åtkomst till telemetri för enheter osv. I det senare fallet kanske ett andra konto inte behövs.

- **Konto #2**: slutanvändare, nivå två kunder kan ha sina egna IoT-hubbar. Den part som är kopplad till kontot #1 bara låna ut enheter till rätt hubb i det här kontot. Den här konfigurationen kräver länkning av DPS-och IoT-hubbar i Azure-konton, som kan göras med Azure Resource Manager mallar.

#### <a name="all-in-one-oem"></a>Allt-i-ett-OEM

Tillverkaren kan vara en "allt-i-ett-OEM" där endast ett enda tillverkar konto skulle behövas. Tillverkaren hanterar säkerhet och etablering från slut punkt till slut punkt.

Tillverkaren kan tillhandahålla ett molnbaserad program till kunder som köper enheter. Det här programmet skulle vara gränssnittet med IoT Hub som tilldelats av tillverkaren.

Vending-datorer eller automatiserade kaffe maskiner representerar exempel för det här scenariot.




## <a name="next-steps"></a>Nästa steg

Det kan vara bra att märka den här artikeln som referens punkt när du arbetar med motsvarande snabb starter för automatisk etablering. 

Börja med att slutföra snabb starten för "Konfigurera automatisk etablering" som passar ditt hanterings verktygs alternativ, vilket vägleder dig genom fasen "tjänst konfiguration":

- [Konfigurera automatisk etablering med hjälp av Azure CLI](quick-setup-auto-provision-cli.md)
- [Konfigurera automatisk etablering med hjälp av Azure Portal](quick-setup-auto-provision.md)
- [Konfigurera automatisk etablering med en Resource Manager-mall](quick-setup-auto-provision-rm.md)

Fortsätt sedan med snabb starten "etablera en enhet" som passar din mekanism för enhets attestering och SDK/språk inställningar för enhets etablerings tjänsten. I den här snabb starten ska du gå igenom faserna "enhets registrering" och "enhets registrering och konfiguration": 

| Mekanism för enhets attestering | Snabb start SDK/språk | 
| ------------------------------- | -------------------- |
| Symmetrisk nyckel | [C](quick-create-simulated-device-symm-key.md)<br>[Java](quick-create-simulated-device-symmetric-key-java.md)<br>[Python](quick-create-device-symmetric-key-python.md) |
| X. 509-certifikat | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |
| Simulerade Trusted Platform Module (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |




