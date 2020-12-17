---
title: Snabb start – skapa en privat Azure-DNS-zon med Azure CLI
description: I den här snabb starten skapar och testar du en privat DNS-zon och-post i Azure DNS. Det här är en stegvis guide om hur du skapar och hanterar din första privata DNS-zon och DNS-post med hjälp av Azure CLI.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 820641af00caea4ffca450be8aa81b5357ba1261
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652979"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-cli"></a>Snabb start: skapa en privat Azure-DNS-zon med Azure CLI

Den här snabb starten vägleder dig genom stegen för att skapa din första privata DNS-zon och-post med hjälp av Azure CLI.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera en privat DNS-zon i ett virtuellt nätverk anger du listan med virtuella nätverk som får matcha poster i zonen.  Dessa kallas *länkade* virtuella nätverk. När autoregistrering har Aktiver ATS uppdaterar Azure DNS även zon posterna när en virtuell dator skapas, ändrar dess IP-adress eller raderas.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Du kan också slutföra den här snabb starten med [Azure PowerShell](private-dns-getstarted-powershell.md).

## <a name="create-the-resource-group"></a>Skapa resursgruppen

Först skapar du en resursgrupp som ska innehålla DNS-zonen: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-private-dns-zone"></a>Skapa en privat DNS-zon

I följande exempel skapas ett virtuellt nätverk med namnet **myAzureVNet**. Sedan skapar den en DNS-zon med namnet **Private.contoso.com** i resurs gruppen **MyAzureResourceGroup** , länkar DNS-zonen till det virtuella **MyAzureVnet** -nätverket och aktiverar automatisk registrering.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network private-dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com

az network private-dns link vnet create -g MyAzureResourceGroup -n MyDNSLink \
   -z private.contoso.com -v myAzureVNet -e true
```

Om du vill skapa en zon enbart för namn matchning (ingen automatisk värdnamn registrering) kan du använda- `-e false` parametern.

### <a name="list-dns-private-zones"></a>Lista privata DNS-zoner

Använd `az network private-dns zone list` om du vill räkna upp DNS-zoner. Om du vill ha hjälp, så gå till `az network dns zone list --help`.

När du anger resursgrupp så listas bara zonerna i resursgruppen:

```azurecli
az network private-dns zone list \
  -g MyAzureResourceGroup
```

Om du utesluter resursgruppen listas alla zoner i prenumerationen:

```azurecli
az network private-dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Skapa de virtuella testdatorerna

Nu skapar du två virtuella datorer så att du kan testa din privata DNS-zon:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter
```

Det här kan ta några minuter.

## <a name="create-an-additional-dns-record"></a>Skapa en ytterligare DNS-post

Skapa en DNS-post genom att använda kommandot `az network private-dns record-set [record type] add-record`. Mer information att till exempel lägga till A-poster finns i `az network private-dns record-set A add-record --help`.

 I följande exempel skapas en post med det relativa namnet **db** i DNS-zonen **Private.contoso.com** i resurs gruppen **MyAzureResourceGroup**. Det fullständigt kvalificerade namnet på post uppsättningen är **db.Private.contoso.com**. Posttypen är ”A” med IP-adressen ”10.2.0.4”.

```azurecli
az network private-dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Visa DNS-poster

Om du vill visa en lista med DNS-poster i din zon kör du:

```azurecli
az network private-dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```

## <a name="test-the-private-zone"></a>Testa den privata zonen

Nu kan du testa namn matchningen för din privata **Private.contoso.com** -zon.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurera virtuella datorer för att tillåta inkommande ICMP

Du kan använda ping-kommandot för att testa namnmatchning. Konfigurera därför brandväggen på båda de virtuella datorerna att tillåta inkommande ICMP-paket.

1. Anslut till myVM01 och öppna ett Windows PowerShell-fönster med administratörsbehörighet.
2. Kör följande kommando:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Upprepa för myVM02.

### <a name="ping-the-vms-by-name"></a>Pinga de virtuella datorerna efter namn

1. Från Windows PowerShell-kommandotolken för myVM02 pingar du myVM01 med hjälp av det automatiskt registrerade värddatornamnet:

   ```powershell
   ping myVM01.private.contoso.com
   ```

   Du bör se utdata som liknar det här:

   ```output
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Nu pingar du det **db**-namn som du skapade tidigare:

   ```powershell
   ping db.private.contoso.com
   ```

   Du bör se utdata som liknar det här:

   ```output
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen **MyAzureResourceGroup** för att ta bort de resurser som skapats i den här snabb starten.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Scenarier för Azure DNS Private Zones](private-dns-scenarios.md)

