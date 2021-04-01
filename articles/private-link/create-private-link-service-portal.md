---
title: Snabb start – skapa en privat länk-tjänst med hjälp av Azure Portal
titlesuffix: Azure Private Link
description: Lär dig hur du skapar en privat länk-tjänst med hjälp av Azure Portal i den här snabb starten
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/18/2021
ms.author: allensu
ms.openlocfilehash: d394a475c5121607f70c03437382e104a5d0cbee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746415"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Snabb start: skapa en privat länk-tjänst med hjälp av Azure Portal

Kom igång med att skapa en privat länk-tjänst som refererar till din tjänst.  Ge privat länk åtkomst till din tjänst eller resurs som distribueras bakom en Azure-Standard Load Balancer.  Användare av tjänsten har privat åtkomst från det virtuella nätverket.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Skapa en intern lastbalanserare

I det här avsnittet ska du skapa ett virtuellt nätverk och ett internt Azure Load Balancer.

### <a name="virtual-network"></a>Virtuellt nätverk

I det här avsnittet skapar du ett virtuellt nätverk och ett undernät som är värd för belastningsutjämnaren som har åtkomst till din privata länk tjänst.

1. Välj **Skapa en resurs > Nätverk > Virtuellt nätverk** eller sök efter **virtuellt nätverk** i sökrutan på den övre vänstra sidan på skärmen.

2. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **CreatePrivLinkService-RG** |
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
    | Namn på undernät | Ange **undernät** |
    | Adressintervall för undernätet | Ange **10.1.0.0/24** |

7. Välj **Spara**.

8. Välj fliken **Granska + skapa** eller Välj knappen **Granska + skapa** .

9. Välj **Skapa**.

### <a name="create-a-standard-load-balancer"></a>Skapa en standardlastbalanserare

Använd portalen för att skapa en standard intern belastningsutjämnare. 

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Load Balancer**.

2. På fliken **grundläggande** på sidan **skapa belastnings utjämning** anger eller väljer du följande information: 

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **CreatePrivLinkService-RG** som du skapade i föregående steg.|
    | Name                   | Ange **myLoadBalancer**                                   |
    | Region         | Välj **USA, östra 2**.                                        |
    | Typ          | Välj **Intern**.                                        |
    | SKU           | Välj **standard** |
    | Virtuellt nätverk | Välj **myVNet** som skapades i föregående steg. |
    | Undernät  | Välj det **undernät** som skapades i föregående steg. |
    | Tilldelning av IP-adress | Välj **Dynamisk**. |
    | Tillgänglighetszon | Välj **zon – redundant** |

3. Acceptera standardinställningarna för återstående inställningar och välj sedan **Granska + skapa**.

4. På fliken **Granska och skapa** väljer du **skapa**.   

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
    | Protokoll | Välj **TCP**. |
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

## <a name="create-a-private-link-service"></a>Skapa en privat länk-tjänst

I det här avsnittet ska du skapa en privat länk-tjänst bakom en standard belastningsutjämnare.

1. I den övre vänstra delen av sidan i Azure Portal väljer du **skapa en resurs**.

2. Sök efter **privat länk** i rutan **Sök på Marketplace** .

3. Välj **Skapa**.

4. I **Översikt** under **Private Link Center** väljer du knappen blå **skapa privat länk tjänst** .

5. På fliken **grundläggande** under **skapa privat länk tjänst** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** |  |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **CreatePrivLinkService-RG**. |
    | **Instansinformation** |  |
    | Name | Ange **myPrivateLinkService**. |
    | Region | Välj **USA, östra 2**. |

6. Välj fliken **utgående inställningar** eller Välj **Nästa: utgående inställningar** längst ned på sidan.

7. På fliken **utgående inställningar** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Lastbalanserare | Välj **myLoadBalancer**. |
    | IP-adress för belastnings Utjämnings klient | Välj **LoadBalancerFrontEnd (10.1.0.4)**. |
    | Käll-NAT-undernät | Välj **undernät (10.1.0.0/24)**. |
    | Aktivera TCP-proxy v2 | Lämna standardvärdet **Nej**. </br> Om ditt program förväntar sig ett TCP proxy v2-huvud väljer du **Ja**. |
    | **Inställningar för privata IP-adresser** |  |
    | Lämna standardinställningarna |  |

8. Välj fliken **åtkomst säkerhet** eller Välj **Nästa: åtkomst säkerhet** längst ned på sidan.

9. Lämna standardinställningen **endast rollbaserad åtkomst kontroll** på fliken **åtkomst säkerhet** .

10. Välj fliken **taggar** eller Välj **Nästa: Taggar** längst ned på sidan.

11. Välj fliken **Granska + skapa** eller Välj **Nästa: granska + skapa** längst ned på sidan.

12. Välj **skapa** på fliken **Granska + skapa** .

