---
title: Självstudie – säkerhetskopiera virtuella Linux-datorer i Azure Portal
description: I den här självstudiekursen lär du dig hur du använder Azure Portal för att skydda dina virtuella Linux-datorer med Azure Backup.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1865d307e7b79e7e40b0e98f2dcfa2535b0313fd
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551715"
---
# <a name="tutorial-back-up-and-restore-files-for-linux-virtual-machines-in-azure"></a>Självstudier: Säkerhetskopiera och återställa filer för virtuella Linux-datorer i Azure

Du kan skydda dina data genom att säkerhetskopiera med jämna mellanrum. Med Azure Backup skapas återställningspunkter som lagras i geo-redundanta återställningsvalv. När du återställer från en återställningspunkt kan du återställa hela den virtuella datorn eller specifika filer. I den här artikeln förklaras hur du återställer en enda fil till en virtuell Linux-dator som kör nginx. Om du inte redan har en virtuell dator att använda kan du skapa en med hjälp av [Linux-snabbstarten](quick-create-cli.md). I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en säkerhetskopia av en virtuell dator
> * Schemalägga en daglig säkerhetskopiering
> * Återställa en fil från en säkerhetskopia

## <a name="backup-overview"></a>Översikt över Backup

När Azure Backup-tjänsten initierar en säkerhetskopiering utlöser den ett säkerhetskopieringstillägg för att ta en ögonblicksbild. Azure Backup-tjänsten använder tillägget _VMSnapshotLinux_ i Linux. Tillägget installeras under den första säkerhetskopieringen av den virtuella datorn om den virtuella datorn körs. Om den virtuella datorn inte körs tar Backup-tjänsten en ögonblicksbild av snapshot det underliggande lagringsutrymmet (eftersom ingen programskrivning medan den virtuella datorn stoppas).

Som standard skapar Azure Backup en filsystemkonsekvent säkerhetskopia för virtuella Linux-datorer, men det kan konfigureras för att skapa en [programkonsekvent säkerhetskopia med ramverk för förskript och efterskript](../../backup/backup-azure-linux-app-consistent.md). När Azure Backup-tjänsten har tagit ögonblicksbilden överförs data till valvet. För att maximera effektiviteten identifierar och överför tjänsten endast de datablock som har ändrats sedan föregående säkerhetskopia.

När dataöverföringen har slutförts tas ögonblicksbilden bort och en återställningspunkt skapas.


## <a name="create-a-backup"></a>Skapa en säkerhetskopia
Skapa en schemalagd daglig säkerhetskopiering till ett Recovery Services-valv:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Virtuella datorer** på menyn till vänster. 
3. Välj en virtuell dator som du vill säkerhetskopiera i listan.
4. På VM-bladet går du till avsnittet **Åtgärder** och klickar på **Säkerhetskopiering**. Bladet **Aktivera säkerhetskopiering** öppnas.
5. I **Recovery Services-valv** klickar du på **Skapa nytt** och anger namnet för det nya valvet. Ett nytt valv skapas i samma resursgrupp och på samma plats som den virtuella datorn.
6. Klicka på **Säkerhetskopieringspolicy**. I det här exemplet behåller du standardvärdena och klickar på **OK**.
7. Klicka på **Aktivera säkerhetskopiering** på bladet **Aktivera säkerhetskopiering**. Då skapas en daglig säkerhetskopia baserat på standardschemat.
10. Om du vill skapa en första återställningspunkt går du till bladet **Säkerhetskopiera** och klickar på **Säkerhetskopiera nu**.
11. På bladet **Säkerhetskopiera nu** klickar du på kalender ikonen, använder kalender kontrollen för att välja den sista dagen då återställnings punkten behålls och klickar på **säkerhetskopiera**.
12. På bladet **Säkerhetskopiera** för din virtuella dator ser du antalet återställningspunkter som är klara.

    ![Återställningspunkter](./media/tutorial-backup-vms/backup-complete.png)

Den första säkerhetskopieringen tar ungefär 20 minuter. Fortsätt till nästa del av den här självstudien när din säkerhetskopia är klar.

## <a name="restore-a-file"></a>Återställa en fil

