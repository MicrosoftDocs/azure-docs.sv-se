---
title: Azure Firewall privata IP-adressintervall för SNAT
description: Du kan konfigurera IP-adressintervall för SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: 91d4d631376c03b668128936f3840ce1119f9b6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482759"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall privata IP-adressintervall för SNAT

Azure Firewall tillhandahåller automatisk SNAT för all utgående trafik till offentliga IP-adresser. Som standard Azure Firewall inte SNAT med nätverksregler när mål-IP-adressen är i ett privat IP-adressintervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Programregler tillämpas alltid med en transparent proxy oavsett ip-måladress. [](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy)

Den här logiken fungerar bra när du dirigerar trafik direkt till Internet. Men om du har aktiverat tvingad [tunneltrafik](forced-tunneling.md)är Internetbunden trafik SNATed till en av brandväggens privata IP-adresser i AzureFirewallSubnet, vilket döljer källan från din lokala brandvägg.

Om din organisation använder ett offentligt IP-adressintervall för privata nätverk Azure Firewall SNAT trafiken till en av brandväggens privata IP-adresser i AzureFirewallSubnet. Du kan dock konfigurera att Azure Firewall att **inte använda** SNAT för ditt offentliga IP-adressintervall. Om du till exempel vill ange en enskild IP-adress kan du ange den så här: `192.168.1.10` . Om du vill ange ett intervall med IP-adresser kan du ange det så här: `192.168.1.0/24` .

- Om du Azure Firewall att aldrig **använda** SNAT oavsett mål-IP-adress använder du **0.0.0.0/0** som ditt privata IP-adressintervall. Med den här Azure Firewall kan du aldrig dirigera trafik direkt till Internet. 

- Om du vill konfigurera brandväggen till **att** alltid använda SNAT oavsett måladress använder du **255.255.255.255/32** som ditt privata IP-adressintervall.

> [!IMPORTANT]
> Det intervall för privata adresser som du anger gäller endast nätverksregler. För närvarande är programregler alltid SNAT.

> [!IMPORTANT]
> Om du vill ange dina egna privata IP-adressintervall och behålla standardadressintervallen för IANA RFC 1918 kontrollerar du att din anpassade lista fortfarande innehåller IANA RFC 1918-intervallet. 

Du kan konfigurera privata SNAT-IP-adresser med hjälp av följande metoder. Du måste konfigurera de privata SNAT-adresserna med den metod som passar din konfiguration. Brandväggar som är associerade med en brandväggsprincip måste ange intervallet i principen och inte använda `AdditionalProperties` .


