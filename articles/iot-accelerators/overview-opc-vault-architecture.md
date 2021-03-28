---
title: OPC valv arkitektur – Azure | Microsoft Docs
description: Arkitektur för certifikat hanterings tjänst för OPC Vault
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: eb558d967ad657d14158684fba92b13979ea5fe2
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646932"
---
# <a name="opc-vault-architecture"></a>Arkitektur för OPC Vault

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Den här artikeln ger en översikt över mikrotjänsten OPC Vault och OPC-valvet IoT Edge.

OPC UA-program använder program instans certifikat för att tillhandahålla säkerhet på program nivå. En säker anslutning upprättas med hjälp av asymmetrisk kryptering, för vilken program certifikaten tillhandahåller det offentliga och privata nyckel paret. Certifikaten kan vara självsignerade eller signerade av en certifikat utfärdare (CA).

Ett OPC UA-program innehåller en lista över betrodda certifikat som representerar de program som är betrodda. Dessa certifikat kan vara självsignerade eller signerade av en certifikat utfärdare eller vara rot certifikat utfärdare eller en under certifikat utfärdare själva. Om ett betrott certifikat är en del av en större certifikat kedja, litar programmet på alla certifikat som kedjar upp till certifikatet i listan över betrodda certifikat. Detta är sant så länge den fullständiga certifikat kedjan kan verifieras.

Den största skillnaden mellan att lita på självsignerade certifikat och att lita på ett CA-certifikat är den installations ansträngning som krävs för att distribuera och upprätthålla förtroende. Det finns också ytterligare arbete för att vara värd för en företagsspecifik CA. 

Om du vill distribuera förtroende för självsignerade certifikat för flera servrar med ett enda klient program måste du installera alla Server program certifikat i listan över betrodda klient program. Dessutom måste du installera klient program certifikatet på alla listor över betrodda Server program. Den här administrativa ansträngningen är mycket betungande och ökar när du måste ta hänsyn till certifikat livs längd och förnya certifikat.

Användningen av en företagsspecifik certifikat utfärdare kan avsevärt förenkla hanteringen av förtroende med flera servrar och klienter. I det här fallet genererar administratören ett CA signerat program instans certifikat en gång för varje klient och server som används. Dessutom installeras CA-certifikatet i varje program förtroende lista på alla servrar och klienter. Med den här metoden behöver endast utgångna certifikat förnyas och ersättas för de program som påverkas.

Azure-tjänsten för industriella OPC UA-certifikat hjälper dig att hantera en företagsspecifik certifikat utfärdare för OPC UA-program. Den här tjänsten bygger på mikrotjänsten OPC Vault. OPC-valvet tillhandahåller en mikrotjänst som är värd för en företagsspecifik CA i ett säkert moln. Den här lösningen backas upp av tjänster som skyddas av Azure Active Directory (Azure AD), Azure Key Vault med HSM: er (Hardware Security modules), Azure Cosmos DB och alternativt IoT Hub som ett program arkiv.

Mikrotjänsten i OPC Vault har utformats för att stödja rollbaserade arbets flöden, där säkerhets administratörer och god kännare med signerings rättigheter i Azure Key Vault Godkänn eller avvisa begär Anden.

För att vara kompatibel med befintliga OPC UA-lösningar inkluderar tjänsterna stöd för en OPC Vault-modul för mikrotjänster. Detta implementerar **OPC UA global Discovery Server and Certificate Management** Interface, för att distribuera certifikat och betrodda listor enligt del 12 i specifikationen. 


## <a name="architecture"></a>Arkitektur

Arkitekturen baseras på OPC Vault mikrotjänst, med en OPC-valv IoT Edge modul för fabriks nätverket och ett UX-exempel för att styra arbets flödet:

