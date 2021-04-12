---
title: Använda extern fil lagring i labb tjänster | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb som använder extern fil lagring i labb tjänster.
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: 888e04db76567051f8c5eae7cf94c77e684cb146
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112028"
---
# <a name="using-external-file-storage-in-lab-services"></a>Använda extern fil lagring i labb tjänster

Den här artikeln beskriver några av alternativen för extern fil lagring när du använder Azure Lab Services.  [Azure Files](https://azure.microsoft.com/services/storage/files/) erbjuder fullständigt hanterade fil resurser i molnet [tillgängligt via SMB 2,1 och SMB 3,0.](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)  En Azure Files resurs kan vara antingen offentligt eller privat inom ett virtuellt nätverk.  Det kan också konfigureras för att använda Students AD-autentiseringsuppgifter för att ansluta till fil resursen.  Att använda Azure NetApp Files med NFS-volymer för Linux-datorer är ett annat alternativ för extern fil lagring med Azure Lab Services.  

## <a name="deciding-which-solution-to-use"></a>Bestämma vilken lösning som ska användas

Varje lösning har olika krav och möjligheter.  I tabellen nedan visas viktiga punkter som du bör tänka på för varje lösning.  

|     Lösning    |    Viktigt att veta    |
| -------------- | ------------------------ |
| [Azure Files resurs med offentlig slut punkt](#azure-files-share) | <ul><li>Alla har Läs-/Skriv behörighet.</li><li>Ingen VNet-peering krävs.</li><li>Tillgänglig för alla virtuella datorer, inte bara för virtuella labb datorer.</li><li>Om du använder Linux får eleverna åtkomst till lagrings konto nyckeln.</li></ul> |
| [Azure Files resurs med privat slut punkt](#azure-files-share) | <ul><li>Alla har Läs-/Skriv behörighet.</li><li>VNet-peering krävs.</li><li>Endast tillgängligt för virtuella datorer i samma nätverk (eller peer-nätverk) som lagrings konto.</li><li>Om du använder Linux får eleverna åtkomst till lagrings konto nyckeln.</li></ul> |
| [Azure Files med Identity-Base-auktorisering](#azure-files-with-identity-base-authorization) | <ul><li>Antingen Läs-eller Läs-/Skriv behörighet kan ställas in för mapp eller fil.</li><li>VNet-peering krävs.</li><li>Lagrings kontot måste vara anslutet till Active Directory.</li><li>Virtuella labb datorer måste vara domänanslutna.</li><li>Lagrings konto nyckel används inte för studenter för att ansluta till fil resursen.</li></ul> |
| [NetApp-filer med NFS-volymer](#netapp-files-with-nfs-volumes) | <ul><li>Läs-eller Läs-/Skriv behörighet kan ställas in för volymer.</li><li>Behörigheter anges med den virtuella elevens IP-adress.</li><li>VNet-peering krävs.</li><li>Kan behöva registrera sig för att använda tjänsten NetApp Files.</li><li>Endast Linux.</li></ul>

Kostnaden för att använda extern lagring ingår inte i kostnaden för att använda Azure Lab Services.  Mer information om priser finns i [Azure Files priser](https://azure.microsoft.com/pricing/details/storage/files/) och [Azure NetApp Files priser](https://azure.microsoft.com/pricing/details/netapp/).

## <a name="azure-files-share"></a>Azure Files resurs

Azure-filresurser nås via en offentlig eller privat slut punkt.  Azure-filresurser monteras med lagrings konto nyckeln som lösen ord.  Med den här metoden har alla till gång till Läs-och Skriv behörighet till fil resursen.

Om du använder en offentlig slut punkt för Azure Files resursen är det viktigt att komma ihåg:

- Det virtuella nätverket för lagrings kontot behöver inte vara peer-kopplat till labb kontot.  Fil resursen kan skapas när den virtuella mallen har publicerats.
- Fil resursen kan nås från vilken dator som helst om en användare har lagrings konto nyckeln.  
- Linux-studenter kan se lagrings konto nyckeln.  Autentiseringsuppgifter för montering av en Azure Files resurs lagras `{file-share-name}.cred` på virtuella Linux-datorer och kan läsas av sudo.  Eftersom studenter får sudo åtkomst som standard i virtuella datorer i Azure Lab-tjänsten kan de läsa lagrings konto nyckeln.  Om lagrings kontots slut punkt är offentlig kan studenter få åtkomst till fil resursen utanför sin elev-VM.  Överväg att rotera lagrings konto nyckeln efter att klassen har slutat och använder privata fil resurser.

Om du använder en privat slut punkt för Azure Files resursen är det viktigt att komma ihåg:

- Åtkomst är begränsad till trafik som härstammar från det privata nätverket och kan inte nås via det offentliga Internet.  Endast virtuella datorer i det privata virtuella nätverket, virtuella datorer i ett nätverk som är peer-anslutna till det privata virtuella nätverket eller datorer som är anslutna till ett VPN för det privata nätverket kan komma åt fil resursen.  
- Linux-studenter kan se lagrings konto nyckeln.  Autentiseringsuppgifter för montering av en Azure Files resurs lagras `{file-share-name}.cred` på virtuella Linux-datorer och kan läsas av sudo.  Eftersom studenter får sudo åtkomst som standard i virtuella datorer i Azure Lab-tjänsten kan de läsa lagrings konto nyckeln.  Överväg att rotera lagrings konto nyckeln efter att klassen har upphört.
- Den här metoden kräver att fil resursens virtuella nätverk är peer-kopplas till labb kontot.  Det virtuella nätverket för det Azure Storage kontot måste peer-kopplas till det virtuella nätverket för labb kontot **innan** labbet skapas.

> [!NOTE]
> Fil resurser större än 5TB är bara tillgängliga för [[LRS-konton (lokalt redundant lagring)]](/azure/storage/files/storage-files-how-to-create-large-file-share#restrictions).

Följ dessa steg om du vill skapa en virtuell dator som är ansluten till en Azure-filresurs.

1. Skapa ett [Azure Storage-konto](/azure/storage/files/storage-how-to-create-file-share). På sidan anslutnings metod väljer du offentlig slut punkt eller privat slut punkt.
2. Om du använder skapar du en [privat slut punkt](/azure/private-link/create-private-endpoint-storage-portal) för att fil resurserna ska kunna nås från det virtuella nätverket.  Skapa en [privat DNS-zon](/azure/dns/private-dns-privatednszone) eller Använd en befintlig. Privata Azure DNS zoner ger namn matchning i ett virtuellt nätverk.
3. Skapa en [Azure-filresurs](/azure/storage/files/storage-how-to-create-file-share). Fil resursen kan kommas åt av det offentliga värd namnet för lagrings kontot.
4. Montera Azure-filresursen i mallen VM:
    - [Aktivitets](/azure/storage/files/storage-how-to-use-files-windows)
    - [[Linux]](/azure/storage/files/storage-how-to-use-files-linux).  Se [använda Azure Files med Linux](#using-azure-files-with-linux) för att undvika monterings problem på Students virtuella datorer.
5. [Publicera](how-to-create-manage-template.md#publish-the-template-vm) mallen VM.

> [!IMPORTANT]
> Kontrol lera att brand väggen inte blockerar utgående SMB-anslutning via port 445. Som standard tillåts SMB för virtuella Azure-datorer.

### <a name="using-azure-files-with-linux"></a>Använda Azure Files med Linux

Om du använder _standard instruktionerna_ för att montera en Azure-filresurs kommer fil resursen att försvinna på elevens virtuella datorer när mallen har publicerats.  Nedan ändras skript som åtgärdar det här problemet.  

```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Om den virtuella datorns mall redan har publicerats som monterar Azure-filresursen i `/mnt` katalogen, kan eleven göra något av följande.

- Flytta instruktionen för att montera `/mnt` högst upp i `/etc/fstab` filen.  
- Ändra instruktionen för att montera `/mnt/{file-share-name}` till en annan katalog som `/prm-mnt/{file-share-name}` .

Eleverna bör köra `mount -a` för att montera om kataloger.

Mer allmän information om hur du använder fil resurser med Linux finns i [använda Azure Files med Linux](/azure/storage/files/storage-how-to-use-files-linux).

## <a name="azure-files-with-identity-base-authorization"></a>Azure Files med Identity-Base-auktorisering

Azure Files resurser kan också nås med AD-autentisering om

1. Den virtuella elev datorn är domänansluten.
2. AD-autentisering är [aktiverat på det Azure Storage konto](/azure/storage/files/storage-files-active-directory-overview) som är värd för fil resursen.  

Nätverks enheten monteras på den virtuella datorn med hjälp av användarens identitet, inte nyckeln till lagrings kontot.  Åtkomst till lagrings kontot kan använda offentliga eller privata slut punkter.

Vi går igenom några viktiga punkter i den här metoden.

- Behörigheter kan ställas in på en katalog eller fil nivå.
- Aktuella användarautentiseringsuppgifter används för att autentisera till fil resursen.

Om du använder en offentlig slut punkt för Azure Files resursen är det viktigt att komma ihåg:

- Det virtuella nätverket för lagrings kontot behöver inte peer-koppla till labb kontot.  Fil resursen kan skapas när den virtuella mallen har publicerats.

Om du använder en privat slut punkt för Azure Files resursen är det viktigt att komma ihåg:

- Åtkomst är begränsad till trafik som härstammar från det privata nätverket och kan inte nås via det offentliga Internet.  Endast virtuella datorer i det privata virtuella nätverket, virtuella datorer i ett nätverk som är peer-anslutna till det privata virtuella nätverket eller datorer som är anslutna till ett VPN för det privata nätverket kan komma åt fil resursen.  
- Den här metoden kräver att fil resursens virtuella nätverk är peer-kopplas till labb kontot.  Det virtuella nätverket för det Azure Storage kontot måste peer-kopplas till det virtuella nätverket för labb kontot **innan** labbet skapas.

Följ stegen nedan för att skapa en AD-autentisering som är aktive rad Azure Files resurs och domän Anslut till virtuella labb datorer.

1. Skapa ett [Azure Storage-konto](/azure/storage/files/storage-how-to-create-file-share).
2. Om du använder skapar du en [privat slut punkt](/azure/private-link/create-private-endpoint-storage-portal) för att fil resurserna ska kunna nås från det virtuella nätverket.  Skapa en [privat DNS-zon](/azure/dns/private-dns-privatednszone) eller Använd en befintlig. Privata Azure DNS zoner ger namn matchning i ett virtuellt nätverk.
3. Skapa en [Azure-filresurs](/azure/storage/files/storage-how-to-create-file-share).
4. Följ stegen för att aktivera Identity-baserad auktorisering.  Om du använder lokal AD som är synkroniserad med Azure AD följer du stegen för [lokal Active Directory Domain Services autentisering över SMB för Azure-filresurser](/azure/storage/files/storage-files-identity-auth-active-directory-enable).  Om du bara använder Azure AD följer du stegen för att [aktivera Azure Active Directory Domain Services autentisering på Azure Files](/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable).
    >[!IMPORTANT]
    >Prata med teamet som hanterar din annons för att kontrol lera att alla krav som anges i anvisningarna är uppfyllda.
5. Tilldela behörighets roller för SMB-resurs i Azure.  Mer information om behörigheter som beviljas för varje roll finns i [behörigheter på resurs nivå](/azure/storage/files/storage-files-identity-ad-ds-assign-permissions).
    1. Rollen lagrings fil data SMB-resurs upphöjt bidrag måste tilldelas till den person eller grupp som ska ange behörigheter för innehållet i fil resursen.
    2. Rollen lagrings fil data SMB-resurs deltagare ska tilldelas till studenter som behöver lägga till eller redigera filer på fil resursen.
    3. Rollen lagrings fil data SMB Share Reader ska tilldelas till studenter som bara behöver läsa filerna från fil resursen.
6. Konfigurera katalog-/fil nivå behörigheter för fil resursen.  Behörigheter måste konfigureras från en domänansluten dator som har nätverks åtkomst till fil resursen.  **Om du vill ändra behörigheter för katalogen/filnivån monterar du fil resursen med lagrings nyckeln, inte dina AAD-autentiseringsuppgifter.**  [Ange ACL](/powershell/module/microsoft.powershell.security/set-acl) PowerShell-kommando eller [icacls](/windows-server/administration/windows-commands/icacls) är de rekommenderade verktyg som du kan använda när du tilldelar behörigheter.
7. [Peer det virtuella nätverket](how-to-connect-peer-virtual-network.md) för lagrings kontot till labb kontot.
8. [Skapa klass rums labbet](how-to-manage-classroom-labs.md).
9. Spara ett skript på den VM-mall som eleverna kan köra för att ansluta till nätverks enheten.  Så här hämtar du exempel skript:
    1. Öppna lagringskontot i Azure-portalen.
    1. Välj **fil resurser** under **fil tjänst** på menyn.
    1. Leta upp den resurs som du vill ansluta till, Välj knappen ovaler längst till höger och välj **Anslut**.
    1. **Connect** -infarten öppnas med instruktioner för Windows, Linux och MacOS.  Om du använder Windows ställer du in **autentiseringsmetoden** till **Active Directory**.
    1. Kopiera koden i exemplet och spara den på mallfilen i en `.ps1` fil för Windows eller `.sh` fil för Linux.
10. På mall-datorn laddar du ned och kör skript för att [ansluta student datorer till domänen](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage).  `Join-AzLabADTemplate`Skriptet publicerar automatiskt [mallen VM](how-to-create-manage-template.md#publish-the-template-vm) .  
    > [!NOTE]
    > Mall datorn kommer inte att vara domänansluten. Instruktörer bör tilldela en publicerad elev-VM för sig själva för att visa filer på resursen.
11. Studenter som använder Windows kan ansluta till den Azure Files resursen med hjälp av [Utforskaren](/azure/storage/files/storage-how-to-use-files-windows) med sina autentiseringsuppgifter när de har fått sökvägen till fil resursen.  Alternativt kan studenter köra skriptet som skapats ovan för att ansluta till nätverks enheten.  För studenter som använder Linux kör du skriptet som du skapade ovan.

## <a name="netapp-files-with-nfs-volumes"></a>NetApp-filer med NFS-volymer

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) är en högpresterande, högpresterande fil lagrings tjänst med hög prestanda.

- Åtkomst principer kan anges per volym.
- Behörighets principer är IP-baserade för varje volym.
- Om eleverna behöver sin egen volym som andra studenter inte har åtkomst till, måste behörighets principerna tilldelas efter att labbet har publicerats.
- Det finns bara stöd för Linux-datorer i samband med Azure Lab Services.
- Det virtuella nätverket för Azure NetApp Files kapacitets gruppen måste vara peer-kopplat till det virtuella nätverket för labb kontot **innan** labbet skapas.

Följ stegen nedan om du vill använda en Azure NetApp Files resurs i Azure Lab Services.

1. Publicera till [Azure NetApp Files](https://aka.ms/azurenetappfiles), om det behövs.
2. Om du vill skapa en NetApp och NFS-volym (er) kan du läsa [konfigurera Azure NetApp Files-och NFS-volym](/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes).  Information om tjänste nivåer finns i [service nivåer för Azure NetApp Files](/azure/azure-netapp-files/azure-netapp-files-service-levels).
3. [Peer det virtuella nätverket](how-to-connect-peer-virtual-network.md) för NetApp-filernas kapacitets uppsättning till labb kontot.
4. [Skapa klass rums labbet](how-to-manage-classroom-labs.md).
5. Installera nödvändiga komponenter på mallen VM för att använda NFS-filresurser.
    - Ubuntu

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS

        ```bash
        sudo yum install nfs-utils
        ```

6. På mallen VM sparar du skriptet nedan `mount_fileshare.sh` för att [montera NetApp-fildelningen](/azure/azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines).  Tilldela den `capacity_pool_ipaddress` variabla IP-adressen för monterings målet för poolen.  Hämta monterings instruktionerna för volymen för att hitta lämpligt värde.  Skriptet förväntar sig sökväg/namn för NetApp-filernas volym.  Glöm inte att köra `chmod u+x mount_fileshare.sh` för att se till att skript kan köras av användare.

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ may cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. Om alla studenter delar åtkomst till samma NetApp-filvolym `mount_fileshare.sh` kan skriptet köras på den mall datorn innan publicering.  Om studenterna får sin egen volym sparar du skript som ska köras senare av studenten.
8. [Publicera](how-to-create-manage-template.md#publish-the-template-vm) mallen VM.
9. [Konfigurera princip](/azure/azure-netapp-files/azure-netapp-files-configure-export-policy) för fil resurs.  Export policy kan tillåta att en enskild virtuell dator eller flera virtuella datorer har åtkomst till en volym.  Skrivskyddad eller Läs-/Skriv behörighet kan beviljas.
10. Eleverna måste starta sin virtuella dator och köra skriptet för att montera fil resursen.  De behöver bara köra skriptet en gång.  Kommandot kommer att se ut så här `./mount_fileshare.sh myvolumename` .

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in alla labb.

- [Skapa och hantera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users)