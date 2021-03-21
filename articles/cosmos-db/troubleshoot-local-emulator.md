---
title: Felsöka problem när du använder Azure Cosmos DB-emulatorn
description: Lär dig hur du troubleshot-tjänsten inte är tillgänglig, certifikat, kryptering och versions problem när du använder Azure Cosmos DB-emulatorn.
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: bb07adafbc68ff1e9b7d3ee49bb7631dc4395d77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033467"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-db-emulator"></a>Felsöka problem när du använder Azure Cosmos DB-emulatorn
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB-emulatorn ger en lokal miljö som emulerar Azure Cosmos DB-tjänsten för utveckling. Använd tipsen i den här artikeln för att felsöka problem som du stöter på när du installerar eller använder Azure Cosmos DB-emulatorn. 

Om du har installerat en ny version av emulatorn och fel uppstår ska du återställa dina data. Du kan återställa dina data genom att högerklicka på ikonen Azure Cosmos DB emulator i system fältet och sedan klicka på Återställ data.... Om detta inte löser felen kan du avinstallera emulatorn och eventuella äldre versioner av emulatorn om de hittas, ta bort *C:\Program files\Azure Cosmos DB emulator* -katalogen och installera om emulatorn. I [Avinstallera den lokala emulatorn](local-emulator.md#uninstall) finns instruktioner. Om det inte fungerar att återställa data går du till `%LOCALAPPDATA%\CosmosDBEmulator` plats och tar bort mappen.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>Felsöka skadade Windows-prestandaräknare

* Om Azure Cosmos DB emulatorn kraschar samlar du in dumpfiler från `%LOCALAPPDATA%\CrashDumps` mappen, komprimerar dem och öppnar ett support ärende från [Azure Portal](https://portal.azure.com).

* Om du upplever krascher i kan `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` detta vara ett symtom där prestanda räknarna är i ett skadat tillstånd. Genom att köra följande kommando från en administratörs kommando tolk åtgärdar du problemet:

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>Felsöka anslutningsproblem

* Om du stöter på problem med anslutningen [samlar du in spårningsfiler](#trace-files), komprimerar dem och öppnar ett support ärende i [Azure Portal](https://portal.azure.com).

* Om du ser ett meddelande om att **tjänsten inte är tillgänglig** kanske emulatorn misslyckas med att initiera nätverksstacken. Se efter om du har Pulse Secure-klienten eller Juniper-nätverksklienten installerad, eftersom deras nätverksfilterdrivrutiner kan orsaka problemet. Avinstallation av nätverksfilterdrivrutiner från tredje part åtgärdar vanligen problemet. Du kan också starta emulatorn med/DisableRIO, som växlar kommunikationen mellan emulatorn och den vanliga Winsock-anslutningen. 

* Om du stöter på **"förbjuden", "meddelande": "begäran görs med en otillåten kryptering i överförings protokoll eller Cipher. Kontrol lera kontot SSL/TLS lägsta tillåtna protokoll inställning... "** anslutnings problem, detta kan bero på globala ändringar i operativ systemet (till exempel Insider Preview version 20170) eller webb läsar inställningarna som aktiverar TLS 1,3 som standard. Liknande fel kan uppstå när du använder SDK för att köra en begäran mot Cosmos-emulatorn, till exempel **Microsoft.Azure.Documents.DocumentClientException: begäran görs med en otillåten kryptering i överförings protokoll eller chiffer. Kontrol lera konto inställningen SSL/TLS lägsta tillåtna protokoll**. För närvarande är detta förväntat eftersom Cosmos-emulatorn bara accepterar och fungerar med TLS 1.2-protokollet. Det rekommenderade arbetet – runt är att ändra inställningarna och standardvärdet för TLS 1,2; i IIS-hanteraren navigerar du exempelvis till "platser" – > "standard webbplatser" och letar upp "webbplats bindningar" för port 8081 och redigerar dem för att inaktivera TLS 1,3. En liknande åtgärd kan utföras för webbläsaren via alternativen i Inställningar.

* Om datorn försätts i viloläge eller om operativsystemet uppdateras när emulatorn körs kan du se meddelandet **Tjänsten är inte tillgänglig just nu**. Återställ emulatorns data genom att högerklicka på ikonen som visas i meddelande fältet i Windows och välj **Återställ data**.

## <a name="collect-trace-files"></a><a id="trace-files"></a>Samla in spårningsfiler

För att samla in felsökningsspårningar kör du följande kommandon från en administrativ kommandotolk:

1. Navigera till sökvägen där emulatorn är installerad:

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. Stäng emulatorn och titta på system fältet för att kontrol lera att programmet har stängts av. Det kan ta en minut att slutföra. Du kan också välja **Avsluta** i användar gränssnittet för Azure Cosmos DB emulator.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. Starta loggning med följande kommando:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. Starta emulatorn

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. Återskapa problemet. Om data Utforskaren inte fungerar, behöver du bara vänta tills webbläsaren har öppnats några sekunder för att kunna fånga felet.

1. Stoppa loggning med följande kommando:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. Navigera till `%ProgramFiles%\Azure Cosmos DB Emulator` sökvägen och hitta *docdbemulator_000001. etl* -filen.

1. Öppna ett support ärende i [Azure Portal](https://portal.azure.com) och inkludera. ETL-filen tillsammans med återskapnings-steg.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig att felsöka problem med den lokala emulatorn. Nu kan du fortsätta till nästa artiklar:

* [Exportera Azure Cosmos DB emulator-certifikat för användning med Java-, python-och Node.js-appar](local-emulator-export-ssl-certificates.md)
* [Använd kommando rads parametrar och PowerShell-kommandon för att styra emulatorn](emulator-command-line-parameters.md)
