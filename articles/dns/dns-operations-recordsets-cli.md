---
title: Hantera DNS-poster i Azure DNS med Azure CLI
description: Hantering av DNS-postuppsättningar och poster på Azure DNS när du är värd för din domän på Azure DNS.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: how-to
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 2d3989b3c477a35d602f1ccf3e45d6f597f5d78d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011583"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Hantera DNS-poster och post uppsättningar i Azure DNS med Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Den här artikeln visar hur du hanterar DNS-poster för din DNS-zon med hjälp av plattforms oberoende Azure CLI, som är tillgängligt för Windows, Mac och Linux. Du kan också hantera dina DNS-poster med [Azure PowerShell](dns-operations-recordsets.md) eller [Azure Portal](dns-operations-recordsets-portal.md).

I exemplen i den här artikeln förutsätter vi att du redan har [installerat Azure CLI, loggat in och skapat en DNS-zon](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Introduktion

Innan du skapar DNS-poster i Azure DNS, måste du först förstå hur Azure DNS organiserar DNS-poster i DNS-postuppsättningar.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Mer information om DNS-poster i Azure DNS finns i [DNS-zoner och poster](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Skapa en DNS-post

Om du vill skapa en DNS-post använder du `az network dns record-set <record-type> add-record` kommandot (där `<record-type>` är typen av post, dvs. a, SRV, TXT osv.) Mer information finns i `az network dns record-set --help` .

När du skapar en post måste du ange resursgruppsnamn, zonnamn, postuppsättningsnamn, posttyp och information om posten som skapas. Det angivna post uppsättnings namnet måste vara ett *relativt* namn, vilket innebär att det måste utesluta zon namnet.

Om postuppsättningen inte redan finns, skapar det här kommandot den åt dig. Om postuppsättningen redan finns, lägger det här kommandot till posten som du anger till den befintliga postuppsättningen.

Om en ny postuppsättning skapas, används ett standard TTL-värde (Time to Live) på 3600. Instruktioner för hur du använder olika TTL-poster finns i [skapa en DNS-uppsättning](#create-a-dns-record-set).

Följande exempel skapar en A-post som heter *www* i zonen *contoso.com* i resursgruppen *MyResourceGroup*. IP-adressen för A-posten är *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Om du vill skapa en post som angetts i zonens Apex (i det här fallet "contoso.com") använder du post namnet " \@ ", inklusive citat tecken:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Skapa en DNS-post uppsättning

I exemplen ovan lades DNS-posten antingen till i en befintlig post uppsättning eller också skapades post uppsättningen *implicit*. Du kan också skapa post uppsättningen *explicit* innan du lägger till poster i den. Azure DNS stöder tomma post uppsättningar, som kan fungera som plats hållare för att reservera ett DNS-namn innan DNS-poster skapas. Tomma post uppsättningar visas i Azure DNS kontroll planet, men visas inte på Azure DNS namnservrar.

Post uppsättningar skapas med hjälp av `az network dns record-set <record-type> create` kommandot. Om du vill ha hjälp, så gå till `az network dns record-set <record-type> create --help`.

Genom att skapa post uppsättningen kan du uttryckligen ange egenskaper för post uppsättning, till exempel [TTL (Time to Live)](dns-zones-records.md#time-to-live) och metadata. [Metadata för post uppsättningar](dns-zones-records.md#tags-and-metadata) kan användas för att associera programspecifika data med varje post uppsättning som nyckel/värde-par.

I följande exempel skapas en tom post uppsättning av typen "A" med en 60-sekunders-TTL med hjälp av `--ttl` parametern (kort form `-l` ):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

I följande exempel skapas en post uppsättning med två metadata-poster, "avd = ekonomi" och "miljö = produktion", med hjälp av `--metadata` parametern:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

När du har skapat en tom post uppsättning kan du lägga till poster med `azure network dns record-set <record-type> add-record` enligt beskrivningen i [skapa en DNS-post](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Skapa poster av andra typer

I följande exempel visas hur du skapar poster med andra post typer som stöds av Azure DNS, om du har sett hur man skapar poster av typen A.

Parametrarna som används för att ange postdata varierar beroende på posttypen. För en post av typen "A", anger du exempelvis IPv4-adressen med parametern `--ipv4-address <IPv4 address>`. Parametrarna för varje post typ kan listas med `az network dns record-set <record-type> add-record --help` .

I varje fall visar vi hur du skapar en enskild post. Posten läggs till i den befintliga post uppsättningen, eller så har en post uppsättning skapats implicit. Mer information om hur du skapar post uppsättningar och definierar parameter uppsättnings parameter explicit finns i [skapa en DNS-uppsättning](#create-a-dns-record-set).

Vi ger inget exempel på att skapa en SOA-postuppsättning eftersom SOAs skapas och tas bort med varje DNS-zon och inte kan skapas eller tas bort separat. [SOA kan dock ändras, vilket visas i ett senare exempel](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>Skapa en AAAA-post

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Skapa en CAA-post

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Skapa en CNAME-post

> [!NOTE]
> DNS-standarderna tillåter inte CNAME-poster på toppen av en zon ( `--Name "@"` ), eller så tillåter de inte post uppsättningar som innehåller mer än en post.
> 
> Mer information finns i [CNAME-poster](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Skapa en MX-post

I det här exemplet använder vi post uppsättnings namnet " \@ " för att skapa MX-posten i zonens Apex (i det här fallet "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Skapa en NS-post

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Skapa en PTR-post

I det här fallet representerar "my-arpa-zone.com" den ARPA-zon som representerar ditt IP-intervall. Varje PTR-post som har angetts i den här zonen motsvarar en IP-adress i IP-intervallet.  Post namnet 10 är den sista oktetten i IP-adressen i det här IP-intervallet som representeras av den här posten.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Skapa en SRV-post

När du skapar en [SRV-postuppsättning](dns-zones-records.md#srv-records)anger du *\_ tjänsten* och *\_ protokollet* i post uppsättningens namn. Du behöver inte inkludera " \@ " i post uppsättnings namnet när du skapar en SRV-postuppsättning på zonens Apex.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Skapa en TXT-post

I följande exempel visas hur du skapar en TXT-post. Mer information om den maximala sträng längden som stöds i TXT-poster finns i [TXT-poster](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Hämta en post uppsättning

Använd om du vill hämta en befintlig post uppsättning `az network dns record-set <record-type> show` . Om du vill ha hjälp, så gå till `az network dns record-set <record-type> show --help`.

När du skapar en post eller en post uppsättning måste post uppsättnings namnet vara ett *relativt* namn, vilket innebär att det måste utesluta zon namnet. Du måste också ange post typen, zonen som innehåller post uppsättningen och resurs gruppen som innehåller zonen.

I följande exempel hämtas post- *www* av typen A från zonen *contoso.com* i resurs gruppen *MyResourceGroup*:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>List post uppsättningar

Du kan lista alla poster i en DNS-zon med hjälp av `az network dns record-set list` kommandot. Om du vill ha hjälp, så gå till `az network dns record-set list --help`.

Det här exemplet returnerar alla post uppsättningar i zonen *contoso.com*, i resurs grupp *MyResourceGroup*, oavsett typ av namn eller post:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Det här exemplet returnerar alla post uppsättningar som matchar den aktuella post typen (i det här fallet "A"-poster):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Lägga till en post i en befintlig post uppsättning

Du kan använda `az network dns record-set <record-type> add-record` båda för att skapa en post i en ny post uppsättning eller lägga till en post i en befintlig post uppsättning.

Mer information finns i [skapa en DNS-post](#create-a-dns-record) och [skapa poster av andra typer](#create-records-of-other-types) ovan.

## <a name="remove-a-record-from-an-existing-record-set"></a>Ta bort en post från en befintlig post uppsättning.

Om du vill ta bort en DNS-post från en befintlig post uppsättning använder du `az network dns record-set <record-type> remove-record` . Om du vill ha hjälp, så gå till `az network dns record-set <record-type> remove-record -h`.

Det här kommandot tar bort en DNS-post från en post uppsättning. Om den sista posten i en post uppsättning tas bort, tas även själva post uppsättningen bort. Använd alternativet om du vill behålla den tomma post uppsättningen i stället `--keep-empty-record-set` .

Du måste ange den post som ska tas bort och zonen som den ska tas bort från, med samma parametrar som när du skapar en post med hjälp av `az network dns record-set <record-type> add-record` . Dessa parametrar beskrivs i [skapa en DNS-post](#create-a-dns-record) och [skapa poster av andra typer](#create-records-of-other-types) ovan.

I följande exempel tar bort en post med värdet 1.2.3.4 från post uppsättningen med namnet *www* i zonen *contoso.com* i resurs gruppen *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Ändra en befintlig post uppsättning

Varje post uppsättning innehåller en [Time to Live (TTL)](dns-zones-records.md#time-to-live), [metadata](dns-zones-records.md#tags-and-metadata)och DNS-poster. I följande avsnitt beskrivs hur du ändrar var och en av dessa egenskaper.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Ändra en A-, AAAA-, CAA-, MX-, NS-, PTR-, SRV-eller TXT-post

Om du vill ändra en befintlig post av typen A, AAAA, CAA, MX, NS, PTR, SRV eller TXT bör du först lägga till en ny post och sedan ta bort den befintliga posten. Detaljerade anvisningar om hur du tar bort och lägger till poster finns i de tidigare avsnitten i den här artikeln.

I följande exempel visas hur du ändrar en "A"-post, från IP-adressen 1.2.3.4 till IP-5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Det går inte att lägga till, ta bort eller ändra posterna i den automatiskt skapade NS-postuppsättningen i zonens Apex ( `--Name "@"` inklusive citat tecken). För den här post uppsättningen är de enda tillåtna ändringarna att ändra post uppsättningens TTL och metadata.

### <a name="to-modify-a-cname-record"></a>Ändra en CNAME-post

Till skillnad från de flesta andra post typer kan en CNAME-postuppsättning bara innehålla en enda post.  Därför kan du inte ersätta det aktuella värdet genom att lägga till en ny post och ta bort den befintliga posten, som för andra post typer.

Använd i stället om du vill ändra en CNAME-post `az network dns record-set cname set-record` . Mer information finns i`az network dns record-set cname set-record --help`

I exemplet ändras www post set- *www* i zonen *contoso.com*, i resurs grupp *MyResourceGroup*, så att den pekar på "www.fabrikam.net" i stället för dess befintliga värde:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Ändra en SOA-post

Till skillnad från de flesta andra post typer kan en CNAME-postuppsättning bara innehålla en enda post.  Därför kan du inte ersätta det aktuella värdet genom att lägga till en ny post och ta bort den befintliga posten, som för andra post typer.

Använd i stället om du vill ändra SOA-posten `az network dns record-set soa update` . Om du vill ha hjälp, så gå till `az network dns record-set soa update --help`.

I följande exempel visas hur du ställer in egenskapen "e-post" för SOA-posten för zonen *contoso.com* i resurs gruppen *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Ändra NS-poster i zonen Apex

NS-postuppsättningen på zon Apex skapas automatiskt med varje DNS-zon. Den innehåller namnen på de Azure DNS namnservrar som har tilldelats zonen.

Du kan lägga till fler namnservrar i den här NS-postuppsättningen för att stödja samvärdbaserade domäner med fler än en DNS-Provider. Du kan också ändra TTL och metadata för den här post uppsättningen. Du kan dock inte ta bort eller ändra de förifyllda Azure DNS namnservrarna.

Observera att detta endast gäller för NS-postuppsättningen i zonens Apex. Andra NS-postuppsättningar i din zon (som används för att delegera underordnade zoner) kan ändras utan begränsning.

I följande exempel visas hur du lägger till ytterligare en namnserver i NS-postuppsättningen i zonen Apex:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Ändra TTL för en befintlig post uppsättning

Om du vill ändra TTL för en befintlig post uppsättning använder du `azure network dns record-set <record-type> update` . Om du vill ha hjälp, så gå till `azure network dns record-set <record-type> update --help`.

I följande exempel visas hur du ändrar en post uppsättnings-TTL, i det här fallet till 60 sekunder:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Ändra metadata för en befintlig post uppsättning

[Metadata för post uppsättningar](dns-zones-records.md#tags-and-metadata) kan användas för att associera programspecifika data med varje post uppsättning som nyckel/värde-par. Om du vill ändra metadata för en befintlig post uppsättning använder du `az network dns record-set <record-type> update` . Om du vill ha hjälp, så gå till `az network dns record-set <record-type> update --help`.

I följande exempel visas hur du ändrar en post uppsättning med två metadata-poster, "avd = ekonomi" och "miljö = produktion". Observera att alla befintliga metadata *ersätts* med de värden som anges.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Ta bort en post uppsättning

Post uppsättningar kan tas bort med hjälp av `az network dns record-set <record-type> delete` kommandot. Om du vill ha hjälp, så gå till `azure network dns record-set <record-type> delete --help`. Om du tar bort en post uppsättning raderas även alla poster i post uppsättningen.

> [!NOTE]
> Du kan inte ta bort SOA-och NS-postuppsättningarna i Zone Apex ( `--name "@"` ).  De skapas automatiskt när zonen skapas och tas bort automatiskt när zonen tas bort.

I följande exempel tar bort post uppsättningen med namnet *www* av typ A från zonen *contoso.com* i resurs gruppen *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Du uppmanas att bekräfta borttagnings åtgärden. Använd växeln för att ignorera den här prompten `--yes` .

## <a name="next-steps"></a>Nästa steg

Läs mer om [zoner och poster i Azure DNS](dns-zones-records.md).
<br>
Lär dig hur du [skyddar dina zoner och poster när du](dns-protect-zones-recordsets.md) använder Azure DNS.
