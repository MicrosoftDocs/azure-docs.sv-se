---
title: 'Självstudie: integrera NAT-gateway med en offentlig belastningsutjämnare – Azure Portal'
titleSuffix: Virtual Network NAT
description: I den här självstudien lär du dig att integrera en Virtual Network NAT-gateway med en offentlig belastningsutjämnare med hjälp av Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: 8382dd10536a8c0475444d0cdff30340ad124e9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722811"
---
# <a name="tutorial-integrate-a-nat-gateway-with-a-public-load-balancer-using-the-azure-portal"></a>Självstudie: integrera en NAT-gateway med en offentlig belastningsutjämnare med hjälp av Azure Portal

I den här självstudien får du lära dig hur du integrerar en NAT-gateway med en offentlig belastningsutjämnare.

Som standard är en Azure-Standard Load Balancer säker. Utgående anslutning definieras explicit genom att aktivera utgående SNAT (käll nätverks adress översättning). SNAT är aktiverat i en belastnings Utjämnings regel eller utgående regler. 

NAT gateway-integreringen ersätter behovet av utgående regler för utgående SNAT-pool i Server delen. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Azure Load Balancer
> * Skapa två virtuella datorer för backend-poolen för Azure Load Balancer
> * Skapa en NAT-gateway
> * Verifiera utgående anslutning för de virtuella datorerna i backend-poolen för belastnings utjämning

## <a name="prerequisites"></a>Förutsättningar

Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-load-balancer"></a>Skapa en lastbalanserare

I det här avsnittet ska du skapa en standard Azure Load Balancer. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Skapa en resurs**. 
3. I rutan Sök anger du **Load Balancer**. Välj **Load Balancer** i Sök resultaten.
4. På sidan **belastnings utjämning** väljer du **skapa**.
5. På sidan **skapa belastnings utjämning** anger eller väljer du följande information: 

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | **Projektinformation** |   |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och ange **TutorPubLBNAT-RG** i text rutan. </br> Välj **OK**.|
    | **Instansinformation** |   |
    | Name                   | Ange **myLoadBalancer**                                   |
    | Region         | Välj **(US) USA, östra**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Lämna kvar standardinställningen **Standard**. |
    | Nivå          | Lämna standard **regionen**. |
    | **Offentlig IP-adress** |   |
    | Offentlig IP-adress | Välj **Skapa ny**. </br> Om du har en befintlig offentlig IP-adress som du vill använda väljer du **Använd befintlig**. |
    | Namn på offentlig IP-adress | Ange **myPublicIP – lb** i text rutan.|
    | Tillgänglighetszon | Välj **zon-redundant** för att skapa en elastisk belastningsutjämnare. Om du vill skapa en zonindelade-belastningsutjämnare väljer du en speciell zon från 1, 2 eller 3 |
    | Lägg till en offentlig IPv6-adress | Välj **Nej**. </br> Mer information om IPv6-adresser och belastningsutjämnare finns i [Vad är IPv6 för Azure Virtual Network?](../virtual-network/ipv6-overview.md)  |
    | Routningsprioritet | Lämna standardvärdet för **Microsoft-nätverk**. </br> Mer information om inställningar för routning finns i [Vad är cirkulations inställningar (för hands version)?](../virtual-network/routing-preference-overview.md). |

6. Acceptera standardinställningarna för återstående inställningar och välj sedan **Granska + skapa**.

7. På fliken **Granska och skapa** väljer du **skapa**.

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
   

3. Lämna resten av standardinställningarna och välj **Lägg till**.

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
    | Tids gräns för inaktivitet (minuter) | Ange **15** minuter. |
    | TCP-återställning | Välj **Aktiverad**. |
    | Utgående käll Network Address Translation (SNAT) | Välj **(rekommenderas) Använd utgående regler för att ge backend-poolens medlemmar åtkomst till Internet.** |

4. Lämna resten av standardinställningarna och välj sedan **OK**.

## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät.

1. Välj **Skapa en resurs > Nätverk > Virtuellt nätverk** eller sök efter **virtuellt nätverk** i sökrutan på den övre vänstra sidan på skärmen.

2. Välj **Skapa**. 

3. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **TutorPubLBNAT-RG** |
    | **Instansinformation** |                                                                 |
    | Name             | Ange **myVNet**                                    |
    | Region           | Välj **USA, östra** |

4. Välj fliken **IP-adresser** eller Välj **Nästa: knappen Nästa: IP-adress** längst ned på sidan.

5. På fliken **IP-adresser** anger du den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | IPv4-adressutrymme | Ange **10.1.0.0/16** |

6. Under **under näts namn** väljer du ordet **standard**.

7. I **Redigera undernät** anger du den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | Namn på undernät | Ange **myBackendSubnet** |
    | Adressintervall för undernätet | Ange **10.1.0.0/24** |

8. Välj **Spara**.

9. Välj fliken **säkerhet** .

10. Under **BastionHost** väljer du **Aktivera**. Ange den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | Skydds namn | Ange **myBastionHost** |
    | AzureBastionSubnet-adressutrymme | Ange **10.1.1.0/24** |
    | Offentlig IP-adress | Välj **Skapa ny**. </br> Som **namn** anger du **myBastionIP**. </br> Välj **OK**. |


