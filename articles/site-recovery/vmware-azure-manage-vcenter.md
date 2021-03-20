---
title: Hantera VMware vCenter-servrar i Azure Site Recovery
description: Den här artikeln beskriver hur du lägger till och hanterar VMware vCenter för haveri beredskap för virtuella VMware-datorer till Azure med Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84692531"
---
# <a name="manage-vmware-vcenter-server"></a>Hantera VMware vCenter Server

I den här artikeln sammanfattas hanterings åtgärder för en VMware vCenter Server i [Azure Site Recovery](site-recovery-overview.md).

## <a name="verify-prerequisites-for-vcenter-server"></a>Verifiera förutsättningar för vCenter Server

Kraven för vCenter-servrar och virtuella datorer under haveri beredskap för virtuella VMware-datorer till Azure visas i [support mat ris](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="set-up-an-account-for-automatic-discovery"></a>Konfigurera ett konto för automatisk identifiering

När du konfigurerar haveri beredskap för lokala virtuella VMware-datorer behöver Site Recovery åtkomst till vCenter Server/vSphere-värden. Site Recovery processervern kan sedan automatiskt identifiera virtuella datorer och redundansväxla dem efter behov. Som standard körs processervern på Site Recovery konfigurations servern. Lägg till ett konto för konfigurations servern för att ansluta till vCenter Server/vSphere-värden på följande sätt:

1. Logga in på konfigurations servern.
1. Öppna konfigurations Server verktyget (_cspsconfigtool.exe_) med hjälp av Skriv bords gen vägen.
1. På fliken **Hantera konto** klickar du på **Lägg till konto**.

   ![Lägg till konto](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Ange konto informationen och klicka på **OK** för att lägga till den. Kontot måste ha behörigheterna sammanfattat i konto behörighets tabellen.

   > [!NOTE]
   > Det tar ungefär 15 minuter att synkronisera konto information med Site Recovery.

### <a name="account-permissions"></a>Konto behörigheter

|**Uppgift** | **Redovisning** | **Behörigheter** | **Information**|
|--- | --- | --- | ---|
|**Identifiering/migrering av virtuell dator (utan återställning)** | Minst ett skrivskyddat användar konto. | Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, data lager, virtuella datorer och nätverk).|
|**Replikering/redundans** | Minst ett skrivskyddat användar konto. | Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med **spridning till underordnat** objekt till underordnade objekt (vSphere-värdar, data lager, virtuella datorer och nätverk).<br/><br/> Användbart för migrering, men inte fullständig replikering, redundans och återställning efter fel.|
|**Replikering/redundans/återställning** | Vi rekommenderar att du skapar en roll (AzureSiteRecoveryRole) med de behörigheter som krävs och sedan tilldelar rollen till en VMware-användare eller-grupp. | Data Center objekt – > sprids till underordnat objekt, roll = AzureSiteRecoveryRole<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, data lager, virtuella datorer och nätverk).|

## <a name="add-vmware-server-to-the-vault"></a>Lägg till VMware-servern i valvet

När du konfigurerar haveri beredskap för lokala virtuella VMware-datorer lägger du till vCenter Server/vSphere-värden som du identifierar virtuella datorer i Site Recovery valvet på följande sätt:

1. Öppna konfigurations servern i valv > **Site Recovery infrastruktur**  >  **konfigurations** servrar.
1. Klicka på **vCenter** på sidan **information** .
1. I **Lägg till vCenter** anger du ett eget namn för vSphere-värden eller vCenter-servern.
1. Ange IP-adressen eller det fullständiga domän namnet för servern.
1. Lämna porten som 443 om inte dina VMware-servrar är konfigurerade för att lyssna efter begäranden på en annan port.
1. Välj det konto som används för att ansluta till VMware vCenter-eller vSphere ESXi-servern. Klicka sedan på **OK**.

## <a name="modify-credentials"></a>Ändra autentiseringsuppgifter

Om det behövs kan du ändra autentiseringsuppgifterna som används för att ansluta till vCenter Server/vSphere-värden på följande sätt:

