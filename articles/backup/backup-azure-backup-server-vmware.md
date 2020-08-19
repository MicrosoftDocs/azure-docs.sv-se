---
title: Säkerhetskopiera virtuella VMware-datorer med Azure Backup Server
description: I den här artikeln lär du dig hur du använder Azure Backup Server för att säkerhetskopiera virtuella VMware-datorer som körs på en VMware vCenter/ESXi-Server.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 892d308df15ef745f34419ec315e2c5000b0852c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263545"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Säkerhetskopiera virtuella VMware-datorer med Azure Backup Server

Den här artikeln beskriver hur du säkerhetskopierar virtuella VMware-datorer som körs på VMware ESXi värdar/vCenter Server till Azure med Azure Backup Server.

Den här artikeln förklarar hur du:

- Konfigurera en säker kanal så att Azure Backup Server kan kommunicera med VMware-servrar via HTTPS.
- Konfigurera ett VMware-konto som Azure Backup Server använder för att få åtkomst till VMware-servern.
- Lägg till kontoautentiseringsuppgifter till Azure Backup.
- Lägg till vCenter-eller ESXi-servern i Azure Backup Server.
- Konfigurera en skydds grupp som innehåller de virtuella VMware-datorer som du vill säkerhetskopiera, ange inställningar för säkerhets kopiering och Schemalägg säkerhets kopieringen.

## <a name="before-you-start"></a>Innan du börjar

- Kontrol lera att du använder en version av vCenter/ESXi som stöds för säkerhets kopiering. Se support mat ris [här](./backup-mabs-protection-matrix.md).
- Kontrol lera att du har konfigurerat Azure Backup Server. Om du inte gör det måste du [göra det](backup-azure-microsoft-azure-backup.md) innan du börjar. Du bör köra Azure Backup Server med de senaste uppdateringarna.
- Se till att följande nätverks portar är öppna:
  - TCP 443 mellan MABS och vCenter
  - TCP 443 och TCP 902 mellan MABS-och ESXi-värden

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Skapa en säker anslutning till vCenter Server

Azure Backup Server kommunicerar som standard med VMware-servrar via HTTPS. Om du vill konfigurera HTTPS-anslutningen laddar du ned VMware Certificate Authority-certifikatet (CA) och importerar det på Azure Backup Server.

### <a name="before-you-begin"></a>Innan du börjar

