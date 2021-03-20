---
title: 'Självstudie: kopiera från virtuella hård diskar till Managed disks'
titleSuffix: Azure Data Box Heavy
description: Lär dig hur du kopierar data från virtuella hård diskar från lokala VM-arbetsbelastningar till din Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 335b564225bc2b7a4c2217c1d912f952239ecf24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92127207"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Självstudie: Använd Data Box Heavy för att importera data som Managed disks i Azure

I den här självstudien beskrivs hur du använder Azure Data Box Heavy för att migrera lokala virtuella hård diskar till hanterade diskar i Azure. Virtuella hård diskar från lokala virtuella datorer kopieras till Data Box Heavy som Page blobbar och överförs till Azure som hanterade diskar. Dessa hanterade diskar kan sedan anslutas till virtuella Azure-datorer.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Gå igenom förhandskraven
> * Ansluta till Data Box Heavy
> * Kopiera data till Data Box Heavy


## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har slutfört självstudien [: Konfigurera Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Du har fått din Data Box Heavy-enhet och orderstatusen på portalen är **Levererad**.
3. Du har anslutning till ett höghastighetsnätverk. För snabbast kopieringshastighet kan två 40-GbE-anslutningar (en per nod) användas parallellt. Om du inte har någon tillgänglig 40-GbE-anslutning rekommenderar vi att du har minst två 10 GbE-anslutningar (en per nod). 
4. Du har granskat:

    - [Hanterade disk storlekar som stöds i storleks gränser för Azure-objekt](data-box-heavy-limits.md#azure-object-size-limits).
    - [Introduktion till Azure Managed disks](../virtual-machines/managed-disks-overview.md). 

## <a name="connect-to-data-box-heavy"></a>Ansluta till Data Box Heavy

Baserat på de angivna resurs grupperna skapar Data Box Heavy en resurs för varje associerad resurs grupp per nod. Om och till exempel `mydbmdrg1` `mydbmdrg2` skapades när ordern placerades, skapas följande resurser:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

I varje resurs skapas följande tre mappar som motsvarar behållare i ditt lagrings konto.

- Premium SSD
- Standard HDD
- Standard SSD

I följande tabell visas UNC-sökvägar till resurserna på din Data Box Heavy.
 
|        Anslutnings protokoll           |             UNC-sökväg till resursen                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Beroende på om du använder SMB eller NFS för att ansluta till Data Box Heavy-resurser skiljer sig stegen för att ansluta.

> [!NOTE]
> - Anslutning via REST stöds inte för den här funktionen.
> - Upprepa anslutnings anvisningarna för att ansluta till den andra noden i Data Box Heavy.

### <a name="connect-to-data-box-heavy-via-smb"></a>Ansluta till Data Box Heavy via SMB

Om du använder en Windows Server-värddator följer du stegen nedan för att ansluta till Data Box Heavy.

1. Det första steget är att autentisera och starta en session. Gå till **Anslut och kopiera**. Klicka på **Hämta autentiseringsuppgifter** för att få autentiseringsuppgifter för de resurser som är kopplade till din resurs grupp. Du kan också få åtkomst behörigheterna från **enhets informationen** i Azure Portal.

    > [!NOTE]
    > Autentiseringsuppgifterna för alla resurser för hanterade diskar är identiska.

    ![Hämta resursautentiseringsuppgifter](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. I dialog rutan åtkomst resurs och kopiera data kopierar du **användar namnet** och **lösen ordet** för resursen. Klicka på **OK**.
    
    ![Hämta resursautentiseringsuppgifter 2](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Öppna ett kommando fönster för att få åtkomst till de resurser som är associerade med din resurs (*mydbmdrg1* i följande exempel) från värddatorn. Skriv följande i kommandotolken:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Dina UNC-delnings Sök vägar i det här exemplet är följande:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Ange lösenordet för resursen när du tillfrågas. Följande exempel visar hur du ansluter till en resurs via kommandot ovan.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Tryck på Windows + R. I fönstret **Kör** anger du `\\<device IP address>\<ShareName>`. Öppna Utforskaren genom att klicka på **OK**.
    
    ![Ansluta till resurs via Utforskaren](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Nu bör du se följande förskapade mappar i varje resurs.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Ansluta till Data Box Heavy via NFS

Om du använder en Linux-värddator utför du följande steg för att konfigurera enheten för att tillåta åtkomst till NFS-klienter.

1. Ange IP-adresserna för de tillåtna klienterna som har åtkomst till resursen. I det lokala webbgränssnittet går du till sidan **Anslut och kopiera**. Under **NFS-inställningar** klickar du på **NFS-klientåtkomst**.

    ![Konfigurera åtkomst till NFS-klienter](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Ange NFS-klientens IP-adress och klicka på **Add**. Du kan konfigurera åtkomst för flera NFS genom att upprepa det här steget. Klicka på **OK**.

    ![Konfigurera NFS-klientåtkomst 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Kontrollera att Linux-värddatorn har en NFS-klient av en [version som stöds](data-box-system-requirements.md) installerad. Använd den specifika versionen för din Linux-distribution.

3. När NFS-klienten har installerats använder du följande kommando för att montera NFS-resursen på enheten:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    I följande exempel visas hur du ansluter via NFS till en Data Box-enhet eller Data Box Heavy resurs. Data Box-enhet-eller Data Box Heavy-enhetens IP-adress är `169.254.250.200` , är resursen `mydbmdrg1_MDisk` monterad på ubuntuVM, monterings punkt `/home/databoxubuntuhost/databox` .

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Kopiera data till Data Box Heavy

När du är ansluten till data servern är nästa steg att kopiera data. VHD-filen kopieras till mellanlagrings kontot som Page blob. Sid-bloben konverteras sedan till en hanterad disk och flyttas till en resurs grupp.

Granska följande överväganden innan du börjar kopiera data:

- Kopiera alltid de virtuella hårddiskarna till en av de förskapade mapparna. Om du kopierar de virtuella hård diskarna utanför dessa mappar eller i en mapp som du har skapat, kommer de virtuella hård diskarna att överföras till Azure Storage-konto som sid-blobar och inte hanterade diskar.
- Endast fasta virtuella hårddiskar kan laddas upp för att skapa hanterade diskar. VHDX-filer eller dynamiska och differentierande virtuella hård diskar stöds inte.
- Du kan bara ha en hanterad disk med ett angivet namn i en resurs grupp i alla förskapade mappar. Det betyder att de virtuella hårddiskarna som laddats upp till de förskapade mapparna måste ha unika namn. Kontrollera att namnet inte matchar en redan befintlig hanterad disk i en resurs grupp.
- Granska begränsningar för hanterade diskar i [storleks gränser för Azure-objekt](data-box-heavy-limits.md#azure-object-size-limits).

Beroende på om du ansluter via SMB eller NFS kan du använda:

- [Kopiera data via SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Kopiera data via NFS](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

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

I den här självstudien om Azure Data Box Heavy har du bland annat lärt dig att:

> [!div class="checklist"]
> * Gå igenom förhandskraven
> * Ansluta till Data Box Heavy
> * Kopiera data till Data Box Heavy


Gå vidare till nästa självstudie och lär dig hur du skickar tillbaka din Data Box Heavy till Microsoft.

> [!div class="nextstepaction"]
> [Skicka Azure Data Box Heavy till Microsoft](./data-box-heavy-deploy-picked-up.md)