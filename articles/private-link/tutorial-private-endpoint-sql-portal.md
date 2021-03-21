---
title: 'Självstudie: ansluta till en Azure SQL-Server med en privat Azure-slutpunkt – Portal'
description: Använd den här självstudien för att lära dig hur du skapar en Azure SQL-Server med en privat slut punkt med hjälp av Azure Portal.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 8bb227bdc4bbcdf986c2ed64e1f89e1dfa289b83
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554980"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>Självstudie: ansluta till en Azure SQL-Server med hjälp av en privat Azure-slutpunkt – Azure Portal

Den privata Azure-slutpunkten är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, till exempel virtuella datorer, att kommunicera med privata länk resurser privat.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och en skydds-värd.
> * Skapa en virtuell dator.
> * Skapa en Azure SQL-Server och en privat slut punkt.
> * Testa anslutningen till den privata SQL Server-slutpunkten.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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
    | Resursgrupp   | Välj **CreateSQLEndpointTutorial-RG** |
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
    | Resursgrupp | Välj **CreateSQLEndpointTutorial** |
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

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>Skapa en Azure SQL-Server och en privat slut punkt

I det här avsnittet ska du skapa en SQL-Server i Azure. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **databaser**  >  **SQL-databas**.

1. På fliken **grundläggande** i **skapa SQL Database** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **CreateSQLEndpointTutorial**. Du har skapat den här resurs gruppen i föregående avsnitt.|
    | **Databasinformation** |  |
    | Databasnamn  | Ange **mysqldatabase**. Om det här namnet tas skapar du ett unikt namn. |
    | Server | Välj **Skapa ny**. |

6. I **ny server** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Servernamn  | Ange **mysqlserver**. Om det här namnet tas skapar du ett unikt namn.|
    | Inloggning för serveradministratör | Ange ett administratörs namn som du väljer. |
    | Lösenord | Ange ett valfritt lösenord. Lösen ordet måste vara minst 8 tecken långt och uppfylla de definierade kraven. |
    | Location | Välj **USA, östra** |
    
7. Välj **OK**.

8. Välj fliken **nätverk** eller Välj **Nästa: knappen nätverk** .

9. På fliken **nätverk** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Nätverksanslutningar** | |
    | Anslutningsmetod | Välj **privat slut punkt**. |
   
10. Välj **+ Lägg till privat slut punkt** i **privata slut punkter**.

11. I **skapa privat slut punkt** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **CreateSQLEndpointTutorial**. |
    | Location | Välj **USA, östra**. |
    | Name | Ange **myPrivateSQLendpoint**. |
    | Målunderresurs | Välj **SQLServer**. |
    | **Nätverk** |  |
    | Virtuellt nätverk | Välj **myVNet**. |
    | Undernät | Välj **undernät**. |
    | **Privat DNS-integrering** | |
    | Integrera med privat DNS-zon | Låt standardinställningen vara **Ja**. |
    | Privat DNS-zon | Lämna standard **-privatelink.Database.Windows.net (ny)**. |

12. Välj **OK**. 

13. Välj **Granska + skapa**.

14. Välj **Skapa**.

## <a name="test-connectivity-to-private-endpoint"></a>Testa anslutningen till privat slut punkt

I det här avsnittet ska du använda den virtuella datorn som du skapade i föregående steg för att ansluta till SQL Server över den privata slut punkten.

1. Välj **resurs grupper** i det vänstra navigerings fönstret.

2. Välj **CreateSQLEndpointTutorial**.

3. Välj **myVM**.

4. På sidan Översikt för **myVM** väljer du **Anslut** sedan **skydds**.

5. Välj knappen blå **användnings skydds** .

6. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

7. Öppna Windows PowerShell på servern när du har anslutit.

8. Ange `nslookup <sqlserver-name>.database.windows.net`. Ersätt **\<sqlserver-name>** med namnet på den SQL Server som du skapade i föregående steg.  Du får ett meddelande som liknar det som visas nedan:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    En privat IP-adress för **10.1.0.5** returneras för SQL Server-namnet.  Adressen finns i under nätet för det virtuella nätverk som du skapade tidigare.


9. Installera [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) på **myVM**.

10. Öppna **SQL Server Management Studio**.

4. I **Anslut till Server** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Servertyp | Välj **Databasmotor**.|
    | Servernamn | Ange **\<sqlserver-name> . Database.Windows.net** |
    | Autentisering | Välj **SQL Server-autentisering**. |
    | Användarnamn | Ange det användar namn som du angav när servern skapades |
    | Lösenord | Ange lösen ordet som du angav när servern skapades |
    | Kom ihåg lösenordet | Välj **Ja**. |

1. Välj **Anslut**.
2. Bläddra bland databaser från menyn till vänster.
3. Du kan också Skapa eller fråga efter information från **mysqldatabase**.
4. Stäng fjärr skrivbords anslutningen till **myVM**. 

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slut punkten, SQL Server och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller: 
1. Skriv **CreateSQLEndpointTutorial** i **sökrutan längst** upp i portalen och välj **CreateSQLEndpointTutorial** från Sök resultaten. 
2. Välj **Ta bort resursgrupp**. 
3. Ange CreateSQLEndpointTutorial för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en:

* Virtuellt nätverk och skydds-värd.
* Virtuell dator.
* Azure SQL Server med privat slut punkt.

Du använde den virtuella datorn för att testa anslutningen på ett säkert sätt till SQL Server över den privata slut punkten.

Som nästa steg kanske du också är intresse rad av arkitektur scenariot för **webbappen med privat anslutning till Azure SQL Database** , som ansluter ett webb program utanför det virtuella nätverket till en Databass privata slut punkt.
> [!div class="nextstepaction"]
> [Webbapp med privat anslutning till Azure SQL Database](/azure/architecture/example-scenario/private-web-app/private-web-app)