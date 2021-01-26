---
title: Felsöka SSIS Integration Runtime Management
description: Den här artikeln innehåller fel söknings vägledning för hanterings problem med SSIS Integration Runtime (SSIS IR)
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 34fd595a03f67201b303c94764668bf86a6c244b
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796903"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Felsöka SSIS Integration Runtime Management i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln innehåller fel söknings vägledning för hanterings problem i Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), även kallat SSIS IR.

## <a name="overview"></a>Översikt

Om du stöter på ett problem under etableringen eller avetableringen av SSIS IR visas ett fel meddelande i Microsoft Azure Data Factory portalen eller ett fel som returneras från en PowerShell-cmdlet. Felet visas alltid i formatet för en felkod med ett detaljerat fel meddelande.

Om felkoden är InternalServerError har tjänsten tillfälliga problem och du bör försöka igen senare. Kontakta Azure Data Factory support-teamet om det inte går att göra ett nytt försök.

Annars kan tre större externa beroenden orsaka fel: Azure SQL Database eller Azure SQL-hanterad instans, ett anpassat installations skript och en konfiguration för virtuellt nätverk.

## <a name="sql-database-or-sql-managed-instance-issues"></a>Problem med SQL Database eller SQL-hanterad instans

SQL Database-eller SQL-hanterad instans krävs om du konfigurerar SSIS IR med en SSIS-katalog databas. SSIS IR måste kunna komma åt SQL Database eller SQL-hanterad instans. Inloggnings kontot för SQL Database eller SQL-hanterad instans måste också ha behörighet att skapa en SSIS-katalog databas (SSISDB). Om det uppstår ett fel visas en felkod med ett detaljerat SQL-undantagsmeddelande i Data Factory-portalen. Använd informationen i följande lista för att felsöka felkoderna.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Det här felet kan uppstå när du etablerar en ny SSIS IR eller medan IR körs. Om det här felet uppstår under IR-etableringen får du kanske ett detaljerat SqlException-meddelande i felmeddelandet som anger något av följande problem:

* Ett problem med nätverksanslutning. Kontrol lera om värd namnet för SQL Database eller SQL-hanterad instans är tillgängligt. Kontrollera även att ingen brandvägg eller nätverkssäkerhetsgrupp (NSG) blockerar SSIS IR-åtkomst till servern.
* Inloggningen misslyckades under SQL-autentisering. Det angivna kontot kan inte logga in på SQL Server-databasen. Se till att du anger rätt användarkonto.
* Inloggningen misslyckades under Microsoft Azure Active Directory-autentisering (Azure AD) (hanterad identitet). Lägg till den hanterade identiteten för din fabrik till en AAD-grupp och se till att den hanterade identiteten har åtkomstbehörighet till din katalogdatabasserver.
* Tidsgräns för anslutning. Det här felet orsakas alltid av en säkerhetsrelaterad konfiguration. Vi rekommenderar att du gör följande:
  1. Skapa en ny virtuell dator.
  1. Anslut den virtuella datorn till samma Microsoft Azure Virtual Network av IR om IR finns i ett virtuellt nätverk.
  1. Installera SSMS och kontrol lera status för SQL Database-eller SQL-hanterad instans.

För andra problem löser du det problem som visas i det detaljerade felmeddelandet för SQL-undantag. Om du fortfarande har problem kan du kontakta support teamet för SQL Database eller SQL-hanterad instans.

Om du ser felet när IR körs, förhindrar nätverks säkerhets gruppen eller brand Väggs ändringar förmodligen SSIS IR Worker-noden från att komma åt SQL Database-eller SQL-hanterad instans. Avblockera noden SSIS IR Worker så att den kan komma åt SQL Database-eller SQL-hanterad instans.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Den här typen av fel meddelande kan se ut så här: "databasen" SSISDB "har nått sin storleks kvot. Partitionera eller ta bort data, släpp index eller Läs om möjliga lösningar i dokumentationen. " 

Möjliga lösningar är följande:
* Öka kvot storleken för din SSISDB.
* Ändra konfigurationen för SSISDB för att minska storleken genom att:
   * Reducering av kvarhållningsperioden och antal projekt versioner.
   * Minskar loggens kvarhållningsperiod.
   * Ändra standard nivån för loggen.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Det här felet innebär att SQL Database-eller SQL-hanterad instans redan har en SSISDB och att den används av en annan IR. Du måste antingen ange en annan SQL Database eller SQL-hanterad instans eller ta bort den befintliga SSISDB och starta om den nya IR-filen.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Felet kan uppstå på grund av någon av följande orsaker:

