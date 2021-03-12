---
title: Vanliga frågor och svar om FHIR-tjänster i Azure – Azure API för FHIR
description: 'Få svar på vanliga frågor om Azure API för FHIR, till exempel lagrings platsen för data bakom FHIR-API: er och versions stöd.'
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 22d9df80bce6e79f0058567f8df76935e431e598
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020561"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Vanliga frågor och svar om Azure API för FHIR

## <a name="azure-api-for-fhir-the-basics"></a>Azure API för FHIR: grunderna

### <a name="what-is-fhir"></a>Vad är FHIR?
Resurserna för snabb sjukvårds samverkan (FHIR-uttalad "brand") är en driftskompatibilitet som är avsedd att möjliggöra utbyte av sjukvårds data mellan olika hälso system. Den här standarden har utvecklats av HL7-organisationen och antas av sjukvårds organisationer runtom i världen. Den mest aktuella versionen av FHIR som är tillgänglig är R4 (version 4). Azure API för FHIR stöder R4 och har även stöd för den tidigare versionen STU3 (standard för utvärderings version 3). Mer information om FHIR finns på [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Är data bakom FHIR-API: er lagrade i Azure?

Ja, data lagras i hanterade databaser i Azure. Azure API för FHIR ger inte direkt åtkomst till det underliggande data lagret.

### <a name="what-identity-provider-do-you-support"></a>Vilken identitetsprovider stöder du?

Vi stöder för närvarande Microsoft Azure Active Directory som identitets leverantör.

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>Vad är återställnings punkt målet för Azure API för FHIR?
Azure API för FHIR backas upp av Cosmos DB som vår beständiga Provider. Därför är återställningen för tjänsten lika med [Cosmos dB (en region)](../../cosmos-db/consistency-levels.md) och är < 240 minuter.

### <a name="what-fhir-version-do-you-support"></a>Vilken FHIR-version har du stöd för?

Vi stöder versioner av 4.0.0 och 3.0.1 för både Azure API för FHIR (PaaS) och FHIR Server för Azure (öppen källkod).

Mer information finns i [funktioner som stöds](fhir-features-supported.md). Läs om vad som har ändrats mellan FHIR-versioner (t. ex. STU3 till R4) i [versions historiken för HL7 FHIR](https://hl7.org/fhir/R4/history.html).

Azure IoT Connector för FHIR (för hands version) stöder för närvarande endast FHIR-R4 och är bara synlig på R4-instanser av Azure API för FHIR.

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Vad är skillnaden mellan "Microsoft FHIR Server for Azure" och "Azure API for FHIR"?

Azure API för FHIR är en värdbaserad och hanterad version av Microsoft FHIR-servern med öppen källkod för Azure. I den hanterade tjänsten tillhandahåller Microsoft alla underhåll och uppdateringar. 

När du kör FHIR-servern för Azure har du direkt åtkomst till de underliggande tjänsterna, men ansvarar för att underhålla och uppdatera servern och all nödvändig kompatibilitet om du lagrar PHI-data.

För en utvecklings synpunkt distribueras alla funktioner som inte bara gäller för den hanterade tjänsten först till Microsoft FHIR-servern med öppen källkod för Azure. När den har verifierats i öppen källkod kommer den att lanseras till PaaS Azure API för FHIR-lösningen. Tiden mellan versionen i öppen källkod och PaaS beror på komplexiteten i funktionen och andra prioriteringar för prioriteringar. Detta är samma process för alla våra tjänster, till exempel Azure IoT Connector för FHIR (för hands version).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Var kan jag se vad som släpps i Azure API för FHIR?

Om du vill se en del av vad som är lanserat i Azure API för FHIR, se [lanseringen](https://github.com/microsoft/fhir-server/releases) av FHIR-servern med öppen källkod. Från och med november 2020 har vi märkt objekt med Azure-API-for-FHIR om objektet med öppen källkod kommer att släppas till den hanterade tjänsten. Dessa funktioner är vanligt vis tillgängliga två veckor efter att de finns på sidan release i öppen källkod. Vi har också inkluderat instruktioner om hur du testar bygget [här] ( https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) om du vill testa i din egen miljö. Vi utvärderar hur du bäst delar ytterligare hanterade tjänst uppdateringar.

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>I vilka regioner är Azure API för FHIR tillgängligt?

För närvarande har vi allmän tillgänglighet för både offentlig och myndighets i [flera geo-regioner](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia). Information om statliga moln tjänster på Microsoft finns i Azure- [tjänster av FedRAMP](../../azure-government/compliance/azure-services-in-fedramp-auditscope.md).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Var kan jag se vad som släpps i Azure API för FHIR?

Om du vill se en del av vad som är lanserat i Azure API för FHIR, se [lanseringen](https://github.com/microsoft/fhir-server/releases) av FHIR-servern med öppen källkod. Vi har arbetat med att tagga objekt med Azure-API-for-FHIR om de kommer att lanseras till den hanterade tjänsten och är vanligt vis tillgängliga två veckor efter att de finns på sidan release i öppen källkod. Vi har också inkluderat instruktioner om hur du testar build [här](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) om du vill testa i din egen miljö. Vi utvärderar hur du bäst delar ytterligare hanterade tjänst uppdateringar.

### <a name="what-is-smart-on-fhir"></a>Vad är SMART på FHIR?

SMARTA (utbytbara medicinska program och återanvändbar teknik) på FHIR är en uppsättning öppna specifikationer för att integrera partner program med FHIR-servrar och andra hälso-IT-system, till exempel elektroniska hälso poster och hälso informations utbyten. Genom att skapa ett SMART on FHIR-program kan du se till att ditt program kan nås och utnyttjas av en mängd olika med olika system.
Autentisering och Azure API för FHIR. Om du vill veta mer om SMART kan du gå till [Smart hälsa](https://smarthealthit.org/).

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>Var hittar jag vilken version av FHIR som körs på databasen. 

Du kan hitta den exakta FHIR-versionen som visas i kapacitets instruktionen under egenskapen "fhirVersion".

## <a name="fhir-implementations-and-specifications"></a>FHIR-implementeringar och specifikationer

### <a name="can-i-create-a-custom-fhir-resource"></a>Kan jag skapa en anpassad FHIR-resurs?

Vi tillåter inte anpassade FHIR-resurser. Om du behöver en anpassad FHIR-resurs kan du bygga en anpassad resurs ovanpå den [grundläggande resursen](http://www.hl7.org/fhir/basic.html) med tillägg. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Stöds [tillägg](https://www.hl7.org/fhir/extensibility.html) i Azure API för FHIR?

Du kan läsa in alla giltiga FHIR JSON-data till-servern. Om du vill lagra struktur definitionen som definierar tillägget kan du spara den som en resurs för struktur definition. För närvarande kan du inte söka efter tillägg.

### <a name="what-is-the-limit-on-_count"></a>Vad är gränsen för _count?

Den aktuella gränsen för _count är 100. Om du anger _count till mer än 100 får du en varning i paketet att endast 100 poster visas.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Finns det några begränsningar för grupp export funktionen?

För grupp export exporterar vi bara inkluderade referenser från gruppen, inte alla egenskaperna för [grupp resursen](https://www.hl7.org/fhir/group.html).

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>Kan jag publicera ett paket till Azure-API: et för FHIR?

Vi stöder för närvarande publicering av [batch-paket](https://www.hl7.org/fhir/valueset-bundle-type.html) , men stöder inte bokning av transaktions paket i Azure-API: et för FHIR. Du kan använda FHIR-servern med öppen källkod som backas upp av SQL för att publicera transaktions paket.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Hur kan jag hämta alla resurser för en enda patient i Azure-API: et för FHIR?

Vi stöder [Utrymmes sökning](https://www.hl7.org/fhir/compartmentdefinition.html) i Azure API för FHIR. På så sätt kan du hämta alla resurser som är relaterade till en speciell patient. Observera att det nu finns alla resurser som är relaterade till patienten, men inte själva patienten, så du behöver även söka för att få patienten om du behöver patient resursen i resultatet.

Några exempel på detta är nedan:

* Hämta patient/<id>/*
* Hämta patient- <id> /observation
* Hämta patient- <id> /observation? Code = 8302-2

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>Vad är standard sortering vid sökning efter resurser i Azure API för FHIR?

Vi stöder sortering efter datumet då uppdateringen senast uppdaterades: _sort = _lastUpdated. Mer information om andra Sök parametrar som stöds finns på [sidan funktioner som stöds](fhir-features-supported.md#search).

### <a name="does-the-azure-api-for-fhir-support-everything"></a>Stöder Azure API för FHIR $everything? 

Nej. För tillfället har vi inte stöd för $everything. Det kan dock uppnås med två API-anrop. Om du till exempel vill hämta patient $ allt kan du först ta tag i patient posten med/patient/[ID] och sedan ett andra anrop för att hämta alla patient data med/patient/[ID]/*.

Du kan se mer information i det här [Community-inlägget](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/.24everything.20with.20_type). 

### <a name="how-does-export-work"></a>Hur fungerar $export?

$export är en del av FHIR-specifikationen: https://hl7.org/fhir/uv/bulkdata/export/index.html . Om FHIR-tjänsten har kon figurer ATS med en hanterad identitet och ett lagrings konto, och om den hanterade identiteten har åtkomst till det lagrings kontot, kan du bara anropa $export i FHIR-API: et och alla FHIR-resurser kommer att exporteras till lagrings kontot. Mer information finns [i vår artikel om $export](export-data.md).

### <a name="is-de-identified-export-available-at-patient-and-group-level-as-well"></a>Är de avidentifierade exporterna tillgängliga på patient-och grupp nivå också?
Anonymiserats-export stöds för närvarande endast på en fullständig system export (/$export) och inte för export av patienter (/patient/$export). Vi arbetar på att göra det även tillgängligt på patient nivå.

## <a name="using-azure-api-for-fhir"></a>Använda Azure API för FHIR

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>Hur gör jag för att aktivera Log Analytics för Azure API för FHIR?

Vi aktiverar diagnostikloggning och gör det möjligt att granska exempel frågor för de här loggarna. Mer information om hur du aktiverar gransknings loggar och exempel frågor finns i [det här avsnittet](enable-diagnostic-logging.md). Om du vill inkludera ytterligare information i loggarna kan du kolla in [med anpassade HTTP-huvuden](use-custom-headers.md).

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Var kan jag se några exempel på hur man använder Azure API för FHIR i ett arbets flöde?

Vi har en samling referens arkitekturer som är tillgängliga på [sidan för hälso arkitekturs GitHub](https://github.com/microsoft/health-architectures).

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>Var kan jag se ett exempel på att ansluta ett webb program till Azure API för FHIR?

Vi har en [GitHub-sida för hälso arkitektur](https://aka.ms/health-architectures) som innehåller exempel program och scenarier. Den illustrerar hur du ansluter ett webb program till Azure API för FHIR.  

## <a name="azure-api-for-fhir-features-and-services"></a>Azure API för FHIR-funktioner och-tjänster 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>Finns det något sätt att kryptera mina data med min personliga nyckel inte en standard nyckel?

Ja, Azure API för FHIR gör det möjligt att konfigurera Kundhanterade nycklar, vilket ger stöd från Cosmos DB. Mer information om hur du krypterar dina data med en personlig nyckel finns i [det här avsnittet](customer-managed-key.md).

## <a name="azure-api-for-fhir-preview-features"></a>Azure API för FHIR: för hands versions funktioner

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Kan jag konfigurera skalnings kapacitet för Azure IoT Connector för FHIR (för hands version)?

Eftersom Azure IoT Connector för FHIR är kostnads fritt under en offentlig för hands version, är dess skalnings kapacitet fast och begränsad. Azure IoT Connector för FHIR-konfiguration som är tillgänglig i den offentliga för hands versionen förväntas tillhandahålla ett data flöde på cirka 200 meddelanden per sekund. Viss typ av resurs kapacitets konfiguration görs tillgänglig i allmän tillgänglighet (GA).

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Varför kan jag inte installera Azure IoT Connector för FHIR (för hands version) när en privat länk är aktive rad på Azure API för FHIR?

Azure IoT Connector för FHIR har inte stöd för funktionen för privat länk för tillfället. Om du har aktiverat privat länk i Azure API för FHIR kan du därför inte installera Azure IoT Connector för FHIR och vice versa. Den här begränsningen förväntas gå bort när Azure IoT Connector för FHIR är tillgängligt för allmän tillgänglighet (GA).