Din privata länk tjänst skapas och kan ta emot trafik. Om du vill se trafikflöden konfigurerar du ditt program bakom din standard belastningsutjämnare.


## <a name="create-private-endpoint"></a>Skapa privat slut punkt

I det här avsnittet ska du mappa den privata länk tjänsten till en privat slut punkt. Ett virtuellt nätverk innehåller den privata slut punkten för den privata länk tjänsten. Det här virtuella nätverket innehåller de resurser som kommer att ha åtkomst till din privata länk tjänst.

### <a name="create-private-endpoint-virtual-network"></a>Skapa privat slut punkt för virtuellt nätverk

1. Välj **Skapa en resurs > Nätverk > Virtuellt nätverk** eller sök efter **virtuellt nätverk** i sökrutan på den övre vänstra sidan på skärmen.

2. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **CreatePrivLinkService-RG** |
    | **Instansinformation** |                                                                 |
    | Name             | Ange **myVNetPE**                                    |
    | Region           | Välj **USA, östra 2** |

3. Välj fliken **IP-adresser** eller Välj **Nästa: knappen Nästa: IP-adress** längst ned på sidan.

4. På fliken **IP-adresser** anger du den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | IPv4-adressutrymme | Ange **11.1.0.0/16** |

5. Under **under näts namn** väljer du ordet **standard**.

6. I **Redigera undernät** anger du den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | Namn på undernät | Ange **mySubnetPE** |
    | Adressintervall för undernätet | Ange **11.1.0.0/24** |

7. Välj **Spara**.

8. Välj fliken **Granska + skapa** eller Välj knappen **Granska + skapa** .

9. Välj **Skapa**.

### <a name="create-private-endpoint"></a>Skapa privat slut punkt

1. På den övre vänstra sidan av skärmen i portalen väljer du **skapa en resurs**  >  **nätverk**  >  **privat länk** eller i rutan Sök anger du **privat länk**.

2. Välj **Skapa**.

3. I **Private Link Center** väljer du **privata slut punkter** i den vänstra menyn.

4. I **privata slut punkter** väljer du **+ Lägg till**.

5. På fliken **grundläggande** i **skapa en privat slut punkt** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **CreatePrivLinkService-RG**. Du har skapat den här resurs gruppen i föregående avsnitt.|
    | **Instansinformation** |  |
    | Name  | Ange **myPrivateEndpoint**. |
    | Region | Välj **USA, östra 2**. |

6. Välj fliken **resurs** eller **Nästa: resurs** längst ned på sidan.
    
7. I **resurs** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Anslutningsmetod | Välj **Anslut till en Azure-resurs i min katalog**. |
    | Prenumeration | Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. Network/privateLinkServices**. |
    | Resurs | Välj **myPrivateLinkService**. |

8. Välj fliken **konfiguration** eller **Nästa: konfigurations** knappen längst ned på skärmen.

9. I **konfiguration** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Nätverk** |  |
    | Virtual Network | Välj **myVNetPE**. |
    | Undernät | Välj **mySubnetPE**. |

10. Välj fliken **Granska + skapa** eller knappen **Granska + skapa** längst ned på skärmen.

11. Välj **Skapa**.

### <a name="ip-address-of-private-endpoint"></a>IP-adress för privat slut punkt

I det här avsnittet hittar du IP-adressen för den privata slut punkt som motsvarar belastningsutjämnaren och tjänsten privata länkar.

1. I kolumnen till vänster i Azure Portal väljer du **resurs grupper**.

2. Välj resurs gruppen **CreatePrivLinkService-RG** .

3. I resurs gruppen **CreatePrivLinkService-RG** väljer du **myPrivateEndpoint**.

4. På sidan **Översikt** i **myPrivateEndpoint** väljer du namnet på det nätverks gränssnitt som är associerat med den privata slut punkten.  Nätverks gränssnittets namn börjar med **myPrivateEndpoint. NIC**.

5. På sidan **Översikt** i nätverkskortet för privat slut punkt visas IP-adressen för slut punkten i den **privata IP-adressen**.
    

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den privata länk tjänsten tar du bort resurs gruppen för att rensa resurserna som används i den här snabb starten.

1. Ange **CreatePrivLinkService-RG** i rutan Sök högst upp i portalen och välj **CreatePrivLinkService-RG** från Sök resultaten.
1. Välj **Ta bort resursgrupp**.
1. I **Skriv resurs gruppens namn**, anger du **CreatePrivLinkService-RG**.
1. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten kommer du att göra följande:

* Skapade ett virtuellt nätverk och internt Azure Load Balancer.
* En privat länk tjänst har skapats.
* Ett virtuellt nätverk och en privat slut punkt har skapats för den privata länk tjänsten.

Om du vill veta mer om den privata Azure-slutpunkten fortsätter du till:
> [!div class="nextstepaction"]
> [Snabb start: skapa en privat slut punkt med hjälp av Azure Portal](create-private-endpoint-portal.md)