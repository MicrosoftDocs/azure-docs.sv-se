---
title: Felsöka Data Management Gateway problem
description: Innehåller tips för att felsöka problem som rör Data Management Gateway.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 284486c5db248ced8ada6e7194c7bc5a9be5689f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100388353"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Felsöka problem med gateway för datahantering
Den här artikeln innehåller information om fel sökning av problem med att använda Data Management Gateway.

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [integration runtime med egen värd i Data Factory](../create-self-hosted-integration-runtime.md).

Se [Data Management Gateway](data-factory-data-management-gateway.md) -artikeln för detaljerad information om gatewayen. Se artikeln [Flytta data mellan lokalt och moln](data-factory-move-data-between-onprem-and-cloud.md) för en genom gång av hur du flyttar data från en SQL Server databas till Microsoft Azure Blob Storage med hjälp av gatewayen.

## <a name="failed-to-install-or-register-gateway"></a>Det gick inte att installera eller registrera Gateway
### <a name="1-problem"></a>1. problem
Du ser det här fel meddelandet när du installerar och registrerar en gateway, särskilt när du laddar ned gatewayens installations fil.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Orsak
Den dator som du försöker installera gatewayen på har inte kunnat hämta den senaste Gateway-installationsmappen från Download Center på grund av ett nätverks problem.

