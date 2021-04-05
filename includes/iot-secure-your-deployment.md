---
title: ta med fil
description: ta med fil
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 08cca67455df4b2d28bba0a7410fccc11446fcdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010716"
---
Den här artikeln innehåller nästa detalj nivå för att skydda Azure IoT-baserade Sakernas Internet-infrastrukturen (IoT). Den länkar till implementerings nivå information för att konfigurera och distribuera varje komponent. Den innehåller också jämförelser och val mellan olika konkurrerande metoder.

Att skydda Azure IoT-distributionen kan delas upp i följande tre säkerhets områden:

* **Enhets säkerhet**: säkra IoT-enheten när den distribueras i den.

* **Anslutnings säkerhet**: säkerställa att alla data som överförs mellan IoT-enheten och IoT Hub är konfidentiella och Manipulerings bevis.

* **Moln säkerhet**: tillhandahålla ett sätt att skydda data medan den flyttas genom och lagras i molnet.

![Tre säkerhets områden](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Skydda enhets etablering och autentisering

IoT Solution Accelerators skyddar IoT-enheter på följande två sätt:

* Genom att ange en unik identitets nyckel (säkerhetstoken) för varje enhet, som kan användas av enheten för att kommunicera med IoT Hub.

* Genom att använda ett [X. 509-certifikat](https://www.itu.int/rec/T-REC-X.509-201210-S) och en privat nyckel som ett sätt att autentisera enheten till IoT Hub. Den här autentiseringsmetoden garanterar att den privata nyckeln på enheten inte är känd utanför enheten när som helst, vilket ger en högre säkerhets nivå.

Metoden säkerhetstoken tillhandahåller autentisering för varje anrop som görs av enheten för att IoT Hub genom att associera den symmetriska nyckeln till varje anrop. X. 509-baserad autentisering tillåter autentisering av en IoT-enhet på det fysiska skiktet som en del av uppetableringen av TLS-anslutning. Den säkerhetstoken-baserade metoden kan användas utan X. 509-autentisering, vilket är ett mindre säkert mönster. Valet mellan de två metoderna styrs främst av hur säker enhetsautentisering måste vara, och tillgängligheten för säkert lagrings utrymme på enheten (för att lagra den privata nyckeln på ett säkert sätt).

## <a name="iot-hub-security-tokens"></a>IoT Hub säkerhetstoken

IoT Hub använder säkerhetstoken för att autentisera enheter och tjänster för att undvika att skicka nycklar i nätverket. Dessutom är säkerhetstoken begränsade inom giltighets tid och omfång. Azure IoT SDK: er genererar automatiskt tokens utan att kräva någon speciell konfiguration. Vissa scenarier kräver dock att användaren genererar och använder säkerhetstoken direkt. Dessa scenarier omfattar direkt användning av MQTT-, AMQP-eller HTTP-ytor, eller implementeringen av mönstret för token-tjänsten.

Mer information om säkerhets-tokens struktur och dess användning finns i följande artiklar:

* [Struktur för säkerhetstoken](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Använda SAS-token som en enhet](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Varje IoT Hub har ett [identitets register](../articles/iot-hub/iot-hub-devguide-identity-registry.md) som kan användas för att skapa resurser per enhet i tjänsten, till exempel en kö som innehåller meddelanden från moln till enhet, och för att ge åtkomst till enhets slut punkter. Registret för IoT Hub identitet ger säker lagring av enhets identiteter och säkerhets nycklar för en lösning. Enskilda eller grupper av enhets identiteter kan läggas till i en lista över tillåtna eller blockerade enheter, vilket möjliggör fullständig kontroll över enhets åtkomsten. I följande artiklar finns mer information om strukturen i identitets registret och vilka åtgärder som stöds.

[IoT Hub stöder protokoll som MQTT, AMQP och http](../articles//iot-hub/iot-hub-devguide-security.md). Vart och ett av dessa protokoll använder säkerhetstoken från IoT-enheten för att IoT Hub på olika sätt:

* AMQP: SASL PLAIN-och AMQP-baserad säkerhet ( `{policyName}@sas.root.{iothubName}` med IoT Hub-token-token-token, `{deviceId}` med enhets omfångs-token).

* MQTT: Anslut paket använder `{deviceId}` som `{ClientId}` , `{IoThubhostname}/{deviceId}` i fältet **username** och en SAS-token i fältet **lösen ord** .

* HTTP: giltig token finns i rubriken för Authorization-begäran.

IoT Hub identitets registret kan användas för att konfigurera säkerhets referenser för varje enhet och åtkomst kontroll. Men om en IoT-lösning redan har en betydande investering i ett [anpassat enhets identitets register och/eller autentiseringsschema](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication), kan den integreras i en befintlig infrastruktur med IoT Hub genom att skapa en token-tjänst.

### <a name="x509-certificate-based-device-authentication"></a>509-certifikatbaserad enhetsautentisering

Användningen av ett [enhets X. 509-certifikat](../articles/iot-hub/iot-hub-devguide-security.md) och dess associerade privata och offentliga nyckel par tillåter ytterligare autentisering i det fysiska skiktet. Den privata nyckeln lagras på ett säkert sätt i enheten och kan inte identifieras utanför enheten. X. 509-certifikatet innehåller information om enheten, till exempel enhets-ID och annan organisations information. En signatur för certifikatet genereras med hjälp av den privata nyckeln.

Enhets etablerings flöde på hög nivå:

* Koppla en identifierare till en fysisk enhet – enhets identitet och/eller X. 509-certifikat som är kopplade till enheten vid enhets tillverkning eller-provision.

* Skapa en motsvarande identitets post i IoT Hub – enhets identitet och associerad enhets information i IoT Hub identitets registret.

* Lagra säkert tumavtryck för X. 509-certifikat i IoT Hub Identity Registry.

### <a name="root-certificate-on-device"></a>Rot certifikat på enhet

När du etablerar en säker TLS-anslutning med IoT Hub autentiserar IoT-enheten IoT Hub med hjälp av ett rot certifikat som är en del av enhetens SDK. För C client SDK finns certifikatet under mappen "C-certifikat \\ \\ " under roten i lagrings platsen. Även om dessa rot certifikat är långvariga kan de fortfarande gå ut eller återkallas. Om det inte finns något sätt att uppdatera certifikatet på enheten kanske enheten inte kan ansluta till IoT Hub (eller någon annan moln tjänst). Att ha ett sätt att uppdatera rot certifikatet när IoT-enheten har distribuerats effektivt minimerar den här risken.

## <a name="securing-the-connection"></a>Skydda anslutningen

Internet anslutning mellan IoT-enheten och IoT Hub skyddas med hjälp av Transport Layer Security (TLS) standard. Azure IoT stöder [tls 1,2](https://tools.ietf.org/html/rfc5246), TLS 1,1 och TLS 1,0, i den här ordningen. Stöd för TLS 1,0 tillhandahålls endast för bakåtkompatibilitet. Kontrol lera [TLS-stöd i IoT Hub](../articles/iot-hub/iot-hub-tls-support.md) för att se hur du konfigurerar navet att använda TLS 1,2, eftersom det ger störst säkerhet.

## <a name="securing-the-cloud"></a>Skydda molnet

Med Azure IoT Hub kan du definiera [principer för åtkomst kontroll](../articles/iot-hub/iot-hub-devguide-security.md) för varje säkerhets nyckel. Den använder följande uppsättning behörigheter för att ge åtkomst till var och en av IoT Hubens slut punkter. Behörigheter begränsar åtkomsten till en IoT Hub baserat på funktioner.

* **RegistryRead**. Ger Läs behörighet till identitets registret. Mer information finns i [identitets registret](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **RegistryReadWrite**. Ger Läs-och Skriv behörighet till identitets registret. Mer information finns i [identitets registret](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **ServiceConnect**. Beviljar åtkomst till moln tjänst – riktad kommunikation och övervakning av slut punkter. Till exempel ger den behörighet till Server dels moln tjänster för att ta emot meddelanden från enheten till molnet, skicka meddelanden från moln till enhet och hämta motsvarande leverans bekräftelser.

* **DeviceConnect**. Ger åtkomst till enhets slut punkter. Den ger till exempel behörighet att skicka meddelanden från enheten till molnet och ta emot meddelanden från molnet till enheten. Den här behörigheten används av enheter.

Det finns två sätt att hämta **DeviceConnect** -behörigheter med IoT Hub med [säkerhetstoken](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app): använda en enhets identitets nyckel eller en delad åtkomst nyckel. Dessutom är det viktigt att notera att alla funktioner som är tillgängliga från enheter exponeras genom design på slut punkter med prefix `/devices/{deviceId}` .

[Tjänst komponenter kan bara skapa säkerhetstoken](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) med hjälp av principer för delad åtkomst som beviljar lämpliga behörigheter.

Azure IoT Hub och andra tjänster som kan ingå i lösningen tillåter hantering av användare med hjälp av Azure Active Directory.

Data som matas in av Azure IoT Hub kan användas av en rad olika tjänster, till exempel Azure Stream Analytics och Azure Blob Storage. Dessa tjänster tillåter hanterings åtkomst. Läs mer om de här tjänsterna och tillgängliga alternativ:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): en skalbar, fullständigt indexerad databas tjänst för halv strukturerade data som hanterar metadata för de enheter som du etablerar, till exempel attribut, konfiguration och säkerhets egenskaper. Azure Cosmos DB erbjuder data behandling med höga prestanda och hög genom strömning, schema-oberoende indexering av data och ett omfattande SQL-frågeuttryck.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): strömnings bearbetning i real tid i molnet som hjälper dig att snabbt utveckla och distribuera en analys lösning med låg kostnad för att få insikter i real tid från enheter, sensorer, infrastruktur och program. Data från den här fullständigt hanterade tjänsten kan skalas till vilken volym som helst samtidigt som stora data flöden, låga svars tider och återhämtning uppnås.

* [Azure App tjänster](https://azure.microsoft.com/services/app-service/): en moln plattform för att bygga kraftfulla webb-och mobilappar som ansluter till data överallt. i molnet eller lokalt. Utveckla intressanta mobilappar för iOS, Android och Windows. Integrera med din program vara som en tjänst (SaaS) och företags program med direkt anslutning till dussin tals molnbaserade tjänster och företags program. Koda på ditt favorit språk och IDE (.NET, Node.js, PHP, python eller Java) för att bygga webbappar och API: er snabbare än någonsin.

* [Logic Apps](https://azure.microsoft.com/services/app-service/logic/): funktionen Logic Apps i Azure App Service hjälper dig att integrera IoT-lösningen till dina befintliga affärs system och automatisera arbets flödes processer. Logic Apps gör det möjligt för utvecklare att utforma arbets flöden som startar från en utlösare och sedan köra en serie steg – regler och åtgärder som använder kraftfulla kopplingar för att integrera med dina affärs processer. Logic Apps erbjuder direkt anslutning till ett omfattande eko system med SaaS, molnbaserade och lokala program.

* [Azure Blob Storage](https://azure.microsoft.com/services/storage/): tillförlitlig, ekonomisk moln lagring för de data som dina enheter skickar till molnet.

## <a name="conclusion"></a>Slutsats

Den här artikeln innehåller en översikt över implementerings nivå information för att utforma och distribuera en IoT-infrastruktur med hjälp av Azure IoT. Att konfigurera varje komponent för att vara säker är en nyckel för att skydda den övergripande IoT-infrastrukturen. De design alternativ som är tillgängliga i Azure IoT ger viss flexibilitet och valmöjlighet. varje val kan dock påverka säkerheten. Vi rekommenderar att var och en av dessa val utvärderas genom en risk-/kostnads bedömning.
