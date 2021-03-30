---
title: Skapa SQL Server på en virtuell Windows-dator i Azure Portal | Microsoft Docs
description: I den här självstudien visas hur du skapar en virtuell Windows-dator med SQL Server 2017 i Azure Portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 335a63faa440e057c282f992b67b301289a7a4bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97356981"
---
# <a name="quickstart-create-sql-server-2017-on-a-windows-virtual-machine-in-the-azure-portal"></a>Snabb start: skapa SQL Server 2017 på en virtuell Windows-dator i Azure Portal

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


> [!div class="op_single_selector"]
> * [Windows](sql-vm-create-portal-quickstart.md)
> * [Linux](../linux/sql-vm-create-portal-quickstart.md)

Den här snabb starten beskriver hur du skapar en SQL Server virtuell dator (VM) i Azure Portal.


  > [!TIP]
  > - I snabbstarten finns en sökväg för snabb etablering och anslutning till en SQL-VM. Mer information om andra alternativ för etablering av SQL-VM finns i [etablerings hand boken för SQL Server på den virtuella Windows-datorn i Azure Portal](create-sql-vm-portal.md).
  > - Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](frequently-asked-questions-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Skaffa en Azure-prenumeration

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> Välj en avbildning av en virtuell SQL Server-dator

1. Logga in på [Azure-portalen](https://portal.azure.com) med ditt konto.

1. Välj **Azure SQL** i den vänstra menyn i Azure Portal. Om **Azure SQL** inte finns i listan väljer du **alla tjänster** och skriver sedan *Azure SQL* i sökrutan.
1. Välj **+ Lägg** till för att öppna **alternativ sidan Välj SQL-distribution** . Du kan visa mer information genom att välja **Visa information** på panelen **SQL Virtual Machines** .
1. Välj den **kostnads fria SQL Server licensen: SQL Server 2017-utvecklare på Windows Server 2016** -avbildningen i list rutan.

   ![Skärm bild som visar var du väljer den kostnads fria SQL Servers licensen: SQL Server 2017-utvecklare på Windows Server 2016-avbildning.](./media/sql-vm-create-portal-quickstart/select-sql-2017-vm-image.png)

1. Välj **Skapa**.

   ![Nytt sökfönster](./media/sql-vm-create-portal-quickstart/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Ange grundläggande information

Ange följande information på fliken **grundläggande** :

1. I avsnittet **projekt information** väljer du din Azure-prenumeration och väljer sedan **Skapa ny** för att skapa en ny resurs grupp. Skriv _SQLVM-RG_ som namn.

   ![Prenumeration](./media/sql-vm-create-portal-quickstart/basics-project-details.png)

1. Under **instans information**:
    1. Skriv _SQLVM_ som namn på den **virtuella datorn**. 
    1. Välj en plats för din **region**. 
    1. I den här snabb starten måste du lämna **tillgänglighets alternativen** inställt på _ingen infrastruktur-redundans krävs_. Om du vill veta mer om tillgänglighets alternativ, se [tillgänglighet](../../../virtual-machines/availability.md). 
    1. I listan **avbildning** väljer du _gratis SQL Server licens: SQL Server 2017-utvecklare på Windows Server 2016_. 
    1. Välj att **ändra storleken** **på den** virtuella datorn och välj **a2 Basic** -erbjudandet. Se till att rensa dina resurser när du är klar med dem för att förhindra eventuella oväntade kostnader. 

   ![Instansinformation](./media/sql-vm-create-portal-quickstart/basics-instance-details.png)

1. Under **administratörs konto** anger du ett användar namn, till exempel _azureuser_ och ett lösen ord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Administratörskonto](./media/sql-vm-create-portal-quickstart/basics-administrator-account.png)

1. Under **regler för inkommande port** väljer du **Tillåt valda portar** och väljer sedan **RDP (3389)** i list rutan. 

   ![Regler för inkommande portar](./media/sql-vm-create-portal-quickstart/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>SQL Server-inställningar

Konfigurera följande alternativ på fliken **SQL Server inställningar** :

1. Under **säkerhet & nätverk** väljer du _offentliga (Internet_) för **SQL-anslutning** och ändrar porten till `1401` för att undvika att använda ett välkänt port nummer i det offentliga scenariot. 
1. Under **SQL-autentisering** väljer du **Aktivera**. Autentiseringsuppgifterna för SQL-inloggning har angetts till samma användar namn och lösen ord som du konfigurerade för den virtuella datorn. Använd standardinställningen för Azure Key Vault- [**integrering**](azure-key-vault-integration-configure.md). **Lagrings konfigurationen** är inte tillgänglig för den grundläggande SQL Server VM-avbildningen, men du hittar mer information om tillgängliga alternativ för andra avbildningar i [lagrings konfigurationen](storage-configuration.md#new-vms).  

   ![Säkerhets inställningar för SQL Server](./media/sql-vm-create-portal-quickstart/sql-server-settings.png)


1. Ändra eventuella andra inställningar om det behövs och välj sedan **Granska + skapa**. 

   ![Granska + skapa](./media/sql-vm-create-portal-quickstart/review-create.png)


## <a name="create-the-sql-server-vm"></a>Skapa den virtuella SQL Server-datorn

På fliken **Granska + skapa** granskar du sammanfattningen och väljer  **skapa** för att skapa SQL Server, resurs grupp och resurser som angetts för den här virtuella datorn.

Du kan övervaka distributionen från Azure Portal. Knappen **Meddelanden** längst upp på skärmen visar grundläggande status för distributionen. Distributionen kan ta flera minuter. 

## <a name="connect-to-sql-server"></a>Anslut till SQL Server

1. I portalen hittar du den **offentliga IP-adressen** för din SQL Server VM i **översikts** avsnittet i egenskaperna för den virtuella datorn.

1. Öppna [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)på en annan dator som är ansluten till Internet.


1. I dialogrutan **Anslut till server** eller **Anslut till databasmotor**, redigerar du värdet för **Servernamn**. Ange den virtuella datorns offentliga IP-adress. Lägg sedan till ett kommatecken och Lägg till den anpassade port (**1401**) som du angav när du konfigurerade den nya virtuella datorn. Till exempel `11.22.33.444,1401`.

1. I rutan **Autentisering**, markerar du **SQL Server-autentisering**.

1. I rutan **Inloggning**, skriver du namnet på en giltig SQL-inloggning.

1. I rutan **Lösenord**, skriver du lösenordet för inloggningen.

1. Välj **Anslut**.

    ![ssms anslut](./media/sql-vm-create-portal-quickstart/ssms-connect.png)

## <a name="log-in-to-the-vm-remotely"></a><a id="remotedesktop"></a> Logga in på den virtuella datorn via en fjärranslutning

Använd följande anvisningar för att ansluta till den virtuella SQL Server-datorn med Fjärrskrivbord:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

När du ansluter till den virtuella SQL Server-datorn kan du starta SQL Server Management Studio och ansluta med Windows-autentisering med hjälp av dina autentiseringsuppgifter som lokal administratör. Om du har aktiverat SQL Server-autentisering kan du också ansluta med SQL-autentisering med hjälp av SQL-inloggningsnamnet och SQL-lösenordet som du konfigurerade under etableringen.

När du har anslutit till datorn kan du direkt ändra inställningarna för datorn och SQL Server efter behov. Du kan till exempel konfigurera brandväggsinställningarna eller ändra konfigurationsinställningarna för SQL Server.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver köra den virtuella SQL-datorn kontinuerligt kan du undvika onödiga kostnader genom att stoppa den när den inte används. Du kan även permanent ta bort alla resurser som är kopplade till den virtuella datorn genom att ta bort deras kopplade resursgrupper i portalen. Det här tar även permanent bort den virtuella datorn, så använd det här kommandot med försiktighet. Mer information finns i [Manage Azure resources through portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md) (Hantera Azure-resurser genom portalen).


## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en virtuell SQL Server 2017-dator i Azure Portal. Mer information om hur du migrerar data till den nya SQL-servern finns i följande artikel.

> [!div class="nextstepaction"]
> [Migrera en databas till en virtuell SQL-dator](migrate-to-vm-from-sql-server.md)