|Metod            |Använda klassiska regler  |Använda brandväggsprincip  |
|---------|---------|---------|
|Azure Portal     | [Stöds](#classic-rules-3)| [Stöds](#firewall-policy-1)|
|Azure PowerShell     |[Konfigurera `PrivateRange`](#classic-rules)|stöds inte för närvarande|
|Azure CLI|[Konfigurera `--private-ranges`](#classic-rules-1)|stöds inte för närvarande|
|ARM-mall     |[konfigurera `AdditionalProperties` i brandväggsegenskapen](#classic-rules-2)|[konfigurera `snat/privateRanges` i brandväggsprincip](#firewall-policy)|


## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Konfigurera privata IP-adressintervall för SNAT – Azure PowerShell
### <a name="classic-rules"></a>Klassiska regler

Du kan använda Azure PowerShell för att ange privata IP-adressintervall för brandväggen.

> [!NOTE]
> `PrivateRange`Brandväggsegenskapen ignoreras för brandväggar som är associerade med en brandväggsprincip. Du måste använda egenskapen i enligt beskrivningen i Konfigurera privata IP-adressintervall för `SNAT` `firewallPolicies` [SNAT – ARM-mall](#firewall-policy).

#### <a name="new-firewall"></a>Ny brandvägg

För en ny brandvägg med klassiska regler är Azure PowerShell cmdleten:

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> Distribution av Azure Firewall med hjälp `New-AzFirewall` av kräver ett befintligt VNet och en offentlig IP-adress. Se [Distribuera och konfigurera Azure Firewall med Azure PowerShell för](deploy-ps.md) en fullständig distributionsguide.

> [!NOTE]
> IANAPrivateRanges expanderas till de aktuella standardvärdena på Azure Firewall medan de andra intervallen läggs till i den. Om du vill behålla standardinställningen för IANAPrivateRanges i din privata intervallspecifikation måste den finnas kvar i din specifikation `PrivateRange` enligt följande exempel.

Mer information finns i [New-AzFirewall](/powershell/module/az.network/new-azfirewall).

#### <a name="existing-firewall"></a>Befintlig brandvägg

Om du vill konfigurera en befintlig brandvägg med hjälp av klassiska regler använder du Azure PowerShell cmdlets:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>Konfigurera privata IP-adressintervall för SNAT – Azure CLI
### <a name="classic-rules"></a>Klassiska regler

Du kan använda Azure CLI för att ange privata IP-adressintervall för brandväggen med hjälp av klassiska regler. 

#### <a name="new-firewall"></a>Ny brandvägg

För en ny brandvägg som använder klassiska regler är Azure CLI-kommandot:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Distribution av Azure Firewall azure CLI-kommando kräver `az network firewall create` ytterligare konfigurationssteg för att skapa offentliga IP-adresser och IP-konfiguration. Se [Distribuera och konfigurera Azure Firewall azure CLI för](deploy-cli.md) en fullständig distributionsguide.

> [!NOTE]
> IANAPrivateRanges expanderas till de aktuella standardvärdena på Azure Firewall medan de andra intervallen läggs till i den. Om du vill behålla standardinställningen för IANAPrivateRanges i din privata intervallspecifikation måste den finnas kvar i din specifikation `private-ranges` enligt följande exempel.

#### <a name="existing-firewall"></a>Befintlig brandvägg

Om du vill konfigurera en befintlig brandvägg med hjälp av klassiska regler är Azure CLI-kommandot:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>Konfigurera privata IP-adressintervall för SNAT – ARM-mall
### <a name="classic-rules"></a>Klassiska regler

Om du vill konfigurera SNAT Malldistribution ARM-konfiguration kan du lägga till följande i `additionalProperties` egenskapen :

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```
### <a name="firewall-policy"></a>Brandväggsprincip

Azure Firewalls som är associerade med en brandväggsprincip har stöd för privata SNAT-intervall sedan API-versionen 2020-11-01. För närvarande kan du använda en mall för att uppdatera det privata SNAT-intervallet i brandväggsprincipen. I följande exempel konfigureras brandväggen till **att alltid använda** SNAT-nätverkstrafik:

```json
{ 

            "type": "Microsoft.Network/firewallPolicies", 
            "apiVersion": "2020-11-01", 
            "name": "[parameters('firewallPolicies_DatabasePolicy_name')]", 
            "location": "eastus", 
            "properties": { 
                "sku": { 
                    "tier": "Standard" 
                }, 
                "snat": { 
                    "privateRanges": [255.255.255.255/32] 
                } 
            } 
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Konfigurera privata IP-adressintervall för SNAT – Azure Portal
### <a name="classic-rules"></a>Klassiska regler

Du kan använda Azure Portal för att ange privata IP-adressintervall för brandväggen.

1. Välj din resursgrupp och välj sedan brandväggen.
2. På sidan **Översikt,** Privata **IP-intervall,** väljer du standardvärdet **IANA RFC 1918**.

   Sidan **Redigera privata IP-prefix** öppnas:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Redigera privata IP-prefix":::

1. Som standard **konfigureras IANAPrivateRanges.**
2. Redigera de privata IP-adressintervallen för din miljö och välj sedan **Spara.**

### <a name="firewall-policy"></a>Brandväggsprincip

1.  Välj din resursgrupp och välj sedan brandväggsprincipen.
2.  Välj **Privata IP-intervall (SNAT)** i **kolumnen** Inställningar.

    Som standard är **Använd standardinställningen Azure Firewall SNAT-beteende för** princip har valts. 
3. Om du vill anpassa SNAT-konfigurationen avmarkerar du kryssrutan och under **Utför SNAT** väljer du villkoren för att utföra SNAT för din miljö.
      :::image type="content" source="media/snat-private-range/private-ip-ranges-snat.png" alt-text="Privata IP-intervall (SNAT)":::


4.   Välj **Använd**.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer [Azure Firewall tvingad tunneling](forced-tunneling.md).