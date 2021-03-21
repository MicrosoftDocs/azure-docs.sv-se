---
title: 'Självstudie: Anslut till en webbapp med en privat Azure-slutpunkt'
titleSuffix: Azure Private Link
description: Kom igång med den här självstudien med Azures privata slut punkt för att ansluta till en webapp privat.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 502c48a92f5b41c4434d03139335a0ce05fa451f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896981"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>Självstudie: Ansluta till en webbapp via en privat Azure-slutpunkt

Den privata Azure-slutpunkten är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, till exempel virtuella datorer, att kommunicera med privata länk resurser privat.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och en skydds-värd.
> * Skapa en virtuell dator.
> * Skapa en webapp.
> * Skapa en privat slut punkt.
> * Testa anslutningen till den privata slut punkten för webb program.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!Note]
> Privat slut punkt är tillgänglig i offentliga regioner för PremiumV2-nivå, PremiumV3 Windows-webbappar, Linux-webbappar och Azure Functions Premium-plan (kallas ibland elastisk Premium-plan). 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Skapa ett virtuellt nätverk och en skydds-värd

I det här avsnittet ska du skapa ett virtuellt nätverk, ett undernät och en skydds-värd. 

Skydds-värden kommer att användas för att ansluta säkert till den virtuella datorn för att testa den privata slut punkten.

1. Välj **Skapa en resurs > Nätverk > Virtuellt nätverk** eller sök efter **virtuellt nätverk** i sökrutan på den övre vänstra sidan på skärmen.

2. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **myResourceGroup** |
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
    | Namn på undernät | Ange **undernät** |
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

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

I det här avsnittet ska du skapa en virtuell dator som ska användas för att testa den privata slut punkten.


1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator** eller Sök efter **virtuell dator** i sökrutan.
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroup** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVM** |
    | Region | Välj **Europa, västra** |
    | Tillgänglighets alternativ | Välj **ingen redundans för infrastruktur krävs** |
    | Bild | Välj **Windows Server 2019 Data Center – gen1** |
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
    | Undernät | **mySubnet** |
    | Offentlig IP-adress | Välj **Ingen**. |
    | Nätverks säkerhets grupp för nätverkskort | **Basic**|
    | Offentliga inkommande portar | Välj **Ingen**. |
   
5. Välj **Granska + skapa**. 
  
6. Granska inställningarna och välj sedan **Skapa**.

## <a name="create-web-app"></a>Skapa webbapp

I det här avsnittet ska du skapa en webbapp.

1. På den vänstra menyn väljer du **skapa en resurs**  >  **lagrings**  >  **webbapp** eller söker efter **webb program** i sökrutan.

2. På fliken **grundläggande** i **skapa webbapp** anger eller väljer du följande information:

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroup** |
    | **Instansinformation** |  |
    | Name | Ange **webapp**. Om namnet inte är tillgängligt anger du ett unikt namn. |
    | Publicera | Välj **Kod**. |
    | Körningsstack | Välj **.net Core 3,1 (LTS)**. |
    | Operativsystem | Välj **Windows**. |
    | Region | Välj **Europa, västra** |
    | **App Service-plan** |  |
    | Windows-plan (Europa, västra) | Välj **Skapa ny**. </br> Ange **myServicePlan** i **namn**. |
    | SKU och storlek | Välj **Ändra storlek**. </br> Välj **P2V2** på skärmen Välj **specifikation** . </br> Välj **Använd**. |
   
3. Välj **Granska + skapa**.

4. Välj **Skapa**.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Fliken grunder i skapa en webbapp i Azure Portal." border="true":::

## <a name="create-private-endpoint"></a>Skapa privat slut punkt

1. I den vänstra menyn väljer du **alla resurser**  >   eller det namn som du valde när du skapade.

2. I översikten över webbapp väljer du **Inställningar**  >  **nätverk**.

3. I **nätverk** väljer **du konfigurera dina privata slut punkts anslutningar**.

4. Välj **+ Lägg till** på skärmen **anslutningar för privat slut punkt** .

5. Ange eller Välj följande information på skärmen **Lägg till privat slut punkt** :

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **mywebappendpoint**. |
    | Prenumeration | Välj din prenumeration. |
    | Virtuellt nätverk | Välj **myVNet**. |
    | Undernät | Välj **undernät**. |
    | Integrera med privat DNS-zon | Välj **Ja**. |

6. Välj **OK**.
    

## <a name="test-connectivity-to-private-endpoint"></a>Testa anslutningen till privat slut punkt

I det här avsnittet ska du använda den virtuella datorn som du skapade i föregående steg för att ansluta till webbappen över den privata slut punkten.

1. Välj **resurs grupper** i det vänstra navigerings fönstret.

2. Välj **myResourceGroup**.

3. Välj **myVM**.

4. På sidan Översikt för **myVM** väljer du **Anslut** sedan **skydds**.

5. Välj knappen blå **användnings skydds** .

6. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

7. Öppna Windows PowerShell på servern när du har anslutit.

8. Ange `nslookup <webapp-name>.azurewebsites.net`. Ersätt **\<webapp-name>** med namnet på den webbapp som du skapade i föregående steg.  Du får ett meddelande som liknar det som visas nedan:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    En privat IP-adress för **10.1.0.5** returneras för namnet på webb programmet.  Adressen finns i under nätet för det virtuella nätverk som du skapade tidigare.

9. Öppna en webbläsare på den lokala datorn och ange den externa webb adressen för din webbapp, **https:// \<webapp-name> . azurewebsites.net**.

10. Kontrol lera att du får en **403** -sida. Den här sidan visar att webbappen inte är tillgänglig externt.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="403-sida för extern webbapp." border="true":::

11. Öppna Internet Explorer i skydds-anslutningen till **myVM**.

12. Ange URL: en för din webbapp, **https:// \<webapp-name> . azurewebsites.net**.

13. Verifiera att du får standard sidan för webb program.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="Standard webb program sida." border="true":::

18. Stäng anslutningen till **myVM**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet, tar du bort det virtuella nätverket, den virtuella datorn och webbappen med följande steg:

1. Välj **resurs grupper** på den vänstra menyn.

2. Välj **myResourceGroup**.

3. Välj **Ta bort resursgrupp**.

4. Ange **myResourceGroup** i **Skriv resurs gruppens namn**.

5. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar en privat länk-tjänst:
> [!div class="nextstepaction"]
> [Skapa en Private Link-tjänst](create-private-link-service-portal.md)
