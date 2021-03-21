---
title: 'Självstudie: Anslut till ett lagrings konto med hjälp av en privat Azure-slutpunkt'
titleSuffix: Azure Private Link
description: Kom igång med den här självstudien med Azures privata slut punkt för att ansluta till ett lagrings konto privat.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: eefef0eb61df9aa0597206b537f3d58e8b8b3213
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96484829"
---
# <a name="tutorial-connect-to-a-storage-account-using-an-azure-private-endpoint"></a>Självstudie: Anslut till ett lagrings konto med hjälp av en privat Azure-slutpunkt

Den privata Azure-slutpunkten är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, till exempel virtuella datorer, att kommunicera med privata länk resurser privat.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och en skydds-värd.
> * Skapa en virtuell dator.
> * Skapa ett lagrings konto med en privat slut punkt.
> * Testa anslutningen till lagrings kontots privata slut punkt.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

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
    | Region           | Välj **USA, östra** |

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
    | Region | Välj **USA, östra** |
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

## <a name="create-storage-account-with-a-private-endpoint"></a>Skapa ett lagrings konto med en privat slut punkt

I det här avsnittet ska du skapa ett lagrings konto och konfigurera den privata slut punkten.

1. På den vänstra menyn väljer du **skapa ett resurs**  >  **lagrings**  >  **lagrings konto** eller Sök efter **lagrings konto** i sökrutan.

2. På fliken **grundläggande** i **skapa lagrings konto** anger eller väljer du följande information:

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroup** |
    | **Instansinformation** |  |
    | Lagringskontonamn | Ange **mystorageaccount**. Om namnet inte är tillgängligt anger du ett unikt namn. |
    | Location | Välj **USA, östra** |
    | Prestanda | Lämna **standard standard** |
    | Typ av konto | Lämna standard **lagrings utrymmet (generell användning v2)** |
    | Replikering| Lämna standard **Geo-redundant lagring med Läs behörighet (RA-GRS)** |
   
3. Välj fliken **nätverk** eller Välj **Nästa: knappen nätverk** .

4. På fliken **nätverk** under **anslutnings metod** väljer du **privat slut punkt**.

5. I **privat slut punkt** väljer du **+ Lägg till**.

6. I **skapa privat slut punkt** anger eller väljer du följande information:

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroup** |
    | Location | Välj **USA, östra** |
    | Name | Ange **myPrivateEndpoint** |
    | Lagrings under resurs | Lämna standard- **bloben** |
    | **Nätverk** |  |
    | Virtuellt nätverk | Välj **myVNet** |
    | Undernät | Välj **mySubnet** |
    | **Privat DNS-integrering** |
    | Integrera med privat DNS-zon | Låt standardinställningen vara **Ja** |
    | Privat DNS-zon | Lämna standard-privatelink.blob.core.windows.net (ny) |

7. Välj **OK**.

8. Välj **Granska + skapa**.

9. Välj **Skapa**.

10. Välj **resurs grupper** i det vänstra navigerings fönstret.

11. Välj **myResourceGroup**.

12. Välj det lagrings konto som du skapade i föregående steg.

13. I avsnittet **Inställningar** i lagrings kontot väljer du **åtkomst nycklar**.

14. Välj Kopiera i **anslutnings strängen** för **KEY1**.

## <a name="test-connectivity-to-private-endpoint"></a>Testa anslutningen till privat slut punkt

I det här avsnittet ska du använda den virtuella datorn som du skapade i föregående steg för att ansluta till lagrings kontot över den privata slut punkten.

1. Välj **resurs grupper** i det vänstra navigerings fönstret.

2. Välj **myResourceGroup**.

3. Välj **myVM**.

4. På sidan Översikt för **myVM** väljer du **Anslut** sedan **skydds**.

5. Välj knappen blå **användnings skydds** .

6. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

7. Öppna Windows PowerShell på servern när du har anslutit.

8. Ange `nslookup <storage-account-name>.blob.core.windows.net`. Ersätt **\<storage-account-name>** med namnet på det lagrings konto som du skapade i föregående steg.  Du får ett meddelande som liknar det som visas nedan:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mystorageaccount8675.privatelink.blob.core.windows.net
    Address:  10.1.0.5
    Aliases:  mystorageaccount8675.blob.core.windows.net
    ```

    En privat IP-adress för **10.1.0.5** returneras för lagrings kontots namn.  Adressen finns i under nätet för det virtuella nätverk som du skapade tidigare.

9. Installera [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) på den virtuella datorn.

10. Välj **Slutför** när **Microsoft Azure Storage Explorer** har installerats.  Låt rutan vara markerad om du vill öppna programmet.

11. Välj **Använd en anslutnings sträng** på skärmen **Anslut till Azure Storage** .

12. Välj **Nästa**.

13. Ange ditt lagrings konto namn från föregående steg i **visnings namn**.

14. I rutan under **anslutnings sträng** klistrar du in anslutnings strängen från det lagrings konto som du kopierade i föregående steg.

15. Välj **Nästa**.

16. Kontrol lera att inställningarna är korrekta i **anslutnings Sammanfattning**.  

17. Välj **Anslut**.

18. Stäng anslutningen till **myVM**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta använda det här programmet tar du bort det virtuella nätverket, den virtuella datorn och lagrings kontot med följande steg:

1. Välj **resurs grupper** på den vänstra menyn.

2. Välj **myResourceGroup**.

3. Välj **Ta bort resursgrupp**.

4. Ange **myResourceGroup** i **Skriv resurs gruppens namn**.

5. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar en privat länk-tjänst:
> [!div class="nextstepaction"]
> [Skapa en Private Link-tjänst](create-private-link-service-portal.md)