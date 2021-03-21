---
title: Integrerings mönster för smarta kontrakt – Azure blockchain Workbench
description: Översikt över Smart kontrakts integrerings mönster i Azure blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: dae63e16356e825d3be31380df1648749e59d8bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015510"
---
# <a name="smart-contract-integration-patterns"></a>Integreringsmönster för smart kontrakt

Smarta kontrakt representerar ofta ett affärs arbets flöde som behöver integreras med externa system och enheter.

Kraven för dessa arbets flöden är ett behov av att initiera transaktioner i en distribuerad redovisning som innehåller data från ett externt system, en tjänst eller en enhet. De måste också ha externa system som reagerar på händelser som härstammar från smarta kontrakt i en distribuerad redovisning.

REST API-och meddelande integrering skickar transaktioner från externa system till smarta kontrakt som ingår i ett Azure blockchain Workbench-program. Den skickar även händelse meddelanden till externa system baserat på ändringar som sker i ett program.

För data integrerings scenarier innehåller Azure blockchain Workbench en uppsättning databasvyer som sammanfogar en kombination av transaktions data från blockchain och meta-data om program och smarta kontrakt.

Dessutom kan vissa scenarier, till exempel de som är relaterade till leverans kedja eller medier, även kräva integrering av dokument. Azure blockchain Workbench tillhandahåller inte API-anrop för att hantera dokument direkt, men dokument kan införlivas i ett blockchain program. Det här avsnittet innehåller också det mönstret.

Det här avsnittet innehåller de mönster som identifieras för att implementera var och en av dessa typer av integreringar i slut punkt till slut punkt-lösningar.

## <a name="rest-api-based-integration"></a>REST API-baserad integrering

Funktioner i Azure blockchain Workbench-genererade webb programmet exponeras via REST API. Funktionerna omfattar Azure blockchain Workbench-överföring, konfiguration och administration av program, överföring av transaktioner till en distribuerad redovisning och frågor om programmetadata och redovisnings data.

REST API används främst för interaktiva klienter som webb-, mobil-och bot-program.

Det här avsnittet tittar på mönster som fokuserar på de aspekter av REST API som skickar transaktioner till en distribuerad redovisning och mönster som frågar data om transaktioner från Azure blockchain Workbench från *kedje* databasen.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Skicka transaktioner till en distribuerad redovisning från ett externt system

Azure blockchain Workbench-REST API skickar autentiserade begär Anden för att köra transaktioner i en distribuerad redovisning.

![Skicka transaktioner till en distribuerad redovisning](./media/integration-patterns/send-transactions-ledger.png)

Körning av transaktioner sker med hjälp av den process som illustrerades tidigare, där:

-   Det externa programmet autentiseras för Azure Active Directory som tillhandahålls som en del av Azure blockchain Workbench-distributionen.
-   Auktoriserade användare får en Bearer-token som kan skickas med begär anden till API: et.
-   Externa program anropar REST API med hjälp av Bearer-token.
-   REST API skickar begäran som ett meddelande och skickar den till Service Bus. Härifrån hämtas, signeras och skickas till rätt distribuerad redovisning.
-   REST API skickar en begäran till Azure blockchain Workbench SQL DB för att registrera begäran och upprätta den aktuella etablerings statusen.
-   SQL DB Returnerar etablerings statusen och API-anropet returnerar ID: t till det externa program som anropade det.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Fråga blockchain Workbench-metadata och distribuerade redovisnings transaktioner

Azure blockchain Workbench-REST API skickar autentiserade begär Anden för att fråga efter information som rör körning av smarta kontrakt i en distribuerad redovisning.

![Frågar metadata](./media/integration-patterns/querying-metadata.png)

Frågan sker med hjälp av den process som visas tidigare, där:

1. Det externa programmet autentiseras för Azure Active Directory som tillhandahålls som en del av Azure blockchain Workbench-distributionen.
2. Auktoriserade användare får en Bearer-token som kan skickas med begär anden till API: et.
3. Externa program anropar REST API med hjälp av Bearer-token.
4. REST API skickar frågor till data för begäran från SQL DB och returnerar den till klienten.