* Det användarkonto som har konfigurerats för SSIS IR har inte behörighet att skapa databasen. Du kan bevilja användaren behörighet att skapa databasen.
* En överskriden tidsgräns inträffar när databasen skapas, till exempel en tidsgräns för körning eller för databasåtgärd. Du bör försöka åtgärden på nytt senare. Om försöket inte fungerar kan du kontakta support teamet för SQL Database eller SQL-hanterad instans.

För andra problem läser du felmeddelandet om SQL-undantag och åtgärdar det problem som beskrivs i felinformationen. Om du fortfarande har problem kan du kontakta support teamet för SQL Database eller SQL-hanterad instans.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Den här typen av fel meddelande ser ut så här: "ogiltigt objekt namn" catalog.catalog_properties "." I så fall har du redan en databas med namnet SSISDB men den skapades inte av SSIS IR, eller så är databasen i ett ogiltigt tillstånd som orsakas av fel under den senaste SSIS IR-etableringen. Du kan släppa den befintliga databasen med namnet SSISDB, eller så kan du konfigurera en ny SQL Database-eller SQL-hanterad instans för IR.

## <a name="custom-setup-issues"></a>Anpassade installations problem

Anpassad installation tillhandahåller ett gränssnitt där du kan lägga till egna installationssteg under etablering eller omkonfiguration av din SSIS IR. Mer information finns i [Anpassa installation för Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md).

Kontrollera att din container endast innehåller nödvändiga filer för anpassad installation. Alla filer i containern laddas ned till SSIS IR-arbetsnoden. Vi rekommenderar att du testar skriptet för anpassad installation på en lokal dator för att åtgärda eventuella problem med skriptkörning innan du kör skriptet i SSIS IR.

Containern för anpassat installationsskript kontrolleras medan IR körs eftersom SSIS IR uppdateras regelbundet. Den här uppdateringen kräver åtkomst till containern för att ladda ned skriptet för anpassad installation och installera det igen. Processen kontrollerar även om containern är tillgänglig och om filen main.cmd finns.

För alla fel som inbegriper anpassad installation visas en CustomSetupScriptFailure-felkod med under kod som CustomSetupScriptBlobContainerInaccessible eller CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Det här felet innebär att SSIS IR inte kan komma åt din Azure-blobcontainer för anpassad installation. Kontrollera att containerns SAS-URI kan nås och inte har upphört att gälla.

Stoppa IR om den körs, konfigurera om IR med en ny SAS-URI för container för anpassad installation och starta sedan om IR.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Det här felet innebär att SSIS IR inte kan hitta ett skript för anpassad installation (main.cmd) i blobcontainern. Se till att main.cmd finns i containern, vilket är startpunkten för anpassad installation.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Detta fel innebär att körningen av skriptet för anpassad inställningar (main.cmd) misslyckades. Testa skriptet på den lokala datorn först eller kontrollera körningsloggarna för anpassad installation i blobcontainern.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Det här felet anger en överskriden tidsgräns för körningen av skriptet för anpassad installation. Kontrollera att skriptet kan köras i tysthet och att det inte behövs några interaktiva indata, och se till att blobcontainern bara innehåller nödvändiga anpassade installationsfiler. Vi rekommenderar att du först testar skriptet på den lokala datorn. Du bör även kontrollera körningsloggarna för anpassad installation i blobcontainern. Den maximala tidsperioden för anpassad installation är 45 minuter innan tidsgränsen uppnås, och i den maximala perioden ingår tiden för att ladda ned alla filer från din container och installera dem på SSIS IR. Om du behöver en längre period kan du skapa en supportbegäran.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Det här felet innebär att försöket att ladda upp körningsloggar för anpassad installation till din blobcontainer misslyckades. Det här problemet inträffar antingen på grund av att SSIS IR inte har skrivbehörighet till din blobcontainer eller på grund av problem med lagring eller nätverk. Om den anpassade installationen lyckas påverkar det här felet inte någon SSIS-funktion, men loggarna kommer att saknas. Om anpassad installation misslyckas med ett annat fel och loggen inte laddas upp kommer vi att rapportera det här felet först så att loggen kan laddas upp för analys. När det här problemet har lösts rapporterar vi eventuella mer specifika problem. Om problemet inte har lösts efter ett nytt försök kontaktar du supportteamet för Azure Data Factory.

