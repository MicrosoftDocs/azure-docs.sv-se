---
title: Skala upp en Azure Service Fabric Primary Node-typ
description: Skala Service Fabric klustret lodrätt genom att lägga till en ny nodtyp och ta bort föregående.
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.openlocfilehash: 325ece761481077171a670c52e9d98071237601a
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251189"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Skala upp en primär nodtyp för Service Fabric-klustret

I den här artikeln beskrivs hur du skalar en Service Fabric-klusters primära nodtyp med minimal stillestånds tid. Den allmänna strategin för att uppgradera en Service Fabric klusternod är att:

1. Lägg till en ny nodtyp till ditt Service Fabric-kluster, som backas upp av den uppgraderade (eller ändrade) SKU: n och konfigurationen för skalnings uppsättningen för virtuella datorer. Det här steget omfattar även att konfigurera en ny belastningsutjämnare, undernät och offentlig IP-adress för skalnings uppsättningen.

1. När båda de ursprungliga och uppgraderade skalnings uppsättningarna körs sida vid sida inaktiverar du de ursprungliga instans instanserna en i taget så att system tjänsterna (eller replikerna av tillstånds känsliga tjänster) migreras till den nya skalnings uppsättningen.

1. Kontrol lera att klustret och de nya noderna är felfria och ta sedan bort den ursprungliga skalnings uppsättningen (och relaterade resurser) och Node-tillståndet för de borttagna noderna.

Följande vägleder dig genom processen för att uppdatera den virtuella datorns storlek och operativ system för primära nodtyper virtuella datorer i ett exempel kluster med [silver tålighet](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), som backas upp av en enda skalnings uppsättning med fem noder. Vi kommer att uppgradera den primära nodtypen:

- Från VM-storlek *Standard_D2_V2* till *standard D4_V2* och
- Från VM operativ system *Windows server 2016 Data Center med behållare* till *Windows Server 2019 Data Center med behållare*.

> [!WARNING]
> Innan du försöker utföra den här proceduren i ett produktions kluster rekommenderar vi att du studerar exempelfilerna och kontrollerar processen mot ett test kluster. Klustret kan också vara otillgängligt under en kort tids period.
>
> Försök inte att skala upp en primär nodtyp om klustrets status är ohälsosam, eftersom detta bara kommer att göra klustret ytterligare.

Här är de stegvisa mallar för Azure-distribution som vi använder för att slutföra det här exemplet på uppgraderings scenario: https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade

## <a name="set-up-the-test-cluster"></a>Konfigurera test klustret

Vi konfigurerar det första Service Fabric test klustret. Börja med att [hämta](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) Azure Resource Manager exempel mallar som vi ska använda för att slutföra det här scenariot.

Logga sedan in på ditt Azure-konto.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Öppna sedan [*parameters.jspå*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) filen och uppdatera värdet för `clusterName` till något unikt (i Azure).