Om du oavsiktligt råkar ta bort eller göra ändringar i en fil kan du använda Filåterställning för att återställa filen från ditt säkerhetskopieringsvalv. Filåterställning använder ett skript som körs på den virtuella datorn för att montera återställningspunkten som en lokal enhet. Enheterna förblir monterade i 12 timmar, så att du kan kopiera filer från återställningspunkten och återställa dem till den virtuella datorn.  

I det här exemplet visar vi hur du återställer standardversionen av nginx-webbsidan /var/www/html/index.nginx-debian.html. Den virtuella datorns offentliga IP-adress i det här exemplet är *13.69.75.209*. Du hittar IP-adressen till den virtuella datorn med:

 ```azurecli
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. På din lokala dator öppnar du en webbläsare och skriver den virtuella datorns offentliga IP-adress för att se standardversionen av nginx-webbsidan.

    ![Standardversion av nginx-webbsida](./media/tutorial-backup-vms/nginx-working.png)

1. SSH till den virtuella datorn.

    ```bash
    ssh 13.69.75.209
    ```

2. Ta bort /var/www/html/index.nginx-debian.html.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. På din lokala dator uppdaterar du webbläsaren genom att trycka på CTRL + F5 för att se att standardversionen av nginx-webbsidan är borta.

    ![Standardversion av nginx-webbsida](./media/tutorial-backup-vms/nginx-broken.png)
    
1. Logga in på [Azure-portalen](https://portal.azure.com/) på din lokala dator.
6. Välj **Virtuella datorer** på menyn till vänster. 
7. Välj den virtuella datorn i listan.
8. Klicka på **Säkerhetskopiering** i avsnittet **Inställningar** på bladet VM. Bladet **Säkerhetskopiera** öppnas. 
9. På menyn överst på bladet väljer du **Filåterställning**. Bladet **Filåterställning** öppnas.
10. I **Steg 1: Välj återställningspunkt** väljer du en återställningspunkt från listrutan.
11. I **Steg 2: Ladda ned skriptet för att söka och återställa filer** klickar du på knappen **Ladda ned körbar fil**. Spara den nedladdade filen till din lokala dator.
7. Klicka på **Ladda ned skript** för att ladda ned skriptfilen lokalt.
8. Öppna en Bash-kommandotolk och skriv följande, och ersätt *Linux_myVM_05-05-2017.sh* med rätt sökväg och filnamn för skriptet som du har laddat ned, ersätt *azureuser* med användarnamnet för den virtuella datorn och *13.69.75.209* med din virtuella dators offentliga IP-adress.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. Öppna en SSH-anslutning till den virtuella datorn på den lokala datorn.

    ```bash
    ssh 13.69.75.209
    ```
    
10. På din virtuella dator lägger du till körbehörighet till skriptfilen.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. På din virtuella dator kör du skriptet för att montera återställningspunkten som ett filsystem.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. Utdata från skriptet ger dig sökvägen till monteringspunkten. Utdata ser ut ungefär så här:

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. På din virtuella dator kopierar du standardversionen av nginx-webbsidan från monteringspunkten och klistrar in den där du tog bort filen.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. På din lokala dator öppnar du webbläsarfliken där du är ansluten till den virtuella datorns IP-adress som visar standardversionen av nginx-webbsidan. Tryck på CTRL + F5 för att uppdatera webbläsaren. Nu bör du se att standardsidan fungerar igen.

    ![Standardversion av nginx-webbsida](./media/tutorial-backup-vms/nginx-working.png)

18. På din lokala dator går du tillbaka till webbläsarfliken för Azure-portalen i **Steg 3: Demontera diskarna efter återställning** klickar du på knappen **Demontera diskar**. Om du glömmer att utföra det här steget stängs anslutningen till monteringspunkten automatiskt efter 12 timmar. Efter de 12 timmarna måste du ladda ned ett nytt skript för att skapa en ny monteringspunkt.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en säkerhetskopia av en virtuell dator
> * Schemalägga en daglig säkerhetskopiering
> * Återställa en fil från en säkerhetskopia

Gå vidare till nästa självstudie om du vill veta mer om övervakning av virtuella datorer.

> [!div class="nextstepaction"]
> [Styra virtuella datorer](tutorial-govern-resources.md)