## <a name="virtual-network-configuration"></a>Konfiguration av virtuellt nätverk

När du kopplar SSIS IR till Azure Virtual Network använder SSIS IR det virtuella nätverk som finns under användarprenumerationen. Mer information finns i [Koppla en Azure-SSIS Integration Runtime till ett virtuellt nätverk](./join-azure-ssis-integration-runtime-virtual-network.md).
Om det uppstår problem med nätverks anslutningen när SSIS IR har startats kan du försöka använda [diagnostisera anslutnings verktyg](ssis-integration-runtime-diagnose-connectivity-faq.md) för att diagnostisera problemet själv.
När det finns ett problem som rör virtuellt nätverk visas något av följande fel.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Det här felet kan inträffa av olika orsaker. Information om hur du felsöker det finns i avsnitten [Förbjudet](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) och [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Förbjudet

Den här typen av fel kan likna detta: "SubnetId har inte Aktiver ATS för det aktuella kontot. Resurs leverantören för Microsoft.BatCH är inte registrerad under samma prenumeration av VNet. "

Den här informationen innebär att Azure Batch inte kan komma åt ditt virtuella nätverk. Registrera Microsoft.Batch-resursprovidern under samma prenumeration som Virtual Network.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Den här typen av fel kan se ut på något av följande sätt: 

- "Antingen finns inte det angivna virtuella nätverket eller också har batch-tjänsten inte åtkomst till det."
- "Det angivna under nätet XXX finns inte."

Dessa fel innebär att det virtuella nätverket inte finns, att Azure Batch-tjänsten inte kan komma åt det eller att det angivna undernätet inte finns. Kontrollera att det virtuella nätverket och undernätet finns och att Azure Batch kan komma åt dem.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Den här typen av fel meddelande kan se ut så här: "Det gick inte att etablera Integration Runtime i VNet. Om DNS-servern eller NSG-inställningarna konfigureras kontrollerar du att DNS-servern är tillgänglig och att NSG har kon figurer ATS korrekt. "

I den här situationen har du förmodligen en anpassad konfiguration av DNS-serverns eller NSG-inställningarna, vilket förhindrar att det Azure-servernamn som krävs av SSIS IR matchas eller nås. Mer information finns i [Konfiguration av virtuellt nätverk för SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md). Om det fortfarande är problem kan du kontakta supportteamet för Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR uppdateras automatiskt med jämna mellanrum. En ny Azure Batch-pool skapas under uppgraderingen, och den gamla Azure Batch-poolen tas bort. Dessutom tas Virtual Network-relaterade resurser för den gamla poolen bort, och de nya Virtual Network-relaterade resurserna skapas under din prenumeration. Det här felet innebär att borttagning av Virtual Network-relaterade resurser för den gamla poolen misslyckades på grund av ett borttagningslås på prenumerations- eller resursgruppsnivå. Eftersom kunden kontrollerar och anger borttagningslåset måste kunden ta bort borttagningslåset i den här situationen.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Om SSIS IR-etableringen misslyckas tas alla resurser som skapades bort. Men om det finns ett resursborttagningslås på prenumerations -eller resursgruppsnivå tas Virtual Network-resurser inte bort som förväntat. Du kan åtgärda det här felet genom att ta bort borttagningslåset och starta om IR.

### <a name="vnetresourcegrouplockedduringstopvnetdeletelock"></a>VNetResourceGroupLockedDuringStop/VNetDeleteLock

När du stoppar SSIS IR tas alla Virtual Network-relaterade resurser bort. Men borttagningen kan misslyckas om det finns ett resursborttagningslås på prenumerations- eller resursgruppsnivå. Även i det här fallet är det kunden som kontrollerar och anger borttagningslåset. Därför måste kunden ta bort borttagningslåset och sedan stoppa SSIS IR igen.

### <a name="nodeunavailable"></a>NodeUnavailable

Det här felet uppstår när IR körs och innebär att IR har blivit defekt. Det här felet orsakas alltid av en ändring i den DNS-server- eller NSG-konfiguration som blockerar SSIS IR från att ansluta till en nödvändig tjänst. Eftersom konfigurationen av DNS-server och NSG kontrolleras av kunden måste kunden åtgärda blockeringsproblemen på deras sida. Mer information finns i [Konfiguration av virtuellt nätverk för SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md). Om det fortfarande är problem kan du kontakta supportteamet för Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Konfiguration av statiska offentliga IP-adresser

När du ansluter Azure-SSIS IR till Azure Virtual Network kan du också ta med egna statiska offentliga IP-adresser för IR så att IR kan komma åt data källor som begränsar åtkomsten till specifika IP-adresser. Mer information finns i [Koppla en Azure-SSIS Integration Runtime till ett virtuellt nätverk](./join-azure-ssis-integration-runtime-virtual-network.md).

Förutom ovanstående problem med virtuella nätverk kan du också möta problem som rör statiska offentliga IP-adresser. Kontrol lera följande fel om du behöver hjälp.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecified

Det här felet kan inträffa av olika orsaker när du startar Azure-SSIS IR:

| Felmeddelande | Lösning|
|:--- |:--- |
| Den angivna statiska offentliga IP-adressen används redan, ange två oanvända för Azure-SSIS Integration Runtime. | Du bör välja två oanvända statiska offentliga IP-adresser eller ta bort aktuella referenser till den angivna offentliga IP-adressen och sedan starta om Azure-SSIS IR. |
| Den angivna statiska offentliga IP-adressen har inget DNS-namn. Ange två av dem med DNS-namnet för din Azure-SSIS Integration Runtime. | Du kan konfigurera DNS-namnet för den offentliga IP-adressen i Azure Portal, som visas i bilden nedan. De olika stegen är följande: (1) öppna Azure Portal och gå till resurs sidan för den här offentliga IP-adressen. (2) Välj **konfigurations** avsnittet och konfigurera DNS-namnet och klicka sedan på knappen **Spara** . (3) starta om Azure-SSIS IR. |
| De angivna VNet-och statiska offentliga IP-adresserna för din Azure-SSIS Integration Runtime måste finnas på samma plats. | Enligt Azure-nätverkets krav bör den statiska offentliga IP-adressen och det virtuella nätverket finnas på samma plats och i samma prenumeration. Ange två giltiga statiska offentliga IP-adresser och starta om Azure-SSIS IR. |
| Den tillhandahållna statiska offentliga IP-adressen är en grundläggande lösning, ange två standard för din Azure-SSIS Integration Runtime. | Se [SKU: er för offentlig IP-adress](../virtual-network/public-ip-addresses.md#sku) för hjälp. |

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Om Azure-SSIS IR etableringen Miss lyckas raderas alla resurser som har skapats. Men om det finns ett resurs borttagnings lås på prenumerationen eller resurs gruppen (som innehåller din statiska offentliga IP-adress), tas inte nätverks resurserna bort som förväntat. Åtgärda felet genom att ta bort borttagnings låset och starta om IR.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

När du stoppar Azure-SSIS IR tas alla nätverks resurser som skapats i resurs gruppen som innehåller din offentliga IP-adress bort. Men det går inte att ta bort om det finns ett resurs borttagnings lås på prenumerationen eller resurs gruppen (som innehåller din statiska offentliga IP-adress) nivå. Ta bort borttagnings låset och starta om IR.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Azure-SSIS IR uppdateras automatiskt regelbundet. Nya IR-noder skapas under uppgraderingen och de gamla noderna tas bort. Dessutom raderas de skapade nätverks resurserna (t. ex. belastningsutjämnaren och nätverks säkerhets gruppen) för de gamla noderna, och de nya nätverks resurserna skapas under din prenumeration. Det här felet innebär att det inte gick att ta bort nätverks resurserna för de gamla noderna på grund av ett borttagnings lås på prenumerationen eller resurs gruppen (som innehåller din statiska offentliga IP-adress) nivå. Ta bort borttagnings låset så att vi kan rensa de gamla noderna och frigöra den statiska offentliga IP-adressen för de gamla noderna. Annars kan inte den statiska offentliga IP-adressen släppas och vi kommer inte att kunna uppgradera dina IR-data.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

När du vill ta med egna statiska offentliga IP-adresser ska två offentliga IP-adresser anges. En av dem kommer att användas för att skapa IR-noderna omedelbart och en annan kommer att användas vid uppgradering av IR. Det här felet kan inträffa när den andra offentliga IP-adressen inte kan användas under uppgraderingen. Det finns möjliga orsaker till  [InvalidPublicIPSpecified](#InvalidPublicIPSpecified) .