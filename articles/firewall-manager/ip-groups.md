---
title: IP-grupper i Azure brand Väggs princip
description: Med IP-grupper kan du gruppera och hantera IP-adresser för Azure brand Väggs princip regler.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 54faa0bc7d414eb15a866b8e1e6d09e15b22b071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500860"
---
# <a name="ip-groups-in-azure-firewall-policy"></a>IP-grupper i Azure brand Väggs princip

Med IP-grupper kan du gruppera och hantera IP-adresser för Azure Firewall-principer på följande sätt:

- Som käll typ i DNAT-regler
- Som käll-eller mål typ i nätverks regler
- Som käll typ i program regler


En IP-grupp kan ha en enskild IP-adress, flera IP-adresser eller ett eller flera IP-adressintervall.

IP-grupper kan återanvändas i Azure brand Väggs DNAT, nätverks-och program regler för flera brand väggar mellan regioner och prenumerationer i Azure. Grupp namn måste vara unika. Du kan konfigurera en IP-grupp i Azure Portal, Azure CLI eller REST API. En exempel mall är avsedd att hjälpa dig att komma igång.

## <a name="sample-format"></a>Samplingsformat

Följande exempel på IPv4-adress format är giltiga för användning i IP-grupper:

- Enskild adress: 10.0.0.0
- CIDR-notering: 10.1.0.0/32
- Adress intervall: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Skapa en IP-grupp

En IP-grupp kan skapas med hjälp av Azure Portal, Azure CLI eller REST API. Mer information finns i [skapa en IP-grupp](../firewall/create-ip-group.md).

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

Nu kan du välja **IP-grupp** som **ursprungs typ** eller **måltyp** för IP-adressen när du skapar en princip med DNAt, program eller nätverks regler.

:::image type="content" source="media/ip-groups/firewall-ip-group.png" alt-text="IP-grupper i brand väggen":::

## <a name="ip-address-limits"></a>IP-adress gränser

Du kan ha högst 100 IP-grupper per brand vägg med högst 5000 enskilda IP-adresser eller IP-prefix per IP-grupp.

## <a name="related-azure-powershell-cmdlets"></a>Relaterade Azure PowerShell-cmdletar

Följande Azure PowerShell-cmdletar kan användas för att skapa och hantera IP-grupper:

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup)
- [New-AzFirewallPolicyNetworkRule](/powershell/module/az.network/new-azfirewallpolicynetworkrule)
- [New-AzFirewallPolicyApplicationRule](/powershell/module/az.network/new-azfirewallpolicyapplicationrule)
- [New-AzFirewallPolicyNatRule](/powershell/module/az.network/new-azfirewallpolicynatrule)

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skydda ditt virtuella WAN med Azure Firewall Manager](secure-cloud-network.md)