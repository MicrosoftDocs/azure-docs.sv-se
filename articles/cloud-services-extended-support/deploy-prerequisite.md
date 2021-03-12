---
title: Krav för att distribuera Azure Cloud Services (utökad support)
description: Krav för att distribuera Azure Cloud Services (utökad support)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 6e5994f05187cd25996bcc007d27a7e10eb76427
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232536"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Krav för att distribuera Azure Cloud Services (utökad support)

> [!IMPORTANT]
> Cloud Services (utökad support) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

För att säkerställa en lyckad Cloud Services-distribution (utökad support) granskar du stegen nedan och Slutför varje objekt innan du försöker utföra några distributioner. 

## <a name="register-the-cloudservices-feature"></a>Registrera funktionen CloudServices
Registrera funktionen för din prenumeration. Registreringen kan ta flera minuter att slutföra. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Kontrol lera status för registreringen med följande:  
```powershell
Get-AzProviderFeature 

#Sample output
FeatureName               ProviderName      RegistrationState
CloudServices           Microsoft.Compute    Registered
```

## <a name="required-service-configuration-cscfg-file-updates"></a>Fil uppdateringar för tjänst konfiguration (. cscfg) som krävs

### <a name="1-virtual-network"></a>1) Virtual Network
Distributioner av moln tjänster (utökad support) måste finnas i ett virtuellt nätverk. Det går att skapa virtuella nätverk via [Azure Portal](../virtual-network/quick-create-portal.md), [POWERSHELL](../virtual-network/quick-create-powershell.md), [Azure CLI](../virtual-network/quick-create-cli.md) eller [arm-mall](../virtual-network/quick-create-template.md). Det virtuella nätverket och undernät måste också refereras i tjänst konfigurationen (. cscfg) i avsnittet [NetworkConfiguration](schema-cscfg-networkconfiguration.md) . 

För ett virtuellt nätverk som tillhör samma resurs grupp som moln tjänsten räcker det att referera till det virtuella nätverks namnet i tjänst konfigurations filen (. cscfg). Om det virtuella nätverket och moln tjänsten finns i två olika resurs grupper måste det fullständiga Azure Resource Manager-ID: t för det virtuella nätverket anges i tjänst konfigurations filen (. cscfg).
 
#### <a name="virtual-network-located-in-same-resource-group"></a>Virtual Network finns i samma resurs grupp
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>Virtuellt nätverk finns i en annan resurs grupp
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2) ta bort gamla plugin-program

Ta bort gamla fjärr skrivbords inställningar från tjänst konfigurations filen (. cscfg).  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```

## <a name="required-service-definition-file-csdef-updates"></a>Nödvändiga uppdateringar för tjänst definitions filen (. csdef)

### <a name="1-virtual-machine-sizes"></a>1) storlekar för virtuella datorer
Följande storlekar är föråldrade i Azure Resource Manager. Men om du vill fortsätta att använda dem uppdaterar du `vmsize` namnet med tillhör ande Azure Resource Manager namngivnings konvention.  

| Namn på föregående storlek | Uppdaterat namn på storlek | 
|---|---|
| ExtraSmall | Standard_A0 | 
| Liten | Standard_A1 |
| Medel | Standard_A2 | 
| Stor | Standard_A3 | 
| ExtraLarge | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| A7 | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 Till exempel `<WorkerRole name="WorkerRole1" vmsize="Medium"` skulle bli `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"` .
 
> [!NOTE]
> Om du vill hämta en lista över tillgängliga storlekar se [resurs-SKU: er – lista](/rest/api/compute/resourceskus/list) och tillämpa följande filter: <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) ta bort gamla plugin-program för fjärr skrivbord
Distributioner som använder gamla plugin-program för fjärr skrivbord måste ta bort modulerna från tjänst definitions filen (. csdef) och eventuella associerade certifikat. 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```

## <a name="key-vault-creation"></a>Skapa Key Vault 

Key Vault används för att lagra certifikat som är kopplade till Cloud Services (utökad support). Lägg till certifikaten i Key Vault och referera sedan till certifikatets tumavtrycken i tjänst konfigurations filen. Du måste också aktivera Key Vault för lämpliga behörigheter så att Cloud Services (utökad support) resurs kan hämta certifikat som lagras som hemligheter från Key Vault. Du kan skapa ett nyckel valv i [Azure Portal](../key-vault/general/quick-create-portal.md) eller genom att använda [PowerShell](../key-vault/general/quick-create-powershell.md). Nyckel valvet måste skapas i samma region och prenumeration som moln tjänsten. Mer information finns i [använda certifikat med Azure Cloud Services (utökad support)](certificates-and-key-vault.md).

## <a name="next-steps"></a>Nästa steg 
- Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Besök den [Cloud Services (utökad support) exempel lagrings plats](https://github.com/Azure-Samples/cloud-services-extended-support)
