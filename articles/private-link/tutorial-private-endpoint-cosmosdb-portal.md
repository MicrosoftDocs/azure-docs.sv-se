---
title: 'Självstudie: ansluta till ett Azure Cosmos-konto med en privat Azure-slutpunkt'
titleSuffix: Azure Private Link
description: Kom igång med den här självstudien med Azures privata slut punkt för att ansluta till ett Azure Cosmos-konto privat.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 3a7e75641f6bb84b490231fcd06e04c3cbad06d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063474"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>Självstudie: ansluta till ett Azure Cosmos-konto med en privat Azure-slutpunkt

Den privata Azure-slutpunkten är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, till exempel virtuella datorer, att kommunicera med privata länk resurser privat.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och en skydds-värd.
> * Skapa en virtuell dator.
> * Skapa ett Cosmos DB-konto med en privat slut punkt.
> * Testa anslutningen till Cosmos DB privata slut punkten för kontot.

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

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>Skapa ett Cosmos DB-konto med en privat slut punkt

I det här avsnittet ska du skapa ett Cosmos DB-konto och konfigurera den privata slut punkten.

1. På den vänstra menyn väljer du **skapa ett resurs**  >  **databaser**  >  **Cosmos DB kontot** eller söker efter **Cosmos DB konto** i sökrutan.

2. På fliken **grundläggande** i **skapa Cosmos DB konto** anger eller väljer du följande information:

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. |
    | **Instansinformation** |  |
    | Kontonamn | Ange **mycosmosdb**. Om namnet inte är tillgängligt anger du ett unikt namn. |
    | API | Välj **Core (SQL)**. |
    | Location | Välj **USA, östra**. |
    | Kapacitetsläge | Lämna det **tillhandahållna standard data flödet**. |
    | Tillämpa rabatt för kostnadsfri nivå | Lämna standardvärdet **Använd inte**. |
    | Geo-redundans | Låt standardvärdet vara **disable**. |
    | Skrivåtgärder för flera regioner | Låt standardvärdet vara **disable**. |
   
3. Välj fliken **nätverk** eller Välj **Nästa: knappen nätverk** .

4. På fliken **nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Nätverksanslutningar** | |
    | Anslutningsmetod | Välj **privat slut punkt**. |
    | **Konfigurera brandvägg** | |
    | Tillåt åtkomst från Azure Portal | Låt standardvärdet **Allow** vara kvar. |
    | Tillåt åtkomst från min IP | Lämna standard alternativet **neka**. |

5. I **privat slut punkt** väljer du **+ Lägg till**.

6. I **skapa privat slut punkt** anger eller väljer du följande information:

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroup** |
    | Location | Välj **USA, östra** |
    | Name | Ange **myPrivateEndpoint** |
    | Mål under resurs | Lämna standard **kärnan (SQL)** |
    | **Nätverk** |  |
    | Virtuellt nätverk | Välj **myVNet** |
    | Undernät | Välj **mySubnet** |
    | **Privat DNS-integrering** |
    | Integrera med privat DNS-zon | Låt standardinställningen vara **Ja** |
    | Privat DNS-zon | Lämna standardvärdet (New) privatelink.documents.azure.com |

7. Välj **OK**.

8. Välj **Granska + skapa**.

9. Välj **Skapa**.

### <a name="add-a-database-and-a-container"></a>Lägg till en databas och en behållare

1. Välj **till resurs** eller på den vänstra menyn i Azure Portal väljer du **alla resurser**  >  **mycosmosdb**.

2. I den vänstra menyn väljer du **datautforskaren**.

3. I fönstret **datautforskaren** väljer du **ny behållare**.

4. I **Lägg till behållare** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Databas-id | Låt standardvärdet **Skapa nytt** vara kvar. </br> Ange **mydatabaseid** i text rutan. |
    | Data flöde (400-100 000 RU/s) | Lämna standardvärdet **manuell**. </br> Ange **400** i text rutan. |
    | Container-ID | Ange **mycontainerid** |
    | Partitionsnyckel | Ange **/MyKey** |

5. Välj **OK**.

6. I avsnittet **Inställningar** i CosmosDB-kontot väljer du **nycklar**.

7. Välj Kopiera på den **primära ANSLUTNINGS strängen**.

## <a name="test-connectivity-to-private-endpoint"></a>Testa anslutningen till privat slut punkt

I det här avsnittet ska du använda den virtuella datorn som du skapade i föregående steg för att ansluta till det Cosmos DB kontot över den privata slut punkten.

1. Välj **resurs grupper** i det vänstra navigerings fönstret.

1. Välj **myResourceGroup**.

1. Välj **myVM**.

1. På sidan Översikt för **myVM** väljer du **Anslut** sedan **skydds**.

1. Välj knappen blå **användnings skydds** .

1. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

1. Öppna Windows PowerShell på servern när du har anslutit.

1. Ange `nslookup <cosmosdb-account-name>.documents.azure.com` och verifiera namn matchningen. Ersätt **\<cosmosdb-account-name>** med namnet på Cosmos DB kontot som du skapade i föregående steg. 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```
    En privat IP-adress för **10.1.0.5** returneras för Cosmos DB kontots namn.  Adressen finns i under nätet för det virtuella nätverk som du skapade tidigare.
    
1. Hämta din Azure Cosmos DB primära anslutnings sträng från portalen. En giltig anslutnings sträng har formatet:
   
   För SQL API-konton: `https://<accountName>.documents.azure.com:443/;AccountKey=<accountKey>;` för Azure Cosmos DB API för MongoDB: `mongodb://<accountName>:<accountKey>@cdbmongo36.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false`

1. Installera [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) på den virtuella datorn.

1. Välj **Slutför** när **Microsoft Azure Storage Explorer** har installerats.  Låt rutan vara markerad om du vill öppna programmet.

1. I fönstret **Anslut till Azure Storage** väljer du **Avbryt**.

1. I Storage Explorer väljer du den högra mus knappen på **Cosmos DB konton** och väljer **Anslut till Cosmos DB**.

1. Låt standardvärdet för **SQL** vara kvar under **Välj API**.

1. I rutan under **anslutnings sträng** klistrar du in anslutnings strängen från det Cosmos DB konto som du kopierade i föregående steg.

1. Välj **Nästa**.

1. Kontrol lera att inställningarna är korrekta i **anslutnings Sammanfattning**.  

1. Välj **Anslut**.

1. Stäng anslutningen till **myVM**.


## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort det virtuella nätverket, den virtuella datorn och Cosmos DB kontot med följande steg:

1. Välj **resurs grupper** på den vänstra menyn.

2. Välj **myResourceGroup**.

3. Välj **Ta bort resursgrupp**.

4. Ange **myResourceGroup** i **Skriv resurs gruppens namn**.

5. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en:

* Virtuellt nätverk och skydds-värd.
* Virtuell dator.
* Cosmos DB konto.

Lär dig hur du skapar en privat länk-tjänst:
> [!div class="nextstepaction"]
> [Skapa en Private Link-tjänst](create-private-link-service-portal.md)