Följande kommandon vägleder dig genom att skapa ett nytt självsignerat certifikat och distribuera test klustret. Om du redan har ett certifikat som du vill använda, kan du hoppa över att [använda ett befintligt certifikat för att distribuera klustret](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Generera ett självsignerat certifikat och distribuera klustret

Först tilldelar du de variabler du behöver för Service Fabric kluster distribution. Justera värdena för `resourceGroupName` ,  `certSubjectName` , `parameterFilePath` och `templateFilePath` för ditt eget konto och din miljö:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> Kontrol lera att `certOutputFolder` platsen finns på den lokala datorn innan du kör kommandot för att distribuera ett nytt Service Fabric-kluster.

Distribuera sedan Service Fabric test kluster:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

När distributionen är klar letar du upp *. pfx* -filen ( `$certPfx` ) på den lokala datorn och importerar den till certifikat arkivet:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

Åtgärden returnerar certifikatets tumavtryck, som du nu kan använda för att [ansluta till det nya klustret](#connect-to-the-new-cluster-and-check-health-status) och kontrol lera dess hälso status. (Hoppa över följande avsnitt, som är en alternativ metod för kluster distribution.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Använd ett befintligt certifikat för att distribuera klustret

Alternativt kan du använda ett befintligt Azure Key Vault-certifikat för att distribuera test klustret. För att göra detta måste du [Hämta referenser till Key Vault](#obtain-your-key-vault-references) och tumavtryck för certifikatet.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Ange sedan ett resurs grupp namn för klustret och ange `templateFilePath` `parameterFilePath` platserna och:

> [!NOTE]
> Den angivna resurs gruppen måste redan finnas och finnas i samma region som din Key Vault.

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

Kör slutligen följande kommando för att distribuera det första test klustret:

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Anslut till det nya klustret och kontrol lera hälso status

Anslut till klustret och se till att alla fem noderna är felfria (Ersätt `clusterName` `thumb` variablerna och med dina egna värden):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Vi är nu redo att påbörja uppgraderings proceduren.

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>Distribuera en ny typ av primär nod med en uppgraderad skalnings uppsättning

För att kunna uppgradera (vertikalt skala) en nodtyp måste vi först distribuera en ny nodtyp som backas upp av en ny skalnings uppsättning och stöd resurser. Den nya skalnings uppsättningen markeras som primär ( `isPrimary: true` ), precis som den ursprungliga skalnings uppsättningen (om du inte gör en uppgradering av en nodtyp som inte är primär). Resurserna som skapas i följande avsnitt kommer slutligen att bli den nya primära nodtypen i klustret, och de ursprungliga primära nodtypen kommer att tas bort.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Uppdatera kluster mal len med den uppgraderade skalnings uppsättningen

Här är ändringar i den ursprungliga kluster distributions mal len för att lägga till en ny typ av primär nod och stöd resurser.

De ändringar som krävs för det här steget har redan gjorts åt dig i [*Step1-AddPrimaryNodeType.jspå*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json) mallfilen, och följande förklarar dessa ändringar i detalj. Om du vill kan du hoppa över förklaringen och fortsätta att [skaffa Key Vault referenser](#obtain-your-key-vault-references) och [distribuera den uppdaterade mallen](#deploy-the-updated-template) som lägger till en ny typ av primär nod i klustret.

> [!Note]
> Se till att du använder namn som är unika från den ursprungliga nodtypen, skalnings uppsättning, belastningsutjämnare, offentlig IP och undernät för den ursprungliga primära nodtypen, eftersom dessa resurser kommer att tas bort i ett senare steg i processen.

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>Skapa ett nytt undernät i det befintliga virtuella nätverket

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>Skapa en ny offentlig IP-adress med en unik domainNameLabel

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>Skapa en ny belastningsutjämnare för den offentliga IP-adressen

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>Skapa en ny skalnings uppsättning för virtuella datorer (med uppgraderad VM och OS SKU: er)

Referens för nodtyp

```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

VM-SKU

```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

OS-SKU

```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Se också till att du inkluderar eventuella ytterligare tillägg som krävs för din arbets belastning.

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Lägg till en ny typ av primär nod i klustret

Nu när den nya nodtypen (vmNodeType1Name) har sitt eget namn, undernät, IP, belastningsutjämnare och skalnings uppsättning, kan den återanvända alla andra variabler från den ursprungliga nodtypen (till exempel `nt0applicationEndPort` , `nt0applicationStartPort` , och `nt0fabricTcpGatewayPort` ):

```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```

När du har implementerat alla ändringar i mall-och frågeparametrar kan du fortsätta till nästa avsnitt för att hämta dina Key Vault referenser och distribuera uppdateringarna till klustret.

### <a name="obtain-your-key-vault-references"></a>Hämta dina Key Vault referenser

Om du vill distribuera den uppdaterade konfigurationen behöver du flera referenser till kluster certifikatet som lagras i Key Vault. Det enklaste sättet att hitta dessa värden är genom Azure Portal. Du behöver:

* **Key Vault-URL för ditt kluster certifikat.** Från din Key Vault i Azure Portal väljer du **certifikat**  >  *ditt önskade certifikat*  >  **hemlighets identifierare**:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **Tumavtryck för ditt kluster certifikat.** (Du har förmodligen redan det här om du har [anslutit till det första klustret](#connect-to-the-new-cluster-and-check-health-status) för att kontrol lera hälso statusen.) Från bladet samma certifikat (**certifikat**  >  som *det önskade certifikatet*) i Azure Portal kopierar du **X. 509 SHA-1-tumavtryck (i hex)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Resurs-ID för Key Vault.** Från Key Vault i Azure Portal väljer du **Egenskaper**  >  **resurs-ID**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen

Justera `templateFilePath` efter behov och kör följande kommando:

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

När distributionen är klar kontrollerar du kluster hälsan igen och ser till att alla noder på båda typerna av noder är felfria.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>Migrera Seed-noder till den nya nodtypen

Nu är det dags att uppdatera den ursprungliga nodtypen som icke-primär och börja inaktivera dess noder. När noderna inaktiverar migreras klustrets system tjänster och dirigeringsrouters till den nya skalnings uppsättningen.

### <a name="unmark-the-original-node-type-as-primary"></a>Avmarkera den ursprungliga nodtypen som primär

Ta först bort `isPrimary` beteckningen i mallen från den ursprungliga nodtypen.

```json
{
    "isPrimary": false,
}
```

Distribuera sedan mallen med uppdateringen. Detta initierar migreringen av startnoder till den nya skalnings uppsättningen.

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> Det tar lite tid att slutföra migreringen av Dirigerings-noden till den nya skalnings uppsättningen. För att garantera data konsekvens kan endast en Seed-nod ändras i taget. För varje Seed-nods ändring krävs en kluster uppdatering. att ersätta en Seed-nod kräver därför två kluster uppgraderingar (en varje för nod tillägg och borttagning). Om du uppgraderar de fem startnoderna i det här exempel scenariot uppstår tio kluster uppgraderingar.

Använd Service Fabric Explorer för att övervaka migreringen av dirigeringsrouters till den nya skalnings uppsättningen. Noderna i den ursprungliga nodtypen (nt0vm) ska vara *falska* i kolumnen **är Seed-Node** och de av den nya nodtypen (nt1vm) kommer att vara *sanna*.

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>Inaktivera noder i skalnings uppsättningen för den ursprungliga nodtypen

När alla startnoder har migrerats till den nya skalnings uppsättningen kan du inaktivera noderna i den ursprungliga skalnings uppsättningen.

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```

Använd Service Fabric Explorer för att övervaka förloppet för noder i den ursprungliga skalnings uppsättningen från att *inaktivera* till *inaktive rad* status.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="Service Fabric Explorer visar status för inaktiverade noder":::

För silver-och guld tålighet kommer vissa noder att hamna i inaktiverat läge, medan andra kan finnas kvar *i ett inaktiverat* tillstånd. I Service Fabric Explorer, se fliken **information** för noderna i inaktivera tillstånd. Om de visar en *väntande säkerhets kontroll* av typen *EnsurePartitionQuorem* (garanterar kvorum för infrastruktur tjänst partitioner), är det säkert att fortsätta.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="Du kan fortsätta med att stoppa data och ta bort noder som fastnat i status &quot;Inaktivera&quot; om de visar en väntande säkerhets kontroll av typen &quot;EnsurePartitionQuorum&quot;.":::

Om klustret är brons hållbarhet väntar du tills alla noder når *inaktiverat* tillstånd.

### <a name="stop-data-on-the-disabled-nodes"></a>Stoppa data på de inaktiverade noderna

Nu kan du stoppa data på de inaktiverade noderna.

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>Ta bort den ursprungliga nodtypen och rensa dess resurser

Vi är redo att ta bort den ursprungliga nodtypen och dess tillhör ande resurser för att avsluta den vertikala skalnings proceduren.

### <a name="remove-the-original-scale-set"></a>Ta bort den ursprungliga skalnings uppsättningen

Ta först bort nodtypen storleks ändring.

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>Ta bort de ursprungliga IP-och belastnings Utjämnings resurserna

Du kan nu ta bort den ursprungliga IP-adressen och belastnings Utjämnings resurserna. I det här steget ska du också uppdatera DNS-namnet.

> [!Note]
> Det här steget är valfritt om du redan använder en offentlig IP-adress och en belastningsutjämnare med *standard* -SKU. I det här fallet kan du ha flera skalnings uppsättningar/Node-typer under samma belastningsutjämnare.

Kör följande kommandon och ändra `$lbname` värdet efter behov.

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>Ta bort nod-tillstånd från den ursprungliga nodtypen

Noderna för den ursprungliga nodtypen kommer nu att visa *fel* för deras **hälso tillstånd**. Ta bort deras nods status från klustret.

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

Service Fabric Explorer bör nu bara avspegla de fem noderna i den nya nodtypen (nt1vm), alla med hälso tillstånds värden för *OK*. Ditt kluster hälso tillstånd kommer fortfarande att visa *fel*. Vi kommer att åtgärda detta genom att uppdatera mallen så att den återspeglar de senaste ändringarna och omdistributionen.

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>Uppdatera distributions mal len så att den återspeglar den nyligen skalade primära nodtypen

De ändringar som krävs för det här steget har redan gjorts åt dig i [*Step3-CleanupOriginalPrimaryNodeType.jspå*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json) mallfilen, och i följande avsnitt förklaras mallens ändringar i detalj. Om du vill kan du hoppa över förklaringen och fortsätta att [distribuera den uppdaterade mallen](#deploy-the-finalized-template) och slutföra självstudien.

#### <a name="update-the-cluster-management-endpoint"></a>Uppdatera kluster hanterings slut punkten

Uppdatera klustret `managementEndpoint` i distributions mal len för att referera till den nya IP-adressen (genom att uppdatera *VmNodeType0Name* med *vmNodeType1Name*).

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>Ta bort referensen för den ursprungliga nodtypen

Ta bort referensen till den ursprungliga nodtypen från Service Fabric resursen i distributions mal len:

```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>Konfigurera hälso principer för att ignorera befintliga fel

Uppdatera kluster resursen i mallen och konfigurera hälso principer för att ignorera `fabric:/System` program hälsa genom att lägga till *applicationDeltaHealthPolicies* under egenskaper för kluster resurs på det sätt som visas nedan. Principen nedan kommer att ignorera befintliga fel men inte tillåta nya hälso fel.

```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>Ta bort stöd resurser för den ursprungliga nodtypen

Ta bort alla andra resurser som är relaterade till den ursprungliga nodtypen från ARM-mallen och parameter filen. Ta bort följande:

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>Distribuera den slutgiltiga mallen

Distribuera slutligen den ändrade Azure Resource Manager-mallen.

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> Det här steget tar en stund, vanligt vis upp till två timmar.

Uppgraderingen kommer att ändra inställningarna till *InfrastructureService*. Därför krävs en omstart av noden. I det här fallet ignoreras *forceRestart* . Parametern `upgradeReplicaSetCheckTimeout` anger den maximala tid som Service Fabric väntar på att en partition ska vara i ett säkert tillstånd, om den inte redan är i säkert tillstånd. När säkerhets kontrollerna har godkänts för alla partitioner på en nod fortsätter Service Fabric med uppgraderingen på noden. Värdet för parametern `upgradeTimeout` kan minskas till 6 timmar, men för maximal säkerhet 12 timmar bör användas.

När distributionen har slutförts kontrollerar du i Azure Portal att resurs statusen för Service Fabric är *klar*. Kontrol lera att du kan komma åt den nya Service Fabric Explorer slut punkten, att **klustrets hälso tillstånd** är *OK* och att alla distribuerade program fungerar korrekt.

Med detta har du vertikalt skalat en primär typ av kluster primär Node!

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [lägger till en nodtyp i ett kluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster program mässigt](service-fabric-cluster-programmatic-scaling.md) med hjälp av Fluent Azure Compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).
