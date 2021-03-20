---
title: 'Snabb start: skapa en Azure DNS zon och post – Azure CLI'
titleSuffix: Azure DNS
description: Snabbstart – Läs om hur du skapar en DNS-zon och en DNS-post i Azure DNS. Detta är en stegvis guide om hur du skapar och hanterar din första DNS-zon och DNS-post med Azure CLI.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1929cd512d18d7fd234aff1f55814c423455e63b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94561377"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Snabbstart: Skapa en Azure DNS-zon och post med Azure CLI

Den här artikeln visar hur du skapar din första DNS-zon och DNS-post med hjälp av Azure CLI, som är tillgängligt för Windows, Mac och Linux. Du kan även utföra de här stegen med [Azure-portalen](dns-getstarted-portal.md) eller [Azure PowerShell](dns-getstarted-powershell.md).

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera din DNS-zon på Internet måste du konfigurera namnservrarna för domänen. Dessa steg beskrivs nedan.

Azure DNS stöder också privata DNS-zoner. Mer information om privata DNS-zoner finns i [Using Azure DNS for private domains](private-dns-overview.md) (Använda Azure DNS för privata domäner). Ett exempel på hur du kan skapa en privat DNS-zon finns i [Kom igång med privata Azure DNS-zoner med CLI](./private-dns-getstarted-cli.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.4 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-the-resource-group"></a>Skapa resursgruppen

Innan du skapar DNS-zonen skapar du en resursgrupp som ska innehålla DNS-zonen:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av kommandot `az network dns zone create`. Om du vill se hjälpen för det här kommandot skriver du `az network dns zone create -h`.

I följande exempel skapas en DNS-zon med namnet *contoso.xyz* i resurs gruppen *MyResourceGroup*. Använd exemplet när du vill skapa en DNS-zon, och ersätt värdena med dina egna.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>Skapa en DNS-post

Skapa en DNS-post genom att använda kommandot `az network dns record-set [record type] add-record`. Hjälp om A-poster finns i `azure network dns record-set A add-record -h`.

I följande exempel skapas en post med det relativa namnet "www" i DNS-zonen "contoso.xyz" i resurs gruppen "MyResourceGroup". Det fullständigt kvalificerade namnet på post uppsättningen är "www.contoso.xyz". Post typen är "A", med IP-adressen "10.10.10.10" och en standard-TTL på 3600 sekunder (1 timme).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>Visa poster

Om du vill visa en lista med DNS-poster i din zon kör du:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>Testa namnmatchningen

Nu när du har en DNS-testzon med en ”A”-testpost kan du testa namnmatchningen med ett verktyg som heter *nslookup*. 

**Så här testar du DNS-namnmatchning:**

1. Kör följande cmdlet för att hämta listan över namnservrar för zonen:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. Kopiera ett av namn server namnen från utdata från föregående steg.

1. Öppna en kommandotolk och kör följande kommando:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Exempel:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Du bör se något som liknar följande skärm:

   ![Skärm bild som visar ett kommando tolks fönster med ett n s lookup-kommando och-värden för Server, adress, namn och adress.](media/dns-getstarted-portal/nslookup.PNG)

Det värdnamn som **www- \. contoso.xyz** matchar **10.10.10.10**, precis som du konfigurerade det. Resultatet verifierar att namnmatchningen fungerar korrekt.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort alla resurser som skapats i den här snabbstarten när de inte behövs längre genom att ta bort resursgruppen:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat din första DNS-zon och DNS-post med hjälp av Azure CLI kan du skapa poster för en webbapp i en anpassad domän.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)