11. Välj fliken **Granska + skapa** eller Välj knappen **Granska + skapa** .

12. Välj **Skapa**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här avsnittet skapar du två virtuella datorer (**myVM1** och **myVM2**) i två olika zoner (**zon 1** och **zon 2**).

De här virtuella datorerna läggs till i backend-poolen för belastningsutjämnaren som skapades tidigare.

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **TutorPubLBNAT-RG** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVM1** |
    | Region | Välj **USA, östra** |
    | Tillgänglighets alternativ | Välj **tillgänglighets zoner** |
    | Tillgänglighetszon | Välj **1** |
    | Bild | Välj **Windows Server 2019 Data Center** |
    | Azure Spot-instans | Lämna standardvärdet |
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
    | Placerar du den virtuella datorn bakom en befintlig belastnings Utjämnings lösning? | Markera kryssrutan.|
    | **Inställningar för belastnings utjämning** |
    | Alternativ för belastnings utjämning | Välj **Azure Load Balancer** |
    | Välj en belastningsutjämnare | Välj **myLoadBalancer**  |
    | Välj en backend-pool | Välj **myBackendPool** |
   
5. Välj **Granska + skapa**. 
  
6. Granska inställningarna och välj sedan **Skapa**.

7. Följ steg 1 till 7 för att skapa en virtuell dator med följande värden och alla andra inställningar på samma sätt som **myVM1**:

    | Inställning | VM 2 |
    | ------- | ----- |
    | Name |  **myVM2** |
    | Tillgänglighetszon | **2** |
    | Nätverkssäkerhetsgrupp | Välj den befintliga **myNSG**| 

## <a name="create-nat-gateway"></a>Skapa NAT-gateway

I det här avsnittet skapar du en NAT-gateway och tilldelar den till under nätet i det virtuella nätverk som du skapade tidigare.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs > nätverk > NAT-gateway** eller Sök efter **NAT-gateway** i sökrutan.

2. Välj **Skapa**. 

3. I **skapa Network Address Translation (NAT) Gateway** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration.                                  |
    | Resursgrupp   | Välj **TutorPubLBNAT-RG**. |
    | **Instansinformation** |                                                                 |
    | Name             | Ange **myNATGateway**                                    |
    | Region           | Välj **(US) USA, östra**  |
    | Tillgänglighetszon | Välj **Ingen**. |
    | Tids gräns för inaktivitet (minuter) | Ange **10**. |

4. Välj fliken **utgående IP-adress** eller Välj **Nästa: utgående IP-** knappen längst ned på sidan.

5. Ange eller Välj följande information på fliken **utgående IP-adress** :

    | **Inställning** | **Värde** |
    | ----------- | --------- |
    | Offentliga IP-adresser | Välj **skapa en ny offentlig IP-adress**. </br> I **namn** anger du **myPublicIP-NAT**. </br> Välj **OK**. |

6. Välj fliken **undernät** eller Välj knappen **Nästa: under nät** längst ned på sidan.

7. På fliken **undernät** väljer du **MyVNet** i det **virtuella nätverkets** listruta.

8. Markera kryss rutan bredvid **myBackendSubnet**.

9. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.

10. Välj **Skapa**.

## <a name="test-nat-gateway"></a>Testa NAT-gateway

I det här avsnittet ska vi testa NAT-gatewayen. Vi identifierar först den offentliga IP-adressen för NAT-gatewayen. Sedan ansluter vi till den virtuella test datorn och verifierar den utgående anslutningen via NAT-gatewayen.
    
1. Hitta den offentliga IP-adressen för NAT-gatewayen på **översikts** skärmen. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myPublicIP**.

2. Anteckna den offentliga IP-adressen:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/find-public-ip.png" alt-text="Skärm bild som identifierar NAT-gatewayens offentliga IP-adress." border="true":::

3. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj **myVM1** i resurs gruppen **TutorPubLBNAT-RG** i resurs listan.

4. På sidan **Översikt** väljer du **Anslut** och sedan **skydds**.

5. Välj knappen blå **användnings skydds** .

6. Ange det användar namn och lösen ord som angavs när den virtuella datorn skapades.

7. Öppna **Internet Explorer** på **myVM1**.

8. Ange **https://whatsmyip.com** i adress fältet.

9. Kontrol lera att den IP-adress som visas matchar NAT-gateway-adressen som du antecknade i föregående steg:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/my-ip.png" alt-text="Skärm bild av Internet Explorer som visar extern utgående IP." border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort det virtuella nätverket, den virtuella datorn och NAT-gatewayen med följande steg:

1. Välj **resurs grupper** på den vänstra menyn.

2. Välj resurs gruppen **TutorPubLBNAT-RG** .

3. Välj **Ta bort resursgrupp**.

4. Ange **TutorPubLBNAT-RG** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Virtual Network NAT finns i:
> [!div class="nextstepaction"]
> [Översikt över Virtual Network NAT](nat-overview.md)
