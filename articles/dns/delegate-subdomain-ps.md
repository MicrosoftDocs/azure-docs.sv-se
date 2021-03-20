---
title: Delegera en under domän – Azure PowerShell-Azure DNS
description: Med den här utbildnings vägen börjar du delegera en Azure DNS under domän med hjälp av Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: 9b37d313aa5d8c2255b4e3be69831dfcb50238ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84712555"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegera en Azure DNS under domän med hjälp av Azure PowerShell

Du kan använda Azure PowerShell för att delegera en DNS-under domän. Om du till exempel äger contoso.com-domänen kan du delegera en under domän som heter *teknik* till en annan, separat zon som du kan administrera separat från zonen contoso.com.

Om du vill kan du delegera en under domän med hjälp av [Azure Portal](delegate-subdomain.md).

> [!NOTE]
> Contoso.com används som ett exempel i den här artikeln. Använd ditt eget domännamn i stället för contoso.com.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill delegera en Azure DNS under domän måste du först delegera din offentliga domän till Azure DNS. Mer information om hur du konfigurerar namnservrar för delegering finns i [delegera en domän till Azure DNS](./dns-delegate-domain-azure-dns.md) . När din domän har delegerats till din Azure DNS zon kan du delegera din under domän.

## <a name="create-a-zone-for-your-subdomain"></a>Skapa en zon för under domänen

Skapa först zonen för under domänen **teknik** .

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Anteckna namnservrarna

Notera sedan de fyra namnservrarna för under domänen teknik.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Skapa en test post

Skapa en **A** -post i den tekniska zon som ska användas för testning.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Skapa en NS-post

Skapa sedan en namnserver post (NS) för den **tekniska** zonen i contoso.com-zonen.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Testa delegeringen

Använd nslookup för att testa delegeringen.

1. Öppna ett PowerShell-fönster.
2. Skriv i kommando tolken `nslookup www.engineering.contoso.com.`
3. Du bör få ett icke-auktoritativt svar som visar adressen **10.10.10.10**.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar omvänd DNS för tjänster som finns i Azure](dns-reverse-dns-for-azure-services.md).