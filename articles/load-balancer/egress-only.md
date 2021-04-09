---
title: Konfiguration för lastbalanserare med ”endast utgående”
titleSuffix: Azure Load Balancer
description: I den här artikeln får du lära dig hur du skapar en intern belastningsutjämnare med utgående NAT
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: ee264a22de5ce094e8a4c1335ace77cbbba49270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94695001"
---
# <a name="outbound-only-load-balancer-configuration"></a>Konfiguration för lastbalanserare med ”endast utgående”

Använd en kombination av interna och externa standard belastnings utjämning för att skapa utgående anslutningar för virtuella datorer bakom en intern belastningsutjämnare. 

Den här konfigurationen ger utgående NAT för ett internt scenario för belastnings utjämning, vilket genererar en "endast utgångs" installation för din backend-pool.


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="Virtual Network NAT">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*Bild: endast utgående belastnings Utjämnings konfiguration*

De steg som krävs är:

1. Skapa ett virtuellt nätverk med en skydds-värd.
2. Skapa en virtuell dator med endast en privat IP-adress.
3. Skapa både interna och offentliga standard belastnings utjämning.
4. Lägg till backend-pooler i båda belastningsutjämnarens belastningsutjämnare och placera den virtuella datorn i varje pool.
5. Anslut till den virtuella datorn via skydds-värden och:
    1. Testa utgående anslutning, 
    2. Konfigurera en utgående regel för offentlig belastningsutjämnare.
    3. Testa om utgående anslutning.

## <a name="create-virtual-network-and-virtual-machine"></a>Skapa virtuellt nätverk och virtuell dator

Skapa ett virtuellt nätverk med två undernät:

* Primärt undernät
* Skydds-undernät

Skapa en virtuell dator i det nya virtuella nätverket.

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

