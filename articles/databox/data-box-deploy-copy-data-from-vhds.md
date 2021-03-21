---
title: 'Självstudie: kopiera från virtuella hård diskar till Managed disks'
titleSuffix: Azure Data Box
description: Lär dig hur du kopierar data från virtuella hård diskar från lokala VM-arbetsbelastningar till din Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/20/2019
ms.author: alkohli
ms.openlocfilehash: 1394cf6511a65a0e406e51229953e8666d4d4d8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94337686"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Självstudie: Använd Data Box-enhet för att importera data som Managed disks i Azure

I den här självstudien beskrivs hur du använder Azure Data Box för att migrera lokala virtuella hård diskar till hanterade diskar i Azure. Virtuella hård diskar från lokala virtuella datorer kopieras till Data Box-enhet som Page blobbar och överförs till Azure som hanterade diskar. Dessa hanterade diskar kan sedan anslutas till virtuella Azure-datorer.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Gå igenom förhandskraven
> * Ansluta till Data Box
> * Kopiera data till Data Box

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har slutfört självstudien [: Konfigurera Azure Data Box](data-box-deploy-set-up.md).
2. Du har fått din Data Box och att orderstatusen i portalen är **Levererad**.
3. Du har anslutning till ett höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10-GbE-anslutning inte är tillgänglig kan du använda en 1-GbE-datalänk men kopierings hastigheten påverkas.
4. Du har granskat:

    - [Hanterade disk storlekar som stöds i storleks gränser för Azure-objekt](data-box-limits.md#azure-object-size-limits).
    - [Introduktion till Azure Managed disks](../virtual-machines/managed-disks-overview.md). 

5. Du har bevarat en kopia av käll informationen tills du har bekräftat att Data Box-enhet överfört data till Azure Storage.

## <a name="connect-to-data-box"></a>Ansluta till Data Box

Baserat på de angivna resurs grupperna skapar Data Box-enhet en resurs för varje associerad resurs grupp. Om och till exempel `mydbmdrg1` `mydbmdrg2` skapades när ordern placerades, skapas följande resurser:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

I varje resurs skapas följande tre mappar som motsvarar behållare i ditt lagrings konto.

- Premium SSD
- Standard HDD
- Standard SSD

I följande tabell visas UNC-sökvägar till resurserna på din Data Box-enhet.
 
|        Anslutnings protokoll           |             UNC-sökväg till resursen                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<PremiumSSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardHDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardSSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<PremiumSSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardHDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardSSD>/file3.vhd` |

Beroende på om du använder SMB eller NFS för att ansluta till Data Box-enhet-resurser skiljer sig stegen för att ansluta.

> [!NOTE]
> Anslutning via REST stöds inte för den här funktionen.

### <a name="connect-to-data-box-via-smb"></a>Ansluta till Data Box-enhet via SMB

Om du använder en Windows Server-värddator följer du stegen nedan för att ansluta till Data Box.

1. Det första steget är att autentisera och starta en session. Gå till **Anslut och kopiera**. Klicka på **Hämta autentiseringsuppgifter** för att få autentiseringsuppgifter för de resurser som är kopplade till din resurs grupp. Du kan också få åtkomst behörigheterna från **enhets informationen** i Azure Portal.

    > [!NOTE]
    > Autentiseringsuppgifterna för alla resurser för hanterade diskar är identiska.

    ![Anslut och kopiera, Hämta autentiseringsuppgifter för delning](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. I dialog rutan **åtkomst resurs och kopiera data** kopierar du **användar namnet** och **lösen ordet** för resursen. Klicka på **OK**.
    
    ![Anslut och kopiera, kopiera autentiseringsuppgifter för resurs](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Öppna ett kommando fönster för att få åtkomst till de resurser som är associerade med din resurs (*mydbmdrg1* i följande exempel) från värddatorn. Skriv följande i kommandotolken:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Dina UNC-delnings Sök vägar i det här exemplet är följande:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Ange lösenordet för resursen när du tillfrågas. Följande exempel visar hur du ansluter till en resurs via kommandot ovan.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for 'mdisk' to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

5. Tryck på Windows + R. I fönstret **Kör** anger du `\\<device IP address>\<ShareName>`. Öppna Utforskaren genom att klicka på **OK**.
    
    ![Ansluta till resurs via Utforskaren](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Nu bör du se följande förskapade mappar i varje resurs.
    
    ![Ansluta till resursen via Utforskaren, mappar för en resurs](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>Ansluta till Data Box-enhet via NFS

Om du använder en Linux-värddator utför du stegen nedan för att konfigurera Data Box att tillåta åtkomst till NFS-klienter.

1. Ange IP-adresserna för de tillåtna klienterna som har åtkomst till resursen. Gå till sidan **Anslut och kopiera** i det lokala webb gränssnittet. Under **NFS-inställningar** klickar du på **NFS-klientåtkomst**.

    ![Konfigurera åtkomst till NFS-klienter](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Ange NFS-klientens IP-adress och klicka på **Add**. Du kan konfigurera åtkomst för flera NFS genom att upprepa det här steget. Klicka på **OK**.

    ![Konfigurera IP-adress för NFS-klienten](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Kontrollera att Linux-värddatorn har en NFS-klient av en [version som stöds](data-box-system-requirements.md) installerad. Använd den specifika versionen för din Linux-distribution.

3. När NFS-klienten har installerats använder du följande kommando för att montera NFS-resursen på Data Box-enheten:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    I följande exempel visas hur du ansluter via NFS till en Data Box-resurs. Data Box-enhetens IP-adress är `169.254.250.200`, resursen `mydbmdrg1_MDisk` är monterad på ubuntuVM, och monteringspunkten är `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Kopiera data till Data Box

När du är ansluten till data servern är nästa steg att kopiera data. VHD-filen kopieras till mellanlagrings kontot som Page blob. Sid-bloben konverteras sedan till en hanterad disk och flyttas till en resurs grupp.

Granska följande överväganden innan du börjar kopiera data:

- Kopiera alltid de virtuella hårddiskarna till en av de förskapade mapparna. Om du kopierar de virtuella hård diskarna utanför dessa mappar eller i en mapp som du har skapat, kommer de virtuella hård diskarna att överföras till Azure Storage-konto som sid-blobar och inte hanterade diskar.
- Endast fasta virtuella hårddiskar kan laddas upp för att skapa hanterade diskar. VHDX-filer eller dynamiska och differentierande virtuella hård diskar stöds inte.
- Du kan bara ha en hanterad disk med ett angivet namn i en resurs grupp i alla förskapade mappar. Det betyder att de virtuella hårddiskarna som laddats upp till de förskapade mapparna måste ha unika namn. Kontrollera att namnet inte matchar en redan befintlig hanterad disk i en resurs grupp.
- Granska begränsningar för hanterade diskar i [storleks gränser för Azure-objekt](data-box-limits.md#azure-object-size-limits).

Beroende på om du ansluter via SMB eller NFS kan du använda:

- [Kopiera data via SMB](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [Kopiera data via NFS](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

Vänta tills kopieringsjobben är klara. Se till att kopierings jobben har avslut ATS utan fel innan du går vidare till nästa steg.

![Inga fel på sidan **Anslut och kopiera**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Om det uppstår fel under kopierings processen kan du hämta loggarna från sidan **Anslut och kopiera** .

- Om du kopierade en fil som inte är 512 bytes, laddas inte filen upp som en sid-blob till ditt lagrings konto för lagring. Du kommer att se ett fel i loggarna. Ta bort filen och kopiera en fil som är 512 byte-justerad.

- Om du har kopierat en VHDX (dessa filer stöds inte) med ett långt namn visas ett fel i loggarna.

    ![Fel i loggarna från * * Connect och kopiera * * Sidan](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Lös felen innan du fortsätter till nästa steg.

För att säkerställa dataintegriteten beräknas kontrollsumman infogat när data kopieras. När kopieringen är klar kontrollerar du det använda utrymmet och det lediga utrymmet på enheten.
    
![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

När kopieringsjobbet är klart kan du gå till **Förbered för att skicka**.


## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Gå igenom förhandskraven
> * Ansluta till Data Box
> * Kopiera data till Data Box


Gå vidare till nästa självstudie och lär dig hur du skickar tillbaka din Data Box-enhet till Microsoft.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box till Microsoft](./data-box-deploy-picked-up.md)