---
title: 'Självstudie: Konfigurera en SQL Server Always on-tillgänglighetsgrupper'
description: I den här självstudien visas hur du skapar en SQL Server Always on-tillgänglighetsgrupper på Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: feab48f32396bcc89621433930c9a9f4689d8286
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97355451"
---
# <a name="tutorial-manually-configure-an-availability-group-sql-server-on-azure-vms"></a>Självstudie: Konfigurera en tillgänglighets grupp manuellt (SQL Server på virtuella Azure-datorer)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här självstudien visar hur du skapar en tillgänglighets grupp som alltid är tillgänglig för SQL Server på Azure Virtual Machines (VM). Den fullständiga självstudien skapar en tillgänglighets grupp med en databas replik på två SQL-servrar.

Även om den här artikeln konfigurerar tillgänglighets grupps miljön manuellt, är det också möjligt att göra detta med hjälp av [Azure Portal](availability-group-azure-portal-configure.md), [POWERSHELL, Azure CLI](availability-group-az-commandline-configure.md)eller [Azures snabb starts mallar](availability-group-quickstart-template-configure.md) . 


**Tids uppskattning**: tar cirka 30 minuter att slutföra när [kraven](availability-group-manually-configure-prerequisites-tutorial.md) är uppfyllda.


## <a name="prerequisites"></a>Förutsättningar

