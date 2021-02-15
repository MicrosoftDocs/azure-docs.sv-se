---
title: Hantera ögonblicks bilder med Azure NetApp Files | Microsoft Docs
description: Beskriver hur du skapar, hanterar och använder ögonblicks bilder med hjälp av Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/10/2021
ms.author: b-juche
ms.openlocfilehash: 4d992bcc202dc8bdacdda6426371df1adb1ec3e6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379122"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Hantera ögonblicksbilder med hjälp av Azure NetApp Files

Azure NetApp Files har stöd för att skapa ögonblicks bilder och använda ögonblicks bilder för att schemalägga automatisk skapande av ögonblicks bilder. Du kan också återställa en ögonblicks bild till en ny volym, återställa en enskild fil med hjälp av en klient eller återställa en befintlig volym med hjälp av en ögonblicks bild.

> [!NOTE] 
> Överväganden om ögonblicks bilds hantering i replikering över flera regioner finns i [krav och överväganden för att använda replikering över flera regioner](cross-region-replication-requirements-considerations.md).

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Skapa en ögonblicks bild på begäran för en volym

Du kan skapa ögonblicks bilder av volymen på begäran. 

1.  Gå till den volym som du vill skapa en ögonblicks bild för. Klicka på **ögonblicks bilder**.

    ![Navigera till ögonblicks bilder](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Klicka på **+ Lägg till ögonblicks bild** för att skapa en ögonblicks bild på begäran för en volym.

    ![Lägg till ögonblicks bild](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  I fönstret ny ögonblicks bild anger du ett namn för den nya ögonblicks bilden som du skapar.   

    ![Ny ögonblicks bild](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klicka på **OK**. 

## <a name="manage-snapshot-policies"></a>Hantera ögonblicks bilds principer

Du kan schemalägga att ögonblicks bilder av volymen ska tas automatiskt med hjälp av ögonblicks bilds principer. Du kan också ändra en ögonblicks bild princip efter behov eller ta bort en ögonblicks bild princip som du inte längre behöver.  

### <a name="register-the-feature"></a>Registrera funktionen

Funktionen för **ögonblicks bilds principen** är för närvarande en för hands version. Om du använder den här funktionen för första gången måste du registrera funktionen först. 

1. Registrera funktionen: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Kontrol lera status för funktions registreringen: 

    > [!NOTE]
    > **RegistrationState** kan vara i ett `Registering` tillstånd i upp till 60 minuter innan den ändras till `Registered` . Vänta tills statusen har **registrerats** innan du fortsätter.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
Du kan också använda [Azure CLI-kommandon](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` och `az feature show` Registrera funktionen och Visa registrerings status. 

### <a name="create-a-snapshot-policy"></a>Skapa en ögonblicks bild princip 

Med en ögonblicks bild princip kan du ange frekvensen för ögonblicks bild skapande i varje timme, varje dag, varje vecka eller månads vis. Du måste också ange det maximala antal ögonblicks bilder som ska behållas för volymen.  

1.  I vyn NetApp konto klickar du på **ögonblicks bild princip**.

    ![Navigering för ögonblicks bild princip](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  I fönstret ögonblicks bild princip anger du princip tillstånd till **aktive rad**. 

3.  Klicka på fliken varje **timme**, **varje dag**, **varje vecka** eller varje **månad** för att skapa dagliga, dagliga, vecko Visa eller månatliga ögonblicks bilds principer. Ange det **antal ögonblicks bilder som ska behållas**.  

    Se [resurs gränser för Azure NetApp Files](azure-netapp-files-resource-limits.md) om det maximala antalet ögonblicks bilder som tillåts för en volym. 

    I följande exempel visas konfiguration av Tim ögonblicks bild princip. 

    ![Ögonblicks bild princip varje timme](../media/azure-netapp-files/snapshot-policy-hourly.png)

    I följande exempel visas daglig princip konfiguration för ögonblicks bilder.

    ![Ögonblicks bild princip varje dag](../media/azure-netapp-files/snapshot-policy-daily.png)

    I följande exempel visas konfigurationen för vecko ögonblicks bilds principer.

    ![Ögonblicks bild princip varje vecka](../media/azure-netapp-files/snapshot-policy-weekly.png)

    I följande exempel visas månatlig konfiguration av ögonblicks bilds principen.

    ![Ögonblicks bild princip varje månad](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Klicka på **Spara**.  

Om du behöver skapa ytterligare ögonblicks bilds principer upprepar du steg 3.
De principer som du har skapat visas på sidan princip för ögonblicks bild.

Om du vill att en volym ska använda ögonblicks bild principen måste du [tillämpa principen på volymen](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Tillämpa en ögonblicks bild princip på en volym

Om du vill att en volym ska använda en ögonblicks bilds princip som du har skapat måste du tillämpa principen på volymen. 

Du kan inte tillämpa en ögonblicks bild princip på en mål volym i replikering mellan regioner.  

1.  Gå till sidan **volymer** , högerklicka på den volym som du vill tillämpa en ögonblicks bild princip på och välj **Redigera**.

    ![Volymer på snabb menyn för volymer](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  I redigerings fönstret, under **ögonblicks bild princip**, väljer du en princip som ska användas för volymen.  Klicka på **OK** för att tillämpa principen.  

    ![Redigera ögonblicks redigerings princip](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Ändra en ögonblicks bild princip 

Du kan ändra en befintlig ögonblicks bild princip om du vill ändra principens tillstånd, frekvens för ögonblicks bilder (varje timme, varje dag, varje vecka eller månad) eller antal ögonblicks bilder som ska behållas.  
 
1.  I vyn NetApp konto klickar du på **ögonblicks bild princip**.

2.  Högerklicka på den ögonblicks bilds princip som du vill ändra och välj sedan **Redigera**.

    ![Ögonblicks bild princip, snabb meny](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Gör ändringarna i fönstret princip för ögonblicks bild som visas och klicka sedan på **Spara**. 

### <a name="delete-a-snapshot-policy"></a>Ta bort en ögonblicks bild princip 

Du kan ta bort en ögonblicks bilds princip som du inte längre vill behålla.   

1.  I vyn NetApp konto klickar du på **ögonblicks bild princip**.

2.  Högerklicka på den ögonblicks bilds princip som du vill ändra och välj sedan **ta bort**.

    ![Ögonblicks bild princip, snabb meny](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Klicka på **Ja** för att bekräfta att du vill ta bort ögonblicks bild principen.   

    ![Bekräftelse av borttagning av ögonblicks princip](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="edit-the-hide-snapshot-path-option"></a>Redigera alternativet Dölj ögonblicks bild Sök väg
Alternativet Dölj ögonblicks bild Sök väg styr om ögonblicks bildens sökväg för en volym är synlig. När du skapar en [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume) -eller [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) -volym har du möjlighet att ange om ögonblicks bildens sökväg ska vara dold. Du kan sedan redigera alternativet Dölj ögonblicks bild Sök väg efter behov.  

> [!NOTE]
> För en [mål volym](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume) i replikering mellan regioner är alternativet Dölj ögonblicks bilds Sök väg aktiverat som standard och inställningen kan inte ändras. 

1. Om du vill visa alternativ inställningen Dölj ögonblicks bild Sök väg för en volym väljer du volymen. Fältet **Dölj ögonblicks bild Sök väg** visar om alternativet är aktiverat.   
    ![Skärm bild som beskriver fältet Dölj ögonblicks bilds Sök väg.](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. Redigera alternativet Dölj ögonblicks bild Sök väg genom att klicka på **Redigera** på sidan volym och ändra alternativet för att **dölja ögonblicks bilds Sök väg** efter behov.   
    ![Skärm bild som beskriver alternativet Redigera ögonblicks bild av volym.](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Återställa en ögonblicks bild till en ny volym

För närvarande kan du bara återställa en ögonblicks bild till en ny volym. 
1. Välj **ögonblicks bilder** från volym bladet för att Visa ögonblicks bild listan. 
2. Högerklicka på den ögonblicks bild som du vill återställa och välj **Återställ till ny volym** från meny alternativet.  

    ![Återställ ögonblicks bild till ny volym](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. Ange information för den nya volymen i fönstret Skapa en volym:  
    * **Namn**   
        Ange namnet på den volym du skapar.  
        
        Namnet måste vara unikt inom sin resursgrupp. Det måste innehålla minst tre tecken.  Du kan använda alla alfanumeriska tecken.

    * **Kvot**  
        Ange mängden logisk lagring som du vill tilldela till volymen.  

    ![Återställ till ny volym](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Klicka på **Granska + skapa**.  Klicka på **Skapa**.   
    Den nya volymen använder samma protokoll som ögonblicks bilden använder.   
    Den nya volymen som ögonblicks bilden återställs till visas på bladet volymer.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>Återställa en fil från en ögonblicks bild med hjälp av en klient

Om du inte vill [återställa hela ögonblicks bilden till en volym](#restore-a-snapshot-to-a-new-volume)kan du välja att återställa en fil från en ögonblicks bild med hjälp av en klient som har volymen monterad.  

Den monterade volymen innehåller en ögonblicks bilds katalog med namnet  `.snapshot` (i NFS-klienter) eller `~snapshot` (i SMB-klienter) som är tillgänglig för klienten. Katalogen för ögonblicks bilder innehåller under kataloger som motsvarar ögonblicks bilderna av volymen. Varje under katalog innehåller filerna i ögonblicks bilden. Om du av misstag tar bort eller skriver över en fil, kan du återställa filen till den överordnade katalogen för skriv skydd genom att kopiera filen från en under katalog i ögonblicks bild till katalogen för Läs-och skriv åtgärder. 

Du kan styra åtkomsten till ögonblicks bild kataloger med hjälp av alternativet för att [dölja ögonblicks bilds Sök väg](#edit-the-hide-snapshot-path-option). Det här alternativet styr huruvida katalogen ska döljas från klienterna. Därför styr den också åtkomst till filer och mappar i ögonblicks bilderna.  

NFSv 4.1 visar inte `.snapshot` katalogen ( `ls -la` ). Men när alternativet Dölj ögonblicks bilds Sök vägen inte är inställt, kan du fortfarande komma åt `.snapshot` katalogen via nfsv 4.1 genom att använda `cd <snapshot-path>` kommandot från klientens kommando rad. 

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Återställa en fil med hjälp av en Linux NFS-klient 

1. Använd `ls` Linux-kommandot för att visa en lista över filen som du vill återställa från `.snapshot` katalogen. 

    Exempel:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. Använd `cp` kommandot för att kopiera filen till den överordnade katalogen.  

    Exempel: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Återställa en fil med hjälp av en Windows-klient 

1. Om `~snapshot` katalogen på volymen är dold, [Visa dolda objekt](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) i den överordnade katalogen som ska visas `~snapshot` .

    ![Visa dolda objekt](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. Navigera till under katalogen i `~snapshot` för att hitta den fil som du vill återställa.  Högerklicka på filen. Välj **Kopiera**.  

    ![Kopiera fil som ska återställas](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Återgå till den överordnade katalogen. Högerklicka på den överordnade katalogen och välj `Paste` att klistra in filen i katalogen.

    ![Klistra in fil som ska återställas](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. Du kan också högerklicka på den överordnade katalogen, välja **Egenskaper**, klicka på fliken **tidigare versioner** om du vill se en lista över ögonblicks bilder och välja **Återställ** för att återställa en fil.  

    ![Egenskaper tidigare versioner](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="revert-a-volume-using-snapshot-revert"></a>Återställa en volym med hjälp av återställning av ögonblicks bilder

Med funktionen för återställning av ögonblicks bilder kan du snabbt återställa en volym till det tillstånd den var i när en viss ögonblicks bild togs. I de flesta fall går det mycket snabbare att återställa en volym än att återställa enskilda filer från en ögonblicks bild till det aktiva fil systemet. Det är också mer utrymmes effektivt jämfört med att återställa en ögonblicks bild till en ny volym. 

Du hittar alternativet Återställ volym på menyn ögonblicks bilder på en volym. När du har valt en ögonblicks bild för ny version återställer Azure NetApp Files volymen till de data och tidsstämplar som den innehöll när den valda ögonblicks bilden togs. 

> [!IMPORTANT]
> Aktiva fil Systems data och ögonblicks bilder som togs efter att den valda ögonblicks bilden togs bort kommer att gå förlorade. Återställnings åtgärden för ögonblicks bilder ersätter *alla* data på mål volymen med data i den valda ögonblicks bilden. Du bör ta hänsyn till ögonblicks bildens innehåll och datum när du väljer en ögonblicks bild. Det går inte att ångra återställnings åtgärden för ögonblicks bilder.

1. Gå till **ögonblicks bilds** menyn för en volym.  Högerklicka på den ögonblicks bild som du vill använda för återställnings åtgärden. Välj **Återställ volym**. 

    ![Skärm bild som beskriver snabb menyn i en ögonblicks bild](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. I fönstret Återställ volym till ögonblicks bild skriver du namnet på volymen och klickar på **Återställ**.   

    Volymen återställs nu till tidpunkten för den valda ögonblicks bilden.

    ![Skärm bild som visar fönstret Återställ volym till ögonblicks bild](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="delete-snapshots"></a>Ta bort ögonblicks bilder  

Du kan ta bort ögonblicks bilder som du inte längre behöver behålla. 

1. Gå till **ögonblicks bilds** menyn för en volym. Högerklicka på den ögonblicks bild som du vill ta bort. Välj **Ta bort**.

    ![Skärm bild som beskriver snabb menyn i en ögonblicks bild](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. I fönstret ta bort ögonblicks bild bekräftar du att du vill ta bort ögonblicks bilden genom att klicka på **Ja**. 

    ![Skärm bild som bekräftar borttagning av ögonblicks bilder](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>Nästa steg

* [Felsöka policyer för ögonblicksbilder](troubleshoot-snapshot-policies.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files ögonblicks bilder 101 video](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
* [Vad är verktyget Azure Application Consistent Snapshot?](azacsnap-introduction.md)
