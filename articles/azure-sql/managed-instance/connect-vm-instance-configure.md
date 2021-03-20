---
title: Konfigurera Azure VM-anslutning
titleSuffix: Azure SQL Managed Instance
description: Anslut till en Azure SQL-hanterad instans med SQL Server Management Studio från en virtuell Azure-dator.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: a1f496d59fa626dc8750493591128f7363afa40d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91620264"
---
# <a name="quickstart-configure-an-azure-vm-to-connect-to-azure-sql-managed-instance"></a>Snabb start: Konfigurera en virtuell Azure-dator för att ansluta till en Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här snabb starten visar hur du konfigurerar en virtuell Azure-dator för att ansluta till en Azure SQL-hanterad instans med hjälp av SQL Server Management Studio (SSMS). 


En snabb start som visar hur du ansluter från en lokal klient dator med en punkt-till-plats-anslutning i stället finns i [Konfigurera en punkt-till-plats](point-to-site-p2s-configure.md)-anslutning.

## <a name="prerequisites"></a>Förutsättningar

I den här snabb starten används de resurser som skapats i [skapa en hanterad instans](instance-create-quickstart.md) som start punkt.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-new-subnet-vnet"></a>Skapa ett nytt undernät VNet

Följande steg skapar ett nytt undernät i det virtuella SQL-hanterade instans-VNet så att en virtuell Azure-dator kan ansluta till den hanterade instansen. Under nätet för SQL-hanterad instans är dedikerat till hanterade instanser. Du kan inte skapa några andra resurser, t. ex. Azure Virtual Machines, i det under nätet.

1. Öppna resurs gruppen för den hanterade instans som du skapade i snabb starten för att [skapa en hanterad instans](instance-create-quickstart.md) . Välj det virtuella nätverket för din hanterade instans.

   ![SQL-hanterade instans resurser](./media/connect-vm-instance-configure/resources.png)

2. Välj **undernät** och välj sedan **+ undernät** för att skapa ett nytt undernät.

   ![SQL-hanterade instans under nät](./media/connect-vm-instance-configure/subnets.png)

3. Fyll i formuläret med hjälp av informationen i den här tabellen:

   | Inställning| Föreslaget värde | Beskrivning |
   | ---------------- | ----------------- | ----------- |
   | **Namn** | Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming).|
   | **Adressintervall (CIDR-block)** | Ett giltigt intervall | Standardvärdet är användbart för den här snabb starten.|
   | **Nätverkssäkerhetsgrupp** | Inget | Standardvärdet är användbart för den här snabb starten.|
   | **Routningstabell** | Inget | Standardvärdet är användbart för den här snabb starten.|
   | **Tjänstslutpunkter** | 0 valda | Standardvärdet är användbart för den här snabb starten.|
   | **Delegering av undernät** | Inget | Standardvärdet är användbart för den här snabb starten.|

   ![Nytt SQL Managed instance-undernät för virtuell klient dator](./media/connect-vm-instance-configure/new-subnet.png)

4. Välj **OK** för att skapa ytterligare ett UNDERNÄT i SQL Managed instance VNet.

## <a name="create-a-vm-in-the-new-subnet"></a>Skapa en virtuell dator i det nya under nätet 

Följande steg visar hur du skapar en virtuell dator i det nya under nätet för att ansluta till SQL-hanterad instans.

## <a name="prepare-the-azure-virtual-machine"></a>Förbered den virtuella Azure-datorn

Eftersom SQL-hanterad instans placeras i ditt privata virtuella nätverk måste du skapa en virtuell Azure-dator med ett installerat SQL-klient verktyg, t. ex. SQL Server Management Studio eller Azure Data Studio. Med det här verktyget kan du ansluta till SQL-hanterad instans och köra frågor. Den här snabbstarten använder SQL Server Management Studio.

Det enklaste sättet att skapa en virtuell klient dator med alla nödvändiga verktyg är att använda Azure Resource Manager mallar.

