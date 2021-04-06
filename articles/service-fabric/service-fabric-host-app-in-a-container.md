---
title: Distribuera en .NET-app i en behållare till Azure Service Fabric
description: Lär dig hur du använder en container med ett befintligt .NET-program med hjälp av Visual Studio och hur du felsöker containrar i Service Fabric lokalt. Programmet i containern skickas via push-teknik till ett Azure-containerregister och distribueras till ett Service Fabric-kluster. När det har distribuerats till Azure använder programmet Azure SQL DB för att spara data.
ms.topic: tutorial
ms.date: 07/08/2019
ms.openlocfilehash: 7930651a0faa5f37336c15557e2a0f068d613011
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936711"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Självstudiekurs: Distribuera ett .NET-program i en Windows-container till Azure Service Fabric

I den här självstudiekursen lär du dig hur du skapar en container för ett befintligt ASP.NET-program och hur du paketerar programmet som ett Service Fabric-program.  Kör containrarna lokalt i Service Fabric-utvecklingsklustret och distribuera sedan programmet till Azure.  Programmet sparar data i [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Använda en container med ett befintligt program med hjälp av Visual Studio
> * Skapa en databas i Azure SQL Database
> * Skapa ett Azure-containerregister
> * Distribuera ett Service Fabric-program till Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

1. Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
2. Aktivera Windows-funktioner **Hyper-V** och **behållare**.
3. Installera [Docker Desktop för Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) så att du kan köra behållare i Windows 10.
4. Installera [Service Fabric Runtime version 6.2 eller senare](service-fabric-get-started.md) och [Service Fabric SDK version 3.1](service-fabric-get-started.md) eller senare.
5. Installera [Visual Studio 2019 Version 16,1](https://www.visualstudio.com/) eller senare med arbets belastningarna **Azure Development** och **ASP.net och webb utveckling** .
6. Installera [Azure PowerShell][link-azure-powershell-install]

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Ladda ned och kör Fabrikam Fiber CallCenter

1. Ladda ned exempelprogrammet [Fabrikam Fiber CallCenter][link-fabrikam-github].  Klicka på länken för att **ladda ned arkiv**.  Gå till katalogen *sourceCode* i filen *fabrikam.zip* och extrahera filen *sourceCode.zip* och sedan katalogen *VS2015* till datorn.

2. Kontrollera att Fabrikam Fiber CallCenter-programmet skapas och körs utan fel.  Starta Visual Studio som **administratör** och öppna filen [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Tryck på F5 för att felsöka och köra programmet.

   ![Skärm bild av start sidan för Fabrikam fiber CallCenter-programmet som körs på den lokala värden. På sidan visas en instrument panel med en lista med support samtal.][fabrikam-web-page]

## <a name="create-an-azure-sql-db"></a>Skapa en Azure SQL DB-databas

När du kör Fabrikam Fiber CallCenter-programmet i produktion måste data sparas i en databas. Det finns för närvarande inget sätt att garantera beständiga data i en container. Därför kan du inte lagra produktionsdata i SQL Server i en container.

Vi rekommenderar [Azure SQL Database](../azure-sql/database/powershell-script-content-guide.md). Konfigurera och kör en hanterad SQL Server-databas i Azure genom att köra följande skript.  Ändra skriptvariablerna efter behov. *clientIP* är utvecklingsdatorns IP-adress. Anteckna namnet på den server som genererade skriptet.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Create the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

> [!TIP]
> Om datorn finns bakom en företagsbrandvägg kanske utvecklingsdatorns IP-adress inte exponeras mot Internet. Kontrollera att databasen har rätt IP-adress för brandväggsregeln genom att gå till [Azure-portalen](https://portal.azure.com) och leta upp din databas i avsnittet för SQL-databaser. Klicka på dess namn. I avsnittet Översikt klickar du sedan på ”Konfigurera serverns brandvägg”. ”Klient-IP-adress” är utvecklingsdatorns IP-adress. Se till att den matchar IP-adressen i regeln ”AllowClient” (tillåt klient).

## <a name="update-the-web-config"></a>Uppdatera webbkonfiguration

I projektet **FabrikamFiber.Web** uppdaterar du anslutningssträngen i filen **web.config** så att den pekar på SQL Server i containern.  Uppdatera *Server*-delen i anslutningssträngen så att det blir servernamnet som skapades med föregående skript. Det bör vara något som liknar ”fab-fiber-751718376.database.windows.net”. I följande XML-fil behöver du bara uppdatera `connectionString` attributet. `providerName` `name` attributen och behöver inte ändras.

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```

>[!NOTE]
>Du kan använda vilken SQL-Server som helst för lokal felsökning så länge den kan nås från värden, men **localdb** stöder inte `container -> host`-kommunikation. Om du vill använda en annan SQL-databas när du skapar ett versionsbygge av ditt webbprogram, lägger du till ytterligare en anslutningssträng i filen *web.release.config*.

## <a name="containerize-the-application"></a>Använd programmet med en container

1. Högerklicka på projektet **FabrikamFiber.Web** > **Lägg till** > **Stöd för Container Orchestrator**.  Välj **Service Fabric** som initierare för containern och klicka på **OK**.

2. Om du uppmanas till det klickar du på **Ja** för att växla Docker till Windows-behållare nu.

   Ett nytt Service Fabric-programprojekt **FabrikamFiber.CallCenterApplication** skapas i lösningen.  En Dockerfile läggs till i det befintliga **FabrikamFiber.Web**-projektet.  En **PackageRoot**-katalog läggs också till i **FabrikamFiber.Web**-projektet, som innehåller tjänstmanifestet och inställningarna för den nya FabrikamFiber.Web-tjänsten.

   Containern är nu redo att byggas och paketeras i ett Service Fabric-program. När du har containeravbildningen klar på datorn kan du lägga den i valfritt containerregister och hämta den till valfri värd för körning.

## <a name="run-the-containerized-application-locally"></a>Kör programmet i containern lokalt

Tryck på **F5** för att köra och felsöka programmet i en container i det lokala Service Fabric-utvecklingsklustret. Klicka på **Ja** om ett meddelande visas där du ombeds att ge gruppen 'ServiceFabricAllowedUsers' läs- och körbehörighet till katalogen Visual Studio-projekt.

Om F5-körningen genererar ett undantag, till exempel följande, har rätt IP inte lagts till i Azure Database-brandväggen.

```text
System.Data.SqlClient.SqlException
HResult=0x80131904
Message=Cannot open server 'fab-fiber-751718376' requested by the login. Client with IP address '123.456.789.012' is not allowed to access the server.  To enable access, use the Windows Azure Management Portal or run sp_set_firewall_rule on the master database to create a firewall rule for this IP address or address range.  It may take up to five minutes for this change to take effect.
Source=.Net SqlClient Data Provider
StackTrace:
<Cannot evaluate the exception stack trace>
```

Kör följande kommando för att lägga till rätt IP-adress till Azure Database-brandväggen.

```powershell
# The IP address of your development computer that accesses the SQL DB.
$clientIPNew = "<client IP from the Error Message>"

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClientNew" -StartIpAddress $clientIPNew -EndIpAddress $clientIPNew
```

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Nu när programmet körs lokalt kan du börja förbereda distributionen till Azure.  Containeravbildningar måste lagras i ett containerregister.  Skapa ett [Azure-containerregister](../container-registry/container-registry-intro.md) med hjälp av följande skript. Containerregisternamnet är synligt för andra Azure-prenumerationer så det måste vara unikt.
Innan du distribuerar programmet till Azure skickar du containeravbildningen via push-teknik till det här registret.  När programmet distribueras till klustret i Azure hämtas containeravbildningen från det här registret.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Skapa ett Service Fabric-kluster i Azure

Service Fabric-program körs i ett kluster, en nätverksansluten uppsättning virtuella eller fysiska datorer.  Innan du kan distribuera programmet till Azure måste du skapa ett Service Fabric-kluster i Azure.

Du kan:

* Skapa ett testkluster från Visual Studio. Med det här alternativet kan du skapa ett säkert kluster direkt från Visual Studio med dina önskade konfigurationer.
* [Skapa ett säkert kluster från en mall](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

I den här självstudiekursen skapar du ett kluster från Visual Studio, vilket är idealiskt för testscenarier. Om du skapar ett kluster på något annat sätt eller om du använder ett befintligt kluster kan du kopiera och klistra in anslutningens slutpunkt eller välja den från din prenumeration.

Innan du startar öppnar du projektet fabrikamfiber. Web-> PackageRoot-> ServiceManifest.xml i Solution Explorer. Anteckna porten för webbklientdelen som visas i **Slutpunkt**.

När du skapar klustret:

1. Högerklicka på **FabrikamFiber.CallCenterApplication**-programprojektet i Solution Explorer och välj **Publicera**.
2. Logga in med ditt Azure-konto så att du får åtkomst till dina prenumerationer.
3. Under List rutan för **anslutnings slut punkten** väljer du alternativet **Skapa nytt kluster...** .
4. Ändra följande inställningar i dialogrutan **Skapa kluster**:

    a. Ange namnet på klustret i fältet **Klusternamn**, samt den prenumeration och plats som du vill använda. Anteckna namnet på klusterresursgruppen.

    b. Valfritt: Du kan ändra antalet noder. Som standard har du tre noder, vilket är det som krävs för att testa scenarier i Service Fabric.

    c. Välj fliken **certifikat** . I den här fliken anger du ett lösen ord som ska användas för att skydda certifikatet för klustret. Med det här certifikatet blir klustret säkrare. Du kan också ändra sökvägen till den plats där du vill spara certifikatet. Visual Studio kan dessutom importera certifikatet åt dig, eftersom det är ett obligatoriskt steg för att kunna publicera programmet i klustret.

    >[!NOTE]
    >Anteckna sökvägen till mappen där det här certifikatet importeras. Nästa steg efter att klustret har skapats är att importera det här certifikatet.

    d. Välj fliken **information om virtuell dator** . Ange det lösen ord som du vill använda för den Virtual Machines (VM) som utgör klustret. Användarnamnet och lösenordet kan användas för att fjärransluta till de virtuella datorerna. Du måste också välja en virtuell datorstorlek och du kan ändra VM-avbildning om det behövs.

    > [!IMPORTANT]
    > Välj en SKU som stöder körningscontainrar. Windows Server-operativsystemet på klusternoderna måste vara kompatibla med Windows Server-operativsystemet för din container. Mer information finns i [Kompatibilitet mellan operativsystem för Windows Server-containrar och värdoperativsystem](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). Den här guiden skapar en Docker-avbildning baserat på Windows Server 2016 LTSC som standard. Containrar som bygger på den här avbildningen kan köras på kluster som skapas med Windows Server 2016 Datacenter med Containers. Men om du skapar ett kluster eller använder ett befintligt kluster baserat på en annan version av Windows Server, måste du ändra den OS-avbildning som behållaren baseras på. Öppna **Dockerfile** i **projektet fabrikamfiber. Web** Project, kommentera ut alla befintliga instruktioner som `FROM` baseras på en tidigare version av Windows Server och Lägg till en `FROM` instruktion som baseras på den önskade versions tag gen på [sidan Windows Server Core DockerHub](https://hub.docker.com/_/microsoft-windows-servercore). För ytterligare information om Windows Server Core-versioner, stöd för tids linjer och versions hantering, se [information om Windows Server Core-utgåvor](/windows-server/get-started/windows-server-release-info). 

    e. På fliken **Avancerat** visar du programporten som ska öppnas i lastbalanseraren när klustret distribueras. Det här är den port som du antecknade innan du började skapa klustret. Du kan också lägga till en befintlig Application Insights-nyckel som används för att dirigera programmets loggfiler.

    f. När du har ändrat inställningarna klickar du på knappen **Skapa**.

5. Åtgärden tar flera minuter. Utdatafönstret visar när klustret har skapats.

## <a name="install-the-imported-certificate"></a>Installera det importerade certifikatet

Installera certifikatet som importer ATS som en del av steget Skapa kluster, till den **aktuella användar** lagrings platsen och ange lösen ordet för den privata nyckeln du angav.

Du kan bekräfta installationen genom att öppna **hantera användar certifikat** från kontroll panelen och bekräfta att certifikatet installeras under **certifikat – aktuella användares**  ->  **personliga**  ->  **certifikat**. Certifikatet bör vara som *[kluster namn]*. *[Kluster plats]*. cloudapp.Azure.com, t. ex. *fabrikamfibercallcenter.southcentralus.cloudapp.Azure.com*. 

## <a name="allow-your-application-running-in-azure-to-access-sql-database"></a>Tillåt att ditt program körs i Azure för att få åtkomst till SQL Database

Tidigare skapade du en SQL-brandväggsregel som gav åtkomst till programmet som körs lokalt.  Nu måste du ge programmet som körs i Azure åtkomst till SQL DB-databasen.  Skapa en [tjänstslutpunkt för virtuellt nätverk](../azure-sql/database/vnet-service-endpoint-rule-overview.md) för Service Fabric-klustret och skapa sedan en regel som ger slutpunkten åtkomst till SQL-databasen. Glöm inte att ange variabeln för klusterresursgruppen som du antecknade när du skapade klustret.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```

## <a name="deploy-the-application-to-azure"></a>Distribuera appen till Azure

Nu när programmet är klart kan du distribuera det till klustret i Azure direkt från Visual Studio.  Högerklicka på **FabrikamFiber.CallCenterApplication**-programprojektet i Solution Explorer och välj **Publicera**.  I **Anslutningens slutpunkt** väljer du slutpunkten för klustret som du skapade tidigare.  I **Azure Container Registry** väljer du behållarregistret som du skapade tidigare.  Publicera programmet till klustret i Azure genom att klicka på **Publicera**.

![Publicera programmet][publish-app]

Följ distributionsförloppet i utdatafönstret När programmet har distribuerats öppnar du en webbläsare och anger klusteradressen och programporten. Till exempel `http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/`.

![Skärm bild av start sidan för Fabrikam fiber CallCenter-programmet som körs på azure.com. På sidan visas en instrument panel med en lista med support samtal.][fabrikam-web-page-deployed]

Om sidan inte kan läsas in eller om det inte går att begära certifikatet, kan du försöka öppna Explorer-sökvägen, till exempel, `https://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:19080/Explorer` och välja det nyligen installerade certifikatet.

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Konfigurera kontinuerlig integrering och distribution (CI/CD) med ett Service Fabric-kluster

Mer information om hur du använder Azure DevOps för att konfigurera en CI/CD-programdistribution till ett Service Fabric-kluster finns i [Självstudiekurs: Distribuera ett program med CI/CD till ett Service Fabric-kluster](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). Den process som beskrivs i självstudien är densamma för det här projektet (FabrikamFiber), förutom att du hoppar nedladdningen av Voting-exemplet och använder FabrikamFiber som namnet på lagringsplatsen i stället för Voting.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar tar du bort alla resurser du skapat.  Det enklaste sättet att göra det är att ta bort resursgrupperna som innehåller Service Fabric-klustret, Azure SQL-databasen och Azure Container Registry.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Använda en container med ett befintligt program med hjälp av Visual Studio
> * Skapa en databas i Azure SQL Database
> * Skapa ett Azure-containerregister
> * Distribuera ett Service Fabric-program till Azure

I nästa del av självstudierna lär du dig hur du [distribuerar ett containerprogram med CI/CD till ett Service Fabric-kluster](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png