#### <a name="resolution"></a>Lösning
Kontrol lera inställningarna för din brand Väggs proxyserver för att se om inställningarna blockerar nätverks anslutningen från datorn till [Download Center](https://download.microsoft.com/)och uppdatera inställningarna efter behov.

Du kan också hämta installations filen för den senaste gatewayen från [Download Center](https://www.microsoft.com/download/details.aspx?id=39717) på andra datorer som har åtkomst till Download Center. Du kan sedan kopiera installations filen till gateway-värddatorn och köra den manuellt för att installera och uppdatera gatewayen.

### <a name="2-problem"></a>2. problem
Du ser det här felet när du försöker installera en gateway genom att klicka på **installera direkt på den här datorn** i Azure Portal.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Orsak
Det finns redan en gateway installerad på datorn.

#### <a name="resolution"></a>Lösning
Avinstallera den befintliga gatewayen på datorn och klicka på länken **installera direkt på datorn** igen.

### <a name="3-problem"></a>3. problem
Du kanske ser det här felet när du registrerar en ny Gateway.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Orsak
Du kan se det här meddelandet av någon av följande orsaker:

* Formatet på Gateway-nyckeln är ogiltigt.
* Gateway-nyckeln har ogiltig förklarats.
* Gateway-nyckeln har återskapats från portalen.  

#### <a name="resolution"></a>Lösning
Kontrol lera om du använder rätt Gateway-nyckel från portalen. Om det behövs återskapar du en nyckel och använder nyckeln för att registrera gatewayen.

### <a name="4-problem"></a>4. problem
Du kan se följande fel meddelande när du registrerar en gateway.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Nyckelns innehåll eller format är ogiltigt](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Orsak
Innehållet eller formatet på den inmatade Gateway-nyckeln är felaktigt. En av orsakerna kan vara att du bara har kopierat en del av nyckeln från portalen eller att du använder en ogiltig nyckel.

#### <a name="resolution"></a>Lösning
Skapa en gateway-nyckel i portalen och Använd kopierings knappen för att kopiera hela nyckeln. Klistra sedan in den i det här fönstret för att registrera gatewayen.

### <a name="5-problem"></a>5. problem
Du kan se följande fel meddelande när du registrerar en gateway.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Skärm bild som visar fel meddelandet som anger att Gateway-nyckeln är ogiltig eller tom.](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Orsak
Gateway-nyckeln har återskapats eller också har gatewayen tagits bort i Azure Portal. Det kan också inträffa om Data Management Gateway-installationen inte är senaste.

#### <a name="resolution"></a>Lösning
Kontrol lera om Data Management Gateway installationen är den senaste versionen kan du hitta den senaste versionen på Microsoft [Download Center](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Om installationen är aktuell/senaste och gateway fortfarande finns på portalen, återskapar du Gateway-nyckeln i Azure Portal och använder kopierings knappen för att kopiera hela nyckeln och sedan klistra in den i det här fönstret för att registrera gatewayen. Annars kan du återskapa gatewayen och börja om.

### <a name="6-problem"></a>6. problem
Du kan se följande fel meddelande när du registrerar en gateway.

`Error: Gateway has been online for a while, then shows "Gateway is not registered" with the status "Gateway key is invalid"`

![Gateway-nyckeln är ogiltig eller tom](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Orsak
Det här felet kan inträffa på grund av att gatewayen har tagits bort eller att den associerade Gateway-nyckeln har återskapats.

#### <a name="resolution"></a>Lösning
Om gatewayen har tagits bort skapar du gatewayen på nytt från portalen, klickar på **Registrera**, kopierar nyckeln från portalen, klistrar in den och försöker registrera gatewayen.

Om gatewayen fortfarande finns men dess nyckel har återskapats använder du den nya nyckeln för att registrera gatewayen. Om du inte har nyckeln kan du återskapa nyckeln igen från portalen.

### <a name="7-problem"></a>7. problem
När du registrerar en gateway kan du behöva ange sökväg och lösen ord för ett certifikat.

![Skärm bild som visar var du anger sökvägen och lösen ordet för certifikatet.](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Orsak
Gatewayen har registrerats på andra datorer tidigare. Under den inledande registreringen av en gateway har ett krypterings certifikat associerats med gatewayen. Certifikatet kan antingen skapas själv av gatewayen eller tillhandahållas av användaren.  Det här certifikatet används för att kryptera autentiseringsuppgifterna för data lagret (länkad tjänst).  

![Exportera certifikatet](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

När du återställer gatewayen på en annan värddator, begär registrerings guiden att det här certifikatet ska dekryptera autentiseringsuppgifterna som tidigare har krypterats med det här certifikatet.  Utan det här certifikatet kan autentiseringsuppgifterna inte dekrypteras av den nya gatewayen och efterföljande kopierings aktivitets körningar som är associerade med den nya gatewayen kommer att Miss kopie ras.  

#### <a name="resolution"></a>Lösning
Om du har exporterat certifikatet för autentiseringsuppgifter från den ursprungliga gateway-datorn med hjälp av knappen **Exportera** på fliken **inställningar** i Data Management Gateway Configuration Manager använder du certifikatet här.

Du kan inte hoppa över det här steget när du återställer en gateway. Om certifikatet saknas måste du ta bort gatewayen från portalen och återskapa en ny Gateway.  Uppdatera dessutom alla länkade tjänster som är relaterade till gatewayen genom att ange sina autentiseringsuppgifter igen.

### <a name="8-problem"></a>8. problem
Du kan se följande fel meddelande.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Orsak
Det här felet inträffar när din gateway finns i en miljö som kräver en HTTP-proxy för att få åtkomst till Internet resurser, eller om proxyns lösen ord för proxyservern ändras, men inte uppdateras i enlighet med din gateway.

#### <a name="resolution"></a>Lösning
Följ anvisningarna i avsnittet om proxy server-överväganden i den här artikeln och konfigurera proxyinställningar med Data Management Gateway Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>Gatewayen är online med begränsade funktioner
### <a name="1-problem"></a>1. problem
Du ser statusen för gatewayen som online med begränsade funktioner.

#### <a name="cause"></a>Orsak
Du ser statusen för gatewayen som online med begränsade funktioner av någon av följande orsaker:

* Gatewayen kan inte ansluta till moln tjänsten via Azure Service Bus.
* Moln tjänsten kan inte ansluta till gatewayen via Service Bus.

Om gatewayen är online med begränsad funktionalitet kanske du inte kan använda Data Factory kopierings guiden för att skapa datapipelines för att kopiera data till eller från lokala data lager. Som en lösning kan du använda Data Factory Editor i portalen, Visual Studio eller Azure PowerShell.

#### <a name="resolution"></a>Lösning
Lösning för det här problemet (online med begränsade funktioner) baseras på om gatewayen inte kan ansluta till moln tjänsten eller det andra sättet. Följande avsnitt innehåller de här lösningarna.

### <a name="2-problem"></a>2. problem
Följande fel visas.

`Error: Gateway cannot connect to cloud service through service bus`

![Gatewayen kan inte ansluta till moln tjänsten](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Orsak
Gatewayen kan inte ansluta till moln tjänsten via Service Bus.

#### <a name="resolution"></a>Lösning
Följ dessa steg om du vill hämta gatewayen online igen:

1. Tillåt utgående regler för IP-adress på gateway-datorn och företags brand väggen. Du hittar IP-adresser från Windows-händelseloggen (ID = = 401): ett försök gjordes att få åtkomst till en socket på ett sätt som tillåts av dess åtkomst behörigheter XX. xx. xx. XX: 9350.
1. Konfigurera proxyinställningar på gatewayen. Mer information finns i avsnittet om proxy server-överväganden.
1. Aktivera utgående portar 5671 och 9350-9354 både i Windows-brandväggen på gateway-datorn och i företags brand väggen. Mer information finns i avsnittet om portar och brand väggar. Det här steget är valfritt, men vi rekommenderar det för prestanda överväganden.

### <a name="3-problem"></a>3. problem
Följande fel visas.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Orsak
Ett tillfälligt fel i nätverks anslutningen.

#### <a name="resolution"></a>Lösning
Följ dessa steg om du vill hämta gatewayen online igen:

1. Vänta ett par minuter så återställs anslutningen automatiskt när felet är borta.
1. Om felet kvarstår startar du om Gateway-tjänsten.

## <a name="failed-to-author-linked-service"></a>Det gick inte att redigera den länkade tjänsten
### <a name="problem"></a>Problem
Du kanske ser det här felet när du försöker använda Autentiseringshanteraren i portalen för att ange autentiseringsuppgifter för en ny länkad tjänst eller uppdatera autentiseringsuppgifter för en befintlig länkad tjänst.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

När du ser det här felet kan inställnings sidan för Data Management Gateway Configuration Manager se ut som på följande skärm bild.

![Det går inte att nå databasen](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Orsak
TLS/SSL-certifikatet kan ha gått förlorat på gateway-datorn. Gateway-datorn kan inte läsa in det certifikat som används för TLS-kryptering. Du kan också se ett fel meddelande i händelse loggen som liknar följande meddelande.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Lösning
Följ dessa steg för att lösa problemet:

1. Starta Data Management Gateway Configuration Manager.
2. Växla till fliken **Inställningar** .  
3. Klicka på knappen **ändra** för att ändra TLS/SSL-certifikatet.

   ![Knappen Ändra certifikat](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Välj ett nytt certifikat som TLS/SSL-certifikat. Du kan använda valfritt TLS/SSL-certifikat som genereras av dig eller någon annan organisation.

   ![Ange certifikat](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Kopierings aktiviteten Miss lyckas
### <a name="problem"></a>Problem
Du kan märka följande "UserErrorFailedToConnectToSqlserver"-problem när du har skapat en pipeline i portalen.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Orsak
Detta kan inträffa av olika orsaker och minskningen varierar.

#### <a name="resolution"></a>Lösning
Tillåt utgående TCP-anslutningar via port TCP/1433 på den Data Management Gateway klient sidan innan du ansluter till en SQL-databas.

Om mål databasen finns Azure SQL Database kontrollerar du SQL Server brand Väggs inställningar för Azure.

Se följande avsnitt för att testa anslutningen till det lokala data lagret.

## <a name="data-store-connection-or-driver-related-errors"></a>Anslutning eller driv rutins fel för data lager
Om du ser data lager anslutning eller driv rutins fel utför du följande steg:

1. Starta Data Management Gateway Configuration Manager på gateway-datorn.
2. Växla till fliken **diagnostik** .
3. I **Testa anslutning** lägger du till värdet för gateway-gruppen.
4. Klicka på **testa** för att se om du kan ansluta till den lokala data källan från gateway-datorn med hjälp av anslutnings informationen och autentiseringsuppgifterna. Om testanslutningen fortfarande misslyckas när du har installerat en drivrutin, ska du starta om gatewayen så att den använder den senaste ändringen.

![Testa anslutning på fliken diagnostik](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Gateway-loggar
### <a name="send-gateway-logs-to-microsoft"></a>Skicka Gateway-loggar till Microsoft
När du kontaktar Microsoft Support för att få hjälp med fel sökning av Gateway-problem kan du bli ombedd att dela dina gateway-loggar. Med versionen av gatewayen kan du dela nödvändiga Gateway-loggar med två klickningar i Data Management Gateway Configuration Manager.    

1. Växla till fliken **diagnostik** i Data Management Gateway Configuration Manager.

    ![Fliken Data Management Gateway diagnostik](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Klicka på **skicka loggar** för att se följande dialog ruta.

    ![Data Management Gateway skicka loggar](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. Valfritt Klicka på **Visa loggar** för att granska loggarna i logg boken.
4. Valfritt Klicka på **Sekretess** om du vill läsa sekretess policyn för Microsofts webb tjänster.
5. När du är nöjd med vad du kommer att överföra klickar du på **skicka loggar** för att skicka loggarna från de senaste sju dagarna till Microsoft för fel sökning. Du bör se statusen för åtgärden skicka-loggar som visas på följande skärm bild.

    ![Skärm bild som visar var du kan visa status för åtgärden skicka-loggar.](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. När åtgärden har slutförts visas en dialog ruta som visas i följande skärm bild.

    ![Data Management Gateway skicka loggar status](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Spara **rapport-ID: t** och dela den med Microsoft support. Rapport-ID: t används för att hitta de Gateway-loggar som du har överfört för fel sökning.  Rapport-ID: t sparas också i logg boken.  Du hittar det genom att titta på händelse-ID "25" och kontrol lera datum och tid.

    ![Rapport-ID för Data Management Gateway skicka loggar](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Arkivera Gateway-loggar på Gateway Host Machine
Det finns vissa scenarier där du har problem med gatewayen och du kan inte dela Gateway-loggar direkt:

* Du installerar gatewayen manuellt och registrerar gatewayen.
* Du försöker registrera gatewayen med en återskapad nyckel i Data Management Gateway Configuration Manager.
* Du försöker skicka loggar och värd tjänsten för gatewayen kan inte anslutas.

I dessa scenarier kan du spara Gateway-loggar som en zip-fil och dela den när du kontaktar Microsoft support. Om du till exempel får ett fel meddelande när du registrerar gatewayen så som visas på följande skärm bild.   

![Data Management Gateway registrerings fel](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Klicka på länken **arkivera Gateway-loggar** för att arkivera och Spara loggar och dela sedan zip-filen med Microsoft support.

![Data Management Gateway Arkiv loggar](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Hitta Gateway-loggar
Du hittar detaljerad information om Gateway-loggen i händelse loggarna i Windows.

1. Starta Windows **Loggboken**.
2. Hitta loggar i Data Management Gateway mappen **program-och tjänst loggar**  >   .

   När du felsöker Gateway-relaterade problem kan du leta efter fel nivå händelser i logg boken.

![Data Management Gateway loggar i logg boken](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
