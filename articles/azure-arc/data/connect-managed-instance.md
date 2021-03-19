---
title: Ansluta till Azure Arc-aktiverad SQL-hanterad instans
description: Ansluta till Azure Arc-aktiverad SQL-hanterad instans
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: abd27e15ccf5b421e69e78b2b726d192ffdecacb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92372369"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>Ansluta till Azure Arc-aktiverad SQL-hanterad instans

Den här artikeln förklarar hur du kan ansluta till din Azure Arc-aktiverade SQL-instansen. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-azure-arc-enabled-sql-managed-instances"></a>Visa aktiverade SQL-hanterade instanser i Azure Arc

Om du vill visa den aktiverade SQL-hanterade instansen av Azure Arc och de externa slut punkterna använder du följande kommando:

```console
azdata arc sql mi list
```

Utdata bör se ut så här:

```console
Name    Replicas    ExternalEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Om du använder AKS eller kubeadm eller OpenShift osv. kan du kopiera den externa IP-adressen och port numret härifrån och ansluta till det med ditt favorit verktyg för att ansluta till en SQL-instans/Azure SQL-instans som Azure Data Studio eller SQL Server Management Studio.  Men om du använder den virtuella datorn för snabb start finns mer information om hur du ansluter till den virtuella datorn utanför Azure. 

> [!NOTE]
> Företagets principer kan blockera åtkomst till IP och port, särskilt om detta skapas i det offentliga molnet.

## <a name="connect"></a>Ansluta 

Anslut till Azure Data Studio, SQL Server Management Studio eller SQLCMD

Öppna Azure Data Studio och Anslut till din instans med den externa slut punktens IP-adress och port nummer ovan. Om du använder en virtuell Azure-dator behöver du den _offentliga_ IP-adressen, som kan identifieras med hjälp av den [särskilda kommentaren om distributioner av virtuella Azure-datorer](#special-note-about-azure-virtual-machine-deployments).

Exempel:

- Server: 52.229.9.30, 30913
- Användar namn: sa
- Lösen ord: ditt angivna SQL-lösenord vid etablerings tiden

> [!NOTE]
> Du kan använda Azure Data Studio [Visa instrument panelerna för SQL-hanterade instanser](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards).

Om du vill ansluta med SQLCMD eller Linux eller Windows kan du använda ett kommando som detta. Ange SQL-lösenordet när du uppmanas:

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Särskilda anmärkningar om distributioner av virtuella Azure-datorer

Om du använder en virtuell Azure-dator visas inte den offentliga IP-adressen i slut punktens IP-adress. Använd följande kommando för att hitta den externa IP-adressen:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Du kan sedan kombinera den offentliga IP-adressen med porten för att upprätta anslutningen.

Du kan också behöva exponera porten för SQL-instansen via NSG (Network Security Gateway). Om du vill tillåta trafik via (NSG) måste du lägga till en regel som du kan göra med hjälp av följande kommando.

Om du vill ange en regel måste du känna till namnet på din NSG som du kan ta reda på med hjälp av kommandot nedan:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

När du har namnet på NSG kan du lägga till en brand Väggs regel med hjälp av följande kommando. Exempel värden skapar en NSG-regel för port 30913 och tillåter anslutning från **alla** käll-IP-adresser.  Detta är inte en säkerhets metod.  Du kan låsa saker bättre genom att ange ett -source-address-prefixes-värde som är speciellt för din klients IP-adress eller ett IP-adressintervall som täcker ditt teams eller organisations IP-adresser.

Ersätt värdet för `--destination-port-ranges` parametern nedan med det port nummer som du fick från `azdata sql instance list` kommandot F ovan.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>Nästa steg

- [Visa instrument paneler för SQL-hanterade instanser](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [Visa SQL-hanterad instans i Azure Portal](view-arc-data-services-inventory-in-azure-portal.md)