1. Kontrol lera att du är inloggad på Azure Portal på en annan flik i webbläsaren. Välj sedan följande knapp för att skapa en virtuell klient dator och installera SQL Server Management Studio:

   [![Bild som visar en knapp med etiketten "distribuera till Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json)

2. Fyll i formuläret med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ---------------- | ----------------- | ----------- |
   | **Prenumeration** | En giltig prenumeration | Måste vara en prenumeration där du har behörighet att skapa nya resurser. |
   | **Resursgrupp** |Den resurs grupp som du angav i snabb starten för att [skapa SQL-hanterad instans](instance-create-quickstart.md)|Den här resurs gruppen måste vara den som VNet finns i.|
   | **Plats** | Platsen för resurs gruppen | Det här värdet fylls i baserat på den valda resurs gruppen. |
   | **Namn på virtuell dator**  | Valfritt giltigt namn | Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming).|
   |**Administratörens användar namn**|Alla giltiga användar namn|Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). Använd inte ”serveradmin” eftersom det är en reserverad servernivåroll.<br>Du använder det här användar namnet varje gången du [ansluter till den virtuella datorn](#connect-to-the-virtual-machine).|
   |**Lösenord**|Valfritt giltigt lösenord|Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Du använder det här lösen ordet när som helst när du [ansluter till den virtuella datorn](#connect-to-the-virtual-machine).|
   | **Storlek på virtuell dator** | Vilken giltig storlek som helst | Standardvärdet i den här mallen för **Standard_B2s** räcker för den här snabb starten. |
   | **Plats**|[resourceGroup (). location].| Ändra inte det här värdet. |
   | **Virtual Network namn**|Det virtuella nätverk där du skapade den hanterade instansen|
   | **Namn på undernät**|Namnet på det undernät som du skapade i föregående procedur| Välj inte det undernät där du skapade den hanterade instansen.|
   | **artefakt plats** | [Deployment (). Properties. templateLink. URI] | Ändra inte det här värdet. |
   | **SAS-token för artefakt plats** | Lämna tomt | Ändra inte det här värdet. |

   ![Skapa en virtuell klientdator](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   Om du använde det föreslagna VNet-namnet och standard under nätet för att [skapa din SQL-hanterade instans](instance-create-quickstart.md), behöver du inte ändra de två senaste parametrarna. Annars bör du ändra dessa värden till de värden som du angav när du konfigurerade nätverks miljön.

3. Markera kryss rutan **Jag accepterar villkoren som anges ovan** .
4. Välj **köp** för att distribuera den virtuella Azure-datorn i nätverket.
5. Välj ikonen **Meddelanden** för att visa status för distributionen.

> [!IMPORTANT]
> Fortsätt inte förrän cirka 15 minuter efter det att den virtuella datorn har skapats för att ange tid för att de färdiga skripten ska installeras SQL Server Management Studio.

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

Följande steg visar hur du ansluter till din nya virtuella dator med hjälp av en fjärr skrivbords anslutning.

1. När distributionen är klar går du till den virtuella datorresursen.

    ![Skärm bild som visar Azure Portal med översikts sidan för den valda virtuella datorn och ansluter markerat.](./media/connect-vm-instance-configure/vm.png)  

2. Välj **Anslut**.

   En Remote Desktop Protocol fil (. RDP-fil) visas med den offentliga IP-adressen och port numret för den virtuella datorn.

   ![RDP-formulär](./media/connect-vm-instance-configure/rdp.png)  

3. Välj **Hämta RDP-fil**.

   > [!NOTE]
   > Du kan också använda SSH för att ansluta till din virtuella dator.

4. Stäng formuläret **Anslut till virtuell dator** .
5. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn.
6. När du uppmanas väljer du **Anslut**. På en Mac behöver du en RDP-klient, till exempel [den här fjärr skrivbords klienten](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) från Mac App Store.

7. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn och välj sedan **OK**.

8. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

Du är ansluten till den virtuella datorn i Serverhanteraren instrument panelen.

## <a name="connect-to-sql-managed-instance"></a>Ansluta till hanterad SQL-instans 

1. Öppna SQL Server Management Studio på den virtuella datorn.

   Det tar en stund att öppna eftersom den måste slutföra konfigurationen eftersom det är första gången SSMS har startats.
2. I dialog rutan **Anslut till Server** anger du det fullständigt kvalificerade **värd namnet** för din hanterade instans i rutan **Server namn** . Välj **SQL Server autentisering**, ange ditt användar namn och lösen ord och välj sedan **Anslut**.

    ![SSMS ansluta](./media/connect-vm-instance-configure/ssms-connect.png)  

När du ansluter kan du visa system- och användardatabaserna i noden för databaser och olika objekt i noderna för säkerhet, server-objekt, replikering, hantering, SQL Server Agent och XEvent Profiler.

## <a name="next-steps"></a>Nästa steg

- En snabb start som visar hur du ansluter från en lokal klient dator med en punkt-till-plats-anslutning finns i [Konfigurera en punkt-till-plats](point-to-site-p2s-configure.md)-anslutning.
- En översikt över anslutnings alternativen för program finns i [ansluta dina program till SQL-hanterad instans](connect-application-instance.md).
- Om du vill återställa en befintlig SQL Server databas från en lokal plats till en hanterad instans kan du använda [Azure Database migration service för migrering](../../dms/tutorial-sql-server-to-managed-instance.md) eller [T-SQL RESTORE-kommandot](restore-sample-database-quickstart.md) för att återställa från en säkerhets kopia av databasen.