1. Logga in på konfigurations servern.
1. Öppna konfigurations Server verktyget (_cspsconfigtool.exe_) med hjälp av Skriv bords gen vägen.
1. Klicka på **Lägg till konto** på fliken **Hantera konto** .

   ![Lägg till konto](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Ange den nya konto informationen och klicka på **OK**. Kontot måste ha de behörigheter som anges i tabellen [konto behörighet](#account-permissions) .
1. Öppna konfigurations servern i valvet > **Site Recovery infrastruktur**  >  **konfigurations** servrar.
1. Klicka på **Uppdatera server** i **information**.
1. När jobbet uppdaterings Server har slutförts väljer du vCenter Server.
1. I **Sammanfattning** väljer du det nyligen tillagda kontot i **vCenter Server/vSphere Host-konto** och klickar på **Spara**.

   ![Ändra-konto](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Ta bort en vCenter Server

1. Öppna konfigurations servern i valvet > **Site Recovery infrastruktur**  >  **konfigurations** servrar.
1. På sidan **information** väljer du vCenter-servern.
1. Klicka på knappen **ta bort** .

   ![ta bort konto](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Ändra IP-adress och port

Du kan ändra IP-adressen för vCenter Server eller portarna som används för kommunikation mellan-servern och Site Recovery. Som standard har Site Recovery åtkomst till vCenter Server/vSphere-värd information via port 443.

1. I valvet > **Site Recovery infrastruktur**  >  **konfigurations servrar** klickar du på den konfigurations server som vCenter Server läggs till i.
1. I **vCenter-servrar** klickar du på vCenter Server som du vill ändra.
1. I **Sammanfattning** uppdaterar du IP-adressen och porten och sparar ändringarna.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. För att ändringarna ska börja gälla, vänta i 15 minuter eller [Uppdatera konfigurations servern](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>Migrera alla virtuella datorer till en ny server

Om du vill migrera alla virtuella datorer för att använda en ny vCenter Server behöver du bara uppdatera IP-adressen som tilldelats vCenter Server. Lägg inte till något annat VMware-konto eftersom det kan leda till dubbla poster. Uppdatera adressen enligt följande:

1. I valvet > **Site Recovery infrastruktur**  >  **konfigurations servrar** klickar du på den konfigurations server som vCenter Server läggs till i.
1. I avsnittet **vCenter-servrar** klickar du på den vCenter Server som du vill migrera från.
1. I **Sammanfattning** uppdaterar du IP-adressen till den nya vCenter Server och sparar ändringarna.
1. Så fort IP-adressen uppdateras börjar Site Recovery ta emot identifierings information för virtuella datorer från den nya vCenter Server. Detta påverkar inte pågående replikerings aktiviteter.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Migrera några virtuella datorer till en ny server

Om du bara vill migrera några av dina replikerade virtuella datorer till en ny vCenter Server gör du följande:

1. [Lägg till](#add-vmware-server-to-the-vault) den nya vCenter Server på konfigurations servern.
1. [Inaktivera replikering](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för virtuella datorer som ska flyttas till den nya servern.
1. Migrera de virtuella datorerna till den nya vCenter Server i VMware.
1. [Aktivera replikering](vmware-azure-tutorial.md#enable-replication) för de migrerade virtuella datorerna igen och välj den nya vCenter Server.

## <a name="migrate-most-vms-to-a-new-server"></a>Migrera de flesta virtuella datorer till en ny server

Om antalet virtuella datorer som du vill migrera till en ny vCenter Server är högre än antalet virtuella datorer som ska finnas kvar på den ursprungliga vCenter Server gör du följande:

1. [Uppdatera IP-adressen](#modify-the-ip-address-and-port) som tilldelats vCenter Server i konfigurations Server inställningarna till adressen till den nya vCenter Server.
1. [Inaktivera replikering](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för de få virtuella datorer som finns kvar på den gamla servern.
1. [Lägg till den gamla vCenter Server](#add-vmware-server-to-the-vault) och dess IP-adress på konfigurations servern.
1. Återaktivera [replikering](vmware-azure-tutorial.md#enable-replication) för de virtuella datorer som finns kvar på den gamla servern.

## <a name="next-steps"></a>Nästa steg

Om du har problem kan du läsa [felsök vCenter Server identifierings fel](vmware-azure-troubleshoot-vcenter-discovery-failures.md).
