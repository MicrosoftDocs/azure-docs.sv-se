---
title: Data Management Gateway för Data Factory
description: Använd Data Management Gateway i Azure Data Factory för att flytta dina data.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 708d84bdb3ebe8fbba6939aa771a9120868d5d1b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375212"
---
# <a name="data-management-gateway"></a>Gateway för datahantering
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory går du till [integration runtime med egen värd i](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> Data Management Gateway har nu märkts som egen värd Integration Runtime.

Data Management Gateway är en klient agent som du måste installera i din lokala miljö för att kunna kopiera data mellan moln-och lokala data lager. De lokala data lager som stöds av Data Factory visas i avsnittet [data källor som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

Den här artikeln kompletterar genom gången i artikeln [Flytta data mellan lokala och molnbaserade data lager](data-factory-move-data-between-onprem-and-cloud.md) . I genom gången skapar du en pipeline som använder gatewayen för att flytta data från en SQL Server-databas till en Azure-blob. Den här artikeln innehåller detaljerad djupgående information om data Management Gateway.

Du kan skala ut en gateway för data hantering genom att associera flera lokala datorer med gatewayen. Du kan skala upp genom att öka antalet data förflyttnings jobb som kan köras samtidigt på en nod. Den här funktionen är också tillgänglig för en logisk Gateway med en enda nod. Mer information finns i avsnittet [skala data Management Gateway i Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) artikeln.

> [!NOTE]
> För närvarande stöder Gateway endast aktiviteten Kopiera aktivitet och lagrad procedur i Data Factory. Det går inte att använda gatewayen från en anpassad aktivitet för att komma åt lokala data källor.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
### <a name="capabilities-of-data-management-gateway"></a>Funktioner i Data Management Gateway
Data Management Gateway tillhandahåller följande funktioner:

* Modellera lokala data källor och moln data källor inom samma data fabrik och flytta data.
* Ha ett fönster med glas för övervakning och hantering med synlighet i Gateway-status från sidan Data Factory.
* Hantera åtkomst till lokala data källor på ett säkert sätt.
  * Inga ändringar krävs för företags brand väggen. Gateway gör bara utgående HTTP-baserade anslutningar till öppna Internet.
  * Kryptera autentiseringsuppgifter för dina lokala data lager med ditt certifikat.
* Flytta data på ett effektivt sätt – data överförs parallellt, elastiska till tillfälliga nätverks problem med logik för automatisk omförsök.

### <a name="command-flow-and-data-flow"></a>Kommando flöde och data flöde
När du använder en kopierings aktivitet för att kopiera data mellan lokala platser och moln, använder den en gateway för att överföra data från en lokal data källa till molnet och vice versa.

Här är data flödet på hög nivå för och en sammanfattning av stegen för att kopiera med datagateway: ![ data flöde med hjälp av Gateway](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Datautvecklaren skapar en gateway för en Azure Data Factory som använder antingen [Azure Portal](https://portal.azure.com) -eller [PowerShell-cmdleten](/powershell/module/az.datafactory/).
2. Data utvecklare skapar en länkad tjänst för ett lokalt data lager genom att ange gatewayen. Som en del av konfigurationen av den länkade tjänsten använder data utvecklare inställningen för att ange autentiseringsuppgifter för att ange autentiseringstyper och autentiseringsuppgifter. Dialog rutan Ange autentiseringsuppgifter för program kommunicerar med data lagret för att testa anslutningen och gatewayen för att spara autentiseringsuppgifter.
3. Gatewayen krypterar autentiseringsuppgifterna med det certifikat som är associerat med gatewayen (tillhandahålls av data utvecklare) innan autentiseringsuppgifterna sparas i molnet.
4. Data Factory tjänsten kommunicerar med gatewayen för att schemalägga & hantering av jobb via en kontroll kanal som använder en delad Azure Service Bus-kö. När ett kopierings aktivitets jobb måste startas, Data Factory köar begäran tillsammans med autentiseringsinformation. Gatewayen startar jobbet när kön har avsökts.
5. Gatewayen dekrypterar autentiseringsuppgifterna med samma certifikat och ansluter sedan till det lokala data lagret med rätt autentiseringstyp och autentiseringsuppgifter.
6. Gatewayen kopierar data från en lokal lagrings plats till en moln lagrings plats, eller vice versa, beroende på hur kopierings aktiviteten har kon figurer ATS i data pipelinen. I det här steget kommunicerar gatewayen direkt med molnbaserade lagrings tjänster, till exempel Azure Blob Storage över en säker kanal (HTTPS).

### <a name="considerations-for-using-gateway"></a>Att tänka på vid användning av Gateway
* En enda instans av data Management Gateway kan användas för flera lokala data källor. **En enda Gateway-instans är dock knuten till endast en Azure-datafabrik** och kan inte delas med en annan data fabrik.
* Du kan **bara ha en instans av data Management Gateway** installerad på en enskild dator. Anta att du har två data fabriker som behöver åtkomst till lokala data källor, du måste installera gatewayer på två lokala datorer. En gateway är med andra ord knuten till en speciell data fabrik
* **Gatewayen behöver inte finnas på samma dator som data källan**. Men med Gateway närmare data källan minskar tiden för gatewayen att ansluta till data källan. Vi rekommenderar att du installerar gatewayen på en annan dator än den som är värd för den lokala data källan. När gatewayen och data källan finns på olika datorer, konkurrerar inte gatewayen om resurser med data källan.
* Du kan ha **flera gateways på olika datorer som ansluter till samma lokala data källa**. Du kan till exempel ha två gatewayer som betjänar två data fabriker men samma lokala data källa har registrerats med båda data fabrikerna.
* Om du redan har en gateway installerad på datorn som har ett **Power BI** scenario, installerar du en **separat Gateway för Azure Data Factory** på en annan dator.
* Gatewayen måste användas även om du använder **ExpressRoute**.
* Behandla din data källa som en lokal data källa (som ligger bakom en brand vägg) även när du använder **ExpressRoute**. Använd gatewayen för att upprätta anslutningar mellan tjänsten och data källan.
* Du måste **använda gatewayen** även om data lagret finns i molnet på en **virtuell Azure IaaS-dator**.

## <a name="installation"></a>Installation
### <a name="prerequisites"></a>Förutsättningar
* De **operativ system** versioner som stöds är Windows 7, Windows 8/8.1, Windows 10, windows Server 2008 R2, windows Server 2012, windows Server 2012 R2. Det finns för närvarande inte stöd för installation av data Management Gateway på en domänkontrollant.
* .NET Framework 4.5.1 eller senare krävs. Om du installerar Gateway på en dator med Windows 7 installerar du .NET Framework 4,5 eller senare. Mer information finns i [.NET Framework system krav](/dotnet/framework/get-started/system-requirements) .
* Den rekommenderade **konfigurationen** för gateway-datorn är minst 2 GHz, 4 kärnor, 8 GB RAM och 80 GB disk.
* Om värddatorn försätts i vilo läge svarar inte gatewayen på data begär Anden. Konfigurera därför ett lämpligt **energi schema** på datorn innan du installerar gatewayen. Om datorn är konfigurerad för vilo läge, visas ett meddelande i Gateway-installationen.
* Du måste vara administratör på datorn för att installera och konfigurera data Management Gateway. Du kan lägga till ytterligare användare i den lokala Windows-gruppen för **Data Management Gateway-användare** . Medlemmarna i den här gruppen kan använda **Data Management Gateway Configuration Manager** verktyget för att konfigurera gatewayen.

Eftersom kopierings aktiviteten körs på en speciell frekvens, följer resursanvändningen (CPU, minne) på datorn även samma mönster med hög belastnings tider. Resursutnyttjande beror också på mängden data som flyttas. När flera kopierings jobb pågår, ser du att resursanvändningen går upp under hög belastnings tider.

### <a name="installation-options"></a>Installationsalternativ
Data Management Gateway kan installeras på följande sätt:

* Genom att hämta ett installations paket för MSI från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). MSI kan också användas för att uppgradera befintlig data Management Gateway till den senaste versionen, med alla inställningar bevarade.
* Genom att klicka på länken **Hämta och installera data Gateway** under manuell installation eller **installera direkt på datorn** under Express installation. Se [Flytta data mellan lokala och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar för stegvisa anvisningar om hur du använder Express installation. Det manuella steget tar dig till Download Center. Anvisningarna för att ladda ned och installera gatewayen från Download Center finns i nästa avsnitt.

### <a name="installation-best-practices"></a>Metod tips för installation:
1. Konfigurera energi schema på värddatorn för gatewayen så att datorn inte försätts i vilo läge. Om värddatorn försätts i vilo läge svarar inte gatewayen på data begär Anden.
2. Säkerhetskopiera certifikatet som är associerat med gatewayen.

### <a name="install-the-gateway-from-download-center"></a>Installera gatewayen från Download Center
1. Gå till [hämtnings sidan för Microsoft Data Management Gateway](https://www.microsoft.com/download/details.aspx?id=39717).
2. Klicka på **Hämta**, Välj **64-bitars** versionen (32-bit stöds inte mer) och klicka på **Nästa**.
3. Kör **MSI** -filen direkt eller spara den på hård disken och kör.
4. Välj ett **språk** på **välkomst** sidan och klicka på **Nästa**.
5. **Godkänn** licens avtalet End-User och klicka på **Nästa**.
6. Välj **mapp** för att installera gatewayen och klicka på **Nästa**.
7. På sidan **klar att installera** klickar du på **Installera**.
8. Klicka på **Slutför** för att slutföra installationen.
9. Hämta nyckeln från Azure Portal. Se nästa avsnitt för steg-för-steg-instruktioner.
10. Utför följande steg på sidan **Registrera Gateway** i **Data Management Gateway Configuration Manager** som körs på datorn:
    1. Klistra in nyckeln i texten.
    2. Du kan också klicka på **Visa Gateway-nyckel** för att se nyckel texten.
    3. Klicka på **Registrera**.

### <a name="register-gateway-using-key"></a>Registrera Gateway med nyckel
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Om du inte redan har skapat en logisk gateway i portalen
Om du vill skapa en gateway i portalen och hämta nyckeln från sidan **Konfigurera** följer du stegen i genom gången i artikeln [Flytta data mellan lokalt och molnet](data-factory-move-data-between-onprem-and-cloud.md) .

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Om du redan har skapat den logiska gatewayen i portalen
1. I Azure Portal går du till sidan **Data Factory** och klickar på panelen **länkade tjänster** .

    ![Data Factory sida](media/data-factory-data-management-gateway/data-factory-blade.png)
2. På sidan **länkade tjänster** väljer du den logiska **Gateway** som du skapade i portalen.

    ![logisk Gateway](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. På sidan **data Gateway** klickar du på **Ladda ned och installera data Gateway**.

    ![Hämta länk i portalen](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. På sidan **Konfigurera** klickar du på **Återskapa nyckel**. Klicka på Ja i varnings meddelandet när du har läst det noggrant.

    ![Knappen återskapa nyckel](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Klicka på knappen Kopiera bredvid nyckeln. Nyckeln kopieras till Urklipp.

    ![Kopiera nyckel](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ikoner/meddelanden i system fältet
Följande bild visar några av de Brick ikoner som visas.

![system fälts ikoner](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Om du flyttar markören över system fältets ikon/meddelande visas information om statusen för gatewayen/uppdaterings åtgärden i ett popup-fönster.

### <a name="ports-and-firewall"></a>Portar och brand vägg
Det finns två brand väggar som du måste tänka på: **företags brand väggen** som körs på den centrala routern i organisationen och **Windows-brandväggen** konfigurerad som daemon på den lokala dator där gatewayen är installerad.

![brand väggar](./media/data-factory-data-management-gateway/firewalls2.png)

På företags brand Väggs nivå måste du konfigurera följande domäner och utgående portar:

| Domännamn | Portar | Beskrivning |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Används för kommunikation med Server dels tjänst för data flyttning |
| *.core.windows.net |443 |Används för mellanlagrad kopia med Azure Blob (om det kon figurer ATS)|
| *.frontend.clouddatahub.net |443 |Används för kommunikation med Server dels tjänst för data flyttning |
| *.servicebus.windows.net |9350-9354, 5671 |Valfritt Service Bus Relay via TCP som används av guiden Kopiera |

På Windows brand Väggs nivå är dessa utgående portar normalt aktiverade. Om inte kan du konfigurera domänerna och portarna på samma sätt på gateway-datorn.

> [!NOTE]
> 1. Baserat på dina källor/mottagare kan du behöva tillåta ytterligare domäner och utgående portar i företagets/Windows-brandväggen.
> 2. För vissa moln databaser (till exempel: [Azure SQL Database](../../azure-sql/database/firewall-configure.md), [Azure Data Lake](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)osv.) kan du behöva tillåta IP-adressen för gateway-datorn i brand Väggs konfigurationen.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kopiera data från ett käll data lager till ett data lager för mottagare
Kontrol lera att brand Väggs reglerna är korrekt aktiverade i företags brand väggen, Windows-brandväggen på gateway-datorn och själva data lagret. Genom att aktivera de här reglerna kan gatewayen ansluta till både källan och mottagaren. Aktivera regler för varje data lager som ingår i kopierings åtgärden.

Om du till exempel vill kopiera från **ett lokalt data lager till en Azure SQL Database mottagare eller en Azure Synapse Analytics-mottagare** gör du så här:

* Tillåt utgående **TCP** -kommunikation på port **1433** för både Windows-brandväggen och företags brand väggen.
* Konfigurera brand Väggs inställningarna för logisk SQL Server för att lägga till IP-adressen för gateway-datorn i listan över tillåtna IP-adresser.

> [!NOTE]
> Om brand väggen inte tillåter utgående port 1433, kan gatewayen inte komma åt Azure SQL direkt. I det här fallet kan du använda [mellanlagrad kopia](./data-factory-copy-activity-performance.md#staged-copy) till SQL Database/SQL-hanterad instans/SQL Azure DW. I det här scenariot behöver du bara HTTPS (port 443) för data flytten.
>
>

### <a name="proxy-server-considerations"></a>Överväganden för proxyserver
Om företagets nätverks miljö använder en proxyserver för att få åtkomst till Internet konfigurerar du gatewayen för data hantering så att rätt proxyinställningar används. Du kan ställa in proxyn under den inledande registrerings fasen.

![Ange proxy under registreringen](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Gatewayen använder proxyservern för att ansluta till moln tjänsten. Klicka på **ändra** länk under den första installationen. Dialog rutan **proxyinställningar** visas.

![Ange proxy med Konfigurations hanteraren 1](media/data-factory-data-management-gateway/SetProxySettings.png)

Det finns tre konfigurations alternativ:

* **Använd inte proxy**: gatewayen använder inte uttryckligen någon proxy för att ansluta till moln tjänster.
* **Använd systemproxy**: gatewayen använder den proxyserver som kon figurer ats i diahost.exe.config och diawp.exe.config. Om ingen proxy har kon figurer ATS i diahost.exe.config och diawp.exe.config ansluter gatewayen till moln tjänsten direkt utan att gå via proxy.
* **Använd anpassad proxy**: konfigurera HTTP-proxyn som ska användas för gateway i stället för att använda konfigurationer i diahost.exe.config och diawp.exe.config. Adress och port måste anges. Användar namn och lösen ord är valfria beroende på proxyns autentiseringsinställningar. Alla inställningar krypteras med certifikatets autentiseringsuppgifter för gatewayen och lagras lokalt på Gateway-värddatorn.

Tjänsten Data Management Gateway Host startas om automatiskt när du har sparat de uppdaterade proxyinställningarna.

När gatewayen har registrerats kan du använda Data Management Gateway Configuration Manager om du vill visa eller uppdatera proxyinställningarna.

1. Starta **Data Management Gateway Configuration Manager**.
2. Växla till fliken **Inställningar** .
3. Klicka på **ändra** länk i avsnittet **http-proxy** för att starta dialog rutan **ange http-proxy** .
4. När du klickar på knappen **Nästa** visas en varnings dialog ruta där du uppmanas att ange din behörighet för att spara proxyinställningarna och starta om Gateway-värdservern.

Du kan visa och uppdatera HTTP-proxyn med hjälp av Configuration Manager-verktyget.

![Ange proxy med Konfigurations hanteraren 2](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Om du konfigurerar en proxyserver med NTLM-autentisering körs Gateway-värd tjänsten under domän kontot. Om du ändrar lösen ordet för domän kontot senare måste du komma ihåg att uppdatera konfigurations inställningarna för tjänsten och sedan starta om det. På grund av detta krav rekommenderar vi att du använder ett dedikerat domän konto för att få åtkomst till proxyservern som inte kräver att du uppdaterar lösen ordet ofta.
>
>

### <a name="configure-proxy-server-settings"></a>Konfigurera inställningar för proxyserver
Om du väljer **Använd systemproxy** -inställningen för HTTP-proxyn använder gatewayen proxyinställningar i diahost.exe.config och diawp.exe.config. Om ingen proxy anges i diahost.exe.config och diawp.exe.config ansluter gatewayen till moln tjänsten direkt utan att gå via proxy. Följande procedur innehåller instruktioner för att uppdatera diahost.exe.config-filen.

1. I Utforskaren gör du en säker kopia av *C: \\ \\ Program Files \\ Microsoft Data Management Gateway \\ 2,0 \\ Shared \\diahost.exe.config* för att säkerhetskopiera original filen.
2. Starta Notepad.exe som körs som administratör och öppna text filen *C: \\ \\ Program Files \\ Microsoft Data Management Gateway \\ 2,0 \\ Shared \\diahost.exe.config*. Du hittar standard tag gen för system.net så som visas i följande kod:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Du kan sedan lägga till information om proxyservern som visas i följande exempel:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Ytterligare egenskaper tillåts i proxy-taggen för att ange nödvändiga inställningar som scriptLocation. Se [proxy-elementet (nätverks inställningar)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) i syntaxen.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Spara konfigurations filen på den ursprungliga platsen och starta sedan om tjänsten Data Management Gateway Host som hämtar ändringarna. Starta om tjänsten: Använd tjänster-applet från kontroll panelen, eller **Data Management Gateway Configuration Manager** > på knappen **stoppa tjänst** och klicka sedan på **starta tjänsten**. Om tjänsten inte startar är det troligt att en felaktig syntax för XML-taggar har lagts till i program konfigurations filen som redigerades.

> [!IMPORTANT]
> Glöm inte att uppdatera **både** diahost.exe.config och diawp.exe.config.

Förutom dessa punkter måste du också se till att Microsoft Azure finns i ditt företags lista över tillåtna. Listan över giltiga Microsoft Azure IP-adresser kan laddas ned från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Möjliga problem för brand Väggs-och proxy server-relaterade problem
Om det uppstår fel som liknar följande, beror det förmodligen på felaktig konfiguration av brand väggen eller proxyservern, som blockerar Gateway från att ansluta till Data Factory för att autentisera sig själv. Se föregående avsnitt för att kontrol lera att din brand vägg och proxyserver är korrekt konfigurerade.

1. När du försöker registrera gatewayen visas följande fel: "Det gick inte att registrera Gateway-nyckeln. Innan du försöker registrera Gateway-nyckeln igen bekräftar du att data Management Gateway är i ett anslutet tillstånd och att den Data Management Gateway värd tjänsten har startats. "
2. När du öppnar Configuration Manager visas status som "frånkopplad" eller "anslutning". När du visar Windows-händelseloggar, under "Loggboken" > "program-och tjänst loggar" > "Data Management Gateway", visas fel meddelanden, till exempel följande fel: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Öppna port 8050 för kryptering av autentiseringsuppgifter
**Inställningen för autentiseringsuppgifter** använder den inkommande port **8050** för att vidarebefordra autentiseringsuppgifter till gatewayen när du konfigurerar en lokal länkad tjänst i Azure Portal. Under Gateway-installationen öppnar Gateway-installationen som standard den på gateway-datorn.

Om du använder en brand vägg från en tredje part kan du öppna port 8050 manuellt. Om du får ett brand Väggs problem under installationen av gatewayen kan du prova att använda följande kommando för att installera gatewayen utan att konfigurera brand väggen.

```cmd
msiexec /q /i DataManagementGateway.msi NOFIREWALL=1
```

Om du väljer att inte öppna port 8050 på gateway-datorn använder du andra metoder än att använda programmet för **inställning av autentiseringsuppgifter** för att konfigurera autentiseringsuppgifter för data arkivet. Du kan till exempel använda PowerShell [-cmdleten New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) . Se Ange autentiseringsuppgifter och säkerhets avsnittet om hur data lagrings uppgifter kan anges.

## <a name="update"></a>Uppdatera
Som standard uppdateras data Management Gateway automatiskt när en nyare version av gatewayen är tillgänglig. Gatewayen uppdateras inte förrän alla schemalagda aktiviteter är klara. Inga ytterligare uppgifter bearbetas av gatewayen förrän uppdaterings åtgärden har slutförts. Om uppdateringen Miss lyckas återställs gatewayen till den tidigare versionen.

Du ser tiden för schemalagd uppdatering på följande platser:

* Sidan gateway-egenskaper i Azure Portal.
* Data Management Gateway Configuration Managerens start sida
* Meddelande i system fältet.

På fliken Start i Data Management Gateway Configuration Manager visas uppdaterings schema och den senaste gången som gatewayen installerades/uppdaterades.

![Schemauppdateringar](media/data-factory-data-management-gateway/UpdateSection.png)

Du kan installera uppdateringen omedelbart eller vänta tills gatewayen uppdateras automatiskt vid den schemalagda tiden. Följande bild visar till exempel meddelande meddelandet som visas i gatewayen Configuration Manager tillsammans med knappen Uppdatera som du kan klicka på för att installera det direkt.

![Uppdatera i DMG Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Aviserings meddelandet i system fältet ser ut som på bilden nedan:

![Meddelande i system fältet](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Du ser status för uppdaterings åtgärden (manuellt eller automatiskt) i system fältet. När du startar Gateway Configuration Manager nästa gång visas ett meddelande i meddelande fältet om att gatewayen har uppdaterats tillsammans med en länk till vad som [är nytt ämne](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Så här inaktiverar/aktiverar du funktionen för automatisk uppdatering
Du kan inaktivera/aktivera funktionen för automatisk uppdatering genom att utföra följande steg:

[För gateway med en nod]
1. Starta Windows PowerShell på gateway-datorn.
2. Växla till mappen *C: \\ \\ program \\ Microsoft integration runtime \\ 3,0 \\ PowerShellScript \\* .
3. Kör följande kommando för att aktivera funktionen för automatisk uppdatering av (inaktivera).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Så här aktiverar du det igen:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [För flera noder med hög tillgänglighet och skalbar Gateway](data-factory-data-management-gateway-high-availability-scalability.md)
1. Starta Windows PowerShell på gateway-datorn.
2. Växla till mappen *C: \\ \\ program \\ Microsoft integration runtime \\ 3,0 \\ PowerShellScript \\* .
3. Kör följande kommando för att aktivera funktionen för automatisk uppdatering av (inaktivera).

    För gateway med funktionen hög tillgänglighet krävs en extra AuthKey-param.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Så här aktiverar du det igen:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
När du har installerat gatewayen kan du starta Data Management Gateway Configuration Manager på något av följande sätt:

1. I fönstret **Sök** skriver du **Data Management Gateway** för att komma åt det här verktyget.
2. Kör den körbara *ConfigManager.exe* i mappen: *C: \\ \\ Program Files \\ Microsoft Data Management Gateway \\ 2,0 \\ Shared*.

### <a name="home-page"></a>Startsida
På Start sidan kan du utföra följande åtgärder:

* Visa status för gatewayen (ansluten till moln tjänsten osv.).
* **Registrera dig** med en nyckel från portalen.
* **Stoppa** och starta **Data Management Gateway värd tjänsten** på gateway-datorn.
* **Schemalägg uppdateringar** vid en angiven tidpunkt på dagar.
* Visa det datum då gatewayen **senast uppdaterades**.

### <a name="settings-page"></a>Sidan Inställningar
På sidan inställningar kan du utföra följande åtgärder:

* Visa, ändra och exportera **certifikatet** som används av gatewayen. Det här certifikatet används för att kryptera autentiseringsuppgifterna för data källan.
* Ändra **https-port** för slut punkten. Gatewayen öppnar en port för att ange autentiseringsuppgifter för data källan.
* **Status** för slut punkten
* Visa **SSL-certifikat** används för TLS/SSL-kommunikation mellan portalen och gatewayen för att ange autentiseringsuppgifter för data källor.

### <a name="remote-access-from-intranet"></a>Fjärråtkomst från intranät
Den här funktionen kommer att aktive ras i framtiden. I kommande uppdateringar (v 3.4 eller senare) kommer vi att låta dig aktivera/inaktivera fjärr anslutningar som idag sker med port 8050 (se avsnittet ovan) när du använder PowerShell-eller Credential Manager-programmet för kryptering av autentiseringsuppgifter.

### <a name="diagnostics-page"></a>Sidan diagnostik
På sidan diagnostik kan du utföra följande åtgärder:

* Aktivera utförlig **loggning**, Visa loggar i logg boken och skicka loggar till Microsoft om det uppstod ett problem.
* **Testa anslutningen** till en data källa.

### <a name="help-page"></a>Hjälpsida
Hjälp sidan visar följande information:

* Kort beskrivning av gatewayen
* Versionsnummer
* Länkar till direkt hjälp, sekretess policy och licens avtal.

## <a name="monitor-gateway-in-the-portal"></a>Övervaka gateway i portalen
I Azure Portal kan du Visa en ögonblicks bild av resursutnyttjande i real tid (CPU, minne, nätverk (in/ut) osv.) på en Gateway-dator.

1. I Azure Portal går du till start sidan för din data fabrik och klickar på panelen **länkade tjänster** .

    ![Datafabrikens startsida](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Välj **gatewayen** på sidan **länkade tjänster** .

    ![Sidan länkade tjänster](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. På sidan **Gateway** kan du se gatewayens minnes-och CPU-användning.

    ![CPU-och minnes användning för gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Aktivera **Avancerade inställningar** om du vill se mer information, till exempel nätverks användning.
    
    ![Avancerad övervakning av Gateway](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Följande tabell innehåller beskrivningar av kolumner i listan **Gateway-noder** :

Övervaknings egenskap | Beskrivning
:------------------ | :----------
Namn | Namnet på den logiska gatewayen och noder som är associerade med gatewayen. Node är en lokal Windows-dator där gatewayen är installerad. Information om hur du har fler än en nod (upp till fyra noder) i en enda logisk Gateway finns i [Data Management Gateway-hög tillgänglighet och skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md).
Status | Status för den logiska gatewayen och gateway-noderna. Exempel: online/offline/begränsat/osv. Information om dessa statusar finns i avsnittet om [Gateway-status](#gateway-status) .
Version | Visar versionen för den logiska gatewayen och varje gateway-nod. Versionen av den logiska gatewayen fastställs baserat på den version av majoriteten av noderna i gruppen. Om det finns noder med olika versioner i installations programmet för den logiska gatewayen fungerar bara noderna med samma versions nummer som den logiska gatewayen. Andra är i begränsat läge och måste uppdateras manuellt (endast om automatisk uppdatering Miss lyckas).
Tillgängligt minne | Tillgängligt minne på en gateway-nod. Det här värdet är en nära real tids ögonblicks bild.
CPU-användning | CPU-användning för en gateway-nod. Det här värdet är en nära real tids ögonblicks bild.
Nätverk (in/ut) | Nätverks användning för en gateway-nod. Det här värdet är en nära real tids ögonblicks bild.
Samtidiga jobb (som körs/begränsas) | Antal jobb eller aktiviteter som körs på varje nod. Det här värdet är en nära real tids ögonblicks bild. Gränsen visar maximalt antal samtidiga jobb för varje nod. Det här värdet definieras baserat på datorns storlek. Du kan öka gränsen för att skala upp samtidiga jobb körningar i avancerade scenarier, där processor/minne/nätverk används, men att aktiviteterna är tids gräns. Den här funktionen är även tillgänglig med en gateway med en nod (även om funktionen skalbarhets-och tillgänglighet inte är aktive rad).
Roll | Det finns två typer av roller i en gateway-dispatcher och Worker med flera noder. Alla noder är arbetare, vilket innebär att de kan användas för att köra jobb. Det finns bara en dispatcher-nod som används för att hämta aktiviteter/jobb från moln tjänster och skicka dem till olika arbetsnoder (inklusive sig själv).

På den här sidan kan du se vissa inställningar som är mer begripliga när det finns två eller fler noder (skala ut scenariot) i gatewayen. Se [Data Management Gateway-hög tillgänglighet och skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md) för mer information om hur du konfigurerar en gateway med flera noder.

### <a name="gateway-status"></a>Gateway-status
Följande tabell innehåller möjliga status värden för en **Gateway-nod**:

Status    | Kommentarer/scenarier
:------- | :------------------
Online | Noden är ansluten till Data Factory tjänsten.
Offline | Noden är offline.
Uppgradera | Noden uppdateras automatiskt.
Begränsad | På grund av anslutnings problem. Kan bero på problem med HTTP-port 8050, problem med Service Bus-anslutning eller utfärdande av autentiseringsuppgifter.
Inaktiv | Noden har en annan konfiguration än konfigurationen av andra majoritets noder.<br/><br/> En nod kan vara inaktiv när den inte kan ansluta till andra noder.

Följande tabell innehåller möjliga status värden för en **logisk Gateway**. Gateway-statusen beror på status för gateway-noderna.

Status | Kommentarer
:----- | :-------
Behöver registrering | Ingen nod har ännu registrerats till denna logiska Gateway
Online | Gateway-noderna är online
Offline | Ingen nod i online-status.
Begränsad | Alla noder i denna gateway är inte i felfritt tillstånd. Den här statusen är en varning om att en nod kan vara nere! <br/><br/>Det kan bero på ett problem med synkronisering av autentiseringsuppgifter på dispatcher/Worker-noden.

## <a name="scale-up-gateway"></a>Skala upp Gateway
Du kan konfigurera antalet **samtidiga data flyttnings jobb** som kan köras på en nod för att skala upp möjligheten att flytta data mellan lokala och molnbaserade data lager.

När det tillgängliga minnet och CPU: n inte används, men den inaktiva kapaciteten är 0, bör du skala upp genom att öka antalet samtidiga jobb som kan köras på en nod. Du kanske också vill skala upp när aktiviteterna är tids gräns eftersom gatewayen är överbelastad. I avancerade inställningar för en gateway-nod kan du öka den maximala kapaciteten för en nod.

## <a name="troubleshooting-gateway-issues"></a>Felsöka Gateway-problem
Se artikeln om [fel sökning av Gateway-problem](data-factory-troubleshoot-gateway-issues.md) för information och tips för fel sökning av problem med data Management Gateway.

## <a name="move-gateway-from-one-machine-to-another"></a>Flytta gatewayen från en dator till en annan
Det här avsnittet innehåller steg för att flytta Gateway-klienten från en dator till en annan.

1. I portalen navigerar du till **Data Factory start sida** och klickar på panelen **länkade tjänster** .

    ![Länk till data gatewayer](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Välj din gateway i avsnittet **data gateways** på sidan **länkade tjänster** .

    ![Sidan länkade tjänster med Gateway vald](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. På sidan **data Gateway** klickar du på **Ladda ned och installera data Gateway**.

    ![Hämta Gateway-länk](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. På sidan **Konfigurera** klickar du på **Hämta och installera data Gateway** och följer anvisningarna för att installera datagatewayen på datorn.

    ![Konfigurera sida](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Behåll **Microsoft Data Management Gateway-Configuration Manager** öppen.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. På sidan **Konfigurera** i portalen klickar du på **Återskapa nyckel** i kommando fältet och klickar på **Ja** för varnings meddelandet. Klicka på **knappen Kopiera** bredvid nyckel text som kopierar nyckeln till Urklipp. Gatewayen på den gamla datorn slutar att fungera så snart du återskapar nyckeln.

    ![Återskapa nyckel 2](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Klistra in **nyckeln** i text rutan på sidan **registrera Gateway** i **Data Management Gateway Configuration Manager** på din dator. valfritt Klicka på kryss rutan **Visa Gateway-nyckel** för att se nyckel texten.

    ![Kopiera nyckel och registrera](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Klicka på **Registrera** för att registrera gatewayen med moln tjänsten.
9. På fliken **Inställningar** klickar du på **ändra** för att välja samma certifikat som användes med den gamla gatewayen, ange **lösen ordet** och klicka på **Slutför**.

   ![Ange certifikat](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Du kan exportera ett certifikat från den gamla gatewayen genom att utföra följande steg: starta Data Management Gateway Configuration Manager på den gamla datorn, växla till fliken **certifikat** , klicka på knappen **Exportera** och följ instruktionerna.
10. När **du har registrerat** en gateway bör du se att **registrerings** uppsättningen är **registrerad** och **statusen** inställd på Start sidan för gateway-Configuration Manager.

## <a name="encrypting-credentials"></a>Kryptering av autentiseringsuppgifter
Gör så här för att kryptera autentiseringsuppgifterna i Data Factory redigeraren:

1. Öppna en webbläsare på **gateway-datorn** och gå till [Azure Portal](https://portal.azure.com). Sök efter din data fabrik om det behövs, öppna data Factory på sidan **data fabrik** och klicka sedan på **författare & distribuera** för att starta Data Factory redigeraren.
2. Klicka på en befintlig **länkad tjänst** i trädvyn för att se dess JSON-definition eller skapa en länkad tjänst som kräver en data Management Gateway (till exempel: SQL Server eller Oracle).
3. I JSON-redigeraren för egenskapen **gatewayName** anger du namnet på gatewayen.
4. Ange Server namnet för **data källans** egenskap i **ConnectionString**.
5. Ange databas namnet för den **inledande katalog** egenskapen i **ConnectionString**.
6. Klicka på knappen **kryptera** i kommando fältet som startar programmet klickning-Once **Credential Manager** . Du bör se dialog rutan **Inställningar för autentiseringsuppgifter** .

    ![Dialog rutan Ange autentiseringsuppgifter](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. I dialog rutan **ange autentiseringsuppgifter** gör du följande:
   1. Välj den **autentisering** som du vill att Data Factory tjänsten ska använda för att ansluta till databasen.
   2. Ange namnet på den användare som har åtkomst till databasen för inställningen **användar namn** .
   3. Ange lösen ordet för användaren för **lösen ords** inställningen.
   4. Klicka på **OK** för att kryptera autentiseringsuppgifterna och stänga dialog rutan.
8. Du bör se en **encryptedCredential** -egenskap i **ConnectionString** nu.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
   Om du har åtkomst till portalen från en annan dator än gateway-datorn, måste du se till att Autentiseringshanteraren-programmet kan ansluta till gateway-datorn. Om programmet inte kan ansluta till gateway-datorn kan du inte ange autentiseringsuppgifter för data källan och testa anslutningen till data källan.

När du använder programmet för **inställning av autentiseringsuppgifter** krypterar portalen autentiseringsuppgifterna med det certifikat som anges på fliken **certifikat** för gateway- **Configuration Manager** på gateway-datorn.

Om du letar efter en API-baserad metod för kryptering av autentiseringsuppgifterna kan du använda PowerShell-cmdleten [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) för att kryptera autentiseringsuppgifterna. Cmdlet: en använder det certifikat som gatewayen är konfigurerad att använda för att kryptera autentiseringsuppgifterna. Du lägger till krypterade autentiseringsuppgifter i **EncryptedCredential** -elementet för **ConnectionString** i JSON. Du använder JSON med cmdleten [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) eller i redigerings programmet för Data Factory.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Det finns en metod för att ställa in autentiseringsuppgifter med hjälp av Data Factory Editor. Om du skapar en SQL Server länkad tjänst med hjälp av redigeraren och anger autentiseringsuppgifter i klartext, krypteras autentiseringsuppgifterna med ett certifikat som Data Factorys tjänsten äger. Det använder inte det certifikat som gatewayen har kon figurer ATS för att använda. Även om den här metoden kan vara lite snabbare i vissa fall är den mindre säker. Därför rekommenderar vi att du följer den här metoden endast för utvecklings-och testnings ändamål.

## <a name="powershell-cmdlets"></a>PowerShell-cmdletar
I det här avsnittet beskrivs hur du skapar och registrerar en gateway med hjälp av Azure PowerShell-cmdletar.

1. Starta **Azure PowerShell** i administratörs läge.
2. Logga in på ditt Azure-konto genom att köra följande kommando och ange dina autentiseringsuppgifter för Azure.

    ```powershell
    Connect-AzAccount
    ```
3. Använd cmdleten **New-AzDataFactoryGateway** för att skapa en logisk gateway på följande sätt:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Exempel kommando och utdata**:

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description "gateway for walkthrough"

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. I Azure PowerShell växlar du till mappen: *C: \\ \\ Program Files \\ Microsoft integration runtime \\ 3,0 \\ PowerShellScript \\*. Kör *RegisterGateway.ps1* som är associerade med den lokala variabeln **$Key** som du ser i följande kommando. Det här skriptet registrerar klient agenten som är installerad på datorn med den logiska gateway som du skapar tidigare.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Du kan registrera gatewayen på en fjärrdator med hjälp av parametern IsRegisterOnRemoteMachine. Exempel:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Du kan använda cmdleten **Get-AzDataFactoryGateway** för att hämta listan över gatewayer i din data fabrik. När **statusen** är **online** innebär det att din gateway är redo att användas.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Du kan ta bort en gateway med cmdleten **Remove-AzDataFactoryGateway** och uppdatera beskrivningen för en gateway med cmdletarna **set-AzDataFactoryGateway** . Syntax och annan information om dessa cmdlets finns i Data Factory cmdlet-referens.  

### <a name="list-gateways-using-powershell"></a>Lista gatewayer med PowerShell

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Ta bort Gateway med PowerShell

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Nästa steg
* Se artikeln [Flytta data mellan lokala och molnbaserade data lager](data-factory-move-data-between-onprem-and-cloud.md) . I genom gången skapar du en pipeline som använder gatewayen för att flytta data från en SQL Server-databas till en Azure-blob.