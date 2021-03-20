---
title: Skydda kommunikationen av OPC UA-enheter med OPC Vault – Azure | Microsoft Docs
description: Så här registrerar du OPC UA-program och hur du utfärdar signerade program certifikat för dina OPC UA-enheter med OPC-valvet.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a1b7564988c8a4d63a37b53d18ed3a7359e65d72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92926418"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Använd certifikat hanterings tjänsten för OPC Vault

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Den här artikeln förklarar hur du registrerar program och hur du utfärdar signerade program certifikat för OPC UA-enheter.

## <a name="prerequisites"></a>Förutsättningar

### <a name="deploy-the-certificate-management-service"></a>Distribuera certifikat hanterings tjänsten

Distribuera först tjänsten till Azure-molnet. Mer information finns i [distribuera OPC Vault Certificate Management Service](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Skapa certifikat utfärdarens CA-certifikat

Om du inte redan har gjort det skapar du certifikat UTFÄRDARens certifikat. Mer information finns i [skapa och hantera utfärdarens certifikat för OPC-valvet](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Säkra OPC UA-program

### <a name="step-1-register-your-opc-ua-application"></a>Steg 1: registrera ditt OPC UA-program 

> [!IMPORTANT]
> Skrivar rollen krävs för att registrera ett program.

1. Öppna certifikat tjänsten på `https://myResourceGroup-app.azurewebsites.net` och logga in.
2. Gå till **Registrera ny**. För en program registrering måste användaren ha minst en tilldelad skrivar roll.
2. Post formuläret följer namngivnings konventioner i OPC UA. I följande skärm bild visas till exempel inställningarna för [OPC UA Reference Server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/Applications/ReferenceServer) som exempel i OPC UA .net-standard stack:

   ![Skärm bild av UA-referens Server registrering](media/howto-opc-vault-secure/reference-server-registration.png "Registrering av UA-referens Server")

5. Välj **Registrera** för att registrera programmet i databasen för certifikat tjänst program. Arbets flödet guidar användaren direkt till nästa steg för att begära ett signerat certifikat för programmet.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Steg 2: skydda ditt program med ett CA-signerat program certifikat

Skydda ditt OPC UA-program genom att utfärda ett signerat certifikat baserat på en certifikat signerings förfrågan (CSR). Alternativt kan du begära ett nytt nyckel par, som innehåller en ny privat nyckel i PFX-eller PEM-format. Information om vilka metoder som stöds för ditt program finns i dokumentationen för din OPC UA-enhet. I allmänhet rekommenderas CSR-metoden, eftersom den inte kräver att en privat nyckel överförs över en tråd.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Begär ett nytt certifikat med ett nytt nyckel par

1. Gå till **program**.
3. Välj **ny begäran** för ett program i listan.

   ![Skärm bild av nytt begär ande certifikat](media/howto-opc-vault-secure/request-new-certificate.png "Begär nytt certifikat")

3. Välj **Begär nytt nyckel par och certifikat** för att begära en privat nyckel och ett nytt signerat certifikat med den offentliga nyckeln för ditt program.

   ![Skärm bild av skapa ett nytt nyckel par och certifikat](media/howto-opc-vault-secure/generate-new-key-pair.png "Generera nytt nyckel par")

4. Fyll i formuläret med ett ämne och domän namnen. För den privata nyckeln väljer du PEM eller PFX med lösen ord. Välj Skapa **nytt nyckel par** för att skapa en certifikatbegäran.

   ![Skärm bild som visar skärmen Visa information om certifikat förfrågan och knappen generera nytt nyckel par.](media/howto-opc-vault-secure/approve-reject.png "Godkänn certifikat")

5. Godkännande kräver en användare med rollen god kännare och med signerings behörighet i Azure Key Vault. I det typiska arbets flödet ska rollen god kännare och beställare tilldelas olika användare. Välj **Godkänn** eller **avvisa** för att starta eller avbryta den faktiska skapandet av nyckel paret och signerings åtgärden. Det nya nyckel paret skapas och lagras på ett säkert sätt i Azure Key Vault tills det laddas ned av certifikat beställaren. Det resulterande certifikatet med offentlig nyckel signeras av CA: n. Det kan ta några sekunder att slutföra dessa åtgärder.

   ![Skärm bild av Visa information om certifikatbegäran med godkännande meddelande längst ned](media/howto-opc-vault-secure/view-key-pair.png "Visa nyckel par")

7. Den resulterande privata nyckeln (PFX eller PEM) och Certificate (DER) kan hämtas härifrån i det format som valts som binär fil hämtning. En Base64-kodad version är också tillgänglig, till exempel för att kopiera och klistra in certifikatet till en kommando rad eller text inmatning. 
8. När den privata nyckeln har hämtats och lagrats på ett säkert sätt kan du välja **ta bort privat nyckel**. Certifikatet med den offentliga nyckeln är fortfarande tillgängligt för framtida bruk.
9. På grund av användningen av ett signerat certifikat för certifikat utfärdare bör certifikat utfärdaren och listan över återkallade certifikat (CRL) också hämtas här.

Det beror nu på den OPC UA-enhet som används för att tillämpa det nya nyckel paret. Vanligt vis kopieras certifikat utfärdarens certifikat och CRL till en `trusted` mapp, medan offentliga och privata nycklar för program certifikatet används i en `own` mapp i certifikat arkivet. Vissa enheter kanske redan har stöd för Server-push för certifikat uppdateringar. Se dokumentationen för din OPC UA-enhet.

#### <a name="request-a-new-certificate-with-a-csr"></a>Begär ett nytt certifikat med en CSR 

1. Gå till **program**.
3. Välj **ny begäran** för ett program i listan.

   ![Skärm bild av nytt begär ande certifikat](media/howto-opc-vault-secure/request-new-certificate.png "Begär nytt certifikat")

3. Välj **Begär nytt certifikat med signerings förfrågan** för att begära ett nytt signerat certifikat för ditt program.

   ![Skärm bild av skapa ett nytt certifikat](media/howto-opc-vault-secure/generate-new-certificate.png "Skapa nytt certifikat")

4. Ladda upp CSR genom att välja en lokal fil eller genom att klistra in en Base64-kodad CSR i formuläret. Välj **Skapa nytt certifikat**.

   ![Skärm bild av Visa information om certifikatbegäran](media/howto-opc-vault-secure/approve-reject-csr.png "Godkänn CSR")

5. Godkännande kräver en användare med rollen god kännare och med signerings behörighet i Azure Key Vault. Välj **Godkänn** eller **avvisa** för att starta eller avbryta den faktiska signerings åtgärden. Det resulterande certifikatet med offentlig nyckel signeras av CA: n. Den här åtgärden kan ta några sekunder att slutföra.

   ![Skärm bild som visar information om Visa certifikat förfrågningar och innehåller ett godkännande meddelande längst ned.](media/howto-opc-vault-secure/view-cert-csr.png "Visa certifikat")

6. Det resulterande certifikatet (DER) kan laddas ned härifrån som en binär fil. En Base64-kodad version är också tillgänglig, till exempel för att kopiera och klistra in certifikatet till en kommando rad eller text inmatning. 
10. När certifikatet har hämtats och lagrats på ett säkert sätt kan du välja **ta bort certifikat**.
11. På grund av användningen av ett signerat certifikat från en certifikat utfärdare, ska CA-certifikatet och CRL: en även hämtas här.

Det beror nu på den OPC UA-enhet som används för att tillämpa det nya certifikatet. Vanligt vis kopieras certifikat utfärdarens certifikat och CRL till en `trusted` mapp, medan program certifikatet används i en `own` mapp i certifikat arkivet. Vissa enheter kanske redan har stöd för Server-push för certifikat uppdateringar. Se dokumentationen för din OPC UA-enhet.

### <a name="step-3-device-secured"></a>Steg 3: säker enhet

OPC UA-enheten är nu redo att kommunicera med andra OPC UA-enheter som skyddas av CA-signerade certifikat, utan ytterligare konfiguration.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skyddar OPC UA-enheter kan du:

> [!div class="nextstepaction"]
> [Köra en tjänst för säker certifikat hantering](howto-opc-vault-secure-ca.md)