Självstudien förutsätter att du har en grundläggande förståelse för SQL Server Always on-tillgänglighetsgrupper. Om du behöver mer information, se [Översikt över Always on Availability groups (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

Innan du påbörjar själv studie kursen måste du [slutföra förutsättningarna för att skapa Always on-tillgänglighetsgrupper i Azure Virtual Machines](availability-group-manually-configure-prerequisites-tutorial.md). Om dessa krav redan har slutförts kan du gå till [skapa kluster](#CreateCluster).

I följande tabell visas de krav som du måste utföra innan du påbörjar den här självstudien:

| Krav |Beskrivning |
|----- |----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Två SQL Server instanser** | – I en Azures tillgänglighets uppsättning <br/> -I en enda domän <br/> – När funktionen kluster för växling vid fel har installerats |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Windows Server** | Fil resurs för kluster vittne |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server tjänst konto** | Domänkonto |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server Agent tjänst konto** | Domänkonto |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Brand Väggs portar öppna** | -SQL Server: **1433** för standard instans <br/> -Slut punkt för databas spegling: **5022** eller valfri tillgänglig port <br/> -Tillgänglighets grupp belastnings utjämning IP-adress hälso avsökning: **59999** eller valfri tillgänglig port <br/> -Kluster kärn belastnings utjämning IP-adress hälso avsökning: **58888** eller valfri tillgänglig port |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Lägg till funktionen kluster för växling vid fel** | Båda SQL Server instanser kräver den här funktionen |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Installations domän konto** | – Lokal administratör på varje SQL Server <br/> – Medlem i den fasta Server rollen SQL Server Sysadmin för varje instans av SQL Server  |

>[!NOTE]
> Många av stegen i den här själv studie kursen kan nu automatiseras med [Azure Portal](availability-group-azure-portal-configure.md), [POWERSHELL och AZ CLI](./availability-group-az-commandline-configure.md) och [Azure snabb starts mallar](availability-group-quickstart-template-configure.md).


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>Skapa klustret

När kraven har slutförts är det första steget att skapa ett Windows Server-redundanskluster som innehåller två SQL-servrar och en vittnes Server.

1. Använd Remote Desktop Protocol (RDP) för att ansluta till den första SQL Server. Använd ett domän konto som är en administratör på både SQL-servrar och vittnes servern.

   >[!TIP]
   >Om du har följt de [nödvändiga dokumenten](availability-group-manually-configure-prerequisites-tutorial.md)skapade du ett konto med namnet **CORP\Install**. Använd det här kontot.

2. I instrument panelen **Serverhanteraren** väljer du **verktyg** och väljer sedan **Klusterhanteraren för växling vid fel**.
3. I det vänstra fönstret högerklickar du på **Klusterhanteraren för växling vid fel** och väljer sedan **skapa ett kluster**.

   ![Skapa kluster](./media/availability-group-manually-configure-tutorial/40-createcluster.png)

4. Skapa ett kluster med en nod i guiden skapa kluster genom att stega igenom sidorna med inställningarna i följande tabell:

   | Sida | Inställningar |
   | --- | --- |
   | Innan du börjar |Använd standardvärden |
   | Välj servrar |Skriv det första SQL Server namnet i **Ange server namn** och välj **Lägg till**. |
   | Verifierings varning |Välj **Nej. jag behöver inte support från Microsoft för det här klustret, och därför vill du inte köra verifierings testen. Fortsätt att skapa klustret när jag väljer nästa**. |
   | Åtkomst punkt för administration av klustret |Ange ett kluster namn, till exempel **SQLAGCluster1** i **kluster namn**.|
   | Bekräftelse |Använd standardvärden om du inte använder lagrings utrymmen. Se kommentaren som följer den här tabellen. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Ange IP-adress för Windows Server-redundanskluster

  > [!NOTE]
  > På Windows Server 2019 skapar klustret ett **distribuerat Server namn** i stället för **klustrets nätverks namn**. Om du använder Windows Server 2019 hoppar du över alla steg som refererar till klustrets kärn namn i den här självstudien. Du kan skapa ett kluster nätverks namn med hjälp av [PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-failover-cluster). Granska blogg [redundansklustret: kluster nätverks objekt](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) för mer information. 

1. I **Klusterhanteraren för växling vid fel** rullar du ned till **kluster kärn resurser** och expanderar kluster informationen. Du bör se både **namn** och **IP** -adressresurser i **felaktigt** tillstånd. Det går inte att ansluta till IP-adressresursen eftersom klustret tilldelas samma IP-adress som själva datorn, och därför är det en dubblett av adressen.

2. Högerklicka på den felande **IP** -adressresursen och välj sedan **Egenskaper**.

   ![Kluster egenskaper](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. Välj **statisk IP-adress** och ange en tillgänglig adress från samma undernät som dina virtuella datorer.

4. I avsnittet **kluster kärn resurser** högerklickar du på kluster namn och väljer **ta online**. Vänta tills båda resurserna är online. När kluster namn resursen är online, uppdaterar den domänkontrollanten (DC) med ett nytt Active Directory dator konto (AD). Använd det här AD-kontot för att köra den klustrade tjänsten för tillgänglighets grupper senare.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>Lägg till den andra SQL Server till klustret

Lägg till den andra SQL Server i klustret.

1. I webb läsar trädet högerklickar du på klustret och väljer **Lägg till nod**.

    ![Lägg till nod i klustret](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. I **guiden Lägg till nod** väljer du **Nästa**. På sidan **Välj servrar** lägger du till den andra SQL Server. Skriv in Server namnet i **Ange server namn** och välj sedan **Lägg till**. När du är färdig väljer du **Nästa**.

1. På sidan **validerings varning** väljer du **Nej** (i ett produktions scenario ska du utföra verifierings testen). Välj **Nästa**.

8. På sidan **bekräftelse** om du använder lagrings utrymmen avmarkerar du kryss rutan **Lägg till alla tillgängliga lagrings enheter i klustret.**

   ![Bekräftelse av Lägg till nod](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

   >[!WARNING]
   >Om du använder lagrings utrymmen och inte avmarkerar **Lägg till alla tillgängliga lagrings enheter i klustret**, kopplar Windows bort de virtuella diskarna under kluster processen. Därför visas de inte i disk hanteraren eller i Utforskaren förrän lagrings utrymmena har tagits bort från klustret och återkopplas med hjälp av PowerShell. Lagrings utrymmen grupper flera diskar i lagringspooler. Mer information finns i [lagrings utrymmen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)).
   >

1. Välj **Nästa**.

1. Välj **Slutför**.

   Klusterhanteraren för växling vid fel visar att klustret har en ny nod och listar det i **noden noder** .

10. Logga ut från fjärrskrivbordssessionen.

### <a name="add-a-cluster-quorum-file-share"></a>Lägg till en fil resurs för klusterkvorum

I det här exemplet använder Windows-klustret en fil resurs för att skapa ett klusterkvorum. I den här självstudien används kvorum för nod-och fil resurs majoritet. Mer information finns i [Understanding Quorum Configurations in a Failover Cluster](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731739(v=ws.11)) (Förstå kvorumkonfigurationer i ett redundanskluster).

1. Anslut till medlems servern för fil resurs vittnet med en fjärrskrivbordssession.

1. På **Serverhanteraren** väljer du **verktyg**. Öppna **dator hantering**.

1. Välj **delade mappar**.

1. Högerklicka på **resurser** och välj **Ny resurs...**.

   ![Högerklicka på resurser och välj ny resurs](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Använd **guiden skapa en delad mapp** för att skapa en resurs.

1. På **mappsökväg** väljer du **Bläddra** och letar upp eller skapar en sökväg för den delade mappen. Välj **Nästa**.

1. I **namn, beskrivning och inställningar** kontrollerar du resursens namn och sökväg. Välj **Nästa**.

1. **Behörigheterna för delade mappar** har angetts **Anpassa behörigheter**. Välj **Anpassad...**

1. På **Anpassa behörigheter** väljer du **Lägg till...**.

1. Kontrol lera att kontot som används för att skapa klustret har fullständig kontroll.

   ![Kontrol lera att kontot som används för att skapa klustret har fullständig kontroll](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. Välj **OK**.

1. I **behörigheter för delad mapp** väljer du **Slutför**. Välj **Slutför** igen.  

1. Logga ut från servern

### <a name="configure-the-cluster-quorum"></a>Konfigurera klusterkvorum

Ange sedan klustrets kvorum.

1. Anslut till den första klusternoden med fjärr skrivbord.

1. I **Klusterhanteraren för växling vid fel** högerklickar du på klustret, pekar på **fler åtgärder** och väljer **Konfigurera inställningar för klusterkvorum...**.

   ![Välj Konfigurera inställningar för klusterkvorum](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. Välj **Nästa** i **guiden Konfigurera klusterkvorum**.

1. I **Välj alternativ för kvorumkonfiguration** väljer **du Välj ett kvorumlogg** och sedan **Nästa**.

1. På **Välj ett kvorumlogg** väljer du **Konfigurera ett fil resurs vittne**.

   >[!TIP]
   >Windows Server 2016 har stöd för ett moln vittne. Om du väljer den här typen av vittne behöver du inte ett fil resurs vittne. Mer information finns i [distribuera ett moln vittne för ett redundanskluster](/windows-server/failover-clustering/deploy-cloud-witness). I den här självstudien används ett fil resurs vittne, som stöds av tidigare operativ system.
   >

1. På sidan **Konfigurera fil resurs vittne** anger du sökvägen till resursen som du skapade. Välj **Nästa**.

1. Verifiera inställningarna vid **bekräftelse**. Välj **Nästa**.

1. Välj **Slutför**.

Kluster kärn resurserna konfigureras med ett fil resurs vittne.

## <a name="enable-availability-groups"></a>Aktivera tillgänglighets grupper

Aktivera sedan funktionen **AlwaysOn Availability groups** . Utför de här stegen på båda SQL-servrarna.

1. Starta **Konfigurationshanteraren för SQL Server** på **Start** skärmen.
2. I webb läsar trädet väljer du **SQL Server tjänster**, högerklickar på tjänsten **SQL Server (MSSQLSERVER)** och väljer **Egenskaper**.
3. Välj fliken **AlwaysOn-hög tillgänglighet** och välj sedan **Aktivera AlwaysOn-tillgänglighetsgrupper** enligt följande:

    ![Aktivera AlwaysOn-tillgänglighetsgrupper](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. Välj **Använd**. Välj **OK** i popup-dialogrutan.

5. Starta om SQL Server-tjänsten.

Upprepa de här stegen på andra SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an availability group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the availability groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, select **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and select **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and select **Next**.
6. In the **Profile** page, accept the default settings and select **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then select **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Skapa en databas på den första SQL Server

1. Starta RDP-filen till den första SQL Server med ett domän konto som är medlem i den fasta Server rollen sysadmin.
1. Öppna SQL Server Management Studio och Anslut till den första SQL Server.
7. I **Object Explorer** högerklickar du på **databaser** och väljer **ny databas**.
8. I **databas namn** skriver du **MyDB1** och väljer sedan **OK**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a> Skapa en säkerhets kopierings resurs

1. Välj **verktyg** på den första SQL Server i **Serverhanteraren**. Öppna **dator hantering**.

1. Välj **delade mappar**.

1. Högerklicka på **resurser** och välj **Ny resurs...**.

   ![Välj ny resurs](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Använd **guiden skapa en delad mapp** för att skapa en resurs.

1. På **mappsökväg** väljer du **Bläddra** och letar upp eller skapar en sökväg för den delade mappen för databas säkerhets kopiering. Välj **Nästa**.

1. I **namn, beskrivning och inställningar** kontrollerar du resursens namn och sökväg. Välj **Nästa**.

1. **Behörigheterna för delade mappar** har angetts **Anpassa behörigheter**. Välj **Anpassad...**

1. På **Anpassa behörigheter** väljer du **Lägg till...**.

1. Kontrol lera att SQL Server-och SQL Server Agent tjänst kontona för båda servrarna har fullständig kontroll.

   ![Kontrol lera att SQL Server-och SQL Server Agent tjänst kontona för båda servrarna har fullständig kontroll.](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. Välj **OK**.

1. I **behörigheter för delad mapp** väljer du **Slutför**. Välj **Slutför** igen.  

### <a name="take-a-full-backup-of-the-database"></a>Gör en fullständig säkerhets kopia av databasen

Du måste säkerhetskopiera den nya databasen för att initiera logg kedjan. Om du inte gör en säkerhets kopia av den nya databasen kan den inte ingå i en tillgänglighets grupp.

1. Högerklicka på databasen i **Object Explorer**, peka på **uppgifter...**, Välj **säkerhetskopiera**.

1. Välj **OK** om du vill göra en fullständig säkerhets kopiering till standard platsen för säkerhets kopiering.

## <a name="create-the-availability-group"></a>Skapa tillgänglighets gruppen

Du är nu redo att konfigurera en tillgänglighets grupp med hjälp av följande steg:

* Skapa en databas på den första SQL Server.
* Gör både en fullständig säkerhets kopiering och en säkerhets kopia av transaktions loggen för-databasen.
* Återställ fullständiga säkerhets kopior och logg säkerhets kopior till den andra SQL Server med alternativet **NORECOVERY** .
* Skapa tillgänglighets gruppen (**AG1**) med synkron genomförande, automatisk redundans och läsbar sekundär repliker.

### <a name="create-the-availability-group"></a>Skapa tillgänglighets gruppen:

1. I fjärrskrivbordssessionen till den första SQL Server. I **Object Explorer** i SSMS högerklickar du på **AlwaysOn hög tillgänglighet** och väljer **ny tillgänglighets grupp guide**.

    ![Starta guiden Ny tillgänglighets grupp](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. På sidan **Introduktion** väljer du **Nästa**. På sidan **Ange tillgänglighets grupp namn** anger du ett namn för tillgänglighets gruppen i **tillgänglighets gruppens namn**. Till exempel **AG1**. Välj **Nästa**.

    ![Guiden ny tillgänglighets grupp, ange tillgänglighets gruppens namn](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. På sidan **Välj databaser** väljer du din databas och väljer sedan **Nästa**.

   >[!NOTE]
   >Databasen uppfyller kraven för en tillgänglighets grupp eftersom du har tagit minst en fullständig säkerhets kopia på den avsedda primära repliken.
   >

   ![Guiden ny tillgänglighets grupp, Välj databaser](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)

4. På sidan **Ange repliker** väljer du **Lägg till replik**.

   ![Guiden ny tillgänglighets grupp, ange repliker](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)

5. Dialog rutan **Anslut till Server** öppnas. Skriv namnet på den andra servern i **Server namn**. Välj **Anslut**.

   På sidan **Ange repliker** bör du nu se den andra servern som listas i **tillgänglighets repliker**. Konfigurera replikerna på följande sätt.

   ![Guiden ny tillgänglighets grupp, ange repliker (slutförd)](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. Välj **slut punkter** om du vill se slut punkten för databas spegling för den här tillgänglighets gruppen. Använd samma port som du använde när du konfigurerade [brand Väggs regeln för slut punkter för databas spegling](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

    ![Guiden ny tillgänglighets grupp väljer du synkronisering av inledande data](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. På sidan **Välj inledande data synkronisering** väljer du **fullständig** och anger en delad nätverks plats. För platsen använder du den [säkerhets kopierings resurs som du skapade](#backupshare). I exemplet **\\ \\<först SQL Server \> \ säkerhets kopiering \\**. Välj **Nästa**.

   >[!NOTE]
   >Fullständig synkronisering tar en fullständig säkerhets kopia av databasen på den första instansen av SQL Server och återställer den till den andra instansen. För stora databaser rekommenderas inte fullständig synkronisering eftersom det kan ta lång tid. Du kan minska den här tiden genom att manuellt göra en säkerhets kopia av databasen och återställa den med `NO RECOVERY` . Om databasen redan har återställts med `NO RECOVERY` den andra SQL Server innan du konfigurerar tillgänglighets gruppen väljer du **Anslut endast**. Om du vill utföra säkerhets kopieringen när du har konfigurerat tillgänglighets gruppen väljer du **hoppa över inledande datasynkronisering**.
   >

   ![Välj hoppa över inledande datasynkronisering](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. På sidan **verifiering** väljer du **Nästa**. Den här sidan bör se ut ungefär som på följande bild:

    ![Guiden ny tillgänglighets grupp, verifiering](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >Det finns en varning för Listener-konfigurationen eftersom du inte har konfigurerat en lyssnare för tillgänglighets grupp. Du kan ignorera den här varningen eftersom du skapar lyssnaren på virtuella Azure-datorer när du har skapat Azure Load Balancer.

10. På sidan **Sammanfattning** väljer du **Slutför** och väntar medan guiden konfigurerar den nya tillgänglighets gruppen. På sidan **förlopp** kan du välja **Mer information** för att visa den detaljerade förloppet. När guiden är klar kan du kontrol lera att tillgänglighets gruppen har skapats genom att granska **resultat** sidan.

     ![Guiden ny tillgänglighets grupp, resultat](./media/availability-group-manually-configure-tutorial/74-results.png)

11. Välj **Stäng** för att avsluta guiden.

### <a name="check-the-availability-group"></a>Kontrol lera tillgänglighets gruppen

1. I **Object Explorer**, expandera **AlwaysOn hög tillgänglighet** och expandera sedan **tillgänglighets grupper**. Nu bör du se den nya tillgänglighets gruppen i den här behållaren. Högerklicka på tillgänglighets gruppen och välj **Visa instrument panel**.

   ![Visa instrument panelen för tillgänglighets gruppen](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   **AlwaysOn-instrumentpanelen** bör se ut ungefär som på följande skärm bild:

   ![Instrument panel för tillgänglighets grupp](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   Du kan se replikerna, växlings läget för varje replik och synkroniseringsstatus.

2. I **Klusterhanteraren för växling vid fel** väljer du ditt kluster. Välj **roller**. Namnet på tillgänglighets gruppen som du använde är en roll i klustret. Den tillgänglighets gruppen har ingen IP-adress för klient anslutningar eftersom du inte har konfigurerat en lyssnare. Du kommer att konfigurera lyssnaren när du har skapat en Azure Load Balancer.

   ![tillgänglighets grupp i Klusterhanteraren för växling vid fel](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Försök inte att redundansväxla tillgänglighets gruppen från Klusterhanteraren för växling vid fel. Alla redundansväxling bör utföras inifrån **AlwaysOn-instrumentpanelen** i SSMS. Mer information finns i [begränsningar för att använda Klusterhanteraren för växling vid fel med tillgänglighets grupper](/sql/database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server).
    >

Nu har du en tillgänglighets grupp med repliker på två instanser av SQL Server. Du kan flytta tillgänglighets gruppen mellan instanser. Du kan inte ansluta till tillgänglighets gruppen ännu eftersom du inte har en lyssnare. På Azure Virtual Machines kräver lyssnaren en belastningsutjämnare. Nästa steg är att skapa belastningsutjämnaren i Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>skapa en Azure Load Balancer

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

På Azure Virtual Machines kräver en belastnings utjämning för en SQL Server tillgänglighets grupp. Belastningsutjämnaren innehåller IP-adresserna för tillgänglighets gruppens lyssnare och Windows Server-redundansklustret. I det här avsnittet sammanfattas hur du skapar belastningsutjämnaren i Azure Portal.

En belastningsutjämnare i Azure kan vara antingen en Standard Load Balancer eller en grundläggande Load Balancer. Standard Load Balancer har fler funktioner än de grundläggande Load Balancer. För en tillgänglighets grupp krävs Standard Load Balancer om du använder en tillgänglighets zon (i stället för en tillgänglighets uppsättning). Mer information om skillnaden mellan belastningsutjämnarens SKU: er finns i [load BALANCER SKU-jämförelse](../../../load-balancer/skus.md).

1. I Azure Portal går du till resurs gruppen där dina SQL-servrar finns och väljer **+ Lägg till**.
1. Sök efter **Load Balancer**. Välj belastningsutjämnaren som publicerats av Microsoft.

   ![Välj den belastningsutjämnare som publicerats av Microsoft](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. Välj **Skapa**.
1. Konfigurera följande parametrar för belastningsutjämnaren.

   | Inställning | Fält |
   | --- | --- |
   | **Namn** |Använd ett text namn för belastningsutjämnaren, till exempel **sqlLB**. |
   | **Typ** |Intern |
   | **Virtuellt nätverk** |Använd namnet på det virtuella Azure-nätverket. |
   | **Undernät** |Använd namnet på det undernät som den virtuella datorn finns i.  |
   | **Tilldelning av IP-adress** |Statisk |
   | **IP-adress** |Använd en tillgänglig adress från under nätet. Använd den här adressen för tillgänglighets gruppens lyssnare. Observera att detta skiljer sig från klustrets IP-adress.  |
   | **Prenumeration** |Använd samma prenumeration som den virtuella datorn. |
   | **Plats** |Använd samma plats som den virtuella datorn. |

   Bladet Azure Portal bör se ut så här:

   ![Skapa Load Balancer](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. Skapa belastningsutjämnaren genom att välja **skapa**.

Om du vill konfigurera belastningsutjämnaren måste du skapa en backend-pool, en avsökning och ange regler för belastnings utjämning. Gör detta i Azure Portal.

### <a name="add-a-backend-pool-for-the-availability-group-listener"></a>Lägg till en backend-pool för tillgänglighets gruppens lyssnare

1. I Azure Portal går du till din tillgänglighets grupp. Du kan behöva uppdatera vyn för att se den nyligen skapade belastningsutjämnaren.

   ![Hitta Load Balancer i resurs gruppen](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. Välj belastningsutjämnaren, Välj **Server dels pooler** och sedan **+ Lägg till**.

1. Ange ett namn för backend-poolen.

1. Koppla backend-poolen till den tillgänglighets uppsättning som innehåller de virtuella datorerna.

1. Under **mål nätverkets IP-konfigurationer** kontrollerar du den **virtuella datorn** och väljer båda de virtuella datorer som ska vara värd för tillgänglighets grupps repliker. Ta inte med fil resurs vittnes servern.

   >[!NOTE]
   >Om båda virtuella datorerna inte anges kommer anslutningarna endast att lyckas till den primära repliken.

1. Välj **OK** för att skapa backend-poolen.

### <a name="set-the-probe"></a>Ange avsökningen

1. Välj belastningsutjämnaren, Välj **hälso avsökningar** och välj sedan **+ Lägg till**.

1. Ange lyssnar hälso avsökningen enligt följande:

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | SQLAlwaysOnEndPointProbe |
   | **Protokoll** | Välj TCP | TCP |
   | **Port** | Oanvänd port | 59999 |
   | **Intervall**  | Tiden mellan avsöknings försök i sekunder |5 |
   | **Tröskelvärde för ej felfri** | Antalet avsöknings fel i följd som måste inträffa för att en virtuell dator ska anses vara ohälsosam  | 2 |

1. Välj **OK** för att ställa in hälso avsökningen.

### <a name="set-the-load-balancing-rules"></a>Ange regler för belastnings utjämning

1. Markera belastningsutjämnaren, Välj **belastnings Utjämnings regler** och välj **+ Lägg till**.

1. Ange reglerna för belastnings utjämning för lyssnare enligt följande.

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | SQLAlwaysOnEndPointListener |
   | **Klientdelens IP-adress** | Välj en adress |Använd adressen som du skapade när du skapade belastningsutjämnaren. |
   | **Protokoll** | Välj TCP |TCP |
   | **Port** | Använd porten för tillgänglighets gruppens lyssnare | 1433 |
   | **Backend-port** | Det här fältet används inte när flytande IP anges för direkt Server retur | 1433 |
   | **Avsökning** |Det namn som du har angett för avsökningen | SQLAlwaysOnEndPointProbe |
   | **Beständig session** | Nedrullningsbar listruta | **Ingen** |
   | **Timeout för inaktivitet** | Minuter för att hålla en TCP-anslutning öppen | 4 |
   | **Flytande IP (direkt Server retur)** | |Enabled |

   > [!WARNING]
   > Direkt Server retur anges när den skapas. Det kan inte ändras.
   >

1. Välj **OK** för att ange regler för avlyssning av avlyssnings belastning.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Lägg till klustrets kärn IP-adress för WSFC-klustret (Windows Server failover Cluster)

WSFC-IP-adressen måste också finnas i belastningsutjämnaren.

1. I Azure Portal går du till samma Azure Load Balancer. Välj **IP-konfiguration för klient** del och välj **+ Lägg till**. Använd IP-adressen som du konfigurerade för WSFC i kluster kärn resurserna. Ange IP-adressen som statisk.

1. I belastningsutjämnaren väljer du **hälso avsökningar** och väljer sedan **+ Lägg till**.

1. Ange hälso avsökningen för WSFC-klustrets kärn IP-adress enligt följande:

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | WSFCEndPointProbe |
   | **Protokoll** | Välj TCP | TCP |
   | **Port** | Oanvänd port | 58888 |
   | **Intervall**  | Tiden mellan avsöknings försök i sekunder |5 |
   | **Tröskelvärde för ej felfri** | Antalet avsöknings fel i följd som måste inträffa för att en virtuell dator ska anses vara ohälsosam  | 2 |

1. Välj **OK** för att ställa in hälso avsökningen.

1. Ange regler för belastnings utjämning. Välj **belastnings Utjämnings regler** och välj **+ Lägg till**.

1. Ange belastnings Utjämnings reglerna för klustrets kärn IP-adress enligt följande.

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | WSFCEndPoint |
   | **Klientdelens IP-adress** | Välj en adress |Använd adressen som du skapade när du konfigurerade WSFC-IP-adressen. Detta skiljer sig från IP-adressen för lyssnaren |
   | **Protokoll** | Välj TCP |TCP |
   | **Port** | Använd porten för klustrets IP-adress. Det här är en tillgänglig port som inte används för avsöknings porten för lyssnaren. | 58888 |
   | **Backend-port** | Det här fältet används inte när flytande IP anges för direkt Server retur | 58888 |
   | **Avsökning** |Det namn som du har angett för avsökningen | WSFCEndPointProbe |
   | **Beständig session** | Nedrullningsbar listruta | **Ingen** |
   | **Timeout för inaktivitet** | Minuter för att hålla en TCP-anslutning öppen | 4 |
   | **Flytande IP (direkt Server retur)** | |Enabled |

   > [!WARNING]
   > Direkt Server retur anges när den skapas. Det kan inte ändras.
   >

1. Välj **OK** för att ange belastnings Utjämnings regler.

## <a name="configure-the-listener"></a><a name="configure-listener"></a> Konfigurera lyssnaren

Nästa sak är att konfigurera en lyssnare för tillgänglighets grupp i redundansklustret.

> [!NOTE]
> I den här självstudien visas hur du skapar en enskild lyssnare med en ILB-IP-adress. Information om hur du skapar en eller flera lyssnare med en eller flera IP-adresser finns i [skapa tillgänglighets grupps lyssnare och belastningsutjämnare | Azure](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Ange lyssnar port

I SQL Server Management Studio anger du lyssnar porten.

1. Starta SQL Server Management Studio och Anslut till den primära repliken.

1. Navigera till tillgänglighets  >    >  **grupps lyssnare för tillgänglighets** grupper med hög tillgänglighet för AlwaysOn.

1. Du bör nu se det lyssnar namn som du skapade i Klusterhanteraren för växling vid fel. Högerklicka på namnet på lyssnaren och välj **Egenskaper**.

1. I rutan **port** anger du Port numret för tillgänglighets gruppens lyssnare. 1433 är standardinställningen. Välj **OK**.

Nu har du en SQL Server tillgänglighets grupp i Azure virtuella datorer som körs i Resource Manager-läge.

## <a name="test-connection-to-listener"></a>Testa anslutning till lyssnare

Så här testar du anslutningen:

1. Använd RDP för att ansluta till en SQL Server som finns i samma virtuella nätverk, men som inte äger repliken. Du kan använda andra SQL Server i klustret.

1. Använd verktyget **SQLCMD** för att testa anslutningen. Följande skript upprättar till exempel en **SQLCMD** -anslutning till den primära repliken via lyssnaren med Windows-autentisering:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Om lyssnaren använder en annan port än standard porten (1433) anger du porten i anslutnings strängen. Till exempel `sqlcmd` ansluter följande kommando till en lyssnare på port 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

SQLCMD-anslutningen ansluts automatiskt till den beroende instansen av SQL Server är värd för den primära repliken.

> [!TIP]
> Kontrol lera att den port du anger är öppen i brand väggen för båda SQL-servrarna. Båda servrarna kräver en regel för inkommande trafik för TCP-porten som du använder. Mer information finns i [Lägg till eller redigera brand Väggs regel](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)).
>

## <a name="next-steps"></a>Nästa steg

- [Lägg till en IP-adress till en belastningsutjämnare för en andra tillgänglighets grupp](availability-group-listener-powershell-configure.md#Add-IP).