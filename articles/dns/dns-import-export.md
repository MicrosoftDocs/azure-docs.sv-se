---
title: Importera och exportera en domän zon fil – Azure CLI
titleSuffix: Azure DNS
description: Lär dig att importera och exportera en DNS-zonfil till Azure DNS med hjälp av Azure CLI
services: dns
author: rohinkoul
ms.service: dns
ms.date: 7/30/2020
ms.author: rohink
ms.topic: how-to
ms.openlocfilehash: e2b998432f6c4417da0242d86347ed43acb5071a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968238"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importera och exportera en DNS-zonfil med Azure CLI

Den här artikeln beskriver hur du importerar och exporterar DNS-zonfiler för Azure DNS med hjälp av Azure CLI.

## <a name="introduction-to-dns-zone-migration"></a>Introduktion till migrering av DNS-zon

En DNS-zonfil är en textfil som innehåller information om varje Domain Name System (DNS)-post i zonen. Det följer standardformat, vilket gör det lämpligt för överföring av DNS-poster mellan DNS-system. Att använda en zonfil är ett snabbt, tillförlitligt och bekvämt sätt att överföra en DNS-zon till eller från Azure DNS.

Azure DNS stöder import och export av zonfiler med hjälp av kommando rads gränssnittet för Azure (CLI). Zon fil import stöds för närvarande **inte** via Azure PowerShell eller Azure Portal.

