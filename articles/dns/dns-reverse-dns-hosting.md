---
title: Värd zoner för omvänd DNS-sökning i Azure DNS
description: Lär dig hur du använder Azure DNS för att vara värd för zoner för omvänd DNS-sökning för dina IP-intervall
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 0c85049d6c8921432a753bf08989cab473b7c734
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99525135"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Värd zoner för omvänd DNS-sökning i Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I den här artikeln förklaras hur du kan vara värd för zoner för omvänd DNS-sökning för dina tilldelade IP-intervall i Azure DNS. IP-intervallen som representeras av zonerna för omvänd sökning måste tilldelas till din organisation, vanligt vis av din Internet leverantör.

Information om hur du konfigurerar omvänd DNS för en Azure-ägd IP-adress som är tilldelad till din Azure-tjänst finns i [Konfigurera omvänd DNS för tjänster som finns i Azure](dns-reverse-dns-for-azure-services.md).

Innan du läser den här artikeln bör du känna till [översikten över omvänd DNS och support i Azure](dns-reverse-dns-overview.md).

Den här artikeln vägleder dig genom stegen för att skapa din första DNS-zon och-post med omvänd sökning med hjälp av Azure Portal, Azure PowerShell, klassisk Azure-CLI eller Azure CLI.

## <a name="create-a-reverse-lookup-dns-zone"></a>Skapa en DNS-zon för omvänd sökning

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. På menyn **hubb** väljer du **nytt**  >  **nätverk** och väljer sedan **DNS-zon**.

   ![Val av DNS-zon](./media/dns-reverse-dns-hosting/figure1.png)