1. [Logga in](https://portal.azure.com) i Azure-portalen.

2. Välj **Skapa en resurs > Nätverk > Virtuellt nätverk** eller sök efter **virtuellt nätverk** i sökrutan på den övre vänstra sidan på skärmen.

2. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **Skapa ny**. </br> Ange **myResourceGroupLB**. </br> Välj **OK**. |
    | **Instansinformation** |                                                                 |
    | Name             | Ange **myVNet**                                    |
    | Region           | Välj **USA, östra 2** |

3. Välj fliken **IP-adresser** eller Välj **Nästa: knappen Nästa: IP-adress** längst ned på sidan.

4. På fliken **IP-adresser** anger du den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | IPv4-adressutrymme | Ange **10.1.0.0/16** |

5. Under **under näts namn** väljer du ordet **standard**.

6. I **Redigera undernät** anger du den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | Namn på undernät | Ange **myBackendSubnet** |
    | Adressintervall för undernätet | Ange **10.1.0.0/24** |

7. Välj **Spara**.

8. Välj fliken **säkerhet** .

9. Under **BastionHost** väljer du **Aktivera**. Ange den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | Skydds namn | Ange **myBastionHost** |
    | AzureBastionSubnet-adressutrymme | Ange **10.1.1.0/24** |
    | Offentlig IP-adress | Välj **Skapa ny**. </br> Som **namn** anger du **myBastionIP**. </br> Välj **OK**. |


8. Välj fliken **Granska + skapa** eller Välj knappen **Granska + skapa** .

9. Välj **Skapa**.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroupLB** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVM** |
    | Region | Välj **USA, östra 2** |
    | Tillgänglighets alternativ | Välj **ingen redundans för infrastruktur krävs** |
    | Bild | Välj **Windows Server 2019 Data Center** |
    | Azure Spot-instans | Välj **Nej** |
    | Storlek | Välj storlek på virtuell dator eller Ställ in standardinställningen |
    | **Administratörs konto** |  |
    | Användarnamn | Ange ett användar namn |
    | Lösenord | Ange ett lösen ord |
    | Bekräfta lösenordet | Ange lösenordet igen |
    | **Regler för inkommande portar** |  |
    | Offentliga inkommande portar | Välj **Tillåt valda portar** |
    | Välj inkommande portar | Välj **RDP (3389)** |

3. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
4. På fliken nätverk väljer eller anger du:

    | Inställning | Värde |
    |-----|------------|
    | **Nätverksgränssnitt** |  |
    | Virtuellt nätverk | **myVNet** |
    | Undernät | **myBackendSubnet** |
    | Offentlig IP-adress | Välj **Ingen**. |
    | Nätverks säkerhets grupp för nätverkskort | Välj **ingen**|
    | Placerar du den virtuella datorn bakom en befintlig belastnings Utjämnings lösning? | Välj **Nej** |
   
5. Välj fliken **Hantering** eller **Nästa** > **Hantering**.

6. På fliken **hantering** väljer eller anger du:
    
    | Inställning | Värde |
    |-|-|
    | **Övervakning** |  |
    | Startdiagnostik | Välj **av** |
   
7. Välj **Granska + skapa**. 
  
8. Granska inställningarna och välj sedan **Skapa**.

## <a name="create-load-balancers-and-test-connectivity"></a>Skapa belastnings utjämning och testa anslutning

Använd Azure Portal för att skapa:

* Intern lastbalanserare
* Offentlig lastbalanserare
 
Lägg till din skapade virtuella dator i Server delens pool.  Sedan konfigurerar du en konfiguration så att den endast tillåter utgående anslutning från den virtuella datorn, testa före och efter.

### <a name="create-internal-load-balancer"></a>Skapa intern belastningsutjämnare

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Load Balancer**.

2. På fliken **grundläggande** på sidan **skapa belastnings utjämning** anger eller väljer du följande information: 

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **myResourceGroupLB** som skapades i föregående steg.|
    | Name                   | Ange **myInternalLoadBalancer**                                   |
    | Region         | Välj **USA, östra 2**.                                        |
    | Typ          | Välj **Intern**.                                        |
    | SKU           | Välj **standard** |
    | Virtuellt nätverk | Välj **myVNet** som skapades i föregående steg. |
    | Undernät  | Välj **myBackendSubnet** som skapades i föregående steg. |
    | Tilldelning av IP-adress | Välj **Dynamisk**. |

3. Acceptera standardinställningarna för återstående inställningar och välj sedan **Granska + skapa**.

4. På fliken **Granska och skapa** väljer du **skapa**.   

### <a name="create-public-load-balancer"></a>Skapa offentlig belastningsutjämnare

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Load Balancer**.

2. På fliken **grundläggande** på sidan **skapa belastnings utjämning** anger eller väljer du följande information: 

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa nytt** och ange **myResourceGroupLB** i text rutan.|
    | Name                   | Ange **myPublicLoadBalancer**                                   |
    | Region         | Välj **USA, östra 2**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Välj **standard** |
    | Offentlig IP-adress | Välj **Skapa ny**. |
    | Namn på offentlig IP-adress | Ange **myFrontendIP** i text rutan.|
    | Tillgänglighetszon | Välj **zon – redundant** |
    | Lägg till en offentlig IPv6-adress | Välj **Nej**. |

3. Acceptera standardinställningarna för återstående inställningar och välj sedan **Granska + skapa**.

4. På fliken **Granska och skapa** väljer du **skapa**.   
   
### <a name="create-internal-backend-address-pool"></a>Skapa intern backend-adresspool

Skapa **myInternalBackendPool** för backend-adresspoolen:

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myInternalLoadBalancer** i listan resurser.

2. Under **Inställningar** väljer du **backend-pooler** och väljer sedan **Lägg till**.

3. På sidan **Lägg till en server dels grupp** anger du **myInternalBackendPool** som namn på din backend-pool.
 
4. I **virtuellt nätverk** väljer du **myVNet**.

5. Under **virtuella datorer** väljer du **Lägg till** och väljer **myVM**.
 
6. Välj **Lägg till**.

### <a name="create-public-backend-address-pool"></a>Skapa offentlig backend-adresspool

Skapa **myPublicBackendPool** för backend-adresspoolen:

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myPublicLoadBalancer** i listan resurser.

2. Under **Inställningar** väljer du **backend-pooler** och väljer sedan **Lägg till**.

3. På sidan **Lägg till en server dels grupp** anger du **myPublicBackendPool** som namn på din backend-pool.

4. I **virtuellt nätverk** väljer du **myVNet**.
 
5. Under **virtuella datorer** väljer du **Lägg till** och väljer **myVM**.
 
6. Välj **Lägg till**.

### <a name="test-connectivity-before-outbound-rule"></a>Testa anslutning före utgående regel

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myVM** i resurs gruppen **myResourceGroupLB** i resurs gruppen.

2. På sidan **Översikt** väljer du **Anslut** och sedan **skydds**.

4. Ange det användar namn och lösen ord som angavs när den virtuella datorn skapades.

5. Välj **Anslut**.

6. Öppna Internet Explorer.

7. Ange **https://whatsmyip.org** i adress fältet.

8. Anslutningen bör inte utföras. Som standard tillåter standard offentlig belastningsutjämnare [inte utgående trafik utan en definierad utgående regel](load-balancer-overview.md#securebydefault).
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>Skapa en offentlig regel för utgående belastningsutjämnare

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myPublicLoadBalancer** i listan resurser.

2. Välj **utgående regler** under **Inställningar** och välj sedan **Lägg till**.

3. Använd de här värdena för att konfigurera de utgående reglerna:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myOutboundRule**. |
    | IP-adress för klient del | Välj **LoadBalancerFrontEnd**.|
    | Tids gräns för inaktivitet (minuter) | Flytta skjutreglaget till **15 minuter**.|
    | TCP-återställning | Välj **Aktiverad**.|
    | Serverdelspool | Välj **myPublicBackendPool**.| |
    | Port tilldelning – > port tilldelning | Välj **Använd standard antalet utgående portar** |

4. Välj **Lägg till**.

### <a name="test-connectivity-after-outbound-rule"></a>Testa anslutning efter utgående regel

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myVM** i resurs gruppen **myResourceGroupLB** i resurs gruppen.

2. På sidan **Översikt** väljer du **Anslut** och sedan **skydds**.

4. Ange det användar namn och lösen ord som angavs när den virtuella datorn skapades.

5. Välj **Anslut**.

6. Öppna Internet Explorer.

7. Ange **https://whatsmyip.org** i adress fältet.

8. Anslutningen ska lyckas.

9. Den IP-adress som visas ska vara IP-adressen för klient delen **myPublicLoadBalancer**.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen, belastningsutjämnare, virtuella datorer och alla relaterade resurser när de inte längre behövs. 

Om du vill göra det väljer du resurs gruppen **myResourceGroupLB** och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en "Utgånget"-konfiguration med en kombination av offentliga och interna belastningsutjämnare.  

Med den här konfigurationen kan du belastningsutjämna inkommande intern trafik till din backend-pool samtidigt som du fortfarande förhindrar offentliga inkommande anslutningar.

- Läs mer om [Azure Load Balancer](load-balancer-overview.md).
- Lär dig om [utgående anslutningar i Azure](load-balancer-outbound-connections.md).
- [Vanliga frågor och svar](load-balancer-faqs.md)om belastnings utjämning.
- Lär dig mer om [Azure skydds](../bastion/bastion-overview.md)