Azure CLI är ett kommando rads verktyg för flera plattformar som används för att hantera Azure-tjänster. Den är tillgänglig för Windows-, Mac-och Linux-plattformarna från [Azures hämtnings sida](https://azure.microsoft.com/downloads/). Stöd för flera plattformar är viktigt för att importera och exportera zonfiler, eftersom den vanligaste namn serverprogram varan, [BIND](https://www.isc.org/downloads/bind/), vanligt vis körs på Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Hämta din befintliga DNS-zonfil

Innan du importerar en DNS-zonfil till Azure DNS måste du skaffa en kopia av zonfilen. Filens källa är beroende av var DNS-zonen för närvarande finns.

* Om din DNS-zon är värd för en partner tjänst (till exempel en domän registrator, dedikerad DNS-provider eller en annan molnbaserad Provider) bör tjänsten ge möjlighet att ladda ned DNS-zonfilen.
* Om din DNS-zon finns i Windows DNS, är standardmappen för zonfilen **%systemroot%\System32\DNS**. Den fullständiga sökvägen till varje zon fil visas också på fliken **Allmänt** i DNS-konsolen.
* Om din DNS-zon är värd för att använda BIND, anges platsen för zonfilen för varje zon i BIND-konfigurationsfilen **med namnet. conf**.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importera en DNS-zonfil till Azure DNS

Om du importerar en zonfil skapas en ny zon i Azure DNS om den inte redan finns. Om zonen redan finns måste post uppsättningarna i zonfilen slås samman med de befintliga post uppsättningarna.

### <a name="merge-behavior"></a>Sammanslagnings beteende

* Som standard slås befintliga och nya post uppsättningar samman. Identiska poster i en sammanfogad post uppsättning är deduplicerade.
* När post uppsättningar slås samman används TTL-värdet (Time to Live) för befintliga post uppsättningar.
* SOA-parametrar (Start of Authority) (förutom `host` ) hämtas alltid från den importerade zonfilen. På samma sätt hämtas TTL-värdet från den importerade zonfilen på samma sätt som den namn server post som angetts i zon Apex.
* En importerad CNAME-post ersätter inte en befintlig CNAME-post med samma namn.  
* När en konflikt uppstår mellan en CNAME-post och en annan post med samma namn men annan typ (oavsett vilken som är befintlig eller ny), behålls den befintliga posten. 

### <a name="additional-information-about-importing"></a>Mer information om hur du importerar

Följande kommentarer ger ytterligare teknisk information om zon import processen.

* `$TTL`Direktivet är valfritt och stöds. När inget `$TTL` direktiv anges importeras poster utan explicit TTL till en standard-TTL på 3600 sekunder. När två poster i samma post uppsättning anger olika TTLs, används det lägre värdet.
* `$ORIGIN`Direktivet är valfritt och stöds. När inget `$ORIGIN` har angetts är standardvärdet som används zon namnet som anges på kommando raden (plus det avslutande ".").
* - `$INCLUDE` Och- `$GENERATE` direktiven stöds inte.
* Dessa post typer stöds: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV och TXT.
* SOA-posten skapas automatiskt av Azure DNS när en zon skapas. När du importerar en zonfil tas alla SOA-parametrar från zonfilen *förutom* `host` parametern. Den här parametern använder det värde som tillhandahålls av Azure DNS. Detta beror på att den här parametern måste referera till den primära namnserver som tillhandahålls av Azure DNS.
* Namn server posten som anges i zonens Apex skapas också automatiskt av Azure DNS när zonen skapas. Endast TTL-värdet för den här post uppsättningen importeras. Dessa poster innehåller namn server namnen som tillhandahålls av Azure DNS. Postdata skrivs inte över av de värden som finns i den importerade zonfilen.
* Under den allmänt tillgängliga för hands versionen stöder Azure DNS bara TXT-poster med en sträng. Multistring-poster sammanfogas och trunkeras till 255 tecken.

### <a name="cli-format-and-values"></a>CLI-format och-värden

Formatet på Azure CLI-kommandot för att importera en DNS-zon är:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Värden:

* `<resource group>` är namnet på resurs gruppen för zonen i Azure DNS.
* `<zone name>` är namnet på zonen.
* `<zone file name>` är sökvägen/namnet på zonfilen som ska importeras.

Om en zon med det här namnet inte finns i resurs gruppen skapas den åt dig. Om zonen redan finns slås de importerade post uppsättningarna samman med befintliga post uppsättningar. 

### <a name="step-1-import-a-zone-file"></a>Steg 1. Importera en zonfil

Så här importerar du en zonfil för zonen **contoso.com**.

1. Om du inte redan har en, måste du skapa en resurs grupp för Resource Manager.

    ```azurecli
    az group create --resource-group myresourcegroup -l westeurope
    ```

2. Om du vill importera zonen **contoso.com** från filen **contoso.com.txt** till en ny DNS-zon i resurs gruppen **myresourcegroup** kör du kommandot `az network dns zone import` .<BR>Detta kommando läser in zonfilen och tolkar den. Kommandot kör en serie kommandon på Azure DNS-tjänsten för att skapa zonen och alla post uppsättningar i zonen. Kommandot rapporterar förlopp i konsol fönstret, tillsammans med eventuella fel eller varningar. Eftersom post uppsättningar skapas i serien kan det ta några minuter att importera en stor zon fil.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Steg 2. Verifiera zonen

Du kan kontrol lera DNS-zonen när du har importerat filen genom att använda någon av följande metoder:

* Du kan lista posterna med hjälp av följande Azure CLI-kommando:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Du kan lista posterna med hjälp av Azure CLI-kommandot `az network dns record-set ns list` .
* Du kan använda `nslookup` för att kontrol lera namn matchningen för posterna. Eftersom zonen inte har delegerats ännu, måste du ange rätt Azure DNS namnservrar uttryckligen. I följande exempel visas hur du hämtar namn server namnen som har tilldelats zonen. Här visas också hur du frågar "www"-post med hjälp av `nslookup` .

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>Steg 3. Uppdatera DNS-delegering

När du har kontrollerat att zonen har importer ATS korrekt måste du uppdatera DNS-delegeringen så att den pekar på Azure DNS namnservrar. Mer information finns i artikeln [Uppdatera DNS-delegeringen](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportera en DNS-zonfil från Azure DNS

Formatet på Azure CLI-kommandot för att exportera en DNS-zon är:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Värden:

* `<resource group>` är namnet på resurs gruppen för zonen i Azure DNS.
* `<zone name>` är namnet på zonen.
* `<zone file name>` är sökvägen/namnet på zonfilen som ska exporteras.

Precis som med zon importen måste du först logga in, välja din prenumeration och konfigurera Azure CLI för att använda Resource Manager-läge.

### <a name="to-export-a-zone-file"></a>Exportera en zonfil

Om du vill exportera den befintliga Azure DNS zonen **contoso.com** i resurs gruppen **myresourcegroup** till filen **contoso.com.txt** (i den aktuella mappen) kör du `azure network dns zone export` . Det här kommandot anropar Azure DNS tjänsten för att räkna upp post uppsättningar i zonen och exportera resultaten till en BIND-kompatibel zon fil.

```azurecli
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [hanterar post uppsättningar och poster](./dns-getstarted-cli.md) i din DNS-zon.

* Lär dig hur du [delegerar din domän till Azure DNS](dns-domain-delegation.md).