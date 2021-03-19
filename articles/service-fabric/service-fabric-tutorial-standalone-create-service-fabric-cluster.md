---
title: Installera Service Fabric fristående klient
description: I den här självstudien får du lära dig hur du installerar den fristående Service Fabric fristående klienten i klustret.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: ae0b343be986f4d8d5176c1f39eef6b23ca81278
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91840650"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>Självstudie: Installera och skapa Service Fabric-kluster

Med fristående Service Fabric-kluster kan du välja en egen miljö och skapa ett kluster som en del av metoden ”valfritt operativsystem, valfritt moln” som används i Service Fabric. I den här självstudien skapar du ett fristående kluster som finns på AWS eller Azure och installerar ett program i det.

Den här självstudien är del två i en serie. Självstudien vägleder dig genom stegen för att skapa ett fristående Service Fabric-kluster.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> * Ladda ned och installera det fristående Service Fabric-paketet
> * Skapa Service Fabric-klustret
> * Ansluta till Service Fabric-klustret

## <a name="download-the-service-fabric-for-windows-server-package"></a>Ladda ned Service Fabric för Windows Server-paketet

Fabric Service tillhandahåller ett konfigurationspaket för att skapa fristående Service Fabric-kluster.  [Ladda ned installationspaketet](https://go.microsoft.com/fwlink/?LinkId=730690) på den lokala datorn.  När du har hämtat kopian via RDP-anslutningen till den virtuella datorn och klistrar in den på Skriv bordet.

Välj zip-filen och öppna snabb menyn och välj **extrahera alla**  >  **extrahera**.  När du extraherar filerna skapas en mapp på skrivbordet med samma namn som zip-filen.

Om du vill kan du hämta mer information om [innehållet i installationspaketet](service-fabric-cluster-standalone-package-contents.md).

## <a name="set-up-your-configuration-file"></a>Konfigurera konfigurationsfilen

Du utvecklar ett Windows-kluster med tre noder, så du måste ändra filen `ClusterConfig.Unsecure.MultiMachine.json`.

Uppdatera sedan de tre IP-adresserna som inträffar i filen på rad 8, 15 och 22 till IP-adresserna för var och en av instanserna.

När du har uppdaterat noderna visas de på följande sätt:

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

Du måste därefter uppdatera ett par av egenskaperna.  På rad 34 måste du ändra anslutningssträngen för diagnostiklagret, som ska se ut så här `"connectionstring": "C:\\ProgramData\\SF\\DiagnosticsStore"`

Slutligen går du till avsnittet `nodeTypes` i konfigurationen och lägger till ett nytt avsnitt för att mappa de tillfälliga portarna som ska användas av Windows.  Konfigurationsfilen bör likna följande:

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>Validera miljön

Skriptet *TestConfiguration.ps1* i det fristående paketet används för att analysera bästa praxis och för att kontrollera om ett kluster kan distribueras i en viss miljö. [Distributionsförberedelser](service-fabric-cluster-standalone-deployment-preparation.md) visar krav och förutsättningar. Kör skriptet för att kontrollera om du kan skapa utvecklingsklustret:

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

Du bör se utdata som exemplet nedan. Om det nedre fältet ”Godkänd” returneras som `True`, har hälsokontrollerna godkänts och klustret ser ut att kunna distribueras baserat på indatakonfigurationen.

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>Skapa klustret

När du har verifierat kluster konfigurationen kör du *CreateServiceFabricCluster.ps1* -skriptet för att distribuera Service Fabric-klustret till de virtuella datorerna i konfigurations filen.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

Om allt fungerar får du utdata som ser ut så här:

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> Distributionsspårningar skrivs till den virtuella dator där du körde PowerShell-skriptet CreateServiceFabricCluster.ps1. Dessa finns i undermappen DeploymentTraces i katalogen som skriptet kördes från. Om du vill se ifall Service Fabric distribuerades korrekt till en dator, letar du reda på de installerade filerna i katalogen FabricDataRoot enligt anvisningarna i klusterkonfigurationsfilens FabricSettings-avsnitt (som standard c:\ProgramData\SF). Dessutom kan man se att processerna FabricHost.exe och Fabric.exe körs i Aktivitetshanteraren.
>
>

### <a name="open-service-fabric-explorer"></a>Öppna Service Fabric Explorer

Nu kan du ansluta till klustret med Service Fabric Explorer antingen direkt från en av datorerna med http: \/ /localhost: 19080/Explorer/index.html eller fjärran slutet med http: \/ /< *IPAddressofaMachine*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Lägga till och ta bort noder

Du kan lägga till eller ta bort noder i det fristående Service Fabric-klustret när ditt företag behöver ändras. Se [Lägga till eller ta bort noder i ett fristående Service Fabric-kluster](service-fabric-cluster-windows-server-add-remove-nodes.md) för detaljerade anvisningar.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du överför stora mängder slumpmässiga data till ett lagrings konto parallellt, till exempel hur du:

> [!div class="checklist"]
> * Konfigurera anslutningssträngen
> * Skapa programmet
> * Kör programmet
> * Validera antalet anslutningar

Gå vidare till avsnitt tre i serien för att installera ett program i klustret som du skapade.

> [!div class="nextstepaction"]
> [Installera programmet i Service Fabric-klustret](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
