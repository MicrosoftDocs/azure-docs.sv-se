---
title: Skapa en skydds-värd med hjälp av Azure CLI | Azure-skydds
description: I den här artikeln lär du dig hur du skapar och tar bort en skydds-värd
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: e4f0a3ea85fa479826fe9e666df2b18f8dba7ce3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92079130"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Skapa en Azure Bastion-värd med hjälp av Azure CLI

Den här artikeln visar hur du skapar en Azure skydds-värd med Azure CLI. När du etablerar Azure skydds-tjänsten i det virtuella nätverket är den sömlösa RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk. Azure skydds-distributionen är per virtuellt nätverk, inte per prenumeration/konto eller virtuell dator.

Du kan också skapa en Azure skydds-värd med hjälp av [Azure Portal](./tutorial-create-host-portal.md)eller genom att använda [Azure PowerShell](bastion-create-host-powershell.md).

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Skapa en skydds-värd

Det här avsnittet hjälper dig att skapa en ny Azure skydds-resurs med hjälp av Azure CLI.

> [!NOTE]
> Som du ser i exemplen använder du- `--location` parametern med `--resource-group` för varje kommando för att säkerställa att resurserna distribueras tillsammans.

1. Skapa ett virtuellt nätverk och ett Azure skydds-undernät. Du måste skapa Azure skydds-undernätet med namnet Value **AzureBastionSubnet**. Med det här värdet kan Azure veta vilket undernät som skydds-resurserna ska distribueras till. Detta skiljer sig från ett Gateway-undernät. Du måste använda ett undernät med minst/27 eller större undernät (/27,/26 osv.). Skapa **AzureBastionSubnet** utan några routningstabeller eller delegeringar. Om du använder nätverks säkerhets grupper på **AzureBastionSubnet**, se artikeln [arbeta med NSG: er](bastion-nsg.md) .

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Skapa en offentlig IP-adress för Azure Bastion. Den offentliga IP-adressen är den offentliga IP-adressen som skydds-resursen som RDP/SSH kommer att få åtkomst till (via port 443). Den offentliga IP-adressen måste finnas i samma region som den skydds-resurs som du skapar.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. Skapa en ny Azure skydds-resurs i AzureBastionSubnet för det virtuella nätverket. Det tar ungefär 5 minuter för skydds-resursen att skapa och distribuera.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## <a name="next-steps"></a>Nästa steg

* Mer information finns i [vanliga frågor och svar om skydds](bastion-faq.md) .
* Information om hur du använder nätverks säkerhets grupper med Azure skydds-undernätet finns i [arbeta med NSG: er](bastion-nsg.md).
