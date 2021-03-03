---
title: Så här uppgraderar du Data Science Virtual Machine till Ubuntu 18,04
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig hur du uppgraderar från CentOS och Ubuntu 16,04 till de senaste Ubuntu 18,04-Data Science Virtual Machine.
keywords: djup inlärning, AI, data vetenskaps verktyg, virtuell data vetenskaps dator, team data science process
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b897ff7527d2d60234162ccbdeb08a00260bb1d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659468"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Uppgradera din Data Science Virtual Machine till Ubuntu 18.04

Om du har en Data Science Virtual Machine som kör en äldre version, till exempel Ubuntu 16,04 eller CentOS, bör du migrera DSVM till Ubuntu 18,04. Vid migreringen ser du till att du får de senaste korrigeringsfilerna, driv rutiner, förinstallerade program och biblioteks versioner. Det här dokumentet innehåller information om hur du migrerar från antingen äldre versioner av Ubuntu eller från CentOS. 

## <a name="prerequisites"></a>Förutsättningar

- Kunskap om SSH och Linux-kommandoraden

## <a name="overview"></a>Översikt

Det finns två möjliga sätt att migrera:

- Migrering på plats, även kallat "samma server"-migrering. Den här migreringen uppgraderar den befintliga virtuella datorn utan att skapa en ny virtuell dator. Migrering på plats är det enklaste sättet att migrera från Ubuntu 16,04 till Ubuntu 18,04.
- Migrering sida vid sida, även kallat migrering mellan servrar. Den här migreringen överför data från den befintliga virtuella datorn till en nyligen skapad virtuell dator. Migrering sida vid sida är sättet att migrera från CentOS till Ubuntu 18,04. Du kanske föredrar migrering sida vid sida för att uppgradera mellan Ubuntu-versioner om du tycker att din gamla installation har blivit onödan.

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>Ögonblicks bild av din virtuella dator om du behöver återställa

I Azure Portal använder du Sök fältet för att hitta funktionerna för **ögonblicks bilder** . 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="Skärm bild som visar Azure Portal och Sök fältet, med * * ögonblicks bilder * * markerat":::

1. Välj **Lägg till**, vilket leder dig till sidan **skapa ögonblicks bild** . Välj den virtuella datorns prenumeration och resurs grupp. För **region** väljer du den region där mål lagringen finns. Välj DSVM lagrings disk och ytterligare alternativ för säkerhets kopiering. **Standard HDD** är en lämplig lagrings typ för det här säkerhets kopierings scenariot.

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="Skärm bild som visar alternativ för att skapa ögonblicks bilder":::

2. När alla uppgifter är fyllda och verifierings pass väljer du **Granska + skapa** för att validera och skapa ögonblicks bilden. När ögonblicks bilden har slutförts visas ett meddelande om att distributionen har slutförts.

## <a name="in-place-migration"></a>Migrering på plats