1. I fönstret **Skapa DNS-zon** namnger du din DNS-zon. Namnet på zonen fungerar annorlunda för IPv4-och IPv6-prefix. Använd instruktionerna för [IPv4](#ipv4) eller [IPv6](#ipv6) för att namnge din zon. När du är klar väljer du **skapa** för att skapa zonen.

### <a name="ipv4"></a>IPv4

Namnet på en IPv4-zon för omvänd sökning baseras på det IP-adressintervall som den representerar. Den bör ha följande format: `<IPv4 network prefix in reverse order>.in-addr.arpa` . Exempel finns i [Översikt över omvänd DNS och support i Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> När du skapar klassbaserade zoner för omvänd DNS-sökning i Azure DNS måste du använda ett bindestreck ( `-` ) i stället för ett snedstreck ( `/` ) i zon namnet.
>
> För IP-intervallet 192.0.2.128/26 måste du till exempel använda `128-26.2.0.192.in-addr.arpa` som zonnamn i stället för `128/26.2.0.192.in-addr.arpa` .
>
> Även om DNS-standarder stöder båda metoderna stöder Azure DNS inte DNS-zonnamn som innehåller tecknen snedstreck ( `/` ).

I följande exempel visas hur du skapar en omvänd DNS-zon för klass C `2.0.192.in-addr.arpa` som heter i Azure DNS via Azure Portal:

 ![Skärm bild som visar hur du skapar en omvänd DNS-zon för klass C med namnet 2.0.192.in-addr. arpa i Azure DNS via Azure Portal.](./media/dns-reverse-dns-hosting/figure2.png)

**Resurs gruppens plats** definierar platsen för resurs gruppen. Den har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid ”global” och visas inte.

I följande exempel visas hur du utför den här uppgiften med hjälp av Azure PowerShell och Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Namnet på en IPv6-zon för omvänd sökning ska ha följande format: `<IPv6 network prefix in reverse order>.ip6.arpa` .  Exempel finns i [Översikt över omvänd DNS och support i Azure](dns-reverse-dns-overview.md#ipv6).


I följande exempel visas hur du skapar en IPv6-zon för omvänd DNS-sökning som heter `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` i Azure DNS via Azure Portal:

 ![Fönstret "skapa DNS-zon" med ifyllda rutor](./media/dns-reverse-dns-hosting/figure3.png)

**Resurs gruppens plats** definierar platsen för resurs gruppen. Den har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid ”global” och visas inte.

I följande exempel visas hur du utför den här uppgiften med hjälp av Azure PowerShell och Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegera en omvänd DNS-sökning zon

Nu när du har skapat din zon för omvänd DNS-sökning måste du se till att zonen är delegerad från den överordnade zonen. DNS-delegering gör det möjligt för DNS-namnmatchning att hitta namnservrarna som är värdar för den omvända zonen för DNS-sökning. Namnservrarna kan sedan svara DNS-omvända frågor för IP-adresserna i adress intervallet.

För zoner för vanlig sökning beskrivs processen för att delegera en DNS-zon i [delegera din domän till Azure DNS](dns-delegate-domain-azure-dns.md). Delegering för zoner för omvänd sökning fungerar på samma sätt. Den enda skillnaden är att du måste konfigurera namnservrarna med Internet leverantören som tillhandahöll ditt IP-adressintervall i stället för domän namns registratorn.

## <a name="create-a-dns-ptr-record"></a>Skapa en DNS PTR-post

### <a name="ipv4"></a>IPv4

Följande exempel vägleder dig genom processen att skapa en PTR-post i en omvänd DNS-zon i Azure DNS. Information om andra posttyper och hur du ändrar befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure Portal](dns-operations-recordsets-portal.md) (på engelska).

1. Välj **+ post uppsättning** längst upp i fönstret **DNS-zon** för att öppna fönstret **Lägg till uppsättning av poster** .

   ![Skärm bild av fönstret DNS-zon med en pil som pekar på knappen + post uppsättning.](./media/dns-reverse-dns-hosting/figure4.png)

1. Namnet på post uppsättningen för en PTR-post måste vara resten av IPv4-adressen i omvänd ordning. 

   I det här exemplet är de tre första oktetterna redan ifyllda som en del av zon namnet (. 2.0.192). Därför anges bara den sista oktetten i rutan **namn** . Du kan till exempel namnge posten uppsättning **15** för en resurs vars IP-adress är 192.0.2.15.  
1. I **typ** väljer du **PTR**.  
1. För **domän namn** anger du det fullständigt kvalificerade domän namnet (FQDN) för den resurs som använder IP-adressen.
1. Klicka på **OK** längst ned i fönstret för att skapa DNS-posten.

   ![Fönstret Lägg till uppsättning av poster med ifyllda rutor i](./media/dns-reverse-dns-hosting/figure5.png)

I följande exempel visas hur du utför den här uppgiften med hjälp av PowerShell eller Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

Följande exempel vägleder dig genom processen med att skapa en ny PTR-post. Information om andra posttyper och hur du ändrar befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure Portal](dns-operations-recordsets-portal.md) (på engelska).

1. Välj **+ post uppsättning** längst upp i fönstret **DNS-zon** för att öppna fönstret **Lägg till uppsättning av poster** .

   ![Knapp för att skapa en post uppsättning](./media/dns-reverse-dns-hosting/figure6.png)

2. Namnet på post uppsättningen för en PTR-post måste vara resten av IPv6-adressen i omvänd ordning. Det får inte innehålla noll komprimering. 

   I det här exemplet är de första 64 bitarna i IPv6 redan ifyllda som en del av zon namnet (0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2. ip6. arpa). Därför anges bara de sista 64 bitarna i rutan **namn** . De sista 64 bitarna i IP-adressen anges i omvänd ordning med en punkt som avgränsare mellan varje hexadecimalt tal. Du kan till exempel namnge din post uppsättning **e. 5.0.4.9. f. a. 1. c. b. 0.1.4.2.5. f** för en resurs vars IP-adress är 2001:0DB8: ABDC: 0000: f524:10bc: 1af9:405e.  
3. I **typ** väljer du **PTR**.  
4. För **domän namn** anger du FQDN för den resurs som använder IP-adressen.
5. Klicka på **OK** längst ned i fönstret för att skapa DNS-posten.

![Skärm bild som visar fönstret Lägg till post uppsättning med en pil som pekar på värdet i fältet typ.](./media/dns-reverse-dns-hosting/figure7.png)

I följande exempel visas hur du utför den här uppgiften med hjälp av PowerShell eller Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Visa poster

Om du vill visa de poster som du har skapat bläddrar du till din DNS-zon i Azure Portal. I den nedre delen av fönstret **DNS-zon** kan du se posterna för DNS-zonen. Du bör se standard NS-och SOA-poster samt eventuella nya poster som du har skapat. NS-och SOA-posterna skapas i varje zon. 

### <a name="ipv4"></a>IPv4

Fönstret **DNS-zon** visar IPv4 PTR-poster:

![Fönstret DNS-zon med IPv4-poster](./media/dns-reverse-dns-hosting/figure8.png)

I följande exempel visas hur du visar PTR-poster med hjälp av PowerShell eller Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Fönstret **DNS-zon** visar IPv6 PTR-poster:

![Fönstret DNS-zon med IPv6-poster](./media/dns-reverse-dns-hosting/figure9.png)

I följande exempel visas hur du visar posterna med hjälp av PowerShell eller Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Vanliga frågor

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Kan jag vara värd för omvänd DNS-sökning zoner för mina Internet-tilldelade IP-block på Azure DNS?

Ja. Att vara värd för zonerna för omvänd sökning (ARPA) för dina egna IP-intervall i Azure DNS stöds fullt ut.

Skapa zonen för omvänd sökning i Azure DNS enligt beskrivningen i den här artikeln och samar beta med din Internet-leverantör för att [delegera zonen](dns-domain-delegation.md). Du kan sedan hantera PTR-posterna för varje omvänd sökning på samma sätt som andra post typer.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Hur mycket är kostnaden för omvänd DNS-sökning zon?

Att vara värd för den omvända DNS-sökzonen för det ISP-tilldelade IP-blocket i Azure DNS debiteras enligt [standard pris Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Kan jag vara värd för zoner för omvänd DNS-sökning för både IPv4-och IPv6-adresser i Azure DNS?

Ja. Den här artikeln beskriver hur du skapar både IPv4-och IPv6-zoner för omvänd DNS-sökning i Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Kan jag importera en befintlig zon för omvänd DNS-sökning?

Ja. Du kan använda Azure CLI för att importera befintliga DNS-zoner till Azure DNS. Den här metoden fungerar för både zoner för vanlig sökning och zoner för omvänd sökning.

Mer information finns i [Importera och exportera en DNS-zonfil med Azure CLI](dns-import-export.md).

## <a name="next-steps"></a>Nästa steg

Mer information om omvänd DNS finns i [Omvänd DNS-sökning på Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Lär dig att [Hantera omvända DNS-poster för dina Azure-tjänster](dns-reverse-dns-for-azure-services.md).
