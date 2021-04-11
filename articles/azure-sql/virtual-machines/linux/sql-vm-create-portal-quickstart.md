---
title: 'Snabb start: skapa en Linux-SQL Server VM i Azure'
description: Den här kursen visar hur du skapar virtuell Linux SQL Server 2017-dator i Azure Portal.
services: virtual-machines-sql
author: MashaMSFT
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: quickstart
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 492d4a799f35d7fee4f500b48cbb31d2a9c5957f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450012"
---
# <a name="provision-a-linux-virtual-machine-running-sql-server-in-the-azure-portal"></a>Etablera en virtuell Linux-dator som kör SQL Server i Azure Portal
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Linux](sql-vm-create-portal-quickstart.md)
> * [Windows](../windows/sql-vm-create-portal-quickstart.md)

I den här snabba självstudiekursen använder du Azure Portal till att skapa en virtuell Linux-dator med SQL Server 2017 installerat. Du lär dig följande: 


* [Skapa en virtuell Linux-dator som kör SQL Server från galleriet](#create)
* [Ansluta till den nya virtuella datorn med ssh](#connect)
* [Ändra SA-lösenordet](#password)
* [Konfigurera för fjärr anslutningar](#remote)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a> Skapa en virtuell Linux-dator med SQL Server installerat

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj **Skapa en resurs** i fönstret till vänster.

1. Klicka på **Compute** i fönstret **Skapa en resurs**.

1. Välj **Visa alla** bredvid rubriken **Aktuella**.

   ![Se alla VM-avbildningar](./media/sql-vm-create-portal-quickstart/azure-compute-blade.png)

1. I rutan Sök skriver du **SQL Server 2019** och väljer **RETUR** för att starta sökningen.

1. Begränsa Sök resultaten genom att välja **operativ systemets**  >  **RedHat**.

    ![Sök filter för SQL Server 2019 VM-avbildningar](./media/sql-vm-create-portal-quickstart/searchfilter.png)

1. Välj en SQL Server 2019 Linux-avbildning från Sök resultaten. I den här självstudien används **SQL Server 2019 på RHEL74**.

   > [!TIP]
   > Med Developer-utgåvan kan du testa eller utveckla med funktionerna i Enterprise-utgåvan, men utan SQL Server-licenskostnader. Du betalar bara för kostnaden för att köra den virtuella Linux-datorn.

1. Välj **Skapa**. 


### <a name="set-up-your-linux-vm"></a>Konfigurera din virtuella Linux-dator

1. Välj **Prenumeration** och **Resursgrupp** på fliken **Grunder**. 

    ![Fönstret Grunder](./media/sql-vm-create-portal-quickstart/basics.png)

1. Ange ett namn för din nya virtuella Linux-dator i **Virtuellt datornamn**.
1. Ange eller välj sedan följande värden:
   * **Region**: Välj den Azure-region som passar dig.
   * **Tillgänglighets alternativ**: Välj det alternativ för tillgänglighet och redundans som passar bäst för dina appar och data.
   * **Ändra storlek**: Välj det här alternativet om du vill välja dator storlek och när du är färdig väljer du **Välj**. Mer information om storlekar för VM-datorer finns i [VM-storlekar](../../../virtual-machines/sizes.md).

     ![Välja storlek för virtuella datorer](./media/sql-vm-create-portal-quickstart/vmsizes.png)

   > [!TIP]
   > För utveckling och funktionstestning bör du använda en storlek för virtuella datorer på **DS2** eller högre. För prestandatestning använder du **DS13** eller högre.

   * **Autentiseringstyp**: Välj **Offentlig SSH-nyckel**.

     > [!Note]
     > Du kan välja mellan offentlig SSH-nyckel eller lösenord för autentisering. SSH är säkrare. Instruktioner om hur du genererar en SSH-nyckel finns i [Skapa SSH-nycklar på Linux och Mac för virtuella Linux-datorer i Azure](../../../virtual-machines/linux/mac-create-ssh-keys.md).

   * **Användar namn**: Ange administratörs namnet för den virtuella datorn.
   * **Offentlig SSH-nyckel**: Ange din offentliga RSA-nyckel.
   * **Offentliga inkommande portar**: Välj **Tillåt valda portar** och välj **SSH-porten (22)** i listan **Välj offentliga inkommande portar** . I den här snabbstarten är det här steget nödvändigt för att ansluta och slutföra SQL Server-konfigurationen. Om du vill fjärrans luta till SQL Server måste du manuellt tillåta trafik till standard porten (1433) som används av Microsoft SQL Server för anslutningar via Internet när den virtuella datorn har skapats.

     ![Ingående portar](./media/sql-vm-create-portal-quickstart/port-settings.png)

1. Gör de ändringar du vill i inställningarna i följande ytterligare flikar eller behåll standardinställningarna.
    * **Diskar**
    * **Nätverk**
    * **Hantering**
    * **Gästkonfiguration**
    * **Taggar**

1. Välj **Granska + skapa**.
1. Välj **Skapa** i fönstret **Granska + skapa**.

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a> Ansluta till den virtuella Linux-datorn

Om du redan använder ett BASH-gränssnitt ansluter du till den virtuella Azure-datorn med **ssh**-kommandot. I följande kommando ersätter du VM-användarnamnet och IP-adressen för att ansluta till din virtuella Linux-dator.

```bash
ssh azureadmin@40.55.55.555
```

Du hittar IP-adressen till den virtuella datorn i Azure Portal.

![IP-adress i Azure Portal](./media/sql-vm-create-portal-quickstart/vmproperties.png)

Om du kör på Windows och inte har något BASH-gränssnitt kan du installera en SSH-klient som t.ex. PuTTY.

1. [Ladda ned och installera PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Kör PuTTY.

1. På PuTTY-konfigurationsskärmen anger du den virtuella datorns offentliga IP-adress.

1. Välj **Öppna** och ange ditt användarnamn och lösenord i prompterna.

Mer information om hur du ansluter till virtuella Linux-datorer finns i [Skapa en virtuell Linux-dator i Azure med hjälp av portalen](../../../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Om du ser en PuTTY-säkerhetsvarning om att serverns värdnyckel inte cachelagras i registret kan du välja bland följande alternativ. Om du litar på den här värden väljer du **Ja** för att lägga till nyckeln i PuTTy-cacheminnet och fortsätta anslutningen. Om du vill fortsätta med att bara ansluta en gång utan att lägga till nyckeln i cacheminnet väljer du **Nej**. Om du inte litar på den här värden lämnar du anslutningen genom att välja **Avbryt**.

## <a name="change-the-sa-password"></a><a id="password"></a> Ändra SA-lösenordet

Den nya virtuella datorn installerar SQL Server med ett slumpmässigt SA-lösenord. Återställ det här lösenordet innan du ansluter till SQL Server med SA-inloggningen.

1. När du har anslutit till din virtuella Linux-dator öppnar du en ny kommandoterminal.

1. Ändra SA-lösenordet med följande kommandon:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Ange ett nytt SA-lösenord och bekräfta lösenordet när du uppmanas till det.

1. Starta om SQL Server-tjänsten.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Lägga till verktygen på sökvägen (valfritt)

Flera SQL Server-[paket](sql-server-on-linux-vm-what-is-iaas-overview.md#packages) är installerade som standard, inklusive SQL Server-kommandoradspaketet Tools. Paketet innehåller verktygen **sqlcmd** och **bcp**. För enkelhetens skull kan du lägga till verktygssökvägen, `/opt/mssql-tools/bin/`, i miljövariabeln **PATH**.

1. Kör följande kommandon för att ändra **PATH** för både inloggningssessioner och interaktiva/icke-inloggningssessioner:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a> Konfigurera för fjärranslutningar

Om du behöver fjärransluta till SQL Server på den virtuella Azure-datorn måste du konfigurera en regel för inkommande trafik för nätverkssäkerhetsgruppen. Med regeln tillåts trafik på porten som SQL Server lyssnar på (standard är 1433). Följande anvisningar visar hur du använder Azure Portal för det här steget.

> [!TIP]
> Om du har valt den inkommande porten **MS SQL (1433)** i inställningarna under etableringen har dessa ändringar har gjorts för dig. Du kan gå till nästa avsnitt om hur du konfigurerar brandväggen.

1. I Portal väljer du **Virtuella datorer** och väljer sedan din virtuella SQL Server-dator.
1. Välj **Nätverk** i det vänstra navigeringsfönstret under **Inställningar**.
1. Välj **Lägg till inkommande port** under **Inkommande portregler** i fönstret Nätverk.

   ![Regler för inkommande portar](./media/sql-vm-create-portal-quickstart/networking.png)

1. I listan **Tjänst** väljer du **MS SQL**.

    ![Regel för MS SQL-säkerhetsgrupp](./media/sql-vm-create-portal-quickstart/sqlnsgrule.png)

1. Klicka på **OK** för att spara regeln för din virtuella dator.

### <a name="open-the-firewall-on-rhel"></a>Öppna brandväggen på RHEL

Den här kursen instruerade dig att skapa en virtuell dator i Red Hat Enterprise Linux (RHEL). Om du vill fjärransluta till virtuella RHEL-datorer måste du även öppna port 1433 på Linux-brandväggen.

1. [Anslut](#connect) till din virtuella RHEL-dator.

1. Kör följande kommandon i BASH-gränssnittet:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Nästa steg

Nu när du har en virtuell SQL Server 2017-dator i Azure kan du ansluta lokalt med **sqlcmd** för att köra Transact-SQL-frågor.

Om du har konfigurerat den virtuella Azure-datorn för SQL Server-fjärranslutningar bör du kunna fjärransluta. Du kan se ett exempel på hur du fjärransluter till SQL Server på Linux från Windows i [Använda SSMS på Windows till att ansluta till SQL Server på Linux](/sql/linux/sql-server-linux-develop-use-ssms). Om du vill ansluta med Visual Studio Code läser du [Använda Visual Studio Code till att skapa och köra Transact-SQL-skript för SQL Server](/sql/linux/sql-server-linux-develop-use-vscode)

Mer allmän information om SQL Server på Linux finns i [Översikt över SQL Server 2017 på Linux](/sql/linux/sql-server-linux-overview). Mer information om hur du använder virtuella SQL Server 2017-datorer för Linux finns i [Översikt över virtuella SQL Server 2017-datorer på Azure](sql-server-on-linux-vm-what-is-iaas-overview.md).