---
title: Hantera DNS-poster i Azure DNS med Azure PowerShell | Microsoft Docs
description: Hantering av DNS-postuppsättningar och poster på Azure DNS när du är värd för din domän på Azure DNS. Alla PowerShell-kommandon för åtgärder på post uppsättningar och poster.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: f4e713f54ab4702b21763dc9fc6c7b606f94a945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011599"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Hantera DNS-poster och post uppsättningar i Azure DNS med Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Klassisk Azure-CLI](./dns-operations-recordsets-cli.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Den här artikeln visar hur du hanterar DNS-poster för din DNS-zon med hjälp av Azure PowerShell. DNS-poster kan också hanteras med hjälp av plattforms oberoende [Azure CLI](dns-operations-recordsets-cli.md) eller [Azure Portal](dns-operations-recordsets-portal.md).

I exemplen i den här artikeln förutsätter vi att du redan har [installerat Azure PowerShell, loggat in och skapat en DNS-zon](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Introduktion

Innan du skapar DNS-poster i Azure DNS, måste du först förstå hur Azure DNS organiserar DNS-poster i DNS-postuppsättningar.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Mer information om DNS-poster i Azure DNS finns i [DNS-zoner och poster](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Skapa en ny DNS-post

Om den nya posten har samma namn och typ som en befintlig post måste du [lägga till den i den befintliga post uppsättningen](#add-a-record-to-an-existing-record-set). Om din nya post har ett annat namn och skriver till alla befintliga poster måste du skapa en ny post uppsättning. 

### <a name="create-a-records-in-a-new-record-set"></a>Skapa poster i en ny post uppsättning

Du skapar postuppsättningar med hjälp av cmdleten `New-AzDnsRecordSet`. När du skapar en post uppsättning måste du ange post uppsättningens namn, zonen, TTL-värdet (Time to Live), post typen och de poster som ska skapas.

Parametrarna för att lägga till poster i en postuppsättning varierar beroende på typen av postuppsättning. Om du till exempel använder en post uppsättning av typen "A" måste du ange IP-adressen med hjälp av parametern `-IPv4Address` . Andra parametrar används för andra post typer. Se fler exempel på post typer för mer information.

I följande exempel skapas en post uppsättning med det relativa namnet "www" i DNS-zonen "contoso.com". Det fullständigt kvalificerade namnet på post uppsättningen är ' www.contoso.com '. Post typen är "A" och TTL är 3600 sekunder. Post uppsättningen innehåller en enda post med IP-adressen 1.2.3.4.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Om du vill skapa en post uppsättning på "Apex" för en zon (i det här fallet "contoso.com") använder du post uppsättningens namn \@ (exklusive citat tecken):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Om du behöver skapa en post uppsättning som innehåller mer än en post måste du först skapa en lokal matris och lägga till posterna och sedan skicka matrisen till `New-AzDnsRecordSet` så här:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Metadata för post uppsättningar](dns-zones-records.md#tags-and-metadata) kan användas för att associera programspecifika data med varje post uppsättning som nyckel/värde-par. I följande exempel visas hur du skapar en post uppsättning med två metadata-poster, Avd = ekonomi och miljö = produktion.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS stöder också tomma post uppsättningar, som kan fungera som plats hållare för att reservera ett DNS-namn innan DNS-poster skapas. Tomma post uppsättningar visas i Azure DNS kontroll planet, men visas på Azure DNS namnservrar. I följande exempel skapas en tom post uppsättning:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Skapa poster av andra typer

I följande exempel visas hur du skapar poster med andra post typer som stöds av Azure DNS, om du har sett hur man skapar poster av typen A.

I varje fall visar vi hur du skapar en post uppsättning som innehåller en enda post. De tidigare exemplen för "A"-poster kan anpassas för att skapa post uppsättningar av andra typer som innehåller flera poster, med metadata eller för att skapa tomma post uppsättningar.

Vi ger inget exempel på att skapa en SOA-postuppsättning eftersom SOAs skapas och tas bort med varje DNS-zon och inte kan skapas eller tas bort separat. [SOA kan dock ändras, vilket visas i ett senare exempel](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Skapa en AAAA-postuppsättning med en post

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Skapa en CAA post uppsättning med en enda post

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Skapa en CNAME-postuppsättning med en post

> [!NOTE]
> DNS-standarderna tillåter inte CNAME-poster på toppen av en zon ( `-Name '@'` ), eller så tillåter de inte post uppsättningar som innehåller mer än en post.
> 
> Mer information finns i [CNAME-poster](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Skapa en MX-postuppsättning med en post

I det här exemplet använder vi post uppsättnings namnet \@ för att skapa en MX-post i zonens Apex (i det här fallet "contoso.com").


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Skapa en NS-postuppsättning med en post

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Skapa en PTR-postuppsättning med en post

I det här fallet representerar "my-arpa-zone.com" den zon för omvänd sökning som representerar ditt IP-intervall. Varje PTR-post som har angetts i den här zonen motsvarar en IP-adress i IP-intervallet. Post namnet 10 är den sista oktetten i IP-adressen i det här IP-intervallet som representeras av den här posten.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Skapa en SRV-postuppsättning med en post

När du skapar en [SRV-postuppsättning](dns-zones-records.md#srv-records)anger du *\_ tjänsten* och *\_ protokollet* i post uppsättningens namn. Du behöver inte ta med \@ i post uppsättnings namnet när du skapar en SRV-postuppsättning på zonens Apex.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Skapa en TXT-postuppsättning med en enda post

I följande exempel visas hur du skapar en TXT-post. Mer information om den maximala sträng längden som stöds i TXT-poster finns i [TXT-poster](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Hämta en post uppsättning

Använd om du vill hämta en befintlig post uppsättning `Get-AzDnsRecordSet` . Den här cmdleten returnerar ett lokalt objekt som representerar den angivna posten i Azure DNS.

Som med `New-AzDnsRecordSet` måste det angivna namnet för post uppsättningen vara ett *relativt* namn, vilket innebär att det måste utesluta zon namnet. Du måste också ange post typen och zonen som innehåller post uppsättningen.

I följande exempel visas hur du hämtar en post uppsättning. I det här exemplet anges zonen med `-ZoneName` `-ResourceGroupName` parametrarna och.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativt kan du också ange zonen med ett zon objekt, som skickas med hjälp av `-Zone` parametern.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>List post uppsättningar

Du kan också använda `Get-AzDnsZone` för att lista post uppsättningar i en zon genom att utelämna `-Name` parametrarna och/eller `-RecordType` .

I följande exempel returneras alla post uppsättningar i zonen:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

I följande exempel visas hur alla post uppsättningar av en specifik typ kan hämtas genom att ange post typen och utelämna post uppsättnings namnet:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Om du vill hämta alla post uppsättningar med ett angivet namn, mellan post typer, måste du hämta alla post uppsättningar och sedan filtrera resultaten:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

I alla ovanstående exempel kan zonen anges antingen med hjälp av `-ZoneName` `-ResourceGroupName` parametrarna och (som visas), eller genom att ange ett zon objekt:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Lägga till en post i en befintlig post uppsättning

Om du vill lägga till en post i en befintlig post uppsättning följer du följande tre steg:

1. Hämta den befintliga post uppsättningen

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Lägg till den nya posten i den lokala post uppsättningen. Detta är en offline-åtgärd.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Genomför ändringen tillbaka till Azure DNS tjänsten. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Om `Set-AzDnsRecordSet` du använder *ersätts* den befintliga post uppsättningen i Azure DNS (och alla poster den innehåller) med den angivna post uppsättningen. [Etag-kontroller](dns-zones-records.md#etags) används för att säkerställa att samtidiga ändringar inte skrivs över. Du kan använda den valfria `-Overwrite` växeln för att ignorera de här kontrollerna.

Den här sekvensen av åtgärder kan också vara *skickas*, vilket innebär att du skickar post uppsättnings objektet med hjälp av pipe i stället för att skicka det som en parameter:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

I exemplen ovan visas hur du lägger till en "A"-post i en befintlig post uppsättning av typen "A". En liknande sekvens med åtgärder används för att lägga till poster i post uppsättningar av andra typer, vilket ersätter `-Ipv4Address` parametern `Add-AzDnsRecordConfig` med andra parametrar som är speciella för varje posttyp. Parametrarna för varje posttyp är desamma som för `New-AzDnsRecordConfig` cmdleten, som du ser i fler exempel på post typer ovan.

Post uppsättningar av typen CNAME eller SOA får inte innehålla fler än en post. Den här begränsningen uppstår i DNS-standarden. Det är inte en begränsning av Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Ta bort en post från en befintlig post uppsättning

Processen för att ta bort en post från en post uppsättning liknar processen för att lägga till en post i en befintlig post uppsättning:

1. Hämta den befintliga post uppsättningen

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Ta bort posten från objektet lokal post uppsättning. Detta är en offline-åtgärd. Posten som tas bort måste vara en exakt matchning med en befintlig post för alla parametrar.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Genomför ändringen tillbaka till Azure DNS tjänsten. Använd den valfria `-Overwrite` växeln för att utelämna [etag-kontroller](dns-zones-records.md#etags) för samtidiga ändringar.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Om du använder ovanstående sekvens för att ta bort den sista posten från en post uppsättning, tas inte post uppsättningen bort, i stället lämnas en tom post uppsättning. Ta bort en post uppsättning helt i [ta bort en post uppsättning](#delete-a-record-set).

På samma sätt som för att lägga till poster i en post uppsättning kan du också skickas:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Olika post typer stöds genom att de lämpliga typspecifika parametrarna skickas till `Remove-AzDnsRecordSet` . Parametrarna för varje posttyp är desamma som för `New-AzDnsRecordConfig` cmdleten, som du ser i fler exempel på post typer ovan.


## <a name="modify-an-existing-record-set"></a>Ändra en befintlig post uppsättning

Stegen för att ändra en befintlig post uppsättning liknar de steg du utför när du lägger till eller tar bort poster från en post uppsättning:

1. Hämta den befintliga post uppsättningen med hjälp av `Get-AzDnsRecordSet` .
2. Ändra det lokala post uppsättnings objektet genom att:
    * Lägga till eller ta bort poster
    * Ändra parametrarna för befintliga poster
    * Ändra metadata för post uppsättningen och TTL (Time to Live)
3. Genomför ändringarna med hjälp av `Set-AzDnsRecordSet` cmdleten. Detta *ersätter* den befintliga post uppsättningen i Azure DNS med den angivna post uppsättningen.

När `Set-AzDnsRecordSet` du använder [etag-kontroller](dns-zones-records.md#etags) används för att säkerställa att samtidiga ändringar inte skrivs över. Du kan använda den valfria `-Overwrite` växeln för att ignorera de här kontrollerna.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Så här uppdaterar du en post i en befintlig post uppsättning

I det här exemplet ändrar vi IP-adressen för en befintlig "A"-post:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Ändra en SOA-post

Det går inte att lägga till eller ta bort poster från den automatiskt skapade SOA-postuppsättningen vid zonens Apex ( `-Name "@"` inklusive citat tecken). Du kan dock ändra någon av parametrarna i SOA-posten (förutom "värd") och post uppsättningens TTL.

I följande exempel visas hur du ändrar *e-* postegenskapen för SOA-posten:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Ändra NS-poster i zonen Apex

NS-postuppsättningen på zon Apex skapas automatiskt med varje DNS-zon. Den innehåller namnen på de Azure DNS namnservrar som har tilldelats zonen.

Du kan lägga till fler namnservrar i den här NS-postuppsättningen för att stödja samvärdbaserade domäner med fler än en DNS-Provider. Du kan också ändra TTL och metadata för den här post uppsättningen. Du kan dock inte ta bort eller ändra de förifyllda Azure DNS namnservrarna.

Observera att detta endast gäller för NS-postuppsättningen i zonens Apex. Andra NS-postuppsättningar i din zon (som används för att delegera underordnade zoner) kan ändras utan begränsning.

I följande exempel visas hur du lägger till ytterligare en namnserver i NS-postuppsättningen i zonen Apex:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Ändra metadata för post uppsättning

[Metadata för post uppsättningar](dns-zones-records.md#tags-and-metadata) kan användas för att associera programspecifika data med varje post uppsättning som nyckel/värde-par.

I följande exempel visas hur du ändrar metadata för en befintlig post uppsättning:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Ta bort en post uppsättning

Post uppsättningar kan tas bort med hjälp av `Remove-AzDnsRecordSet` cmdleten. Om du tar bort en post uppsättning raderas även alla poster i post uppsättningen.

> [!NOTE]
> Du kan inte ta bort SOA-och NS-postuppsättningarna i Zone Apex ( `-Name '@'` ).  Azure DNS skapade dessa automatiskt när zonen skapades och tar bort dem automatiskt när zonen tas bort.

I följande exempel visas hur du tar bort en post uppsättning. I det här exemplet anges post uppsättnings namnet, post uppsättnings typen, zon namnet och resurs gruppen uttryckligen.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativt kan post uppsättningen anges efter namn och typ och zonen som anges med ett-objekt:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Som ett tredje alternativ kan själva post uppsättningen anges med ett post uppsättnings objekt:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

När du anger den post uppsättning som ska tas bort med hjälp av ett post uppsättnings objekt används [etag-kontroller](dns-zones-records.md#etags) för att säkerställa att samtidiga ändringar inte tas bort. Du kan använda den valfria `-Overwrite` växeln för att ignorera de här kontrollerna.

Objektet post uppsättning kan också vara skickas i stället för att skickas som en parameter:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Bekräftelsemeddelanden

Cmdletarna `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` och `Remove-AzDnsRecordSet` stöder alla bekräftelsemeddelanden.

Varje cmdlet uppmanas att bekräfta om `$ConfirmPreference` PowerShell-preferensen har värdet `Medium` eller lägre. Eftersom standardvärdet för `$ConfirmPreference` är `High` , anges inte dessa meddelanden när du använder standard-PowerShell-inställningarna.

Du kan åsidosätta den aktuella `$ConfirmPreference`-inställningen med hjälp av parametern `-Confirm`. Om du anger `-Confirm` eller `-Confirm:$True`, så uppmanar cmdleten dig att bekräfta detta innan den körs. Om du anger `-Confirm:$False`, som ber cmdleten dig inte om bekräftelse. 

Mer information om `-Confirm` och `$ConfirmPreference` finns i [Om inställningsvariabler](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Nästa steg

Läs mer om [zoner och poster i Azure DNS](dns-zones-records.md).
<br>
Lär dig hur du [skyddar dina zoner och poster när du](dns-protect-zones-recordsets.md) använder Azure DNS.
<br>
Läs [Azure DNS PowerShell Reference-dokumentationen](/powershell/module/az.dns).