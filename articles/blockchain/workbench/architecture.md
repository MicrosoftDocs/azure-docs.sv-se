---
title: Azure Blockchain Workbench-arkitektur
description: Översikt över Azure blockchain Workbench Preview-arkitekturen och dess komponenter.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 1fff70ef2eeb1dc27d33769fd48fe5923f56717b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021613"
---
# <a name="azure-blockchain-workbench-architecture"></a>Azure Blockchain Workbench-arkitektur

Azure blockchain Workbench Preview fören klar blockchain program utveckling genom att tillhandahålla en lösning med hjälp av flera Azure-komponenter. Blockchain Workbench kan distribueras med en lösnings mall på Azure Marketplace. Med mallen kan du välja moduler och komponenter som ska distribueras, inklusive blockchain-stack, typ av klient program och stöd för IoT-integrering. När blockchain Workbench har distribuerats ger det till gång till en webbapp, iOS-app och Android-app.

![Blockchain Workbench-arkitektur](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identitet och autentisering

Med hjälp av blockchain Workbench kan ett konsortium federera sina företags identiteter med Azure Active Directory (Azure AD). Workbench genererar nya användar konton för identiteter i kedjan med företags identiteter lagrade i Azure AD. Identitets mappningen underlättar autentiserad inloggning till klient-API: er och program och använder autentiseringsprinciper för organisationer. Workbench ger också möjlighet att associera företags identiteter till vissa roller inom ett och samma smarta kontrakt. Dessutom tillhandahåller Workbench en mekanism för att identifiera vilka åtgärder dessa roller kan utföra och vid vilken tidpunkt.

När blockchain Workbench har distribuerats interagerar användarna med blockchain Workbench via klient programmen, REST-baserade klient-API eller meddelande-API. I samtliga fall måste interaktioner autentiseras, antingen via Azure Active Directory (Azure AD) eller enhetsspecifika autentiseringsuppgifter.

Användare federerar sina identiteter till en konsortiet Azure AD genom att skicka en e-postinbjudan till deltagarna i sin e-postadress. När du loggar in autentiseras dessa användare med hjälp av namn, lösen ord och principer. Till exempel tvåfaktorautentisering i organisationen.

Azure AD används för att hantera alla användare som har åtkomst till blockchain Workbench. Varje enhet som ansluter till ett smart kontrakt är också kopplad till Azure AD.

Azure AD används också för att tilldela användare till en särskild administratörs grupp. Användare som är associerade med gruppen Administratörer beviljas åtkomst till rättigheter och åtgärder i blockchain Workbench, inklusive distribution av kontrakt och ge behörighet till en användare för att få åtkomst till ett kontrakt. Användare utanför den här gruppen har inte åtkomst till administratörs åtgärder.

## <a name="client-applications"></a>Klientprogram

Workbench tillhandahåller automatiskt genererade klient program för webb-och mobil enheter (iOS, Android) som kan användas för att validera, testa och Visa blockchain-program. Program gränssnittet genereras dynamiskt baserat på metadata för smarta kontrakt och kan användas för alla användnings fall. Klient programmen levererar en användar riktad klient del till de kompletta blockchain-programmen som genereras av blockchain Workbench. Klient program autentiserar användare via Azure Active Directory (Azure AD) och presenterar sedan en användar upplevelse som skräddarsys för det smarta kontraktets affärs sammanhang. Användar upplevelsen gör det möjligt att skapa nya smarta kontrakt instanser av auktoriserade individer och ger sedan möjlighet att köra vissa typer av transaktioner vid lämpliga tidpunkter i affärs processen som det smarta kontraktet representerar.

Behöriga användare kan komma åt Administratörskonsol i webb programmet. -Konsolen är tillgänglig för användare i administratörs gruppen i Azure AD och ger åtkomst till följande funktioner:

* Använd de smarta Microsoft-avtalen för populära scenarier. Till exempel ett scenario för till gångs överföring.
* Ladda upp och distribuera egna smarta kontrakt.
* Tilldela en användare åtkomst till det smarta kontraktet i kontexten för en speciell roll.

Mer information finns i [exempel klient programmen för Azure blockchain Workbench på GitHub](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile).

## <a name="gateway-service-api"></a>API för Gateway-tjänsten

Blockchain Workbench innehåller ett REST-baserat Gateway Service-API. När du skriver till en blockchain genererar API: et och levererar meddelanden till en händelse koordinator. När data begärs av API: et, skickas frågor till databasen utanför kedjan. Databasen innehåller en replik av data i kedjan och metadata som innehåller kontext-och konfigurations information om stödda smarta kontrakt. Frågor returnerar de data som krävs från off-Chain-replikeringen i ett format som informeras av kontraktets metadata.

Utvecklare kan komma åt Gateway Service API för att bygga eller integrera blockchain-lösningar utan att behöva lita på blockchain Workbench-klientprogram.

> [!NOTE]
> För att aktivera autentiserad åtkomst till API: n, registreras två klient program i Azure Active Directory. Azure Active Directory kräver distinkta program registreringar varje program typ (inbyggd och webb).

## <a name="message-broker-for-incoming-messages"></a>Meddelande koordinator för inkommande meddelanden

Utvecklare som vill skicka meddelanden direkt till blockchain Workbench kan skicka meddelanden direkt till Service Bus. Meddelande-API kan till exempel användas för system-till-system-integrering eller IoT-enheter.

## <a name="message-broker-for-downstream-consumers"></a>Message Broker för efterföljande konsumenter

Händelser inträffar under programmets livs cykel. Händelser kan utlösas av Gateway-API: t eller i redovisningen. Händelse aviseringar kan initiera efterföljande kod baserat på händelsen.

Blockchain Workbench distribuerar automatiskt två typer av händelse konsumenter. En konsument utlöses av blockchain-händelser för att fylla i SQL-arkivet utanför kedjan. Den andra konsumenten är att avbilda metadata för händelser som genererats av API: n som rör överföring och lagring av dokument.

## <a name="message-consumers"></a>Meddelande konsumenter

 Meddelande konsumenter tar meddelanden från Service Bus. Den underliggande händelse modellen för meddelande konsumenter tillåter tillägg av ytterligare tjänster och system. Du kan till exempel lägga till stöd för att fylla i CosmosDB eller utvärdera meddelanden med Azure streaming Analytics. I följande avsnitt beskrivs de meddelande konsumenter som ingår i blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Distribuerad redovisnings konsument

DLT-meddelanden (Distributed Ledger Technology) innehåller metadata för transaktioner som ska skrivas till blockchain. Klienten hämtar meddelanden och skickar data till ett transaktions verktyg, en undertecknare och en router.

### <a name="database-consumer"></a>Databas konsument

Databas konsumenten tar meddelanden från Service Bus och skickar data till en bifogad databas, till exempel en databas i Azure SQL Database.

### <a name="storage-consumer"></a>Lagrings konsument

Lagrings konsumenten tar meddelanden från Service Bus och skickar data till en ansluten lagrings plats. Lagra till exempel hash-dokument i Azure Storage.

## <a name="transaction-builder-and-signer"></a>Transaction Builder och undertecknare

Om ett meddelande i den inkommande meddelande Broker måste skrivas till blockchain bearbetas det av DLT-klienten. DLT-konsumenten är en tjänst som hämtar meddelandet som innehåller metadata för en önskad transaktion att köra och skickar informationen till *transaktions-och inloggnings verktyget*. *Transaktions byggare och-signeraren* monterar en blockchain-transaktion baserat på data och önskat blockchain-mål. När den har monterats signeras transaktionen. Privata nycklar lagras i Azure Key Vault.

 Blockchain Workbench hämtar lämplig privat nyckel från Key Vault och signerar transaktionen utanför Key Vault. När den har signerats skickas transaktionen till transaktionsspecifikationer och-redovisningar.

## <a name="transaction-routers-and-ledgers"></a>Transaktionsspecifikationer och-redovisningar

Transaktionsspecifikationer och redovisningar tar signerade transaktioner och dirigerar dem till lämplig blockchain. För närvarande stöder blockchain Workbench Ethereum som mål blockchain.

## <a name="dlt-watcher"></a>DLT-övervakare

En fördelad Ledger-teknik (DLT) övervakar händelser som inträffar på block kedjor som är kopplade till blockchain Workbench.
Händelser återspeglar information som är relevant för enskilda användare och system. Till exempel skapas nya kontrakt instanser, körning av transaktioner och tillstånds ändringar. Händelserna samlas in och skickas till den utgående meddelande utjämningen så att de kan användas av efterföljande konsumenter.

SQL-klienten övervakar till exempel händelser, använder dem och fyller i databasen med de värden som ingår. Kopian gör det möjligt att återställa en replik av data i kedjan i en lagrings kedja.

## <a name="azure-sql-database"></a>Azure SQL Database

Databasen som är kopplad till blockchain Workbench lagrar kontrakts definitioner, konfigurations-metadata och en SQL-tillgänglig replik av data som lagras i blockchain. Dessa data kan enkelt frågas, visualiseras eller analyseras genom direkt åtkomst till databasen. Utvecklare och andra användare kan använda databasen för rapportering, analys eller andra datainriktade integreringar. Användare kan till exempel visualisera transaktions data med Power BI.

Den här lagrings kedjan ger företags organisationer möjlighet att fråga data i SQL i stället för en blockchain-redovisning. Genom att standardisera ett standard schema som är oberoende av blockchain Technology-stackar, kan du också använda lagrings kedjan för att återanvända rapporter och andra artefakter i projekt, scenarier och organisationer.

## <a name="azure-storage"></a>Azure Storage

Azure Storage används för att lagra kontrakt och metadata som är kopplade till kontrakt.

Från inköps order och frakt sedlar till bilder som används i Nyheter och medicinska bilder till video som kommer från en Continuum, inklusive roll kameror för hälso deklarationer och större rörliga bilder, spelar dokument en roll i många blockchain-koncentriska scenarier. Dokument är inte lämpliga för att placeras direkt på blockchain.

Blockchain Workbench har stöd för möjligheten att lägga till dokument eller annat medie innehåll med blockchain affärs logik. En hash av dokumentet eller medie innehållet lagras i blockchain och det faktiska dokumentet eller medie innehållet lagras i Azure Storage. Den associerade transaktions informationen levereras till den inkommande meddelande utjämningen, paketerad, signerad och dirigerad till blockchain. Den här processen utlöser händelser som delas via utgående meddelande Broker. SQL DB använder den här informationen och skickar den till databasen för senare frågor. Underordnade system kan också använda dessa händelser för att vidta lämpliga åtgärder.

## <a name="monitoring"></a>Övervakning

Workbench tillhandahåller program loggning med hjälp av Application Insights och Azure Monitor. Application Insights används för att lagra all loggad information från blockchain Workbench och innehåller fel, varningar och lyckade åtgärder. Application Insights kan användas av utvecklare för att felsöka problem med blockchain Workbench. 

Azure Monitor innehåller information om hälso tillståndet för blockchain-nätverket. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera Azure Blockchain Workbench](./deploy.md)
