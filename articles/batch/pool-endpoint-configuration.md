---
title: Konfigurera nodens slut punkter i Azure Batch pool
description: Konfigurera eller inaktivera åtkomst till SSH-eller RDP-portar på datornoderna i en Azure Batch pool.
ms.topic: how-to
ms.date: 02/13/2018
ms.openlocfilehash: 4e7df7da539be75ef1befdff4b4e1fe5244c1702
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109314"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurera eller inaktivera fjärråtkomst till Compute-noder i en Azure Batch pool

Som standard tillåter batch att en [nods användare](/rest/api/batchservice/computenode/adduser) med nätverks anslutning ansluter externt till en Compute-nod i en batch-pool. En användare kan till exempel ansluta via fjärr skrivbord (RDP) på port 3389 till en Compute-nod i en Windows-pool. På samma sätt kan en användare som standard ansluta via Secure Shell (SSH) på port 22 till en Compute-nod i en Linux-pool. 

I din miljö kan du behöva begränsa eller inaktivera dessa standardinställningar för extern åtkomst. Du kan ändra de här inställningarna genom att använda batch-API: erna för att ange egenskapen [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) . 

## <a name="about-the-pool-endpoint-configuration"></a>Om konfigurationen av poolens slut punkt
Slut punkts konfigurationen består av en eller flera [Network Address Translation-pooler (NAT)](/rest/api/batchservice/pool/add#inboundnatpool) för klient dels portar. (Blanda inte ihop en NAT-pool med datornoderna för datornoder.) Du konfigurerar varje NAT-pool för att åsidosätta standard anslutnings inställningarna för poolens datornoder. 

Varje konfiguration av NAT-pool innehåller en eller flera [regler för nätverks säkerhets grupper (NSG)](/rest/api/batchservice/pool/add#networksecuritygrouprule). Varje NSG-regel tillåter eller nekar viss nätverks trafik till slut punkten. Du kan välja att tillåta eller neka all trafik, trafik som identifieras av en [service tagg](../virtual-network/network-security-groups-overview.md#service-tags) (till exempel Internet) eller trafik från vissa IP-adresser eller undernät.

### <a name="considerations"></a>Överväganden
* Konfigurationen för poolens slut punkt är en del av poolens [nätverks konfiguration](/rest/api/batchservice/pool/add#networkconfiguration). Nätverks konfigurationen kan också innehålla inställningar för att ansluta poolen till ett [virtuellt Azure-nätverk](batch-virtual-network.md). Om du konfigurerar poolen i ett virtuellt nätverk kan du skapa NSG-regler som använder adress inställningar i det virtuella nätverket.
* Du kan konfigurera flera NSG-regler när du konfigurerar en NAT-pool. Reglerna kontrol leras i prioritetsordning. När villkoren för en regel uppfylls testas inga fler regler.


## <a name="example-deny-all-rdp-traffic"></a>Exempel: neka all RDP-trafik

Följande C#-kodfragment visar hur du konfigurerar RDP-slutpunkten på Compute-noder i en Windows-pool för att neka all nätverks trafik. Slut punkten använder en frontend-pool med portar i intervallet *60000-60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Exempel: neka all SSH-trafik från Internet

Följande python-kodfragment visar hur du konfigurerar SSH-slutpunkten för Compute-noder i en Linux-pool för att neka all Internet trafik. Slut punkten använder en frontend-pool med portar i intervallet *4000-4100*. 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                    source_address_prefix='Internet'
                )
            ]
        )
        ]
    )
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Exempel: Tillåt RDP-trafik från en speciell IP-adress

Följande C#-kodfragment visar hur du konfigurerar RDP-slutpunkten på datornoderna i en Windows-pool som endast tillåter RDP-åtkomst från IP- *198.51.100.7*. Den andra NSG-regeln nekar trafik som inte matchar IP-adressen.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Exempel: Tillåt SSH-trafik från ett speciellt undernät

Följande python-kodfragment visar hur du konfigurerar SSH-slutpunkten för Compute-noder i en Linux-pool som endast tillåter åtkomst från under nätet *192.168.1.0/24*. Den andra NSG-regeln nekar trafik som inte matchar under nätet.

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access='allow',
                    source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                    priority=175,
                    access='deny',
                    source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Mer information om NSG-regler i Azure finns i [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md).