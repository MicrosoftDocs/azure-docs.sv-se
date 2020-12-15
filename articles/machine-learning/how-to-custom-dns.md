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
ms.openlocfilehash: 07ff656c5eacbbcdc16c6c7cf098478ca6baf745
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509299"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Så här använder du din arbetsyta med en anpassad DNS-server

När du använder en Azure Machine Learning-arbetsyta med en privat slut punkt finns det [flera sätt att hantera DNS-namnmatchning](../private-link/private-endpoint-dns.md). Som standard hanterar Azure automatiskt namn matchning för din arbets yta och privat slut punkt. Om du i stället _använder en egen anpassad DNS-Server_ _ måste du skapa DNS-poster för arbets ytan manuellt.

> [!IMPORTANT]
> Den här artikeln beskriver bara hur du hittar det fullständigt kvalificerade domän namnet (FQDN) och IP-adresser för dessa poster. det ger inte information om hur du konfigurerar DNS-poster för dessa objekt. I dokumentationen för DNS-programvaran finns information om hur du lägger till poster.

## <a name="prerequisites"></a>Krav

- En Azure-Virtual Network som använder [din egen DNS-Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

- En Azure Machine Learning-arbetsyta med en privat slut punkt. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- Du är van vid att använda [nätverks isolering under utbildning &s störningar](./how-to-network-security-overview.md).

- Bekant med [konfiguration av DNS-zon för privat slut punkt i Azure](../private-link/private-endpoint-dns.md)

- Valfritt, [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="find-the-ip-addresses"></a>Hitta IP-adresserna

I följande lista visas de fullständigt kvalificerade domän namnen (FQDN) som används av din arbets yta och privat slut punkt:

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* Om du skapar en beräknings instans måste du också lägga till en post för `<instance-name>.<region>.instances.azureml.ms` med den privata IP-adressen för den privata slut punkten för arbets ytan.

    > [!NOTE]
    > Beräknings instanser kan bara nås från det virtuella nätverket.

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

Informationen som returneras från alla metoder är densamma. en lista över FQDN och privat IP-adress för resurserna.

| FQDN | IP-adress |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Vissa FQDN visas inte i listan av den privata slut punkten, men krävs av arbets ytan. Dessa FQDN visas i följande tabell och måste också läggas till i DNS-servern:
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Om du har en beräknings instans använder du `<instance-name>.<region>.instances.azureml.ms` , där `<instance-name>` är namnet på din beräknings instans. Använd privat IP-adress för privat slut punkt för arbets ytan. Observera att det bara går att få åtkomst till beräknings instans i det virtuella nätverket.
>
> För alla dessa IP-adresser använder du samma adress som de `*.api.azureml.ms` poster som returneras från föregående steg.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Azure Machine Learning med ett virtuellt nätverk finns i [Översikt över virtuella nätverk](how-to-network-security-overview.md).
