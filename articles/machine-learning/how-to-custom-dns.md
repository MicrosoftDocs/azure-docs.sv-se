---
title: Använd anpassad DNS-Server
titleSuffix: Azure Machine Learning
description: Konfigurera en anpassad DNS-server så att den fungerar med en Azure Machine Learning arbets yta och en privat slut punkt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 66a709f15191a8142f10f15d825276ea2ba4b83f
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487992"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Så här använder du din arbetsyta med en anpassad DNS-server

När du använder en Azure Machine Learning-arbetsyta med en privat slut punkt finns det [flera sätt att hantera DNS-namnmatchning](../private-link/private-endpoint-dns.md). Som standard hanterar Azure automatiskt namn matchning för din arbets yta och privat slut punkt. Om du i stället _använder en egen anpassad DNS-Server_ _ måste du manuellt skapa DNS-poster eller använda villkorliga vidarebefordrare för arbets ytan.

> [!IMPORTANT]
> Den här artikeln beskriver bara hur du hittar det fullständigt kvalificerade domän namnet (FQDN) och IP-adresser för dessa poster. det ger inte information om hur du konfigurerar DNS-poster för dessa objekt. I dokumentationen för DNS-programvaran finns information om hur du lägger till poster.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-Virtual Network som använder [din egen DNS-Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

- En Azure Machine Learning-arbetsyta med en privat slut punkt. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- Du är van vid att använda [nätverks isolering under utbildning &s störningar](./how-to-network-security-overview.md).

- Bekant med [konfiguration av DNS-zon för privat slut punkt i Azure](../private-link/private-endpoint-dns.md)

- Valfritt, [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="fqdns-in-use"></a>FQDN som används
### <a name="these-fqdns-are-in-use-in-the-following-regions-eastus-southcentralus-and-westus2"></a>Dessa FQDN används i följande regioner: öster, usasödracentrala och westus2.
Följande lista innehåller de fullständigt kvalificerade domän namnen (FQDN) som används av din arbets yta:

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* Om du skapar en beräknings instans måste du också lägga till en post för `<instance-name>.<region>.instances.azureml.ms` med den privata IP-adressen för den privata slut punkten för arbets ytan.

    > [!NOTE]
    > Beräknings instanser kan bara nås från det virtuella nätverket.
    
### <a name="these-fqdns-are-in-use-in-all-other-public-regions"></a>Dessa FQDN används i alla andra offentliga regioner
Följande lista innehåller de fullständigt kvalificerade domän namnen (FQDN) som används av din arbets yta:

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > Beräknings instanser kan bara nås från det virtuella nätverket.

### <a name="azure-china-21vianet-regions"></a>Azure Kina 21Vianet-regioner

Följande FQDN är för Azure Kina 21Vianet-regioner:

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.cn`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.cn`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.chinacloudapi.cn`

    > [!NOTE]
    > Arbets ytans namn för detta FQDN kan trunkeras. Trunkering görs för att behålla FQDN som är mindre än eller lika med 63 tecken.
* `<instance-name>.<region>.instances.ml.azure.cn`
## <a name="find-the-ip-addresses"></a>Hitta IP-adresserna

Använd någon av följande metoder för att hitta de interna IP-adresserna för FQDN i VNet:

> [!NOTE]
> De fullständigt kvalificerade domän namnen och IP-adresserna är olika beroende på din konfiguration. Till exempel är GUID-värdet i domän namnet unikt för din arbets yta.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)väljer du Azure Machine Learning __arbets ytan__.
1. I avsnittet __Inställningar__ väljer du __anslutningar för privata slut punkter__.
1. Välj länken i kolumnen för den __privata slut punkten__ som visas.
1. En lista över de fullständigt kvalificerade domän namnen (FQDN) och IP-adresserna för den privata slut punkten för arbets ytan finns längst ned på sidan.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Lista över FQDN i portalen":::

---

Informationen som returneras från alla metoder är densamma. en lista över FQDN och privat IP-adress för resurserna. Följande exempel är från en global Azure-region:

| FQDN | IP-adress |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Vissa FQDN visas inte i listan av den privata slut punkten, men krävs av arbets ytan i öster, usasödracentrala och westus2. Dessa FQDN visas i följande tabell och måste också läggas till i DNS-servern och/eller i en Azure Privat DNS-zon:
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Om du har en beräknings instans använder du `<instance-name>.<region>.instances.azureml.ms` , där `<instance-name>` är namnet på din beräknings instans. Använd privat IP-adress för privat slut punkt för arbets ytan. Observera att det bara går att få åtkomst till beräknings instans i det virtuella nätverket.
>
> För alla dessa IP-adresser använder du samma adress som de `*.api.azureml.ms` poster som returneras från föregående steg.

I följande tabell visas exempel på IP-adresser från Azure Kina 21Vianet-regioner:

| FQDN | IP-adress |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.cn` | `10.1.0.5` |
| `ml-mype-pltest-chinaeast2-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.chinacloudapi.cn` | `10.1.0.6` |
## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Azure Machine Learning med ett virtuellt nätverk finns i [Översikt över virtuella nätverk](how-to-network-security-overview.md).

Mer information om hur du integrerar privata slut punkter i din DNS-konfiguration finns i [Azures DNS-konfiguration för privat slut punkt](../private-link/private-endpoint-dns.md).