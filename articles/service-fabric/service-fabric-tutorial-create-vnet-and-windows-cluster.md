---
title: Skapa ett Service Fabric kluster som kör Windows i Azure
description: I den här självstudien får du lära dig hur du distribuerar ett Windows Service Fabric-kluster till ett virtuellt Azure-nätverk och en nätverks säkerhets grupp med hjälp av PowerShell.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: a7390858e55a456ec5fb2f851be1a7443be97082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245066"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Självstudie: Distribuera ett Service Fabric kluster som kör Windows till ett virtuellt Azure-nätverk

Den här självstudien ingår i en serie. Du lär dig hur du distribuerar ett Azure Service Fabric-kluster som kör Windows till ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md) och en [nätverks säkerhets grupp](../virtual-network/virtual-network-vnet-plan-design-arm.md) med hjälp av PowerShell och en mall. När du är klar har du ett kluster som körs i molnet som du kan distribuera program till. Information om hur du skapar ett Linux-kluster som använder Azure CLI finns i [skapa ett säkert Linux-kluster i Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

I den här självstudien beskrivs ett produktionsscenario. Om du vill skapa ett mindre kluster i test syfte, se [skapa ett test kluster](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * skapa ett VNET i Azure med PowerShell
> * skapa ett nyckelvalv och ladda upp ett certifikat
> * Konfigurera Azure Active Directory-autentisering
> * Konfigurera diagnostik-samling
> * Konfigurera EventStore-tjänsten
> * Konfigurera Azure Monitor loggar
> * skapa ett säkert Service Fabric-kluster i Azure med PowerShell
> * skydda klustret med ett X.509-certifikat
> * Ansluta till klustret med PowerShell
> * Ta bort ett kluster

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * skapa ett säkert kluster i Azure
> * [Övervaka ett kluster](service-fabric-tutorial-monitor-cluster.md)
> * [Skala in eller ut ett kluster](service-fabric-tutorial-scale-cluster.md)
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installera [modulen Service Fabric SDK och PowerShell](service-fabric-get-started.md).
* Installera [Azure PowerShell](/powershell/azure/install-az-ps).
* Granska viktiga begrepp i [Azure-kluster](service-fabric-azure-clusters-overview.md).
* [Planera och förbereda](service-fabric-cluster-azure-deployment-preparation.md) för distribution av produktions kluster.

Följande procedurer skapar ett Service Fabric-kluster med sju noder. Använd [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnader som uppstår genom att köra ett Service Fabric kluster i Azure.

## <a name="download-and-explore-the-template"></a>Ladda ned och titta närmare på mallen

Ladda ned följande Azure Resource Manager mallfiler:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Den här mallen distribuerar ett säkert kluster med sju virtuella datorer och tre nodtyper till ett virtuellt nätverk och en nätverkssäkerhetsgrupp.  Andra exempelmallar finns på [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). [azuredeploy.jspå][template] distribuerar ett antal resurser, inklusive följande.

### <a name="service-fabric-cluster"></a>Service Fabric-kluster

I resursen **Microsoft.ServiceFabric/kluster** konfigureras ett Windows-kluster med följande egenskaper:

* Tre nodtyper.
* Fem noder i den primära nodtypen (kan konfigureras i mallparametrar) och en nod i var och en av de andra två typerna av noder.
* OS: Windows Server 2016 Data Center med behållare (kan konfigureras i mallparametrar).
* Certifikatet är skyddat (kan konfigureras i mallparametrar).
* [Omvänd proxy](service-fabric-reverseproxy.md) är aktiverat.
* [DNS-tjänsten](service-fabric-dnsservice.md) är aktive rad.
* [Hållbarhets nivå](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) på brons nivå (kan konfigureras i mallparametrar).
* [Tillförlitlighets nivån](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) Silver (kan konfigureras i mallparametrar).
* Klient anslutnings slut punkt: 19000 (kan konfigureras i mallparametrar).
* HTTP Gateway-slutpunkt: 19080 (kan konfigureras i mallparametrar).

### <a name="azure-load-balancer"></a>Azure Load Balancer

I **Microsoft. Network/belastningsutjämnare** -resursen konfigureras en belastningsutjämnare. Avsökningar och regler har kon figurer ATS för följande portar:

* Klient anslutnings slut punkt: 19000
* HTTP-gatewayslutpunkt: 19080
* Program port: 80
* Program port: 443
* omvänd proxy för Service Fabric: 19081

Om andra program portar behövs måste du justera **Microsoft. Network/belastningsutjämnare** -resursen och resursen **Microsoft. Network/networkSecurityGroups** för att tillåta trafiken i.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuellt nätverk, undernät och nätverkssäkerhetsgrupp

Namnen på det virtuella nätverket, undernätet och nätverkssäkerhetsgruppen deklareras i mallparametrarna. Adressutrymmen i det virtuella nätverket och undernätet deklareras också i mallparametrarna och konfigureras i resursen **Microsoft.Network/virtualNetworks**:

* Adress utrymme för virtuellt nätverk: 172.16.0.0/20
* Service Fabric-undernätets adressutrymme: 172.16.2.0/23

Följande regler för inkommande trafik är aktiverade i resursen **Microsoft.Network/networkSecurityGroups**. Du kan ändra portvärdena genom att ändra mallvariablerna.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* Tillfälligt port intervall: 49152 till 65534 (minst 256 portar krävs).
* Portar för programanvändning: 80 och 443
* Program port intervall: 49152 till 65534 (används för kommunikation mellan tjänster och tjänster. Andra portar är inte öppna i belastningsutjämnaren.
* Blockera alla andra portar

Om andra program portar behövs måste du justera **Microsoft. Network/belastningsutjämnare** -resursen och resursen **Microsoft. Network/networkSecurityGroups** för att tillåta trafiken i.

### <a name="windows-defender"></a>Windows Defender
Som standard installeras [Windows Defender Antivirus program](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) och fungerar på windows Server 2016. Användar gränssnittet installeras som standard på vissa SKU: er, men det är inte obligatoriskt. För varje nodtyp/VM-skalningsuppsättning som deklareras i mallen används [Azure VM Antimalware-tillägget](../virtual-machines/extensions/iaas-antimalware-windows.md) för att utesluta Service Fabric-katalogerna och -processerna:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Ställa in mallparametrar

Parameterfilen [azuredeploy.parameters.json][parameters] deklarerar många värden som används till att distribuera klustret och associerade resurser. Följande är parametrar som ska ändras för distributionen:

**Parameter** | **Exempel värde** | **Kommentarer** 
|---|---|---|
|adminUserName|vmadmin| Administratörsnamn för virtuella datorer i klustret. [Användar namns krav för virtuell dator](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Administratörslösenord för virtuella datorer i klustret. [Lösen ords krav för virtuell dator](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| Namnet på klustret. Får endast innehålla bokstäver och siffror. Längden ska vara mellan 3 och 23 tecken.|
|location|USA, södra centrala| Klustrets placering. |
|certificateThumbprint|| <p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i certifikatets SHA1-tumavtrycksvärde. Till exempel ”6190390162C988701DB5676EB81083EA608DCCF3”.</p> |
|certificateUrlValue|| <p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil. </p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i certifikatets webbadress. Till exempel "https: \/ /mykeyvault.Vault.Azure.net:443/Secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i källans nyckelvärde. Till exempel ”/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”.</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Konfigurera Azure Active Directory-klientautentisering
För Service Fabric-kluster som distribueras i ett offentligt nätverk som hanteras i Azure är rekommendationen för ömsesidig klient-till-nod-autentisering:
* Använd Azure Active Directory för klient identitet.
* Använd ett certifikat för Server identitet och TLS-kryptering av HTTP-kommunikation.

Konfiguration av Azure Active Directory (Azure AD) för att autentisera klienter för ett Service Fabric kluster måste göras innan [klustret skapas](#createvaultandcert). Med Azure AD kan organisationer (som kallas klientorganisationer) hantera användaråtkomst till program. 

Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, däribland den webbaserade [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) och [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Därför kan du skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett internt program.  När programmen har skapats tilldelar du användare till roller som skrivskyddad och administratör.

> [!NOTE]
> Du måste slutföra följande steg innan du skapar klustret. Eftersom skripten förväntar sig klusternamn och slutpunkter bör värdena vara planerade och inte värden som du redan har skapat.

I den här artikeln förutsätter vi att du redan har skapat en klient. Om du inte har gjort det börjar du med [att läsa hur du får en Azure Active Directory klient](../active-directory/develop/quickstart-create-new-tenant.md).

Vi har skapat en uppsättning Windows PowerShell-skript för att förenkla stegen som ingår i att konfigurera Azure AD med ett Service Fabric-kluster. [Ladda ned skripten](https://github.com/Azure-Samples/service-fabric-aad-helpers) till datorn.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Skapa Azure AD-program och tilldela användare till roller
Skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett internt program. När du har skapat programmen som ska representera klustret, tilldelar du användarna de roller som [stöds av Service Fabric](service-fabric-cluster-security-roles.md): skrivskyddad och administratör.

Kör `SetupApplications.ps1` och ange klientorganisations-ID, klusternamn och svars-URL för webbprogram som parametrar. Ange användar namn och lösen ord för användarna. Exempel:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> För nationella moln (till exempel Azure Government, Azure Kina, Azure Germany) anger du `-Location` parametern.

*TenantId* eller katalog-ID finns i [Azure-portalen](https://portal.azure.com). Välj **Azure Active Directory**  >  **Egenskaper** och kopiera värdet för **katalog-ID** .

*ClusterName* (Klusternamn) används för att prefigera de AD-program som skapas av skriptet. Det behöver inte exakt matcha det faktiska kluster namnet. Det gör det bara lättare att mappa Azure AD-artefakter till Service Fabric kluster som används.

*WebApplicationReplyUrl* är den standardslutpunkt som Azure AD returnerar till dina användare när de har slutfört inloggningen. Ange den här slutpunkten som Service Fabric Explorer-slutpunkt för ditt kluster, vilken som standard är:

https://&lt;cluster_domain&gt;:19080/Explorer

Du uppmanas att logga in på ett konto som har administratörs behörighet för Azure AD-klienten. När du har loggat in skapar skriptet webbprogrammet och det interna programmet för att representera ditt Service Fabric-kluster. I klientens program i [Azure Portal](https://portal.azure.com)bör du se två nya poster:

   * *Kluster* \_ namn Flernodskluster
   * *Kluster* \_ namn Klientsession

Skriptet skriver ut JSON-filen som krävs av Resource Manager-mallen när du skapar klustret, så det är en bra idé att låta PowerShell-fönstret vara öppet.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Lägga till Azure AD-konfiguration för att använda Azure AD för klientåtkomst
I [azuredeploy.json][template] konfigurerar du Azure AD i avsnittet **Microsoft.ServiceFabric/clusters**. Lägg till parametrar för klientorganisations-ID, klusterprogram-ID och klientprogram-ID.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Lägg till parametervärdena i parameterfilen [azuredeploy.parameters.json][parameters]. Exempel:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Konfigurera diagnostik-samling i klustret
När du kör ett Service Fabric kluster, är det en bra idé att samla in loggarna från alla noder på en central plats. Genom att logga in på en central plats kan du analysera och felsöka problem i klustret, eller problem i de program och tjänster som körs i klustret.

Ett sätt att ladda upp och samla in loggar är att använda tillägget Azure-diagnostik (WAD), som överför loggar till Azure Storage och även har möjlighet att skicka loggar till Azure Application insikter eller Event Hubs. Du kan också använda en extern process för att läsa händelserna från lagringen och placera dem i en analys plattforms produkt, till exempel Azure Monitor loggar eller en annan logg tolknings lösning.

Om du följer den här självstudien har du redan konfigurerat en diagnostisk samling i [mallen][template].

Om du har ett befintligt kluster som inte har någon diagnostik distribuerad kan du lägga till eller uppdatera det via kluster mal len. Ändra den Resource Manager-mall som används för att skapa det befintliga klustret eller ladda ned mallen från portalen. Ändra template.jspå filen genom att utföra följande uppgifter:

Lägg till en ny lagrings resurs i avsnittet resurser i mallen:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Lägg sedan till parametrar för lagrings kontots namn och skriv till avsnittet parametrar i mallen. Ersätt plats hållarens text lagrings konto namn här med namnet på det lagrings konto som du vill ha.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Lägg sedan till **IaaSDiagnostics** -tillägget i fil tilläggs arrayen för egenskapen **VirtualMachineProfile** för varje **Microsoft. Compute/virtualMachineScaleSets-** resurs i klustret.  Om du använder [exempel mal len][template]finns det tre skalnings uppsättningar för virtuella datorer (en för varje nodtyp i klustret).

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>Konfigurera EventStore-tjänsten
EventStore-tjänsten är ett övervaknings alternativ i Service Fabric. EventStore är ett sätt att förstå tillstånd för ditt kluster eller arbets belastningar vid en viss tidpunkt. EventStore är en tillstånds känslig Service Fabric tjänst som upprätthåller händelser från klustret. Händelsen exponeras via Service Fabric Explorer, REST och API: er. EventStore frågar klustret direkt för att hämta diagnostikdata för alla entiteter i klustret och ska användas för att hjälpa:

* Diagnostisera problem i utveckling eller testning eller där du kan använda en övervaknings pipeline
* Bekräfta att hanterings åtgärder som du vidtar i klustret bearbetas korrekt
* Få en "ögonblicks bild" av hur Service Fabric interagerar med en viss entitet



Om du vill aktivera EventStore-tjänsten i klustret lägger du till följande i egenskapen **fabricSettings** för resursen **Microsoft. ServiceFabric/Clusters** :

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Konfigurera Azure Monitor loggar för klustret

Azure Monitor loggar är vår rekommendation att övervaka händelser på kluster nivå. Om du vill konfigurera Azure Monitor loggar för att övervaka klustret måste du ha [aktiverat diagnostik för att visa händelser på kluster nivå](#configure-diagnostics-collection-on-the-cluster).  

Arbets ytan måste vara ansluten till de diagnostikdata som kommer från klustret.  Dessa loggdata lagras i *applicationDiagnosticsStorageAccountName* lagrings konto, i tabellerna WADServiceFabric * EventTable, WADWindowsEventLogsTable och WADETWEventTable.

Lägg till Azure Log Analytics-arbetsytan och Lägg till lösningen i arbets ytan:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Lägg sedan till parametrar
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Lägg sedan till variabler:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Lägg till tillägget Log Analytics agent i varje skalnings uppsättning för virtuella datorer i klustret och Anslut agenten till arbets ytan Log Analytics. Detta möjliggör insamling av diagnostikdata om behållare, program och prestanda övervakning. Genom att lägga till det som ett tillägg för den virtuella datorns skalnings uppsättnings resurs, ser Azure Resource Manager till att den installeras på varje nod, även när du skalar klustret.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Distribuera det virtuella nätverket och klustret

Konfigurera sedan nätverkstopologin och distribuera Service Fabric-klustret. [azuredeploy.jsi][template] Resource Manager-mallen skapar ett virtuellt nätverk, ett undernät och en nätverks säkerhets grupp för Service Fabric. Mallen distribuerar också ett kluster med certifikatsäkerhet aktiverad. För produktions kluster använder du ett certifikat från en certifikat utfärdare som kluster certifikat. Ett självsignerat certifikat kan användas för att skydda testkluster.

Mallen i den här artikeln distribuerar ett kluster som använder certifikatets tumavtryck för att identifiera klustercertifikatet. Två certifikat kan inte ha samma tumavtryck, vilket gör certifikathantering svårare. Att växla ett distribuerat kluster från certifikat tumavtrycken till Certifikatets gemensamma namn fören klar certifikat hanteringen. Information om hur du uppdaterar klustret så att det använder certifikatets delade namn för certifikat hantering, finns [i Ändra kluster till certifikatets delade namn hantering](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Skapa ett kluster med hjälp av ett befintligt certifikat

Följande skript använder cmdleten [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) och en mall för att distribuera ett nytt kluster i Azure. Cmdleten skapar ett nytt nyckel valv i Azure och laddar upp certifikatet.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Skapa ett kluster med ett nytt, självsignerat certifikat

Följande skript använder cmdleten [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) och en mall för att distribuera ett nytt kluster i Azure. Cmdleten skapar ett nytt nyckel valv i Azure, lägger till ett nytt självsignerat certifikat i nyckel valvet och laddar ned certifikat filen lokalt.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Ansluta till det skyddade klustret

Anslut till klustret med hjälp av Service Fabric PowerShell-modulen som är installerad med Service Fabric SDK.  Först installerar du certifikatet i det personliga arkivet för den aktuella användaren på datorn. Kör följande PowerShell-kommando:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Nu är du redo att ansluta till ditt säkra kluster.

PowerShell-modulen **Service Fabric** har många cmdletar för hantering av Service Fabric-kluster, program och tjänster. Använd cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) till att ansluta till det säkra klustret. Certifikatets SHA1-tumavtryck och information om slutpunkten för anslutning finns i utdata från föregående steg.

Om du tidigare har konfigurerat Azure AD-klientautentisering kör du följande kommando: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Om du inte har konfigurerat Azure AD-klientautentisering kör du följande kommando:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Kontrol lera att du är ansluten och att klustret är felfritt med hjälp av cmdleten [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) .

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Rensa resurser

De andra artiklarna i den här själv studie serien använder det kluster som du har skapat. Om du inte genast fortsätter till nästa artikel kanske du vill [ta bort klustret](./service-fabric-tutorial-delete-cluster.md) för att undvika kostnader.

## <a name="next-steps"></a>Nästa steg

Gå vidare till följande självstudie för att lära dig hur du skalar klustret.

> [!div class="checklist"]
> * skapa ett VNET i Azure med PowerShell
> * skapa ett nyckelvalv och ladda upp ett certifikat
> * Konfigurera Azure Active Directory-autentisering
> * Konfigurera diagnostik-samling
> * Konfigurera EventStore-tjänsten
> * Konfigurera Azure Monitor loggar
> * skapa ett säkert Service Fabric-kluster i Azure med PowerShell
> * skydda klustret med ett X.509-certifikat
> * Ansluta till klustret med PowerShell
> * Ta bort ett kluster

Fortsätt sedan till följande självstudie och lär dig hur du övervakar klustret.
> [!div class="nextstepaction"]
> [Övervaka ett kluster](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
