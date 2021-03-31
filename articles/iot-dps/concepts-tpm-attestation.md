---
title: Azure-IoT Hub Device Provisioning Service – TPM-attestering
description: Den här artikeln innehåller en översikt över flödet för TPM-attestering med IoT Device Provisioning-tjänsten (DPS).
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 12860629d78391ed271306daba29a51aeb326c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531601"
---
# <a name="tpm-attestation"></a>TPM-attestering

IoT Hub Device Provisioning Service är en hjälp tjänst för IoT Hub som du använder för att konfigurera enhets etablering med noll touch till en angiven IoT-hubb. Med enhets etablerings tjänsten kan du etablera miljon tals enheter på ett säkert sätt.

I den här artikeln beskrivs processen för identitets attestering när du använder en Trusted Platform Module (TPM). En TPM är en typ av HSM (Hardware Security Module). Den här artikeln förutsätter att du använder en diskret, inbyggd program vara eller integrerad TPM. Programvarubaserade TPM: er lämpar sig väl för prototyper eller testning, men de ger inte samma säkerhets nivå som diskret, inbyggd program vara eller integrerade TPM: er. Vi rekommenderar inte att du använder programvaru-TPM: er i produktion. Mer information om typer av TPM: er finns i [en kort introduktion till TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Den här artikeln är endast relevant för enheter som använder TPM 2,0 med stöd för HMAC-nycklar och deras bekräftelse nycklar. Det är inte för enheter som använder X. 509-certifikat för autentisering. TPM är en ISO-standard som är bransch standard från Trusted Computing Group och du kan läsa mer om TPM i den [fullständiga tpm 2,0-specifikationen](https://trustedcomputinggroup.org/tpm-library-specification/) eller [ISO/IEC 11889-specifikationen](https://www.iso.org/standard/66510.html). Den här artikeln förutsätter också att du är van vid offentliga och privata nyckel par och hur de används för kryptering.

Enhets Provisioning service-enhetens SDK: er hanterar allt som beskrivs i den här artikeln. Du behöver inte implementera något ytterligare om du använder SDK: er på dina enheter. Den här artikeln hjälper dig att förstå vad som händer med ditt TPM-säkerhetschip när enhets bestämmelserna och varför det är säkert.

## <a name="overview"></a>Översikt

TPM: er använder något som kallas bekräftelse nyckel (EK) som säker rot för förtroendet. EK är unikt för TPM: en och ändrar den så att den ändrar enheten i en ny.

Det finns en annan typ av nyckel som används av TPM, som kallas lagrings rot nyckel (SRK). En SRK-enhet kan genereras av TPM: s ägare när den tar ägarskap över TPM: en. Att bli ägare till TPM är det TPM-specificerade sättet att säga "någon anger ett lösen ord på HSM." Om en TPM-enhet säljs till en ny ägare kan den nya ägaren bli ägare till TPM: en för att generera en ny SRK. Den nya SRK-genereringen säkerställer att den tidigare ägaren inte kan använda TPM. Eftersom SRK är unik för TPM-ägaren, kan SRK användas för att försegla data till själva TPM: en för den ägaren. SRK ger en Sand låda för ägaren att lagra sina nycklar och ger åtkomst till revocability om enheten eller TPM: en säljs. Det är som att flytta till ett nytt hus: När ägarskapet ändras ändras lås på dörrarna och alla möbler som ägs av tidigare ägare (SRK) tas bort, men du kan inte ändra adressen för huset (EK).

När en enhet har kon figurer ATS och är redo att användas har den både en EK och en SRK tillgänglig för användning.

![Bli ägare till en TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

En kommentar om att bli ägare till TPM: en är beroende av många saker, inklusive TPM-tillverkare, den uppsättning TPM-verktyg som används och enhetens operativ system. Följ de anvisningar som är relevanta för ditt system för att bli ägare.

Enhets etablerings tjänsten använder den offentliga delen av EK (EK_pub) för att identifiera och registrera enheter. Enhets leverantören kan läsa EK_pub under tillverkning eller slutlig testning och ladda upp EK_pub till etablerings tjänsten så att enheten kan identifieras när den ansluter till etableringen. Enhets etablerings tjänsten kontrollerar inte SRK eller ägaren, så "rensar" TPM: en tar bort kund information, men EK (och annan leverantörs information) bevaras och enheten kommer fortfarande att identifieras av enhets etablerings tjänsten när den ansluter till etableringen.

## <a name="detailed-attestation-process"></a>Detaljerad attesterings process

När en enhet med en TPM ansluter först till enhets etablerings tjänsten, kontrollerar tjänsten först EK_pub mot EK_pub som lagras i registrerings listan. Om EK_pubs inte matchar, kan enheten inte etableras. Om EK_pubs matchar, kräver tjänsten att enheten försäkrar ägarskapet av den privata delen av EK via en nonce-utmaning, som är en säker utmaning som används för att bevisa identiteten. Enhets etablerings tjänsten genererar ett nonce och krypterar den sedan med SRK och sedan EK_pub, som anges av enheten under det första registrerings anropet. TPM: en behåller alltid den privata delen av den säkra EK-delen. Detta förhindrar förfalskning och säkerställer att SAS-token allokeras säkert till auktoriserade enheter.

Låt oss gå igenom attesterings processen i detalj.

### <a name="device-requests-an-iot-hub-assignment"></a>Enhet begär en IoT Hub tilldelning

Först enheten ansluter till enhets etablerings tjänsten och begär Anden om att etablera. Då tillhandahåller enheten tjänsten med sitt registrerings-ID, ett ID-omfång och EK_pub och SRK_pub från TPM: en. Tjänsten skickar den krypterade nonce tillbaka till enheten och ber enheten att dekryptera nonce och använda det för att signera en SAS-token för att ansluta igen och slutföra etableringen.

![Etablering av enhets begär Anden](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Nonce-utmaning

Enheten tar med nonce och använder de privata delarna av EK och SRK för att dekryptera nonce i TPM: en. ordningen för nonce-kryptering delegerar förtroendet från EK, som är oföränderligt, till SRK, som kan ändra om en ny ägare tar ägande av TPM: en.

![Dekrypterar nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Verifiera nonce och ta emot autentiseringsuppgifter

Enheten kan sedan signera en SAS-token med hjälp av dekrypterad nonce och återupprätta en anslutning till enhets etablerings tjänsten med hjälp av den signerade SAS-token. När nonce-utmaningen har slutförts tillåter tjänsten att enheten etablerar.

![Enheten återupprättar anslutningen till enhets etablerings tjänsten för att validera EK-ägande](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Nästa steg

Enheten ansluter nu till IoT Hub och du är säker på att dina enhets nycklar lagras på ett säkert sätt. Nu när du vet hur enhets etablerings tjänsten verifierar en enhets identitet på ett säkert sätt med hjälp av TPM kan du läsa mer i följande artiklar:

* [Lär dig mer om begreppen etablering](about-iot-dps.md#provisioning-process)
* [Kom igång med automatisk etablering](./quick-setup-auto-provision.md) med hjälp av SDK: er för att ta hand om flödet.
