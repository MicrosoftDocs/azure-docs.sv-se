---
title: 'Självstudie: skapa en NAT-gateway – Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: I den här självstudien får du lära dig hur du skapar och validerar en NAT-gateway med hjälp av Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 82b5892b027627871e5492e3c6cd3776a923632b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553450"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Självstudie: skapa en NAT-gateway med hjälp av Azure Portal

Den här självstudien visar hur du använder Azure Virtual Network NAT-tjänsten. Du skapar en NAT-gateway för att tillhandahålla utgående anslutning för en virtuell dator i Azure. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk.
> * Skapa en virtuell dator.
> * Skapa en NAT-gateway och koppla den till det virtuella nätverket.
> * Anslut till den virtuella datorn och kontrol lera IP-adressen för NAT.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="virtual-network"></a>Virtuellt nätverk

Innan du distribuerar en virtuell dator och kan använda din NAT-gateway måste du skapa resurs gruppen och det virtuella nätverket.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Skapa en resurs > Nätverk > Virtuellt nätverk** eller sök efter **virtuellt nätverk** i sökrutan på den övre vänstra sidan på skärmen.

3. Välj **Skapa**.

4. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **Skapa ny**. </br> Ange **myResourceGroupNAT**. </br> Välj **OK**. |
    | **Instansinformation** |                                                                 |
    | Name             | Ange **myVNet**                                    |
    | Region           | Välj **(Europa) Västeuropa** |

5. Välj fliken **IP-adresser** eller Välj **Nästa: knappen Nästa: IP-adress** längst ned på sidan.

6. På fliken **IP-adresser** anger du den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | IPv4-adressutrymme | Ange **10.1.0.0/16** |

7. Välj **+ Lägg till undernät**. 

8. I **Redigera undernät** anger du den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | Namn på undernät | Ange **undernät** |
    | Adressintervall för undernätet | Ange **10.1.0.0/24** |

9. Välj **Lägg till**.

10. Välj fliken **säkerhet** .

11. Under **BastionHost** väljer du **Aktivera**. Ange den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | Skydds namn | Ange **myBastionHost** |
    | AzureBastionSubnet-adressutrymme | Ange **10.1.1.0/24** |
    | Offentlig IP-adress | Välj **Skapa ny**. </br> Som **namn** anger du **myBastionIP**. </br> Välj **OK**. |

12. Välj fliken **Granska + skapa** eller Välj knappen **Granska + skapa** .

13. Välj **Skapa**.

## <a name="nat-gateway"></a>NAT Gateway

Du kan använda en eller flera offentliga IP-adressresurser, offentliga IP-prefix eller både och. Vi lägger till en offentlig IP-resurs och en NAT-gateway-resurs.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs > nätverk > NAT-gateway** eller Sök efter **NAT-gateway** i sökrutan.

2. Välj **Skapa**. 

3. I **skapa Network Address Translation (NAT) Gateway** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration.                                  |
    | Resursgrupp   | Välj **myResourceGroupNAT**. |
    | **Instansinformation** |                                                                 |
    | Name             | Ange **myNATgateway**                                    |
    | Region           | Välj **(Europa) Västeuropa**  |
    | Tillgänglighetszon | Välj **Ingen**. |
    | Tids gräns för inaktivitet (minuter) | Ange **10**. |

4. Välj fliken **utgående IP-adress** eller Välj **Nästa: utgående IP-** knappen längst ned på sidan.

5. Ange eller Välj följande information på fliken **utgående IP-adress** :

    | **Inställning** | **Värde** |
    | ----------- | --------- |
    | Offentliga IP-adresser | Välj **skapa en ny offentlig IP-adress**. </br> I **namn** anger du **myPublicIP**. </br> Välj **OK**. |

6. Välj fliken **undernät** eller Välj knappen **Nästa: under nät** längst ned på sidan.

7. På fliken **undernät** väljer du **MyVNet** i det **virtuella nätverkets** listruta.

8. Markera kryss rutan bredvid **undernät**.

9. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.

10. Välj **Skapa**.
    
## <a name="virtual-machine"></a>Virtuell dator

I det här avsnittet ska du skapa en virtuell dator för att testa NAT-gatewayen och kontrol lera den offentliga IP-adressen för den utgående anslutningen.

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 

2. På sidan **skapa en virtuell dator** på fliken **grundläggande** anger du eller väljer följande information:

    | **Inställning** | **Värde** |
    | ----------- | --------- |
    | **Projektinformation** |   |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroupNAT**. |
    | **Instansinformation** |   |
    | Namn på virtuell dator | Ange **myVM**. |
    | Region | Välj **(Europa) Västeuropa**. |
    | Alternativ för tillgänglighet | Lämna standardvärdet ingen redundans krävs. |
    | Bild | Välj **Windows Server 2019 Data Center-gen1**. |
    | Storlek | Välj **Standard_DS1_v2**. |
    | **Administratörs konto** |   |
    | Användarnamn | Ange ett användar namn för den virtuella datorn. |
    | Lösenord | Ange ett lösen ord. |
    | Bekräfta lösenordet | Bekräfta lösen ord. |
    | **Regler för inkommande portar** |    |
    | Offentliga inkommande portar | Välj **Ingen**. |

3. Välj fliken **diskar** eller Välj knappen **Nästa: diskar** längst ned på sidan.

4. Låt standardvärdet vara kvar på fliken **diskar** .

5. Välj fliken **nätverk** eller Välj **Nästa: knappen nätverk** längst ned på sidan.

6. På fliken **nätverk** anger eller väljer du följande information:

    | **Inställning** | **Värde** |
    | ----------- | --------- |
    | **Nätverksgränssnitt** |   |
    | Virtuellt nätverk | Välj **myVNet**. |
    | Undernät | Välj **undernät**. |
    | Offentlig IP-adress | Välj **Ingen**. |
    | Nätverks säkerhets grupp för nätverkskort | Välj **Grundläggande**. |
    | Offentliga inkommande portar | Välj **Ingen**. |

7. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.

8. Välj **Skapa**.

## <a name="test-nat-gateway"></a>Testa NAT-gateway

I det här avsnittet ska vi testa NAT-gatewayen. Vi identifierar först den offentliga IP-adressen för NAT-gatewayen. Sedan ansluter vi till den virtuella test datorn och verifierar den utgående anslutningen via NAT-gatewayen.
    
1. Hitta den offentliga IP-adressen för NAT-gatewayen på **översikts** skärmen. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myPublicIP**.

2. Anteckna den offentliga IP-adressen:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Identifiera offentlig IP-adress för NAT-gateway" border="true":::

3. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myVM** i resurs gruppen **myResourceGroupNAT** i resurs gruppen.

4. På sidan **Översikt** väljer du **Anslut** och sedan **skydds**.

5. Välj knappen blå **användnings skydds** .

6. Ange det användar namn och lösen ord som angavs när den virtuella datorn skapades.

7. Öppna **Internet Explorer** på **myTestVM**.

8. Ange **https://whatsmyip.com** i adress fältet.

9. Kontrol lera att den IP-adress som visas matchar NAT-gateway-adressen som du antecknade i föregående steg:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer som visar extern utgående IP" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort det virtuella nätverket, den virtuella datorn och NAT-gatewayen med följande steg:

1. Välj **resurs grupper** på den vänstra menyn.

2. Välj resurs gruppen **myResourceGroupNAT** .

3. Välj **Ta bort resursgrupp**.

4. Ange **myResourceGroupNAT** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Virtual Network NAT finns i:
> [!div class="nextstepaction"]
> [Översikt över Virtual Network NAT](nat-overview.md)
