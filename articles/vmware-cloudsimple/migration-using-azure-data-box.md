---
title: Azure VMware Solution – migrering med hjälp av Azure Data Box
description: Hur du använder Azure Data Box för att mass migrera data till Azure VMware Solution.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741938"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migrera data till Azure VMware Solution med hjälp av Azure Data Box

Med Microsoft Azure Data Box-enhet molnlösningen kan du skicka terabyte (TB) data till Azure på ett snabbt, prisvärt och tillförlitligt sätt. Den säkra dataöverföringen påskyndas genom att skicka en upphovsrättsskyddad Data Box-lagringsenhet. Varje lagringsenhet har en maximal användbar lagringskapacitet på 80 TB och transporteras till ditt datacenter av ett regionalt transportföretag. Enheten har ett robust hölje för att skydda dina data under överföringen.

Genom att Data Box-enhet kan du mass migrera dina VMware-data till ditt privata moln. Data från din VMware vSphere miljö kopieras till Data Box-enhet NFS-protokollet (Network File System). Massmigrering av data innebär att du sparar en kopia till en tidpunkt av virtuella datorer, konfiguration och associerade data till Data Box-enhet och sedan skickar den manuellt till Azure.

I den här artikeln får du lära dig mer om:

* Konfigurera Data Box-enhet.
* Kopiera data från den lokala VMware-miljön till Data Box-enhet via NFS.
* Förbereda för retur av Data Box-enhet.
* Förbereda blobdata för kopiering till Azure VMware Solution.
* Kopiera data från Azure till ditt privata moln.

## <a name="scenarios"></a>Scenarier

Använd Data Box-enhet i följande scenarier för massmigrering av data:

* För att migrera en stor mängd data från lokal plats till Azure VMware Solution. Den här metoden upprättar en baslinje och synkroniserar skillnader över nätverket.
* Migrera ett stort antal virtuella datorer som är avstängda (kalla virtuella datorer).
* Migrera data för virtuella datorer för att konfigurera utvecklings- och testmiljöer.
* Migrera ett stort antal mallar för virtuella datorer, ISO-filer och virtuella datordiskar.

## <a name="before-you-begin"></a>Innan du börjar

* Kontrollera förutsättningarna och [order Data Box-enhet](../databox/data-box-deploy-ordered.md) genom Azure Portal. Under orderprocessen måste du välja ett lagringskonto som aktiverar Blob Storage. När du har fått Data Box-enhet-enheten ansluter du den till [](../databox/data-box-deploy-set-up.md) ditt lokala nätverk och ställer in enheten med en IP-adress som kan nås från vSphere-hanteringsnätverket.

* Skapa ett virtuellt nätverk och ett lagringskonto i samma region där Azure VMware Solution har etablerats.

* Skapa en [virtuell Azure-nätverksanslutning](cloudsimple-azure-network-connection.md) från ditt privata moln till det virtuella nätverket där lagringskontot skapas genom att följa stegen i Ansluta ett virtuellt Azure-nätverk till [CloudSimple med ExpressRoute.](virtual-network-connection.md)

## <a name="set-up-data-box-for-nfs"></a>Konfigurera Data Box-enhet för NFS

Anslut till Data Box-enhet lokala webbgränssnittet genom att följa stegen i avsnittet "Anslut till din enhet" i [Självstudie: Koppla](../databox/data-box-deploy-set-up.md)in och ansluta till Azure Data Box .  Konfigurera Data Box-enhet att tillåta åtkomst till NFS-klienter:

1. I det lokala webbgränssnittet går du till **sidan Anslut och** kopiera. Under **NFS-inställningar** väljer du **NFS-klientåtkomst.** 

    ![Konfigurera NFS-klientåtkomst 1](media/nfs-client-access.png)

