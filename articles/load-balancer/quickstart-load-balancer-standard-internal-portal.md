---
title: 'Snabb start: skapa en intern belastningsutjämnare – Azure Portal'
titleSuffix: Azure Load Balancer
description: Den här snabb starten visar hur du skapar en intern belastningsutjämnare med hjälp av Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a internal load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f70116847a8743cf8b3cb56ff35f9d913f13f359
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562360"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Snabb start: skapa en intern belastningsutjämnare för att belastningsutjämna virtuella datorer med hjälp av Azure Portal

Kom igång med Azure Load Balancer genom att använda Azure Portal för att skapa en intern belastningsutjämnare och tre virtuella datorer.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>Standard-SKU-belastningsutjämnare rekommenderas för produktions arbets belastningar.  Mer information om SKU: er finns i **[Azure Load Balancer SKU: er](skus.md)**.

I det här avsnittet skapar du en belastningsutjämnare som laddar upp virtuella datorer. 

När du skapar en intern belastningsutjämnare konfigureras ett virtuellt nätverk som nätverk för belastningsutjämnaren. 

Följande diagram visar de resurser som skapats i den här snabb starten:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Standard belastnings Utjämnings resurser har skapats för snabb start." border="false":::

En privat IP-adress i det virtuella nätverket har kon figurer ATS som klient del (kallas som **LoadBalancerFrontend** som standard) för belastningsutjämnaren. 

Klient delens IP-adress kan vara **statisk** eller **dynamisk**.

## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät.

1. Välj **Skapa en resurs > Nätverk > Virtuellt nätverk** eller sök efter **virtuellt nätverk** i sökrutan på den övre vänstra sidan på skärmen.

2. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **CreateIntLBQS-RG** |
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

## <a name="create-load-balancer"></a>Skapa en lastbalanserare

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Load Balancer**.

2. På fliken **grundläggande** på sidan **skapa belastnings utjämning** anger eller väljer du följande information: 

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **CreateIntLBQS-RG** som du skapade i föregående steg.|
    | Name                   | Ange **myLoadBalancer**                                   |
    | Region         | Välj **Europa, västra**.                                        |
    | Typ          | Välj **Intern**.                                        |
    | SKU           | Välj **standard** |
    | Virtuellt nätverk | Välj **myVNet** som skapades i föregående steg. |
    | Undernät  | Välj **myBackendSubnet** som skapades i föregående steg. |
    | Tilldelning av IP-adress | Välj **Dynamisk**. |
    | Tillgänglighetszon | Välj **zon – redundant** |

3. Acceptera standardinställningarna för återstående inställningar och välj sedan **Granska + skapa**.

4. På fliken **Granska och skapa** väljer du **skapa**.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="Skapa en intern standard belastnings utjämning." border="true":::
 
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
    
4. Lämna resten av standardinställningarna och välj sedan **OK**.

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet får du:

* Skapa tre virtuella datorer för belastningsutjämnarens backend-pool.
* Installera IIS på de virtuella datorerna för att testa belastningsutjämnaren.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här avsnittet skapar du tre virtuella datorer (**myVM1**, **myVM2** och **myVM3**).

De här virtuella datorerna läggs till i backend-poolen för belastningsutjämnaren som skapades tidigare.

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **CreateIntLBQS-RG** |
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

3. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
4. På fliken nätverk väljer eller anger du:

    | Inställning | Värde |
    |-|-|
    | **Nätverksgränssnitt** |  |
    | Virtuellt nätverk | **myVNet** |
    | Undernät | **myBackendSubnet** |
    | Offentlig IP-adress | Välj **ingen** |
    | Nätverks säkerhets grupp för nätverkskort | Välj **Avancerat**|
    | Konfigurera nätverks säkerhets grupp | Välj **Skapa ny**. </br> I **gruppen Skapa nätverks säkerhet** anger du **myNSG** i **namn**. </br> Välj **OK** |
    | **Belastningsutjämning**  |
    | Placera den här virtuella datorn bakom en befintlig belastnings Utjämnings lösning? | Välj **Ja** |
    | **Inställningar för belastnings utjämning** |
    | Alternativ för belastnings utjämning | Välj **Azure Load Balancing** |
    | Välj en belastningsutjämnare | Välj **myLoadBalancer**  |
    | Välj en backend-pool | Välj **myBackendPool** |
   
5. Välj **Granska + skapa**. 
  
6. Granska inställningarna och välj sedan **Skapa**.

7. Följ steg 1 till 8 för att skapa två fler virtuella datorer med följande värden och alla andra inställningar på samma sätt som **myVM1**:

    | Inställning | VM 2 | VM 3 |
    | ------- | ----- | ---- |
    | Name |  **myVM2** | **myVM3** |
    | Tillgänglighetszon | **2** | **3** |
    | Nätverkssäkerhetsgrupp | Välj den befintliga **myNSG**| Välj den befintliga **myNSG** |


# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-1-create-internal-load-balancer-basic)

>[!NOTE]
>Standard-SKU-belastningsutjämnare rekommenderas för produktions arbets belastningar.  Mer information om SKU: er finns i **[Azure Load Balancer SKU: er](skus.md)**.

I det här avsnittet skapar du en belastningsutjämnare som laddar upp virtuella datorer. 

När du skapar en intern belastningsutjämnare konfigureras ett virtuellt nätverk som nätverk för belastningsutjämnaren. 

Följande diagram visar de resurser som skapats i den här snabb starten:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Grundläggande belastnings Utjämnings resurser skapade i snabb starten." border="false":::

En privat IP-adress i det virtuella nätverket har kon figurer ATS som klient del (kallas som **LoadBalancerFrontend** som standard) för belastningsutjämnaren. 

Klient delens IP-adress kan vara **statisk** eller **dynamisk**.

## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät.

1. Välj **Skapa en resurs > Nätverk > Virtuellt nätverk** eller sök efter **virtuellt nätverk** i sökrutan på den övre vänstra sidan på skärmen.

2. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **CreateIntLBQS-RG** |
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

## <a name="create-load-balancer"></a>Skapa en lastbalanserare

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Load Balancer**.

2. På fliken **grundläggande** på sidan **skapa belastnings utjämning** anger eller väljer du följande information: 

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **CreateIntLBQS-RG** som du skapade i föregående steg.|
    | Name                   | Ange **myLoadBalancer**                                   |
    | Region         | Välj **Europa, västra**.                                        |
    | Typ          | Välj **Intern**.                                        |
    | SKU           | Välj **grundläggande** |
    | Virtuellt nätverk | Välj **myVNet** som skapades i föregående steg. |
    | Undernät  | Välj **myBackendSubnet** som skapades i föregående steg. |
    | Tilldelning av IP-adress | Välj **Dynamisk**. |

3. Acceptera standardinställningarna för återstående inställningar och välj sedan **Granska + skapa**.

4. På fliken **Granska och skapa** väljer du **skapa**.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="Skapa en Basic-intern belastningsutjämnare." border="true":::

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

3. På sidan **Lägg till en server dels grupp** anger eller väljer du:
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myBackendPool**. |
    | Virtuellt nätverk | Välj **myVNet**. |
    | Associerad till | Välj **virtuella datorer** |

4. Välj **Lägg till**.

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

I det här avsnittet skapar du tre virtuella datorer (**myVM1**, **myVM2**, **myVM3**).

De två virtuella datorerna läggs till i en tillgänglighets uppsättning med namnet **myAvailabilitySet**.

De här virtuella datorerna läggs till i backend-poolen för belastningsutjämnaren som skapades tidigare.

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **CreateIntLBQS-RG** |
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
    | Konfigurera nätverks säkerhets grupp | Välj **Skapa ny**. </br> I **gruppen Skapa nätverks säkerhet** anger du **myNSG** i **namn**. </br> Välj **OK** |
    | **Belastningsutjämning**  |
    | Placera den här virtuella datorn bakom en befintlig belastnings Utjämnings lösning? | Välj **Nej** |

5. Välj **Granska + skapa**. 
  
6. Granska inställningarna och välj sedan **Skapa**.

7. Följ steg 1 till 8 för att skapa två fler virtuella datorer med följande värden och alla andra inställningar på samma sätt som **myVM1**:

    | Inställning | VM 2 | VM 3 |
    | ------- | ----- | ---- |
    | Name |  **myVM2** | **myVM3** |
    | Tillgänglighetsuppsättning | Välj **myAvailabilitySet** | Välj **myAvailabilitySet** |
    | Nätverkssäkerhetsgrupp | Välj den befintliga **myNSG** | Välj den befintliga **myNSG** |

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

## <a name="create-test-virtual-machine"></a>Skapa virtuell test dator

I det här avsnittet ska du skapa en virtuell dator med namnet **myTestVM**.  Den här virtuella datorn kommer att användas för att testa belastnings Utjämnings konfigurationen.

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **CreateIntLBQS-RG** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myTestVM** |
    | Region | Välj **Europa, västra** |
    | Tillgänglighets alternativ | Välj **ingen redundans för infrastruktur krävs** |
    | Bild | Välj **Windows Server 2019 Data Center** |
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
    | Virtuellt nätverk | **myVNet** |
    | Undernät | **myBackendSubnet** |
    | Offentlig IP-adress | Välj **Ingen**. |
    | Nätverks säkerhets grupp för nätverkskort | Välj **Avancerat**|
    | Konfigurera nätverks säkerhets grupp | Välj **MyNSG** som skapades i föregående steg.|
       
5. Välj **Granska + skapa**. 
  
6. Granska inställningarna och välj sedan **Skapa**.

## <a name="install-iis"></a>Installera IIS

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj **myVM1** i resurs gruppen **CreateIntLBQS-RG** i resurs listan.

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

1. Hitta den privata IP-adressen för belastningsutjämnaren på **översikts** skärmen. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer**.

2. Anteckna eller kopiera adressen bredvid **privat IP-adress** i **översikten** över **myLoadBalancer**.

3. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj **myTestVM** i resurs gruppen **CreateIntLBQS-RG** i resurs listan.

4. På sidan **Översikt** väljer du **Anslut** och sedan **skydds**.

6. Ange det användar namn och lösen ord som angavs när den virtuella datorn skapades.

7. Öppna **Internet Explorer** på **myTestVM**.

8. Ange IP-adressen från föregående steg i adress fältet i webbläsaren. IIS-webbserverns standardsida visas i webbläsaren.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Skärm bild som visar ett webbläsarfönster som visar standard sidan som förväntat." border="true":::
   
Om du vill se belastningsutjämnaren distribuerar trafik över båda virtuella datorerna kan du anpassa standard sidan för varje virtuell dators IIS-webbserver och sedan framtvinga en uppdatering av webbläsaren från klient datorn.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, lastbalanseraren och alla relaterade resurser när de inte längre behövs. Det gör du genom att markera resurs gruppen **CreateIntLBQS-RG** som innehåller resurserna och sedan välja **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten kommer du att göra följande:

* Skapade en intern belastningsutjämnare för Azure standard eller Basic
* Anslutna tre virtuella datorer till belastningsutjämnaren.
* Konfigurerat trafik regel för belastnings utjämning, hälso avsökning och testar sedan belastningsutjämnaren. 

Om du vill veta mer om Azure Load Balancer fortsätter du till:
> [!div class="nextstepaction"]
> [Vad är Azure Load Balancer?](load-balancer-overview.md)