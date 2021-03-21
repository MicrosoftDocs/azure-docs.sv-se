---
title: Snabb start – skapa en privat slut punkt med hjälp av Azure Portal
description: Använd den här snabb starten för att lära dig hur du skapar en privat slut punkt med hjälp av Azure Portal.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 395912ae70c5a01bd7de9a80cf8a507dd516028e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018077"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>Snabb start: skapa en privat slut punkt med hjälp av Azure Portal

Kom igång med en privat Azure-länk genom att använda en privat slut punkt för att ansluta säkert till en Azure-webbapp.

I den här snabb starten skapar du en privat slut punkt för en Azure-webbapp och distribuerar en virtuell dator för att testa den privata anslutningen.  

Privata slut punkter kan skapas för olika typer av Azure-tjänster, till exempel Azure SQL och Azure Storage.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure-webbapp med en **PremiumV2-** eller högre App Service-plan distribuerad i din Azure-prenumeration.  
    * Mer information och ett exempel finns i [snabb start: skapa en ASP.net Core webbapp i Azure](../app-service/quickstart-dotnetcore.md). 
    * En detaljerad själv studie kurs om hur du skapar en webbapp och en slut punkt finns i [Självstudier: Anslut till en webbapp med en privat Azure-slutpunkt](tutorial-private-endpoint-webapp-portal.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-virtual-network-and-bastion-host"></a>Skapa ett virtuellt nätverk och en skydds-värd

I det här avsnittet ska du skapa ett virtuellt nätverk, ett undernät och en skydds-värd. 

Skydds-värden kommer att användas för att ansluta säkert till den virtuella datorn för att testa den privata slut punkten.

1. Välj **Skapa en resurs > Nätverk > Virtuellt nätverk** eller sök efter **virtuellt nätverk** i sökrutan på den övre vänstra sidan på skärmen.

2. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **CreatePrivateEndpointQS-RG** |
    | **Instansinformation** |                                                                 |
    | Name             | Ange **myVNet**                                    |
    | Region           | Välj **Europa, västra**.|

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
    | Resursgrupp | Välj **CreatePrivateEndpointQS-RG** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVM** |
    | Region | Välj **Europa, västra**. |
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

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

I det här avsnittet ska du skapa en privat slut punkt för den webbapp som du skapade i avsnittet krav.

1. På den övre vänstra sidan av skärmen i portalen väljer du **skapa en resurs**  >  **nätverk**  >  **privat länk** eller i rutan Sök anger du **privat länk**.

2. Välj **Skapa**.

3. I **Private Link Center** väljer du **privata slut punkter** i den vänstra menyn.

4. I **privata slut punkter** väljer du **+ Lägg till**.

5. På fliken **grundläggande** i **skapa en privat slut punkt** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **CreatePrivateEndpointQS-RG**. Du har skapat den här resurs gruppen i föregående avsnitt.|
    | **Instansinformation** |  |
    | Name  | Ange **myPrivateEndpoint**. |
    | Region | Välj **Europa, västra**. |

6. Välj fliken **resurs** eller **Nästa: resurs** längst ned på sidan.
    
7. I **resurs** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Anslutningsmetod | Välj **Anslut till en Azure-resurs i min katalog**. |
    | Prenumeration | Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. Web/Sites**. |
    | Resurs | Välj **\<your-web-app-name>**. </br> Välj namnet på den webbapp som du skapade i kraven. |
    | Målunderresurs | Välj **platser**. |

8. Välj fliken **konfiguration** eller **Nästa: konfigurations** knappen längst ned på skärmen.

9. I **konfiguration** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Nätverk** |  |
    | Virtuellt nätverk | Välj **myVNet**. |
    | Undernät | Välj **undernät**. |
    | **Privat DNS-integrering** |  |
    | Integrera med privat DNS-zon | Låt standardvärdet **Ja** vara kvar. |
    | Prenumeration | Välj din prenumeration. |
    | Privata DNS-zoner | Lämna standardvärdet **(New) privatelink.azurewebsites.net**.
    

13. Välj **Granska + skapa**.

14. Välj **Skapa**.

## <a name="test-connectivity-to-private-endpoint"></a>Testa anslutningen till privat slut punkt

I det här avsnittet ska du använda den virtuella datorn som du skapade i föregående steg för att ansluta till webbappen över den privata slut punkten.

1. Välj **resurs grupper** i det vänstra navigerings fönstret.

2. Välj **CreatePrivateEndpointQS-RG**.

3. Välj **myVM**.

4. På sidan Översikt för **myVM** väljer du **Anslut** sedan **skydds**.

5. Välj knappen blå **användnings skydds** .

6. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

7. Öppna Windows PowerShell på servern när du har anslutit.

8. Ange `nslookup <your-webapp-name>.azurewebsites.net`. Ersätt **\<your-webapp-name>** med namnet på den webbapp som du skapade i föregående steg.  Du får ett meddelande som liknar det som visas nedan:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    En privat IP-adress för **10.1.0.5** returneras för namnet på webb programmet.  Adressen finns i under nätet för det virtuella nätverk som du skapade tidigare.

11. Öppna Internet Explorer i skydds-anslutningen till **myVM**.

12. Ange URL: en för din webbapp, **https:// \<your-webapp-name> . azurewebsites.net**.

13. Du får standard sidan webbapp om ditt program inte har distribuerats:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Standard webb program sida." border="true":::

18. Stäng anslutningen till **myVM**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet, tar du bort det virtuella nätverket, den virtuella datorn och webbappen med följande steg:

1. Välj **resurs grupper** på den vänstra menyn.

2. Välj **CreatePrivateEndpointQS-RG**.

3. Välj **Ta bort resursgrupp**.

4. Ange **CreatePrivateEndpointQS-RG** i **Skriv resurs gruppens namn**.

5. Välj **Ta bort**.


## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:

* Virtuellt nätverk och skydds-värd.
* Virtuell dator.
* Privat slut punkt för en Azure-webbapp.

Du använde den virtuella datorn för att testa anslutningen på ett säkert sätt till webbappen över den privata slut punkten.



Mer information om de tjänster som stöder en privat slut punkt finns i:
> [!div class="nextstepaction"]
> [Tillgänglighet för privat länk](private-link-overview.md#availability)