## <a name="messaging-integration"></a>Meddelande integrering

Meddelande integrering underlättar interaktion med system, tjänster och enheter där det inte är möjligt att använda interaktiva inloggningar eller är önskvärda. Meddelande integrering fokuserar på två typer av meddelanden: meddelanden som begär transaktioner utförs i en distribuerad redovisning och händelser som exponeras av redovisningen när transaktioner har ägt rum.

Meddelande integrering fokuserar på att köra och övervaka transaktioner som rör skapande av användare, kontrakts skapande och körning av transaktioner i kontrakt och används främst av *konsol* lösa backend-system.

Det här avsnittet tittar på mönster som fokuserar på de aspekter av det meddelandebaserade API: et som skickar transaktioner till en distribuerad redovisning och mönster som representerar händelse meddelanden som exponeras av den underliggande distribuerade redovisningen.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Enkelriktad händelse leverans från ett smart kontrakt till en händelse konsument 

I det här scenariot inträffar en händelse inom ett smart kontrakt, till exempel en tillstånds ändring eller körning av en speciell typ av transaktion. Den här händelsen skickas via en Event Grid till efterföljande konsumenter och dessa konsumenter vidtar lämpliga åtgärder.

Ett exempel på det här scenariot är att när en transaktion sker, skulle en konsument bli aviserad och kunna vidta åtgärder, till exempel registrera informationen i en SQL-databas eller Common Data Service. Det här scenariot är samma mönster som Workbench följer för att fylla i sin SQL DB- *kedja* .

Ett annat är om ett smart kontrakt övergår till ett visst tillstånd, till exempel när ett kontrakt ingår i en *OutOfCompliance*. När den här tillstånds ändringen sker kan det utlösa en avisering som skickas till en administratörs mobil telefon.

![Leverans av envägs händelser](./media/integration-patterns/one-way-event-delivery.png)

Det här scenariot inträffar med processen som visas tidigare, där:

-   Det smarta kontraktet övergår till ett nytt tillstånd och skickar en händelse till redovisningen.
-   Redovisningen tar emot och levererar evenemanget till Azure blockchain Workbench.
-   Azure blockchain Workbench prenumererar på händelser från redovisningen och tar emot händelsen.
-   Azure blockchain Workbench publicerar händelsen till prenumeranter på Event Grid.
-   Externa system prenumererar på Event Grid, använder meddelandet och vidtar lämpliga åtgärder.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>En enkelriktad händelse leverans av ett meddelande från ett externt system till ett smart kontrakt

Det finns också ett scenario som flödar från motsatt riktning. I det här fallet genereras en händelse av en sensor eller ett externt system och data från händelsen ska skickas till ett smart kontrakt.

Ett vanligt exempel är leverans av data från finansiella marknader, till exempel priser för råvaror, aktier eller obligationer till ett smart kontrakt.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Direkt leverans av ett Azure blockchain Workbench i förväntat format

Vissa program har skapats för att integreras med Azure blockchain Workbench och genererar och skickar direkt meddelanden i de förväntade formaten.

![Direkt leverans](./media/integration-patterns/direct-delivery.png)

Den här leveransen sker med processen som visas tidigare, där:

-   En händelse inträffar i ett externt system som utlöser skapandet av ett meddelande för Azure blockchain Workbench.
-   Det externa systemet har skriven kod för att skapa det här meddelandet i ett känt format och skickar det direkt till Service Bus.
-   Azure blockchain Workbench prenumererar på händelser från Service Bus och hämtar meddelandet.
-   Azure blockchain Workbench initierar ett anrop till redovisningen och skickar data från det externa systemet till ett speciellt kontrakt.
-   När meddelandet har mottagits övergår kontraktet till ett nytt tillstånd.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Leverans av ett meddelande i ett format som inte är känt för Azure blockchain Workbench

Vissa system kan inte ändras för att leverera meddelanden i de standardformat som används av Azure blockchain Workbench. I dessa fall kan befintliga mekanismer och meddelande format från dessa system ofta användas. Mer specifikt kan de egna meddelande typerna för dessa system omvandlas med hjälp av Logic Apps, Azure Functions eller annan anpassad kod som ska mappas till ett av de standard meddelande format som förväntas.