Om du migrerar en äldre Ubuntu-version kan du välja att göra en migrering på plats. Den här migreringen skapar inte någon ny virtuell dator och har färre steg än en sida-vid-sida-migrering.  Om du vill göra en migrering sida vid sida eftersom du vill ha mer kontroll eller om du migrerar från en annan distribution, till exempel CentOS, går du till avsnittet [sida-vid-sida](#side-by-side-migration) -migrering. 

1. Starta DSVM från Azure Portal och logga in med SSH. Det gör du genom att välja **Anslut** och **SSH** och följa anslutnings anvisningarna. 

1. När du har anslutit till en terminalsession på din DSVM kör du följande uppgraderings kommando:

    ```bash
    sudo do-release-upgrade
    ```

Det tar en stund att slutföra uppgraderings processen. När den är över ber programmet om behörighet att starta om den virtuella datorn. Svara **Ja**. Du kommer att kopplas bort från SSH-sessionen när systemet startas om.

### <a name="if-necessary-regenerate-ssh-keys"></a>Om det behövs kan du återskapa SSH-nycklar

> [!IMPORTANT] 
> När du har uppgraderat och startat om kan du behöva återskapa dina SSH-nycklar.

När du har uppgraderat och startat om den virtuella datorn försöker du komma åt den igen via SSH. IP-adressen kan ha ändrats under omstarten, så bekräfta den innan den försöker ansluta.

Om du får en fel **identifiering av FJÄRRvärden har ändrats** måste du återskapa dina SSH-autentiseringsuppgifter.

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="PowerShell-skärm bild som visar ändrad varning för fjärrvärd-identifiering":::

Det gör du genom att köra kommandot på den lokala datorn:

```bash
ssh-keygen -R "your server hostname or ip"
```

Nu bör du kunna ansluta med SSH. Om du fortfarande har problem går du till sidan **Anslut** och följer länken för att **FELSÖKA problem med ssh-anslutningen**.

## <a name="side-by-side-migration"></a>Migrering sida vid sida

Om du migrerar från CentOS eller vill ha en ren OS-installation kan du utföra en migrering sida vid sida. Den här typen av migrering har fler steg, men ger dig kontroll över exakt vilka filer som överförs.

Migreringar från andra system baserade på samma uppsättning överordnade käll paket bör vara relativt enkla, till exempel [FAQ/CentOS3](https://wiki.centos.org/FAQ/CentOS3).

Du kan välja att uppgradera operativ systemets delar av fil systemet och lämna användar kataloger, t `/home` . ex. på plats. Om du lämnar de gamla hem katalogerna på plats förväntar sig några problem med menyerna GNOME/KDE och andra Skriv bords objekt. Det kan vara enklast att skapa nya användar konton och montera de gamla katalogerna någon annan stans i fil systemet för referens, kopiering eller länkning av användarens material efter migreringen.

### <a name="migration-at-a-glance"></a>En snabb migrering

1.  Skapa en ögonblicks bild av din befintliga virtuella dator enligt beskrivningen ovan

1.  Skapa en disk från ögonblicks bilden

1.  Skapa en ny Ubuntu-Data Science Virtual Machine

1.  Återskapa användar kontona på den nya virtuella datorn

1.  Montera disken för den virtuella datorn snapshotted som en datadisk på din nya Data Science Virtual Machine

1.  Kopiera de önskade data manuellt

### <a name="create-a-disk-from-your-vm-snapshot"></a>Skapa en disk från ögonblicks bilden av den virtuella datorn

Om du inte redan har skapat en ögonblicks bild av en virtuell dator enligt beskrivningen ovan, gör du det. 

1. Sök efter **diskar** i Azure Portal och välj **Lägg till**, så öppnas sidan **disk** .

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="Skärm bild av Azure Portal som visar sidan Sök efter diskar och knappen Lägg till":::

2. Ange **prenumeration**, **resurs grupp** och **region** till värdena för ögonblicks bilden av den virtuella datorn. Välj ett **namn** för disken som ska skapas.

3. Välj **Källtyp** som **ögonblicks** bild och välj den virtuella dator ögonblicks bilden som **käll ögonblicks bild**. Granska och skapa disken. 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="Skärm bild av dialog rutan för att skapa diskar med alternativ":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>Skapa en ny Ubuntu-Data Science Virtual Machine

Skapa en ny Ubuntu-Data Science Virtual Machine med hjälp av [Azure Portal](https://portal.azure.com) eller en [arm-mall](./dsvm-tutorial-resource-manager.md). 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>Återskapa användar kontona på den nya Data Science Virtual Machine

Eftersom du bara ska kopiera data från den gamla datorn måste du återskapa de användar konton och program miljöer som du vill använda på den nya datorn.

Linux är tillräckligt flexibelt för att du ska kunna anpassa kataloger och sökvägar på den nya installationen så att de följer din gamla dator. I allmänhet är det enklare att använda den moderna Ubuntu-layouten och ändra användar miljön och skripten för att anpassa.

Mer information finns i [snabb start: konfigurera data science Virtual Machine för Linux (Ubuntu)](./dsvm-ubuntu-intro.md).

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>Montera disken för den virtuella datorn snapshotted som en datadisk på din nya Data Science Virtual Machine

1. Kontrol lera att Data Science Virtual Machine körs i Azure Portal.

2. Gå till sidan för din Data Science Virtual Machine i Azure Portal. Välj bladet **diskar** på vänster järnväg. Välj **Anslut befintliga diskar**.

3. I list rutan **disk namn** väljer du den disk som du skapade från den gamla ögonblicks bilden av den virtuella datorn.

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="Skärm bild av sidan med DSVM alternativ med alternativ för disk bilagor":::

4. Välj **Spara** för att uppdatera den virtuella datorn.

> [!Important]
> Den virtuella datorn ska köras när du ansluter data disken. Om den virtuella datorn inte körs kan diskarna läggas till i en felaktig ordning, vilket leder till ett förvirrande och eventuellt icke-startbart system. Om du lägger till datadisken med den virtuella datorn väljer du **X** bredvid data disken, startar den virtuella datorn och kopplar den igen.

### <a name="manually-copy-the-wanted-data"></a>Kopiera de önskade data manuellt 

1. Logga in på den virtuella datorn som körs med SSH.

2. Bekräfta att du har kopplat disken som skapats från din gamla VM-ögonblicksbild genom att köra följande kommando:

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    Resultaten bör se ut ungefär som på följande bild. I avbildningen `sda1` monteras disken i roten och `sdb2` är den `/mnt` virtuella hård disken. Data disken som skapas från ögonblicks bilden av den gamla virtuella datorn identifieras som `sdc1` men är ännu inte tillgänglig, vilket framgår av bristen på en monterings plats. Resultaten kan ha olika identifierare, men du bör se ett liknande mönster.
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="Skärm bild av lsblk-utdata, som visar frånkopplad data enhet":::
    
3. Skapa en plats för data enheten och montera den för att få åtkomst till den. Ersätt `/dev/sdc1` med lämpligt värde som returneras av `lsblk` :

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. Nu `/datadrive` innehåller kataloger och filer för din gamla data science Virtual Machine. Flytta eller kopiera de kataloger eller filer som du vill från data enheten till den nya virtuella datorn som du vill.

Mer information finns i [använda portalen för att koppla en datadisk till en virtuell Linux-dator](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).

## <a name="connect-and-confirm-version-upgrade"></a>Anslut och bekräfta versions uppgradering

Oavsett om du gjorde en migrering på plats eller sida vid sida, kontrollerar du att du har uppgraderat. Kör följande från en terminalsession: 

```bash
cat /etc/os-release
```

Vi bör se att du kör Ubuntu 18,04.

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="Skärm bild av Ubuntu-terminalen som visar data för operativ system version":::

Ändring av version visas också i Azure Portal.

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="Skärm bild av portalen som visar DSVM-egenskaper inklusive OS-version":::

## <a name="next-steps"></a>Nästa steg

- [Data vetenskap med en Ubuntu data science-dator i Azure](./linux-dsvm-walkthrough.md)
- [Vilka verktyg ingår i Azure Data Science Virtual Machine?](./tools-included.md)