2. Ange IP-adressen för de VMware ESXi värdarna och välj Lägg **till**. Du kan konfigurera åtkomst för alla värdar i vSphere-klustret genom att upprepa det här steget. Välj **OK**.

    ![Konfigurera NFS-klientåtkomst 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *rotmappen* i lagringskontot.

Under blockblob- och sidblobresurser är entiteter på första nivån containrar och entiteter på andra nivån är blobar. Under resurser för Azure Files är entiteter på första nivån resurser och entiteter på andra nivån är filer.

I följande tabell visas UNC-sökvägen till filresurser på din Data Box och Azure Storage-sökvägens URL som data har överförts till. URL:en till den sista Azure Storage-sökvägen kan härledas från sökvägen till UNC-resursen.
 
| Blobar och filer | Sökväg och URL |
|---------------- | ------------ |
| Azure Block blobs | <li>UNC-sökväg till resurser: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-sidblobar  | <li>UNC-sökväg till resurser: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-sökväg till resurser: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Använd Azure Block-blobar för att kopiera VMware-data.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Montera NFS-resursen som ett datalager i ditt lokala vCenter-kluster och kopiera data

NFS-resursen från din Data Box-enhet måste monteras som ett datalager i ditt lokala vCenter-kluster eller VMware ESXi-värden för att kunna kopiera data till NFS-datalagringen:

1. Logga in på din lokala vCenter-server.

2. Högerklicka på **Datacenter,** välj **Lagring,** **välj Nytt datalager** och välj sedan **Nästa.**

   ![Lägg till nytt datalager](media/databox-migration-add-datastore.png)

3. I steg 1 i guiden Lägg till datalager väljer du **NFS** under **Typ.**

   ![Lägg till nytt datalager – typ](media/databox-migration-add-datastore-type.png)

4. I steg 2 i guiden väljer du **NFS 3** som NFS-version och sedan **Nästa.**

   ![Lägg till nytt datalager – NFS-version](media/databox-migration-add-datastore-nfs-version.png)

5. I steg 3 i guiden anger du namnet på datalagringen, sökvägen och servern. Du kan använda IP-adressen för Data Box-enhet för servern. Mappsökvägen har `/<StorageAccountName_BlockBlob>/<ContainerName>/` formatet .

   ![Lägg till nytt datalager – NFS-konfiguration](media/databox-migration-add-datastore-nfs-configuration.png)

6. I steg 4 i guiden väljer du de ESXi-värdar där du vill att datalagringen ska monteras och väljer sedan **Nästa.**  I ett kluster väljer du alla värdar för att säkerställa migreringen av de virtuella datorerna.

   ![Lägg till nytt datalager – Välj värdar](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. I steg 5 i guiden granskar du sammanfattningen och väljer **Slutför**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Kopiera data till Data Box-enhet NFS-datalager

Virtuella datorer kan migreras eller klonas till det nya datalagringscentret.  Alla oanvända virtuella datorer som du vill migrera kan migreras till Data Box-enhet NFS-datalager med hjälp av **storage vMotion-alternativet.** Aktiva virtuella datorer kan klonas till Data Box-enhet NFS-datalager.

* Identifiera och lista de virtuella datorer som kan **flyttas**.
* Identifiera och lista de virtuella datorer som måste **klonas.**

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Flytta en virtuell dator till ett Data Box-enhet datalager

1. Högerklicka på den virtuella dator som du vill flytta till Data Box-enhet datalager och välj sedan **Migrera**.

    ![Migrera virtuell dator](media/databox-migration-vm-migrate.png)

2. Välj **Ändra endast lagring** som migreringstyp och välj sedan **Nästa.**

    ![Migrera virtuell dator – endast lagring](media/databox-migration-vm-migrate-change-storage.png)

3. Välj **Databox-Datastore** som mål och välj sedan **Nästa.**

    ![Migrera virtuell dator – välj datalager](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Granska informationen och välj **Slutför.**

5. Upprepa steg 1 till 4 för ytterligare virtuella datorer.

> [!TIP]
> Du kan välja flera virtuella datorer som har samma energisparläge (påslagna eller avstängda) och migrera dem gruppindelad.

Den virtuella datorn migreras till NFS-datalagringen från Data Box-enhet. När alla virtuella datorer har migrerats kan du stänga av (stänga av) de aktiva virtuella datorerna som förberedelse för migrering av data till Azure VMware Solution.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Klona en virtuell dator eller en mall för virtuella datorer till Data Box-enhet datalager

1. Högerklicka på en virtuell dator eller en mall för virtuell dator som du vill klona. Välj   >  **Klona klona till virtuell dator.**

    ![Kloning av virtuell dator](media/databox-migration-vm-clone.png)

2. Välj ett namn för den klonade virtuella datorn eller mallen för virtuella datorer.

3. Välj den mapp där du vill placera det klonade objektet och välj sedan **Nästa.**

4. Välj det kluster eller den resurspool där du vill placera det klonade objektet och välj sedan **Nästa.**

5. Välj **Databox-Datastore** som lagringsplats och välj sedan **Nästa.**

    ![Klona virtuell dator – välj datalager](media/databox-migration-vm-clone-select-datastore.png)

6. Om du vill anpassa alternativ för det klonade objektet väljer du anpassningsalternativen och sedan **Nästa.**

7. Granska konfigurationerna och välj **Slutför**.

8. Upprepa steg 1 till 7 för ytterligare virtuella datorer eller mallar för virtuella datorer.

Virtuella datorer klonas och lagras i NFS-dataarkivet från Data Box-enhet. När de virtuella datorerna har klonas kontrollerar du att de är avstängda inför migreringen av data till Azure VMware Solution.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Kopiera ISO-filer Data Box-enhet datalager

1. Från ditt lokala vCenter-webbgränssnitt går du till **Storage**.  Välj **Databox-Datastore** och välj sedan **Filer.** Skapa en ny mapp för lagring av ISO-filer.

    ![Kopiera ISO – skapa ny mapp](media/databox-migration-create-folder.png)

2. Ange ett namn för mappen där ISO-filer ska lagras.

3. Dubbelklicka på den nyligen skapade mappen för att öppna den.

4. Välj **Ladda upp filer** och välj sedan de ISO-filer som du vill ladda upp.
    
    ![Kopiera ISO – ladda upp filer](media/databox-migration-upload-iso.png)

> [!TIP]
> Om du redan har ISO-filer i ditt lokala datalager  kan du välja filerna och Kopiera till för att kopiera filerna till Data Box-enhet NFS-datalager.


## <a name="prepare-data-box-for-return"></a>Förbereda Data Box-enhet för retur

När alla data för virtuella datorer, malldata för virtuella datorer och ISO-filer har kopierats till Data Box-enhet NFS-datalager kan du koppla från datalagringen från ditt vCenter. Alla virtuella datorer och mallar för virtuella datorer måste tas bort från lagret innan du kopplar från datalagret.

### <a name="remove-objects-from-inventory"></a>Ta bort objekt från inventeringen

1. Från ditt lokala vCenter-webbgränssnitt går du till **Storage**. Välj **Databox-Datastore** och välj sedan **Virtuella datorer.**

    ![Ta bort virtuella datorer från inventeringen – inaktiverat](media/databox-migration-select-databox-vm.png)

2. Kontrollera att alla virtuella datorer är avstängda.

3. Markera alla virtuella datorer, högerklicka och välj sedan Ta **bort från inventeringen.**

    ![Ta bort virtuella datorer från inventeringen](media/databox-migration-remove-vm-from-inventory.png)

4. Välj **VM-mallar i mappar** och upprepa sedan steg 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Ta bort Data Box-enhet NFS-datalager från vCenter

Den Data Box-enhet NFS-datalager måste kopplas från VMware ESXi värdar innan du förbereder för retur.

1. Från ditt lokala vCenter-webbgränssnitt går du till **Storage**.

2. Högerklicka på **Databox-Datastore och** välj **Demontera datalager.**

    ![Demontera Data Box-enhet datalager](media/databox-migration-unmount-datastore.png)

3. Välj alla ESXi-värdar där datalagringen är monterad och välj **OK.**

    ![Demontera Data Box-enhet datalager – välj värdar](media/databox-migration-unmount-datastore-select-hosts.png)

4. Granska och acceptera eventuella varningar och välj **OK.**

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Förbered Data Box-enhet retur och returnera den sedan

Följ stegen i artikeln Return Azure Data Box och verifiera datauppladdningen [till Azure för](../databox/data-box-deploy-picked-up.md) att returnera Data Box-enhet. Kontrollera statusen för datakopian till ditt Azure Storage-konto. När statusen är klar kan du verifiera data i ditt Azure Storage-konto.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Kopiera data från Azure Storage till Azure VMware Solution

Data som kopieras till Data Box-enhet-enheten blir tillgängliga på ditt Azure-lagringskonto när orderstatusen för din Data Box-enhet visas som slutförd. Data kan nu kopieras till din Azure VMware Solution. Data i lagringskontot måste kopieras till det virtuella SAN-dataarkivet i ditt privata moln med hjälp av NFS-protokollet. 

Kopiera först Blob Storage-data till en hanterad disk på en virtuell Linux-dator i Azure med hjälp av **AzCopy**. Gör den hanterade disken tillgänglig via NFS, montera NFS-resursen som ett datalager i ditt privata moln och kopiera sedan data. Den här metoden möjliggör snabbare kopiering av data till ditt privata moln.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Kopiera data till ditt privata moln med en virtuell Linux-dator och hanterade diskar och exportera sedan som NFS-resurs

1. Skapa en [virtuell Linux-dator](../virtual-machines/linux/quick-create-portal.md) i Azure i samma region där ditt lagringskonto skapas och har en virtuell Nätverksanslutning i Azure till ditt privata moln.

2. Skapa en hanterad disk vars lagringskapacitet är större än mängden blobdata och koppla [den till den virtuella Linux-datorn](../virtual-machines/linux/attach-disk-portal.md).  Om mängden blobdata är större än kapaciteten hos den största tillgängliga hanterade disken, måste data kopieras i flera steg eller med hjälp av flera hanterade diskar.

3. Anslut till den virtuella Linux-datorn och montera den hanterade disken.

4. Installera [AzCopy på den virtuella Linux-datorn](../storage/common/storage-use-azcopy-v10.md).

5. Ladda ned data från Azure Blob Storage till den hanterade disken med hjälp av AzCopy.  Kommandosyntax: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` .  Ersätt `<storage-account-name>` med namnet på ditt Azure-lagringskonto och med `<container-name>` containern som innehåller de data som kopierats Data Box-enhet.

6. Installera NFS-servern på den virtuella Linux-datorn:

    - På en Ubuntu-/Debian-distribution: `sudo apt install nfs-kernel-server` .
    - På en Enterprise Linux-distribution: `sudo yum install nfs-utils` .

7. Ändra behörigheten för mappen på den hanterade disken där data från Azure Blob Storage kopierades.  Ändra behörigheterna för alla mappar som du vill exportera som en NFS-resurs.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Tilldela behörigheter för klientens IP-adresser för att få åtkomst till NFS-resursen genom att redigera `/etc/exports` filen.

    ```bash
    sudo vi /etc/exports
    ```
    
    Ange följande rader i filen för varje ESXi-värd-IP för ditt privata moln.  Om du skapar resurser för flera mappar lägger du till alla mappar.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exportera NFS-resurser med hjälp av `sudo exportfs -a` kommandot .

10. Starta om NFS-kernelservern med hjälp av `sudo systemctl restart nfs-kernel-server` kommandot .


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Montera NFS-resursen för den virtuella Linux-datorn som ett datalager i ett vCenter-kluster i ett privat moln och kopiera sedan data

NFS-resursen från din virtuella Linux-dator måste monteras som ett datalager i ditt privata moln-vCenter-kluster. När den har monterats kan data kopieras från NFS-datalagringen till det privata molnets vSAN-datalager.

1. Logga in på vCenter-servern för ditt privata moln.

2. Högerklicka på **Datacenter,** välj **Lagring,** **välj Nytt datalager** och välj sedan **Nästa.**

   ![Lägg till nytt datalager](media/databox-migration-add-datastore.png)

3. I steg 1 i guiden Lägg till datalager väljer du **NFS-typ.**

   ![Lägg till nytt datalager – typ](media/databox-migration-add-datastore-type.png)

4. I steg 2 i guiden väljer du **NFS 3** som NFS-version och sedan **Nästa.**

   ![Lägg till nytt datalager – NFS-version](media/databox-migration-add-datastore-nfs-version.png)

5. I steg 3 i guiden anger du namnet på datalagringen, sökvägen och servern.  Du kan använda IP-adressen för din virtuella Linux-dator för servern.  Mappsökvägen har `/<folder>/<subfolder>/` formatet .

   ![Lägg till nytt datalager – NFS-konfiguration](media/databox-migration-add-datastore-nfs-configuration.png)

6. I steg 4 i guiden väljer du de ESXi-värdar där du vill att datalagringen ska monteras och väljer sedan **Nästa.**  I ett kluster väljer du alla värdar för att säkerställa migreringen av de virtuella datorerna.

   ![Lägg till nytt datalager – Välj värdar](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. I steg 5 i guiden granskar du sammanfattningen och väljer sedan **Slutför**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Lägga till virtuella datorer och mallar för virtuella datorer från ett NFS-datalager i lagret

1. Från vCenter-webbgränssnittet för ditt privata moln går du till **Storage**.  Välj ett NFS-datalager för en virtuell Linux-dator och välj sedan **Filer.**

    ![Välja filer från NFS-datalager](media/databox-migration-datastore-select-files.png)

2. Välj en mapp som innehåller en virtuell dator eller en mall för virtuella datorer.  I informationsfönstret väljer du en .vmx-fil för en virtuell dator eller en .vmtx-fil för en mall för virtuella datorer.

3. Välj **Registrera virtuell dator** för att registrera den virtuella datorn i ditt privata moln vCenter.

    ![Registrera virtuell dator](media/databox-migration-datastore-register-vm.png)

4. Välj det datacenter, den mapp och den kluster-/resurspool där du vill att den virtuella datorn ska registreras.

4. Upprepa steg 3 och 4 för alla virtuella datorer och mallar för virtuella datorer.

5. Gå till mappen som innehåller ISO-filerna.  Välj ISO-filerna och välj sedan **Kopiera till för** att kopiera filerna till en mapp i ditt vSAN-datalager.

De virtuella datorerna och mallarna för virtuella datorer är nu tillgängliga i ditt privata moln vCenter. Dessa virtuella datorer måste flyttas från NFS-datalagringen till det virtuella SAN-dataarkivet innan du aktiverar dem. Du kan använda alternativet **storage vMotion** och välja det virtuella SAN-dataarkivet som mål för de virtuella datorerna.

Mallarna för virtuella datorer måste klonas från din virtuella Linux-dators NFS-datalager till ditt vSAN-datalager.

### <a name="clean-up-your-linux-virtual-machine"></a>Rensa din virtuella Linux-dator

När alla data har kopierats till ditt privata moln kan du ta bort NFS-datalagringen från ditt privata moln:

1. Se till att alla virtuella datorer och mallar flyttas och klonas till ditt vSAN-datalager.

2. Ta bort alla mallar för virtuella datorer från NFS-datalagret från inventeringen.

3. Demontera den virtuella Linux-datorns datalager från ditt privata moln vCenter.

4. Ta bort den virtuella datorn och den hanterade disken från Azure.

5. Om du inte vill behålla de data som överfördes av Data Box-enhet ditt lagringskonto tar du bort Azure Storage-kontot.  
    


## <a name="next-steps"></a>Nästa steg

* Läs mer om [att Data Box-enhet](../databox/data-box-overview.md).
* Läs mer om olika alternativ [för att migrera arbetsbelastningar till ditt privata moln.](migrate-workloads.md)
