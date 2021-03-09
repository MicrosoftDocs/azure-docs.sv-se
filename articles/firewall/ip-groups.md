---
title: IP-grupper i Azure-brandväggen
description: Med IP-grupper kan du gruppera och hantera IP-adresser för Azure brand Väggs regler.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 907ed0774db0e07a61a3ad568e6c451ea5d37b44
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501744"
---
# <a name="ip-groups-in-azure-firewall"></a>IP-grupper i Azure-brandväggen

Med IP-grupper kan du gruppera och hantera IP-adresser för Azure brand Väggs regler på följande sätt:

- Som käll adress i DNAT-regler
- Som käll-eller mål adress i nätverks regler
- Som käll adress i program regler


En IP-grupp kan ha en enskild IP-adress, flera IP-adresser eller ett eller flera IP-adressintervall.

IP-grupper kan återanvändas i Azure brand Väggs DNAT, nätverks-och program regler för flera brand väggar mellan regioner och prenumerationer i Azure. Grupp namn måste vara unika. Du kan konfigurera en IP-grupp i Azure Portal, Azure CLI eller REST API. En exempel mall är avsedd att hjälpa dig att komma igång.

> [!NOTE]
> IP-grupper är för närvarande inte tillgängliga i Azures nationella moln miljöer.

## <a name="sample-format"></a>Samplingsformat

Följande exempel på IPv4-adress format är giltiga för användning i IP-grupper:

- Enskild adress: 10.0.0.0
- CIDR-notering: 10.1.0.0/32
- Adress intervall: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Skapa en IP-grupp

En IP-grupp kan skapas med hjälp av Azure Portal, Azure CLI eller REST API. Mer information finns i [skapa en IP-grupp](create-ip-group.md).

## <a name="browse-ip-groups"></a>Bläddra bland IP-grupper
1. Skriv **IP-grupper** i sök fältet Azure Portal och markera det. Du kan se listan över IP-grupper, eller så kan du välja **Lägg till** för att skapa en ny IP-grupp.
2. Välj en IP-grupp för att öppna översikts sidan. Du kan redigera, lägga till eller ta bort IP-adresser eller IP-grupper.

   ![Översikt över IP-grupper](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Hantera en IP-grupp

Du kan se alla IP-adresser i IP-gruppen och de regler eller resurser som är associerade med den. Om du vill ta bort en IP-grupp måste du först koppla från IP-gruppen från den resurs som använder den.

1. Om du vill visa eller redigera IP-adresserna väljer du **IP-adresser** under **Inställningar** i den vänstra rutan.
2. Om du vill lägga till en enda eller flera IP-adresser väljer du **Lägg till IP-adresser**. Då öppnas sidan **dra eller bläddra** för att ladda upp, eller så kan du ange adressen manuellt.
3.    Välj ellipserna (**...**) till höger om du vill redigera eller ta bort IP-adresser. Om du vill redigera eller ta bort flera IP-adresser markerar du rutorna och väljer **redigera** eller **ta bort** högst upp.
4. Slutligen kan du exportera filen i CSV-filformat.

> [!NOTE]
> Om du tar bort alla IP-adresser i en IP-grupp när den fortfarande används i en regel hoppas denna regel över.


## <a name="use-an-ip-group"></a>Använda en IP-grupp

Nu kan du välja **IP-grupp** som **ursprungs typ** eller **måltyp** för IP-adressen när du skapar Azure Firewall DNAt, program eller nätverks regler.

![IP-grupper i brand väggen](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Regional tillgänglighet

IP-grupper är tillgängliga i alla offentliga moln regioner.

## <a name="ip-address-limits"></a>IP-adress gränser

Du kan ha högst 100 IP-grupper per brand vägg med högst 5000 enskilda IP-adresser eller IP-prefix per IP-grupp.

## <a name="related-azure-powershell-cmdlets"></a>Relaterade Azure PowerShell-cmdletar

Följande Azure PowerShell-cmdletar kan användas för att skapa och hantera IP-grupper:

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup)
- [New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
- [New-AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule)
- [New-AzFirewallNatRule](/powershell/module/az.network/new-azfirewallnatrule)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).