![Okänt meddelande format](./media/integration-patterns/unknown-message-format.png)

Detta inträffar med processen som visas tidigare, där:

-   En händelse inträffar i ett externt system som utlöser skapandet av ett meddelande.
-   En Logic app eller anpassad kod används för att ta emot meddelandet och omvandla det till ett standardiserat Azure blockchain Workbench-formaterat meddelande.
-   Logic-appen skickar det transformerade meddelandet direkt till Service Bus.
-   Azure blockchain Workbench prenumererar på händelser från Service Bus och hämtar meddelandet.
-   Azure blockchain Workbench initierar ett anrop till redovisningen och skickar data från det externa systemet till en speciell funktion i kontraktet.
-   Funktionen körs och ändrar vanligt vis status. Ändringen av tillstånd flyttar framåt det arbets flöde som återspeglas i det smarta kontraktet, vilket gör att andra funktioner nu kan utföras efter behov.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Över gångs kontroll till en extern process och väntar på slut för ande

Det finns scenarier där ett smart kontrakt måste stoppa intern körning och skickas vidare till en extern process. Den externa processen slutförs, skickar ett meddelande till det smarta kontraktet och körningen fortsätter sedan inom det smarta kontraktet.

#### <a name="transition-to-the-external-process"></a>Över gång till den externa processen

Det här mönstret implementeras vanligt vis med hjälp av följande metod:

-   Det smarta kontraktet övergår till ett angivet tillstånd. I det här läget kan antingen inget eller ett begränsat antal funktioner utföras tills ett externt system vidtar en önskad åtgärd.
-   Ändringen av tillstånd visas som en händelse för en underordnad konsument.
-   Den efterföljande konsumenten tar emot händelsen och utlöser extern kod körning.