- Om du inte vill använda HTTPS kan du [inaktivera HTTPS-certifikat validering för alla VMware-servrar](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Du ansluter vanligt vis från en webbläsare på den Azure Backup Server datorn till vCenter/ESXi-servern med hjälp av vSphere-webbklienten. Första gången du gör detta är anslutningen inte säker och kommer att visa följande.
- Det är viktigt att förstå hur Azure Backup Server hanterar säkerhets kopieringar.
  - Det första steget Azure Backup Server säkerhetskopierar data till den lokala disk lagringen. Azure Backup Server använder en lagringspool, en uppsättning diskar och volymer där Azure Backup Server lagrar disk återställnings punkter för skyddade data. Lagringspoolen kan vara direktansluten lagring (DAS), en Fiber Channel SAN-enhet eller en iSCSI-lagringsenhet eller SAN. Det är viktigt att se till att du har tillräckligt med lagrings utrymme för lokal säkerhets kopiering av dina virtuella VMware-Datadata.
  - Azure Backup Server säkerhetskopierar sedan från den lokala disk lagringen till Azure.
  - [Få hjälp](/system-center/dpm/create-dpm-protection-groups#figure-out-how-much-storage-space-you-need) med att räkna ut hur mycket lagrings utrymme du behöver. Informationen är för DPM men kan även användas för Azure Backup Server.

### <a name="set-up-the-certificate"></a>Konfigurera certifikatet

Konfigurera en säker kanal enligt följande:

1. I webbläsaren på Azure Backup Server anger du webb adressen för webb klienten vSphere. Om inloggnings sidan inte visas kontrollerar du inställningarna för anslutning och webbläsare.

    ![vSphere-webbklient](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. På sidan för vSphere webb klient inloggning väljer du **Hämta betrodda certifikat från rot certifikat utfärdare**.

    ![Hämta certifikat för betrodd rot certifikat utfärdare](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. En fil med namnet **Download** har laddats ned. Beroende på din webbläsare får du ett meddelande som frågar om du vill öppna eller spara filen.

    ![Hämta CA-certifikat](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Spara filen på Azure Backup Server dator med fil namns tillägget. zip.

5. Högerklicka på **download.zip**  >  **extrahera alla**. Zip-filen extraherar dess innehåll till mappen **certifikat** , som innehåller:
   - Rot certifikat filen med ett tillägg som börjar med en numrerad sekvens som. 0 och. 1.
   - CRL-filen har ett tillägg som börjar med en sekvens som. R0 eller. R1. CRL-filen är associerad med ett certifikat.

    ![Hämtade certifikat](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. I mappen **certifikat** högerklickar du på rot certifikat filen > **byter namn**.

    ![Byt namn på rot certifikat](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Ändra rot certifikatets tillägg till. CRT och bekräfta. Fil ikonen ändras till en som representerar ett rot certifikat.

8. Högerklicka på rot certifikatet och välj **Installera certifikat**på popup-menyn.

9. I **guiden Importera certifikat**väljer du **lokal dator** som mål för certifikatet och väljer sedan **Nästa**. Bekräfta om du tillfrågas om du vill tillåta ändringar i datorn.

    ![Välkommen till guiden](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. På sidan **certifikat Arkiv** väljer du **Placera alla certifikat i följande Arkiv**och väljer sedan **Bläddra** för att välja certifikat arkivet.

    ![Certifikat lagring](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. I **Välj certifikat Arkiv**väljer du **betrodda rot certifikat utfärdare** som målmapp för certifikaten. Välj sedan **OK**.

    ![Målmapp för certifikat](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. När **du har slutfört guiden Importera certifikat**kontrollerar du mappen och väljer sedan **Slutför**.

    ![Verifiera att certifikatet finns i rätt mapp](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. När certifikat importen har bekräftats loggar du in på vCenter Server för att bekräfta att anslutningen är säker.

### <a name="disable-https-certificate-validation"></a>Inaktivera verifiering av HTTPS-certifikat

Om du har säkra gränser i din organisation och inte vill använda HTTPS-protokollet mellan VMware-servrar och Azure Backup Server datorn inaktiverar du HTTPS på följande sätt:

1. Kopiera och klistra in följande text i en. txt-fil.

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. Spara filen på den Azure Backup Server datorn med namnet **DisableSecureAuthentication. reg**.

3. Dubbelklicka på filen för att aktivera register posten.

## <a name="create-a-vmware-role"></a>Skapa en VMware-roll

Azure Backup Server behöver ett användar konto med behörighet att komma åt v-Center-servern/ESXi-värden. Skapa en VMware-roll med vissa behörigheter och associera sedan ett användar konto med rollen.

1. Logga in på vCenter Server (eller ESXi-värden om du inte använder vCenter Server).
2. I panelen **överblick** väljer du **Administration**.

    ![Administration](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. I **administrations**  >  **roller**väljer du ikonen Lägg till roll (symbolen +).

    ![Lägg till roll](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. I **skapa roll**  >  **roll namn**anger du *BackupAdminRole*. Roll namnet kan vara vad du vill, men det bör vara igenkännligt för rollens syfte.

5. Välj behörigheterna som sammanfattas i tabellen nedan och välj sedan **OK**.  Den nya rollen visas i listan på **roll** panelen.
   - Välj ikonen bredvid den överordnade etiketten för att expandera den överordnade och Visa de underordnade privilegierna.
   - Om du vill välja VirtualMachine-privilegier måste du gå flera nivåer till den överordnade underordnade hierarkin.
   - Du behöver inte markera alla underordnade behörigheter inom en överordnad behörighet.

    ![Hierarki för överordnad underordnad behörighet](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Roll behörigheter

I följande tabell visas de privilegier som du måste tilldela till det användar konto som du skapar:

| Behörigheter för vCenter 6,5-användar konto                          | Behörigheter för vCenter 6,7-användar konto                            |
|----------------------------------------------------------------------------|----------------------------------------------------------------------------|
| Data lager cluster.Configurera ett data lager kluster                           | Data lager cluster.Configurera ett data lager kluster                           |
| Data lager. AllocateSpace                                                    | Data lager. AllocateSpace                                                    |
| Data lager. browse-datalager                                                 | Data lager. browse-datalager                                                 |
| Data lager för fil åtgärder på låg nivå                                        | Data lager för fil åtgärder på låg nivå                                        |
| Global. inaktivera metoder                                                     | Global. inaktivera metoder                                                     |
| Globala. Aktivera metoder                                                      | Globala. Aktivera metoder                                                      |
| Globala. licenser                                                            | Globala. licenser                                                            |
| Global. log-händelse                                                           | Global. log-händelse                                                           |
| Global. Hantera anpassade attribut                                            | Global. Hantera anpassade attribut                                            |
| Globalt. Ange anpassat attribut                                                | Globalt. Ange anpassat attribut                                                |
| Värd. local-åtgärder. Skapa virtuell dator                               | Värd. local-åtgärder. Skapa virtuell dator                               |
| Nätverk. tilldela nätverk                                                     | Nätverk. tilldela nätverk                                                     |
| Klusterresursen. Tilldela en virtuell dator till en resurspool                          | Klusterresursen. Tilldela en virtuell dator till en resurspool                          |
| vApp. Lägg till virtuell dator                                                   | vApp. Lägg till virtuell dator                                                   |
| vApp. tilldela resurspool                                                  | vApp. tilldela resurspool                                                  |
| vApp. avregistrera                                                            | vApp. avregistrera                                                            |
| VirtualMachine.Configuration. Lägg till eller ta bort enhet                         | VirtualMachine.Configuration. Lägg till eller ta bort enhet                         |
| Virtuell machine.Configuration. Disk lån                                   | Virtuell machine.Configuration. Förvärva disk lån                           |
| Virtuell machine.Configuration. Lägg till ny disk                                 | Virtuell machine.Configuration. Lägg till ny disk                                 |
| Virtuell machine.Configuration. Erfar                                     | Virtuell machine.Configuration. Avancerad konfiguration                       |
| Virtuell machine.Configuration. Disk ändrings spårning                         | Virtuell machine.Configuration. Växla spårning av disk ändring                  |
| Virtuell machine.Configuration. Värd USB-enhet                              | Virtuell machine.Configuration.Configurera-värd USB-enhet                    |
| Virtuell machine.Configuration. Utöka virtuell disk                          | Virtuell machine.Configuration. Utöka virtuell disk                          |
| Virtuell machine.Configuration. Fråga efter filer som inte ägs                          | Virtuell machine.Configuration. Fråga efter filer som inte ägs                          |
| Virtuell machine.Configuration. Swapfile placering                           | Virtuell machine.Configuration. Ändra swapfile placering                    |
| Virtuell dator. Gäst verksamhet. körning av gäst åtgärds program         | Virtuell dator. Gäst verksamhet. körning av gäst åtgärds program         |
| Virtuell dator. Gäst åtgärder. ändringar i gäst åtgärden             | Virtuell dator. Gäst åtgärder. ändringar i gäst åtgärden             |
| Virtuell dator. Gäst verksamhet. frågor om gäst åtgärder                   | Virtuell dator. Gäst verksamhet. frågor om gäst åtgärder                   |
| Virtuell dator. Interaktion. Enhets anslutning                            | Virtuell dator. Interaktion. Enhets anslutning                            |
| Virtuell dator. Interaktion. Hantering av gäst operativ system med VIX-API | Virtuell dator. Interaktion. Hantering av gäst operativ system med VIX-API |
| Virtuell dator. Interaktion. Stäng av                                    | Virtuell dator. Interaktion. Stäng av                                    |
| Virtuell dator. Inventering. Skapa ny                                      | Virtuell dator. Inventering. Skapa ny                                      |
| Virtuell dator. Inventering. ta bort                                          | Virtuell dator. Inventering. ta bort                                          |
| Virtuell dator. Inventering. register                                        | Virtuell dator. Inventering. register                                        |
| Virtuell dator. Etablering. Tillåt disk åtkomst                            | Virtuell dator. Etablering. Tillåt disk åtkomst                            |
| Virtuell dator. Etablering. Tillåt fil åtkomst                            | Virtuell dator. Etablering. Tillåt fil åtkomst                            |
| Virtuell dator. Etablering. Tillåt skrivskyddad åtkomst till disk                  | Virtuell dator. Etablering. Tillåt skrivskyddad åtkomst till disk                  |
| Virtuell dator. Etablering. Tillåt nedladdning av virtuell dator               | Virtuell dator. Etablering. Tillåt nedladdning av virtuell dator               |
| Virtuell dator. Ögonblicks bilds hantering. Skapa en ögonblicksbild                      | Virtuell dator. Ögonblicks bilds hantering. Skapa en ögonblicksbild                      |
| Virtuell dator. Ögonblicks bilds hantering. Ta bort ögonblicks bild                       | Virtuell dator. Ögonblicks bilds hantering. Ta bort ögonblicks bild                       |
| Virtuell dator. Ögonblicks bilds hantering. Återgå till ögonblicks bild                    | Virtuell dator. Ögonblicks bilds hantering. Återgå till ögonblicks bild                    |

> [!NOTE]
> I följande tabell visas privilegierna för vCenter 6,0 och vCenter 5,5-användarkonton.

| Behörigheter för vCenter 6,0-användar konto | Behörigheter för vCenter 5,5-användar konto |
| --- | --- |
| Data lager. AllocateSpace | Nätverk. tilldela |
| Global. Hantera anpassade attribut | Data lager. AllocateSpace |
| Globalt. Ange anpassat attribut | VirtualMachine.Config. ChangeTracking |
| Värd. local-åtgärder. Skapa virtuell dator | VirtualMachine. State. RemoveSnapshot |
| Nätverks. Tilldela nätverk | VirtualMachine. State. CreateSnapshot |
| Klusterresursen. Tilldela en virtuell dator till en resurspool | VirtualMachine. Provisioning. DiskRandomRead |
| Virtuell machine.Configuration. Lägg till ny disk | VirtualMachine. interagera. avstängnings läge |
| Virtuell machine.Configuration. Erfar | VirtualMachine. Inventory. Create |
| Virtuell machine.Configuration. Disk ändrings spårning | VirtualMachine.Config. AddNewDisk |
| Virtuell machine.Configuration. Värd USB-enhet | VirtualMachine.Config. HostUSBDevice |
| Virtuell machine.Configuration. Fråga efter filer som inte ägs | VirtualMachine.Config. AdvancedConfig |
| Virtuell machine.Configuration. Swapfile placering | VirtualMachine.Config. SwapPlacement |
| Virtuell dator. Interaktion. Stäng av | Global. ManageCustomFields |
| Virtuell dator. Hantering. Skapa ny |   |
| Virtuell dator. Etablering. Tillåt disk åtkomst |   |
| Virtuell dator. Etablerings. Tillåt skrivskyddad disk åtkomst |   |
| Virtuell dator. Ögonblicks bilds hantering. Skapa ögonblicks bild |   |
| Virtuell dator. Ögonblicks bilds hantering. Ta bort ögonblicks bild |   |

## <a name="create-a-vmware-account"></a>Skapa ett VMware-konto

1. I panelen vCenter Server **Navigator** väljer **du användare och grupper**. Om du inte använder vCenter Server skapar du kontot på lämplig ESXi-värd.

    ![Alternativet användare och grupper](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Panelen **vCenter-användare och-grupper** visas.

2. Välj fliken **användare** i panelen **vCenter-användare och grupper** och välj sedan ikonen Lägg till användare (symbolen +).

    ![panelen vCenter-användare och grupper](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. I dialog rutan **ny användare** lägger du till användar informationen > **OK**. I den här proceduren är användar namnet BackupAdmin.

    ![Dialog rutan ny användare](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Om du vill associera användar kontot med rollen går du till panelen **Navigator** och väljer **globala behörigheter**. Välj fliken **Hantera** i panelen **globala behörigheter** och välj sedan ikonen Lägg till (symbolen +).

    ![Panelen globala behörigheter](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. I **global behörighet rot – Lägg till behörighet**väljer du **Lägg till** för att välja användare eller grupp.

    ![Välj användare eller grupp](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. I **Välj användare/grupper**väljer du **BackupAdmin**  >  **Lägg till**. I **användare**används formatet *domän \ användar namn* för användar kontot. Om du vill använda en annan domän väljer du den i listan **domän** . Välj **OK** för att lägga till de valda användarna i dialog rutan **Lägg till behörighet** .

    ![Lägg till BackupAdmin-användare](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. I den **tilldelade rollen**väljer du **BackupAdminRole**  >  **OK**i list rutan.

    ![Tilldela användare till roll](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

På fliken **Hantera** i panelen **globala behörigheter** visas det nya användar kontot och den associerade rollen i listan.

## <a name="add-the-account-on-azure-backup-server"></a>Lägg till kontot på Azure Backup Server

1. Öppna Azure Backup Server. Om du inte hittar ikonen på Skriv bordet öppnar du Microsoft Azure Backup från listan appar.

    ![Azure Backup Server ikon](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. I Azure Backup Server-konsolen väljer du **hanterings**  >   **produktions servrar**  >  **Hantera VMware**.

    ![Azure Backup Server-konsol](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. I dialog rutan **Hantera autentiseringsuppgifter** väljer du **Lägg till**.

    ![Dialog rutan Hantera autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. I **Lägg till autentiseringsuppgift**anger du ett namn och en beskrivning för den nya autentiseringsuppgiften och anger det användar namn och lösen ord som du definierade på VMware-servern. Namnet, *contoso vCenter-autentiseringsuppgiften* används för att identifiera autentiseringsuppgifterna i den här proceduren. Om VMware-servern och Azure Backup Server inte finns i samma domän anger du domänen i användar namnet.

    ![Dialog rutan Azure Backup Server Lägg till autentiseringsuppgift](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Välj **Lägg** till för att lägga till den nya autentiseringsuppgiften.

    ![Lägg till nya autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Lägg till vCenter Server

Lägg till vCenter Server i Azure Backup Server.

1. I Azure Backup Server-konsolen väljer du **hanterings**  >  **produktions servrar**  >  **Lägg till**.

    ![Öppna guiden för att lägga till produktions server](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. I **guiden**för att lägga till produktions servrar  >  **väljer du sidan typ av produktions server** , väljer **VMware-servrar**och väljer sedan **Nästa**.

    ![Guiden för att lägga till produktions server](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. Ange FQDN eller IP-adressen för VMware-servern i **Välj datorer**  **Server namn/IP-adress**. Om alla ESXi-servrar hanteras av samma vCenter anger du vCenter-namnet. Annars lägger du till ESXi-värden.

    ![Ange VMware-Server](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. I **SSL-port**anger du den port som används för att kommunicera med VMware-servern. 443 är standard porten, men du kan ändra den om din VMware-Server lyssnar på en annan port.

5. I **Ange autentiseringsuppgift**väljer du de autentiseringsuppgifter som du skapade tidigare.

    ![Ange autentiseringsuppgift](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Välj **Lägg** till för att lägga till VMware-servern i listan servrar. Välj sedan **Nästa**.

    ![Lägg till VMWare-Server och autentiseringsuppgift](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. På sidan **Sammanfattning** väljer du **Lägg** till för att lägga till VMware-servern i Azure Backup Server. Den nya servern läggs till omedelbart, ingen agent behövs på VMware-servern.

    ![Lägg till VMware-Server i Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Kontrol lera inställningarna på sidan **Slutför** .

   ![Sidan Slutför](./media/backup-azure-backup-server-vmware/summary-screen.png)

Om du har flera ESXi-värdar som inte hanteras av vCenter Server eller om du har flera instanser av vCenter Server måste du köra guiden igen för att lägga till servrarna.

## <a name="configure-a-protection-group"></a>Konfigurera en skydds grupp

Lägg till virtuella VMware-datorer för säkerhets kopiering. Skydds grupper samlar in flera virtuella datorer och tillämpar samma data lagrings-och säkerhets kopierings inställningar för alla virtuella datorer i gruppen.

1. I Azure Backup Server-konsolen väljer du **skydd**> **ny**.

    ![Öppna guiden Skapa ny skydds grupp](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. På Välkomst sidan **Skapa ny skydds grupp** väljer du **Nästa**.

    ![Dialog rutan skapa ny skydds grupp guide](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Välj **servrar** på sidan **Välj typ av skydds grupp** och välj sedan **Nästa**. Sidan **Välj grupp medlemmar** visas.

1. I **Välj grupp medlemmar**väljer du de virtuella datorer (eller VM-mappar) som du vill säkerhetskopiera. Välj sedan **Nästa**.

    - När du väljer en mapp, eller virtuella datorer eller mappar i mappen, väljs även för säkerhets kopiering. Du kan avmarkera mappar och virtuella datorer som du inte vill säkerhetskopiera.
1. Om en virtuell dator eller mapp redan säkerhets kopie ras kan du inte välja den. Detta säkerställer att dubbla återställnings punkter inte skapas för en virtuell dator.

    ![Välj grupp medlemmar](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. På sidan **Välj data skydds metod** anger du ett namn på skydds gruppen och skydds inställningarna. Om du vill säkerhetskopiera till Azure ställer du in kortsiktigt skydd på **disk** och aktiverar onlineskydd. Välj sedan **Nästa**.

    ![Välj dataskyddsmetod](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. I **Ange kortsiktiga mål**anger du hur länge du vill behålla säkerhetskopierade data till disk.
   - I **kvarhållningsintervall**anger du hur många dagar disk återställnings punkter ska behållas.
   - I **Synkroniseringsfrekvens**anger du hur ofta disk återställnings punkter ska tas.
       - Om du inte vill ange ett intervall för säkerhets kopiering kan du kontrol lera **precis innan en återställnings punkt** så att en säkerhets kopiering körs precis innan varje återställnings punkt schemaläggs.
       - Kortsiktiga säkerhets kopieringar är fullständiga säkerhets kopieringar och inte stegvisa.
       - Välj **ändra** för att ändra de tidpunkter/datum då kortsiktiga säkerhets kopieringar sker.

         ![Ange kortsiktiga mål](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Granska disk utrymmet för VM-säkerhetskopieringarna i **Granska diskallokering**. för de virtuella datorerna.

   - De rekommenderade disk tilldelningarna baseras på det kvarhållningsintervall du angav, typ av arbets belastning och storleken på skyddade data. Gör nödvändiga ändringar och välj sedan **Nästa**.
   - **Data storlek:** Storlek på data i skydds gruppen.
   - **Disk utrymme:** Den rekommenderade mängden disk utrymme för skydds gruppen. Om du vill ändra den här inställningen bör du allokera totalt utrymme som är något större än det belopp som du uppskattar att varje data källa växer.
   - **Samplacera data:** Om du aktiverar samplacering kan flera data källor i skyddet mappas till en enda replik och återställnings punkt volym. Samplacering stöds inte för alla arbets belastningar.
   - **Utöka automatiskt:** Om du aktiverar den här inställningen och data i den skyddade gruppen utökar den inledande allokeringen, försöker Azure Backup Server öka disk storleken med 25 procent.
   - **Information om lagringspool:** Visar status för lagringspoolen, inklusive total och återstående disk storlek.

    ![Granska diskallokering](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. På sidan **Välj metod för skapande av replik** anger du hur du vill att den första säkerhets kopieringen ska utföras och väljer sedan **Nästa**.
   - Standardvärdet sker **automatiskt över nätverket** och **nu**.
   - Om du använder standardvärdet rekommenderar vi att du anger en tid med låg belastning. Välj **senare** och ange dag och tid.
   - Överväg att replikera data offline med hjälp av flyttbara media för stora mängder data eller mindre än optimala nätverks förhållanden.

    ![Välj metod för skapande av replik](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. I **alternativ för konsekvens kontroll**väljer du hur och när du vill automatisera konsekvens kontroller. Välj sedan **Nästa**.
      - Du kan köra konsekvens kontroller när replik data blir inkonsekventa eller enligt ett angivet schema.
      - Om du inte vill konfigurera automatiska konsekvens kontroller kan du köra en manuell kontroll. Det gör du genom att högerklicka på skydds gruppen > **utföra konsekvens kontroll**.

1. På sidan **Ange online skydds data** väljer du de virtuella datorer eller VM-mappar som du vill säkerhetskopiera. Du kan välja medlemmar individuellt, eller välja **alla** medlemmar för att välja alla medlemmar. Välj sedan **Nästa**.

    ![Ange skydds data online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. På sidan **Ange schema för onlinesäkerhetskopiering** anger du hur ofta du vill säkerhetskopiera data från lokal lagring till Azure.

    - Moln återställnings punkter för data kommer att genereras enligt schemat. Välj sedan **Nästa**.
    - När återställnings punkten har skapats överförs den till Recovery Services valvet i Azure.

    ![Ange schemat för onlinesäkerhetskopiering](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. På sidan **Ange princip för kvarhållning av online** anger du hur länge du vill behålla återställnings punkterna som skapas med säkerhets kopieringar per dag/vecka/månad/år till Azure. Välj sedan **Nästa**.

    - Det finns ingen tids gräns för hur länge du kan lagra data i Azure.
    - Den enda begränsningen är att du inte kan ha mer än 9999 återställnings punkter per skyddad instans. I det här exemplet är den skyddade instansen VMware-servern.

    ![Ange bevarande princip online](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. På sidan **Sammanfattning** granskar du inställningarna och väljer sedan **Skapa grupp**.

    ![Skydds grupp medlem och inställnings Sammanfattning](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-parallel-backups"></a>Parallella VMware-säkerhetskopieringar

>[!NOTE]
> Den här funktionen gäller för MABS v3-UR1.

I tidigare versioner av MABS utfördes parallella säkerhets kopieringar endast över skydds grupper. Med MABS v3-UR1 är alla dina virtuella VMWare-VM: er i en enda skydds grupp parallella, vilket leder till snabbare VM-säkerhetskopieringar. Alla VMWare delta-migreringsjobb körs parallellt. Som standard är antalet jobb som ska köras parallellt inställt på 8.

Du kan ändra antalet jobb genom att använda register nyckeln enligt nedan (visas inte som standard, du måste lägga till det):

**Nyckel Sök väg**: `Software\Microsoft\Microsoft Data Protection Manager\Configuration\ MaxParallelIncrementalJobs\VMWare`<BR>
**Nyckel typ**: DWORD-värde (32-bit).

> [!NOTE]
> Du kan ändra antalet jobb till ett högre värde. Om du ställer in jobb numret på 1 körs replikeringsdata seriellt. Om du vill öka antalet till ett högre värde måste du tänka på VMWare-prestandan. Överväg att använda det antal resurser som används och ytterligare användning som krävs på VMWare vSphere-servern och fastställa antalet delta-replikeringar som ska köras parallellt. Den här ändringen påverkar även de nyligen skapade skydds grupperna. För befintliga skydds grupper måste du tillfälligt lägga till en annan virtuell dator i skydds gruppen. Detta bör uppdatera skydds gruppens konfiguration enligt detta. Du kan ta bort den här virtuella datorn från skydds gruppen när proceduren har slutförts.

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

Om du vill säkerhetskopiera vSphere 6,7 gör du följande:

- Aktivera TLS 1,2 på DPM-servern

>[!NOTE]
>VMWare 6,7 och tidigare hade TLS aktiverat som kommunikations protokoll.

- Ange register nycklarna enligt följande:

```text
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001
```

## <a name="exclude-disk-from-vmware-vm-backup"></a>Undanta disk från virtuell VMware-säkerhetskopiering

> [!NOTE]
> Den här funktionen gäller för MABS v3-UR1.

Med MABS v3-UR1 kan du undanta den angivna disken från VMware VM backup. Konfigurations skriptet **ExcludeDisk.ps1** finns i `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin folder` .

Följ stegen nedan om du vill konfigurera en disk undantags åtgärd:

### <a name="identify-the-vmware-vm-and-disk-details-to-be-excluded"></a>Identifiera den virtuella VMWare-dator och disk information som ska uteslutas

  1. I VMware-konsolen går du till VM-inställningar för vilka du vill undanta disken.
  2. Välj den disk som du vill undanta och anteckna sökvägen till disken.

        Om du till exempel vill utesluta hård disken 2 från TestVM4 är sökvägen till hård disk 2 **[datastore1] TestVM4/TestVM4 \_ 1. vmdk**.

        ![Hård disk som ska uteslutas](./media/backup-azure-backup-server-vmware/test-vm.png)

### <a name="configure-mabs-server"></a>Konfigurera MABS-Server

Navigera till MABS-servern där den virtuella VMware-datorn har kon figurer ATS för skydd för att konfigurera disk undantag.

  1. Hämta information om VMware-värden som är skyddad på MABS-servern.

        ```powershell
        $psInfo = get-DPMProductionServer
        $psInfo
        ```

        ```output
        ServerName   ClusterName     Domain            ServerProtectionState
        ----------   -----------     ------            ---------------------
        Vcentervm1                   Contoso.COM       NoDatasourcesProtected
        ```

  2. Välj VMware-värden och lista VM-skyddet för VMware-värden.

        ```powershell
        $vmDsInfo = get-DPMDatasource -ProductionServer $psInfo[0] -Inquire
        $vmDsInfo
        ```

        ```output
        Computer     Name     ObjectType
        --------     ----     ----------
        Vcentervm1  TestVM2      VMware
        Vcentervm1  TestVM1      VMware
        Vcentervm1  TestVM4      VMware
        ```

  3. Välj den virtuella dator som du vill undanta en disk för.

        ```powershell
        $vmDsInfo[2]
        ```

        ```output
        Computer     Name      ObjectType
        --------     ----      ----------
        Vcentervm1   TestVM4   VMware
        ```

  4. Om du vill utesluta disken navigerar du till `Bin` mappen och kör *ExcludeDisk.ps1* -skriptet med följande parametrar:

        > [!NOTE]
        > Stoppa DPMRA-tjänsten på MABS-servern innan du kör det här kommandot. Annars returnerar skriptet lyckades, men uppdaterar inte undantags listan. Se till att inga jobb pågår innan du stoppar tjänsten.

     **Kör följande kommando för att lägga till/ta bort disken från undantag:**

      ```powershell
      ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-Add|Remove] "[Datastore] vmdk/vmdk.vmdk"
      ```

     **Exempel**:

     Om du vill lägga till disk undantag för TestVM4 kör du följande kommando:

       ```powershell
      C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Add "[datastore1] TestVM4/TestVM4\_1.vmdk"
       ```

      ```output
       Creating C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\excludedisk.xml
       Disk : [datastore1] TestVM4/TestVM4\_1.vmdk, has been added to disk exclusion list.
      ```

  5. Kontrol lera att disken har lagts till för undantag.

     **Om du vill visa befintliga undantag för vissa virtuella datorer kör du följande kommando:**

        ```powershell
        ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-view]
        ```

     **Exempel**

        ```powershell
        C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -view
        ```

        ```output
        <VirtualMachine>
        <UUID>52b2b1b6-5a74-1359-a0a5-1c3627c7b96a</UUID>
        <ExcludeDisk>[datastore1] TestVM4/TestVM4\_1.vmdk</ExcludeDisk>
        </VirtualMachine>
        ```

     När du har konfigurerat skyddet för den här virtuella datorn visas inte den uteslutna disken under skyddet.

        > [!NOTE]
        > Om du utför de här stegen för en redan skyddad virtuell dator måste du köra konsekvens kontrollen manuellt när du har lagt till disken för undantag.

### <a name="remove-the-disk-from-exclusion"></a>Ta bort disken från undantag

Om du vill ta bort disken från undantag kör du följande kommando:

```powershell
C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Remove "[datastore1] TestVM4/TestVM4\_1.vmdk"
```

## <a name="next-steps"></a>Nästa steg

Information om fel sökning av problem när du konfigurerar säkerhets kopieringar finns i [fel söknings guiden för Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
