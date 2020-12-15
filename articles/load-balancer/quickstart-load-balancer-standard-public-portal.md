---
title: 'Snabb start: skapa en offentlig belastningsutjämnare – Azure Portal'
titleSuffix: Azure Load Balancer
description: Den här snabb starten visar hur du skapar en belastningsutjämnare med hjälp av Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 8827171788bd83a202b3607537204c71c34f29e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511849"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Snabb start: skapa en offentlig belastningsutjämnare för att belastningsutjämna virtuella datorer med hjälp av Azure Portal

Kom igång med Azure Load Balancer genom att använda Azure Portal för att skapa en offentlig belastningsutjämnare och tre virtuella datorer.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Standard-SKU-belastningsutjämnare rekommenderas för produktions arbets belastningar.  Mer information om SKU: er finns i **[Azure Load Balancer SKU: er](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="Standard belastnings Utjämnings resurser har skapats för snabb start." border="false":::

*Bild: resurser som skapats i snabb start.*

I det här avsnittet skapar du en belastningsutjämnare som laddar upp virtuella datorer. 

När du skapar en offentlig belastningsutjämnare, skapar du en ny offentlig IP-adress som är konfigurerad som klient del (kallas som **LoadBalancerFrontend** som standard) för belastningsutjämnaren.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Load Balancer**.

2. På fliken **grundläggande** på sidan **skapa belastnings utjämning** anger eller väljer du följande information: 

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och ange **CreatePubLBQS-RG** i text rutan.|
    | Namn                   | Ange **myLoadBalancer**                                   |
    | Region         | Välj **Europa, västra**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Välj **standard** |
    | Offentlig IP-adress | Välj **Skapa ny**. Om du har en befintlig offentlig IP-adress som du vill använda väljer du **Använd befintlig**. |
    | Namn på offentlig IP-adress | Skriv **myPublicIP** i textrutan.|
    | Tillgänglighetszon | Välj **zon-redundant** för att skapa en elastisk belastningsutjämnare. Om du vill skapa en zonindelade-belastningsutjämnare väljer du en speciell zon från 1, 2 eller 3 |
    | Lägg till en offentlig IPv6-adress | Välj **Nej**. </br> Mer information om IPv6-adresser och belastningsutjämnare finns i [Vad är IPv6 för Azure Virtual Network?](../virtual-network/ipv6-overview.md)  |

3. Acceptera standardinställningarna för återstående inställningar och välj sedan **Granska + skapa**.

4. På fliken **Granska och skapa** väljer du **skapa**.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="Skapa en standardlastbalanserare" border="true":::
 
## <a name="create-load-balancer-resources"></a>Skapa resurser för lastbalansering

I det här avsnittet konfigurerar du:

* Inställningar för belastnings utjämning för en backend-adresspool.
* En hälso avsökning.
* En belastnings Utjämnings regel.

### <a name="create-a-backend-pool"></a>Skapa en serverdelspool

En backend-adresspool innehåller IP-adresserna för de virtuella nätverkskorten som är anslutna till belastningsutjämnaren. 

Skapa **myBackendPool** för backend-adresspoolen för att inkludera virtuella datorer för belastnings utjämning av Internet trafik.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer** i listan resurser.

2. Under **Inställningar** väljer du **backend-pooler** och väljer sedan **Lägg till**.

3. På sidan **Lägg till en server dels grupp** anger du **myBackendPool** som namn på din backend-pool. Välj sedan **Lägg till**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Belastningsutjämnaren övervakar appens status med en hälso avsökning. 

Hälso avsökningen lägger till eller tar bort virtuella datorer från belastningsutjämnaren baserat på deras svar på hälso kontroller. 

Skapa en hälsoavsökning med namnet **myHealthProbe** så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer** i listan resurser.

2. Välj **hälso avsökningar** under **Inställningar** och välj sedan **Lägg till**.
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myHealthProbe**. |
    | Protokoll | Välj **http**. |
    | Port | Ange **80**.|
    | Intervall | Ange **15** som **intervall** i sekunder mellan avsöknings försök. |
    | Tröskelvärde för ej felfri | Välj **2** för antalet fel i **tröskeln** eller på varandra följande avsöknings fel som måste inträffa innan en virtuell dator betraktas som ohälsosam.|
    | | |

3. Lämna resten av standardinställningarna och välj **OK**.

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar klient delens IP-konfiguration för den inkommande trafiken och Server delens IP-pool för att ta emot trafiken. Käll-och mål porten definieras i regeln. 

I det här avsnittet ska du skapa en belastnings Utjämnings regel:

* Med namnet **myHTTPRule**.
* I klient delen med namnet **LoadBalancerFrontEnd**.
* Lyssnar på **Port 80**.
* Dirigerar belastnings bal anse rad trafik till Server delen med namnet **myBackendPool** på **port 80**.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer** i listan resurser.

2. Välj **belastnings Utjämnings regler** under **Inställningar** och välj sedan **Lägg till**.

3. Använd de här värdena för att konfigurera belastnings Utjämnings regeln:
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myHTTPRule**. |
    | IP-version | Välj **IPv4** |
    | IP-adress för klient del | Välj **LoadBalancerFrontEnd** |
    | Protokoll | Välj **TCP**. |
    | Port | Ange **80**.|
    | Serverdelsport | Ange **80**. |
    | Serverdelspool | Välj **myBackendPool**.|
    | Hälsoavsökning | Välj **myHealthProbe**. |
    | Tids gräns för inaktivitet (minuter) | Flytta skjutreglaget till **15** minuter. |
    | TCP-återställning | Välj **Aktiverad**. |
    | Utgående käll Network Address Translation (SNAT) | Välj **(rekommenderas) Använd utgående regler för att ge backend-poolens medlemmar åtkomst till Internet.** |

4. Lämna resten av standardinställningarna och välj sedan **OK**.

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet får du:

* Skapa ett virtuellt nätverk.
* Skapa tre virtuella datorer för belastningsutjämnarens backend-pool.
* Installera IIS på de virtuella datorerna för att testa belastningsutjämnaren.

## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät.

1. Välj **Skapa en resurs > Nätverk > Virtuellt nätverk** eller sök efter **virtuellt nätverk** i sökrutan på den övre vänstra sidan på skärmen.

2. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **CreatePubLBQS-RG** |
    | **Instansinformation** |                                                                 |
    | Name             | Ange **myVNet**                                    |
    | Region           | Välj **Europa, västra** |

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

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här avsnittet skapar du tre virtuella datorer (**myVM1**, **myVM2** och **myVM3**) i tre olika zoner (**zon 1**, **zon 2** och **zon 3**). 

De här virtuella datorerna läggs till i backend-poolen för belastningsutjämnaren som skapades tidigare.

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **CreatePubLBQS-RG** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVM1** |
    | Region | Välj **Europa, västra** |
    | Tillgänglighets alternativ | Välj **tillgänglighets zoner** |
    | Tillgänglighetszon | Välj **1** |
    | Bild | Välj **Windows Server 2019 Data Center** |
    | Azure Spot-instans | Välj **Nej** |
    | Storlek | Välj storlek på virtuell dator eller Ställ in standardinställningen |
    | **Administratörs konto** |  |
    | Användarnamn | Ange ett användar namn |
    | Lösenord | Ange ett lösen ord |
    | Bekräfta lösenordet | Ange lösenordet igen |
    | **Regler för inkommande portar** |  |
    | Offentliga inkommande portar | Välj **ingen** |

3. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
4. På fliken nätverk väljer eller anger du:

    | Inställning | Värde |
    |-|-|
    | **Nätverksgränssnitt** |  |
    | Virtuellt nätverk | **myVNet** |
    | Undernät | **myBackendSubnet** |
    | Offentlig IP-adress | Välj **Ingen**. |
    | Nätverks säkerhets grupp för nätverkskort | Välj **Avancerat**|
    | Konfigurera nätverks säkerhets grupp | Välj **Skapa ny**. </br> I **gruppen Skapa nätverks säkerhet** anger du **myNSG** i **namn**. </br> Under **regler för inkommande** trafik väljer du **+ Lägg till en regel för inkommande trafik**. </br> Under  **mål ports intervall** anger du **80**. </br> Under **prioritet** anger du **100**. </br> I **namn** anger du **myHTTPRule** </br> Välj **Lägg till** </br> Välj **OK** |
    | **Belastningsutjämning**  |
    | Placera den här virtuella datorn bakom en befintlig belastnings Utjämnings lösning? | Välj **Ja** |
    | **Inställningar för belastnings utjämning** |
    | Alternativ för belastnings utjämning | Välj **Azure Load Balancing** |
    | Välj en belastningsutjämnare | Välj **myLoadBalancer**  |
    | Välj en backend-pool | Välj **myBackendPool** |

5. Välj fliken **Hantering** eller **Nästa** > **Hantering**.

6. På fliken **hantering** väljer eller anger du:
    
    | Inställning | Värde |
    |-|-|
    | **Övervakning** |  |
    | Startdiagnostik | Välj **av** |
   
7. Välj **Granska + skapa**. 
  
8. Granska inställningarna och välj sedan **Skapa**.

9. Följ steg 1 till 8 för att skapa två ytterligare virtuella datorer med följande värden och alla andra inställningar på samma sätt som **myVM1**:

    | Inställning | VM 2| VM 3|
    | ------- | ----- |---|
    | Namn |  **myVM2** |**myVM3**|
    | Tillgänglighetszon | **2** |**3**|
    | Nätverkssäkerhetsgrupp | Välj den befintliga **myNSG**| Välj den befintliga **myNSG**|

## <a name="create-outbound-rule-configuration"></a>Skapa utgående regel konfiguration
Utgående regler för belastningsutjämnare konfigurerar utgående SNAT för virtuella datorer i backend-poolen. 

Mer information om utgående anslutningar finns i [utgående anslutningar i Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-rule"></a>Skapa utgående regel

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer** i listan resurser.

2. Välj **utgående regler** under **Inställningar** och välj sedan **Lägg till**.

3. Använd de här värdena för att konfigurera de utgående reglerna:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myOutboundRule**. |
    | IP-adress för klient del | Välj **Skapa ny**. </br> I **namn** anger du **LoadBalancerFrontEndOutbound**. </br> Välj **IP-adress** eller **IP-prefix**. </br> Välj **Skapa ny** under **offentlig IP-adress** eller **offentlig IP-prefix**. </br> Som namn anger du  **myPublicIPOutbound** eller **myPublicIPPrefixOutbound**. </br> Välj **Lägg till**.|
    | Tids gräns för inaktivitet (minuter) | Flytta skjutreglaget till **15 minuter**.|
    | TCP-återställning | Välj **Aktiverad**.|
    | Serverdelspool | Välj **Skapa ny**. </br> Ange **myBackendPoolOutbound** i **namn**. </br> Välj **Lägg till**. |
    | Port tilldelning – > port tilldelning | Välj **manuellt Välj antalet utgående portar** |
    | Utgående portar-> Välj efter | Välj **portar per instans** |
    | Utgående portar-> portar per instans | Ange **10000**. |

4. Välj **Lägg till**.

### <a name="add-virtual-machines-to-outbound-pool"></a>Lägg till virtuella datorer i utgående pool

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer** i listan resurser.

2. Under **Inställningar** väljer du **backend-pooler**.

3. Välj **myBackendPoolOutbound**.

4. I **virtuellt nätverk** väljer du **myVNet**.

5. I **virtuella datorer** väljer du **+ Lägg till**.

6. Markera kryss rutorna bredvid **myVM1**, **myVM2** och **myVM3**. 

7. Välj **Lägg till**.

8. Välj **Spara**.

# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Standard-SKU-belastningsutjämnare rekommenderas för produktions arbets belastningar.  Mer information om SKU: er finns i **[Azure Load Balancer SKU: er](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="Grundläggande belastnings Utjämnings resurser skapade i snabb starten." border="false":::

*Bild: resurser som skapats i snabb start.*

I det här avsnittet skapar du en belastningsutjämnare som laddar upp virtuella datorer. 

När du skapar en offentlig belastningsutjämnare, skapar du en ny offentlig IP-adress som är konfigurerad som klient del (kallas som **LoadBalancerFrontend** som standard) för belastningsutjämnaren.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Load Balancer**.

2. På fliken **grundläggande** på sidan **skapa belastnings utjämning** anger eller väljer du följande information: 

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och skriv **CreatePubLBQS-RG** i text rutan.|
    | Namn                   | Ange **myLoadBalancer**                                   |
    | Region         | Välj **Europa, västra**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Välj **grundläggande** |
    | Offentlig IP-adress | Välj **Skapa ny**. Om du har en befintlig offentlig IP-adress som du vill använda väljer du **Använd befintlig**. |
    | Namn på offentlig IP-adress | Skriv **myPublicIP** i textrutan.|
    | Tilldelning | Välj **dynamisk** |
    | Lägg till en offentlig IPv6-adress | Välj **Nej**. </br> Mer information om IPv6-adresser och belastningsutjämnare finns i [Vad är IPv6 för Azure Virtual Network?](../virtual-network/ipv6-overview.md)  |

3. Acceptera standardinställningarna för återstående inställningar och välj sedan **Granska + skapa**.

4. På fliken **Granska och skapa** väljer du **skapa**.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="Skapa en grundläggande belastningsutjämnare" border="true":::

## <a name="create-load-balancer-resources"></a>Skapa resurser för lastbalansering

I det här avsnittet konfigurerar du:

* Skapa ett virtuellt nätverk.
* Inställningar för belastnings utjämning för en backend-adresspool.
* En hälso avsökning.
* En belastnings Utjämnings regel.

## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät.

1. Välj **Skapa en resurs > Nätverk > Virtuellt nätverk** eller sök efter **virtuellt nätverk** i sökrutan på den övre vänstra sidan på skärmen.

2. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **CreatePubLBQS-RG** |
    | **Instansinformation** |                                                                 |
    | Name             | Ange **myVNet**                                    |
    | Region           | Välj **Europa, västra** |

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
### <a name="create-a-backend-pool"></a>Skapa en serverdelspool

En backend-adresspool innehåller IP-adresserna för de virtuella nätverkskorten som är anslutna till belastningsutjämnaren. 

Skapa **myBackendPool** för backend-adresspoolen för att inkludera virtuella datorer för belastnings utjämning av Internet trafik.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer** i listan resurser.

2. Under **Inställningar** väljer du **backend-pooler** och väljer sedan **Lägg till**.

3. På sidan **Lägg till en server dels grupp** anger eller väljer du:
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myBackendPool**. |
    | Virtuellt nätverk | Välj **myVNet**. |
    | Associerad till | Välj **virtuella datorer** |

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Belastningsutjämnaren övervakar appens status med en hälso avsökning. 

Hälso avsökningen lägger till eller tar bort virtuella datorer från belastningsutjämnaren baserat på deras svar på hälso kontroller. 

Skapa en hälsoavsökning med namnet **myHealthProbe** så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer** i listan resurser.

2. Välj **hälso avsökningar** under **Inställningar** och välj sedan **Lägg till**.
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myHealthProbe**. |
    | Protokoll | Välj **http**. |
    | Port | Ange **80**.|
    | Sökväg | Går **/** |
    | Intervall | Ange **15** som **intervall** i sekunder mellan avsöknings försök. |
    | Tröskelvärde för ej felfri | Välj **2** för antalet fel i **tröskeln** eller på varandra följande avsöknings fel som måste inträffa innan en virtuell dator betraktas som ohälsosam.|

3. Välj **OK**.

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar klient delens IP-konfiguration för den inkommande trafiken och Server delens IP-pool för att ta emot trafiken. Käll-och mål porten definieras i regeln. 

I det här avsnittet ska du skapa en belastnings Utjämnings regel:

* Med namnet **myHTTPRule**.
* I klient delen med namnet **LoadBalancerFrontEnd**.
* Lyssnar på **Port 80**.
* Dirigerar belastnings bal anse rad trafik till Server delen med namnet **myBackendPool** på **port 80**.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer** i listan resurser.

2. Välj **belastnings Utjämnings regler** under **Inställningar** och välj sedan **Lägg till**.

3. Använd de här värdena för att konfigurera belastnings Utjämnings regeln:
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myHTTPRule**. |
    | IP-version | Välj **IPv4** |
    | IP-adress för klient del | Välj **LoadBalancerFrontEnd** |
    | Protokoll | Välj **TCP**. |
    | Port | Ange **80**.|
    | Serverdelsport | Ange **80**. |
    | Serverdelspool | Välj **myBackendPool**.|
    | Hälsoavsökning | Välj **myHealthProbe**. |
    | Tids gräns för inaktivitet (minuter) | Flytta skjutreglaget till **15** minuter. |
 
4. Lämna resten av standardinställningarna och välj sedan **OK**.

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet får du:

* Skapa tre virtuella datorer för belastningsutjämnarens backend-pool.
* Skapa en tillgänglighets uppsättning för de virtuella datorerna.
* Installera IIS på de virtuella datorerna för att testa belastningsutjämnaren.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här avsnittet skapar du tre virtuella datorer (**myVM1**, **myVM2** och **myVM3**) med en grundläggande offentlig IP-adress.  

De tre virtuella datorerna läggs till i en tillgänglighets uppsättning med namnet **myAvailabilitySet**.

De här virtuella datorerna läggs till i backend-poolen för belastningsutjämnaren som skapades tidigare.

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **CreatePubLBQS-RG** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVM1** |
    | Region | Välj **Europa, västra** |
    | Tillgänglighets alternativ | Välj **Tillgänglighetsuppsättning** |
    | Tillgänglighetsuppsättning | Välj **Skapa ny**. </br> Ange **myAvailabilitySet** i **namn**. </br> Välj **OK** |
    | Bild | **Windows Server 2019 Datacenter** |
    | Azure Spot-instans | Välj **Nej** |
    | Storlek | Välj storlek på virtuell dator eller Ställ in standardinställningen |
    | **Administratörs konto** |  |
    | Användarnamn | Ange ett användar namn |
    | Lösenord | Ange ett lösen ord |
    | Bekräfta lösenordet | Ange lösenordet igen |

3. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
4. På fliken nätverk väljer eller anger du:

    | Inställning | Värde |
    |-|-|
    | **Nätverksgränssnitt** |  |
    | Virtuellt nätverk | Välj **myVNet** |
    | Undernät | Välj **myBackendSubnet** |
    | Offentlig IP-adress | Välj **ingen** |
    | Nätverks säkerhets grupp för nätverkskort | Välj **Avancerat**|
    | Konfigurera nätverks säkerhets grupp | Välj **Skapa ny**. </br> I **gruppen Skapa nätverks säkerhet** anger du **myNSG** i **namn**. </br> Under **regler för inkommande** trafik väljer du **+ Lägg till en regel för inkommande trafik**. </br> Under  **mål ports intervall** anger du **80**. </br> Under **prioritet** anger du **100**. </br> I **namn** anger du **myHTTPRule** </br> Välj **Lägg till** </br> Välj **OK** |
    | **Belastningsutjämning**  |
    | Placera den här virtuella datorn bakom en befintlig belastnings Utjämnings lösning? | Välj **Nej** |
 
5. Välj fliken **Hantering** eller **Nästa** > **Hantering**.

6. På fliken **hantering** väljer eller anger du:
    
    | Inställning | Värde |
    |---|---|
    | **Övervakning** | |
    | Startdiagnostik | Välj **av** |

7. Välj **Granska + skapa**. 
  
8. Granska inställningarna och välj sedan **Skapa**.

9. Följ steg 1 till 8 för att skapa två ytterligare virtuella datorer med följande värden och alla andra inställningar på samma sätt som **myVM1**:

    | Inställning | VM 2| VM 3|
    | ------- | ----- |---|
    | Namn |  **myVM2** |**myVM3**|
    | Tillgänglighetsuppsättning| Välj **myAvailabilitySet** | Välj **myAvailabilitySet**|
    | Nätverkssäkerhetsgrupp | Välj den befintliga **myNSG**| Välj den befintliga **myNSG**|

### <a name="add-virtual-machines-to-the-backend-pool"></a>Lägg till virtuella datorer i backend-poolen

De virtuella datorerna som skapades i föregående steg måste läggas till i backend-poolen för **myLoadBalancer**.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer** i listan resurser.

2. Under **Inställningar** väljer du **Server dels pooler** och väljer sedan **myBackendPool**.

3. Välj **virtuella datorer** i **kopplade till**.

4. I avsnittet **virtuella datorer** väljer du **+ Lägg till**.

5. Markera rutorna bredvid **myVM1**, **myVM2** och **myVM3**.

6. Välj **Lägg till**.

7. Välj **Spara**.

---

## <a name="install-iis"></a>Installera IIS

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj **myVM1** i resurs gruppen **CreatePubLBQS-RG** i resurs listan.

2. På sidan **Översikt** väljer du **Anslut** och sedan **skydds**.

4. Ange det användar namn och lösen ord som angavs när den virtuella datorn skapades.

5. Välj **Anslut**.

6. Gå till **Windows administrations verktyg**  >  **Windows PowerShell** på server Skriv bordet.

7. Kör följande kommandon i PowerShell-fönstret för att:

    * Installera IIS-servern
    * Ta bort standard iisstart.htms filen
    * Lägg till en ny iisstart.htm-fil som visar namnet på den virtuella datorn:

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Stäng skydds-sessionen med **myVM1**.

9. Upprepa steg 1 till 6 för att installera IIS och den uppdaterade filen iisstart.htm på **myVM2** och **myVM3**.

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

1. Hitta den offentliga IP-adressen för belastningsutjämnaren på **översikts** skärmen. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

   ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Om du vill se belastningsutjämnaren distribuerar trafik över alla tre virtuella datorer kan du anpassa standard sidan för varje virtuell dators IIS-webbserver och sedan framtvinga en uppdatering av webbläsaren från klient datorn.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen, belastningsutjämnaren och alla relaterade resurser när de inte längre behövs. Det gör du genom att markera resurs gruppen **CreatePubLBQS-RG** som innehåller resurserna och sedan välja **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten kommer du att göra följande:

* Skapat en Azure standard-eller Basic-Load Balancer
* Anslutna tre virtuella datorer till belastningsutjämnaren.
* Konfigurerat trafik regel för belastnings utjämning, hälso avsökning och testar sedan belastningsutjämnaren. 

Om du vill veta mer om Azure Load Balancer fortsätter du till:
> [!div class="nextstepaction"]
> [Vad är Azure Load Balancer?](load-balancer-overview.md)