![Diagrammet visar en tillstånds ändring i kontraktet som orsakar en händelse att gå till distribuerad redovisning. Blockchain Workbench hämtar sedan händelsen och publicerar den.](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Retur av kontroll från det smarta kontraktet

Beroende på möjligheten att anpassa det externa systemet kan det hända att det inte går att leverera meddelanden i något av de standard format som Azure blockchain Workbench förväntar sig. Baserat på det externa systemets möjlighet att generera ett av dessa meddelanden bestämmer du vilken av följande två retur Sök vägar som ska tas med.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Direkt leverans av ett Azure blockchain Workbench i förväntat format

![Diagrammet visar ett P I-meddelande från det externa systemet som hämtas av blockchain Workbench via Service Bus. Blockchain Workbench skickar sedan ett meddelande som en transaktion till distribuerad redovisning för agentens räkning. Den skickas till kontraktet, där den orsakar en tillstånds ändring.](./media/integration-patterns/direct-delivery.png)

I den här modellen sker kommunikationen med kontraktet och efterföljande tillstånds ändringar efter föregående process där-

-   När du har nått slut för ande eller en speciell mil stolpe i den externa kod körningen skickas en händelse till den Service Bus som är ansluten till Azure blockchain Workbench.

-   För system som inte kan anpassas direkt för att skriva ett meddelande som följer förväntningarna i API: t omvandlas det.

-   Innehållet i meddelandet är paketerat och skickas till en speciell funktion i det smarta kontraktet. Den här leveransen utförs åt användaren som är associerad med det externa systemet.

-   Funktionen körs och ändrar vanligt vis status. Ändringen av tillstånd flyttar framåt det arbets flöde som återspeglas i det smarta kontraktet, vilket gör att andra funktioner nu kan utföras efter behov.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Leverans av ett meddelande i ett format som inte är känt för Azure blockchain Workbench

![Okänt meddelande format](./media/integration-patterns/unknown-message-format.png)

I den här modellen där ett meddelande i ett standardformat inte kan skickas direkt, sker kommunikationen med kontraktet och efterföljande tillstånds ändringar efter föregående process där:

1.  När du har nått slut för ande eller en speciell mil stolpe i den externa kod körningen skickas en händelse till den Service Bus som är ansluten till Azure blockchain Workbench.
2.  En Logic app eller anpassad kod används för att ta emot meddelandet och omvandla det till ett standardiserat Azure blockchain Workbench-formaterat meddelande.
3.  Logic-appen skickar det transformerade meddelandet direkt till Service Bus.
4.  Azure blockchain Workbench prenumererar på händelser från Service Bus och hämtar meddelandet.
5.  Azure blockchain Workbench initierar ett anrop till redovisningen och skickar data från det externa systemet till ett speciellt kontrakt.
6. Innehållet i meddelandet är paketerat och skickas till en speciell funktion i det smarta kontraktet. Den här leveransen utförs åt användaren som är associerad med det externa systemet.
7.  Funktionen körs och ändrar vanligt vis status. Ändringen av tillstånd flyttar framåt det arbets flöde som återspeglas i det smarta kontraktet, vilket gör att andra funktioner nu kan utföras efter behov.

## <a name="iot-integration"></a>IoT-integrering

Ett vanligt integrations scenario är att inkludera telemetridata som hämtats från sensorer i ett smart kontrakt. Med hjälp av data som levereras av sensorer kan smarta kontrakt vidta informerade åtgärder och ändra avtalets status.

Om exempelvis en Last bil som levererar medicin hade sin temperatur Soar till 110 grader kan det påverka läkarens effektivitet och kan orsaka ett offentligt säkerhets problem om de inte upptäcks och tas bort från leverans kedjan. Om en driv rutin påskyndade sin bil till 100 km per timme kan den resulterande sensor informationen utlösa en annullering av försäkrings leverantören. Om bilen var en hyr bil kan GPS-data tyda på att driv rutinen gick utanför ett geografiskt område som omfattas av deras hyres avtal och debiterar en avgift.

Utmaningen är att dessa sensorer kan leverera data på konstant basis och det är inte lämpligt att skicka alla dessa data till ett smart kontrakt. En typisk metod är att begränsa antalet meddelanden som skickas till blockchain samtidigt som du levererar alla meddelanden till en sekundär lagrings plats. Du kan till exempel leverera meddelanden som mottagits med endast ett fast intervall, till exempel en gång per timme och när ett inneslutet värde faller utanför det avtalade intervallet för ett smart kontrakt. Genom att kontrol lera värden som ligger utanför toleranserna säkerställer du att de data som är relevanta för kontraktets affärs logik tas emot och körs. Genom att kontrol lera värdet vid intervallet bekräftar du att sensorn fortfarande rapporterar. Alla data skickas till ett sekundärt rapporterings lager för att möjliggöra bredare rapporterings-, analys-och maskin inlärning. Det kan till exempel hända att att det inte krävs en sensor läsning för GPS varje minut för ett smart kontrakt, vilket kan ge intressanta data som används i rapporter eller mappnings vägar.

På Azure-plattformen görs integreringen med enheter vanligt vis med IoT Hub. IoT Hub tillhandahåller routning av meddelanden baserat på innehåll och aktiverar den typ av funktion som beskrivs ovan.

![IoT-meddelanden](./media/integration-patterns/iot.png)

Processen visar ett mönster:

-   En enhet kommunicerar direkt eller via en fält-Gateway till IoT Hub.
-   IoT Hub tar emot meddelanden och utvärderar meddelanden mot vägar som har skapats som kontrollerar innehållet i meddelandet, till exempel. *Rapporterar sensorn en temperatur som är större än 50 grader?*
-   IoT Hub skickar meddelanden som uppfyller villkoren till ett definierat Service Bus för vägen.
-   En Logic app eller annan kod lyssnar på den Service Bus som IoT Hub har upprättat för vägen.
-   Logic app eller annan kod hämtar och transformerar meddelandet till ett känt format.
-   Det transformerade meddelandet, som nu har ett standardformat, skickas till Service Bus för Azure blockchain Workbench.
-   Azure blockchain Workbench prenumererar på händelser från Service Bus och hämtar meddelandet.
-   Azure blockchain Workbench initierar ett anrop till redovisningen och skickar data från det externa systemet till ett speciellt kontrakt.
-   När meddelandet har mottagits utvärderar kontraktet data och kan ändra tillstånd baserat på resultatet av utvärderingen, till exempel för en hög temperatur, ändra tillstånd till *inkompatibilitet*.

## <a name="data-integration"></a>Dataintegrering

Förutom REST-och meddelandebaserade API ger Azure blockchain Workbench även åtkomst till en SQL DB ifylld med program-och kontrakts-meta-data samt transaktions data från distribuerade transaktioner.

![Dataintegrering](./media/integration-patterns/data-integration.png)

Data integreringen är välkänd:

-   Azure blockchain Workbench lagrar metadata om program, arbets flöden, kontrakt och transaktioner som en del av det normala drift beteendet.
-   Externa system eller verktyg ger en eller flera dialog rutor för att under lätta insamling av information om databasen, till exempel databas server namn, databas namn, typ av autentisering, inloggnings uppgifter och vilka databasvyer som ska användas.
-   Frågor skrivs mot databasvyer för att under lätta konsumtionen av externa system, tjänster, rapportering, utvecklarverktyg och produktivitets verktyg för företag.

## <a name="storage-integration"></a>Lagrings integrering

Många scenarier kan kräva att du behöver inkludera filer som kan beskrivas. Av flera skäl är det olämpligt att skicka filer på en blockchain. I stället är en vanlig metod att utföra en kryptografisk hash (till exempel SHA-256) mot en fil och dela denna hash i en distribuerad redovisning. Att utföra en hash igen vid ett senare tillfälle bör returnera samma resultat. Om filen ändras, även om bara en bild punkt ändras i en bild, returnerar hashen ett annat värde.

![Lagrings integrering](./media/integration-patterns/storage-integration.png)

Mönstret kan implementeras där:

-   Ett externt system sparar en fil i en lagrings funktion, t. ex. Azure Storage.
-   En hash genereras med filen eller filen och associerade metadata, till exempel en identifierare för ägaren, URL: en där filen finns osv.
-   Hashen och eventuella metadata skickas till en funktion i ett smart kontrakt, till exempel *FileAdded*
-   I framtiden kan filen och meta-data hashas igen och jämföras med de värden som lagras i redovisningen.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Krav för att implementera integrations mönster med hjälp av REST API: er och meddelande-API: er

För att under lätta för ett externt system eller en enhet att samverka med det smarta kontraktet med antingen REST-eller meddelande-API: et måste följande inträffa:

1. I Azure Active Directory för konsortiet skapas ett konto som representerar det externa systemet eller enheten.
2. Ett eller flera av de smarta kontrakten för ditt Azure blockchain Workbench-program har funktioner definierade för att acceptera händelserna från ditt externa system eller din enhet.
3. Program konfigurations filen för ditt smarta kontrakt innehåller rollen som systemet eller enheten är tilldelad.
4. Programmets konfigurations fil för ditt smarta kontrakt anger i vilka tillstånd den här funktionen anropas av den definierade rollen.
5. Program konfigurations filen och dess smarta kontrakt överförs till Azure blockchain Workbench.

När programmet har laddats upp tilldelas Azure Active Directorys kontot för det externa systemet kontraktet och den associerade rollen.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testa externa system integrerings flöden innan du skriver integrations kod 

Integrering med externa system är ett nyckel krav i många scenarier. Det är önskvärt att kunna verifiera design av Smart kontrakt tidigare eller parallellt med kod utveckling för att integrera med externa system.

Användningen av Azure Active Directory (Azure AD) kan avsevärt påskynda produktiviteten för utvecklare och tid till värde. Mer specifikt kan kod integreringen med ett externt system ta en icke-trivial tids mängd. Genom att använda Azure AD och den automatiska generationen av UX av Azure blockchain Workbench kan du tillåta utvecklare att logga in på blockchain Workbench som det externa systemet och fylla i värden från det externa systemet via UX. Du kan snabbt utveckla och validera idéer i en koncept bevis miljö innan integrations koden skrivs för de externa systemen.