![Diagram över arkitekturen för OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Mikrotjänst för OPC Vault

Mikrotjänsten OPC Vault består av följande gränssnitt för att implementera arbets flödet för att distribuera och hantera en företagsspecifik certifikat utfärdare för OPC UA-program.

### <a name="application"></a>Program 
- Ett OPC UA-program kan vara en server eller en klient, eller både och. OPC-valvet hanterar i detta fall en program registrerings utfärdare. 
- Förutom de grundläggande åtgärderna för att registrera, uppdatera och avregistrera program finns det också gränssnitt för att hitta och fråga efter program med Sök uttryck. 
- Certifikat begär Anden måste referera till ett giltigt program för att kunna bearbeta en begäran och utfärda ett signerat certifikat med alla OPC UA-specifika tillägg. 
- Program tjänsten backas upp av en databas i Azure Cosmos DB.

### <a name="certificate-group"></a>Certifikat grupp
- En certifikat grupp är en entitet som lagrar en rot certifikat utfärdare eller ett certifikat från en certifikat UTFÄRDAre, inklusive den privata nyckeln för att signera certifikat. 
- RSA-nyckelns längd, SHA-2-hash-längd och livstider kan konfigureras för både utfärdare och signerade program certifikat. 
- Du lagrar CA-certifikaten i Azure Key Vault, med FIPS 140-2-nivå 2-HSM. Den privata nyckeln lämnar aldrig det skyddade lagrings utrymmet, eftersom signeringen görs av en Key Vault-åtgärd som skyddas av Azure AD. 
- Du kan förnya CA-certifikaten med tiden och ha dem kvar i säkert lagrings utrymme på grund av Key Vault historik. 
- Listan över återkallade certifikat för varje CA-certifikat lagras också i Key Vault som en hemlighet. När ett program avregistreras, återkallas även program certifikatet i listan över återkallade certifikat (CRL) av en administratör.
- Du kan återkalla enstaka certifikat, samt batch-certifikat.

### <a name="certificate-request"></a>Certifikatbegäran
En certifikatbegäran implementerar arbets flödet för att generera ett nytt nyckel par eller signerat certifikat med hjälp av en certifikat signerings förfrågan (CSR) för ett OPC UA-program. 
- Begäran lagras i en databas med tillhör ande information, t. ex. ämnet eller CSR, och en referens till OPC UA-programmet. 
- Affärs logiken i tjänsten verifierar begäran mot den information som lagras i program databasen. Till exempel måste program-URI: n i databasen matcha program-URI i CSR.
- En säkerhets administratör med signerings rättigheter (det vill säga rollen god kännare) godkänner eller avvisar begäran. Om begäran godkänns genereras ett nytt nyckel par eller signerat certifikat (eller båda). Den nya privata nyckeln lagras på ett säkert sätt i Key Vault och det nya signerade offentliga certifikatet lagras i databasen för certifikatbegäran.
- Beställaren kan avsöka status för begäran tills den godkänns eller återkallas. Om begäran har godkänts kan den privata nyckeln och certifikatet hämtas och installeras i certifikat arkivet för OPC UA-programmet.
- Beställaren kan nu acceptera begäran om att ta bort onödig information från begär ande databasen. 

Under ett signerat certifikats livs längd kan ett program tas bort eller en nyckel kan bli komprometterad. I sådana fall kan CA Manager:
- Ta bort ett program, vilket även tar bort alla väntande och godkända certifikat förfrågningar för appen. 
- Ta bara bort en enskild certifikat förfrågan, om bara en nyckel förnyas eller komprometteras.

Nu har komprometterats godkända och godkända certifikat begär Anden marker ATS som borttagna.

En ansvarig kan regelbundet förnya utfärdares CRL för certifikat utfärdare. Vid förnyelse tiden återkallas alla borttagna certifikat begär Anden och certifikat serie numren läggs till i listan över återkallade certifikat. Återkallade certifikat begär Anden markeras som återkallade. I brådskande händelser kan enskilda certifikat begär Anden också återkallas.

Slutligen är de uppdaterade listorna över återkallade certifikat tillgängliga för distribution till de deltagande OPC UA-klienter och-servrar.

## <a name="opc-vault-iot-edge-module"></a>OPC-valv IoT Edge modul
Om du vill ha stöd för en global identifierings Server för ett fabriks nätverk kan du distribuera OPC Vault-modulen på gränsen. Kör det som ett lokalt .NET Core-program eller starta det i en Docker-behållare. Observera att på grund av brist på stöd för Auth2 i den aktuella OPC UA .NET-standardstacken är funktionerna i OPC Vault Edge-modulen begränsad till en läsar roll. En användare kan inte personifieras från Edge-modulen till mikrotjänsten med hjälp av OPC UA GDS standard-gränssnittet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om OPC Vault-arkitekturen kan du:

> [!div class="nextstepaction"]
> [Bygg och distribuera OPC-valv](howto-opc-vault-deploy.md)
