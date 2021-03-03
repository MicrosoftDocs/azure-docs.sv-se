---
title: Självstudiekurs om etablering av Azure Data Box Gateway i VMware | Microsoft Docs
description: Den andra självstudiekursen om att distribuera Azure Data Box Gateway innebär att etablera en virtuell enhet i VMware.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 948005942be193c8b15c363282aa06c006eb244e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727625"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-vmware"></a>Självstudie: Etablera Azure Data Box Gateway i VMware

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du etablerar Data Box Gateway på ett värdsystem som kör VMware ESXi 6.0, 6.5 eller 6.7. 

Du måste ha administratörsbehörighet för att etablera och ansluta till en virtuell enhet. Etableringen och den inledande installationen kan ta ungefär 10 minuter att slutföra. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Se till att värden uppfyller minimikraven för enhet
> * Etablera en virtuell enhet i VMware
> * Starta den virtuella enheten och hämta IP-adressen

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

Kraven för att etablera en virtuell enhet på ett värdsystem som kör VMware ESXi 6.0, 6.5 eller 6.7 är följande.

### <a name="for-the-data-box-gateway-resource"></a>För Data Box Gateway-resursen

Innan du börjar ska du kontrollera att:

* Du har slutfört alla stegen i [Förbereda portalen för Data Box Gateway](data-box-gateway-deploy-prep.md).
* Du har laddat ned avbildningen av den virtuella enheten för VMware från Azure-portalen enligt beskrivningen i [Förbereda portalen för Data Box Gateway](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > Programvaran som körs på Data Box Gateway kan bara användas med Data Box Gateway-resursen.

### <a name="for-the-data-box-gateway-virtual-device"></a>För den virtuella Data Box Gateway-enheten

Innan du distribuerar en virtuell enhet kontrollerar du att:

* Du har åtkomst till ett värdsystem som kör VMware (ESXi 6.0, 6.5 eller 6.7) som kan användas till att etablera en enhet.
* Värdsystemet kan dedikera följande resurser för att etablera den virtuella enheten:

  * Minst 4 kärnor.
  * Minst 8 GB RAM. Vi rekommenderar starkt minst 16 GB RAM-minne.
  * Ett nätverksgränssnitt.
  * En operativsystemdisk på 250 GB.
  * En virtuell disk på 2 TB för systemdata.

### <a name="for-the-network-in-datacenter"></a>För nätverket i datacentret

Innan du börjar:

- Granska nätverkskraven för att distribuera en Data Box Gateway och konfigurera datacenternätverket enligt kraven. Mer information finns i [Nätverkskrav för Data Box Gateway](data-box-gateway-system-requirements.md#networking-port-requirements).
- Se till att den minsta Internetbandbredden är 20 Mbps för att enheten ska fungera optimalt.

## <a name="check-the-host-system"></a>Kontrollera värdsystemet

För att skapa en virtuell enhet behöver du följande:

* Åtkomst till ett värdsystem som kör VMware ESXi Server 6.0, 6.5 eller 6.7. Värdsystemet kan dedikera följande resurser för den virtuella enheten:
 
  * Minst 4 virtuella processorer.
  * Minst 8 GB RAM. 
  * Ett nätverksgränssnitt anslutet till det nätverk som kan dirigera trafik till Internet.
  * En operativsystemdisk på 250 GB.
  * En virtuell disk på 2 TB för data.
* VMware vSphere-klient i systemet för att hantera ESXi-värden.


## <a name="provision-a-virtual-device-in-hypervisor"></a>Etablera en virtuell enhet i hypervisor-program

Utför följande steg för att etablera en virtuell enhet i ditt hypervisor-program.

1. Kopiera avbildningen av den virtuella enheten i ditt system. Du har laddat ned den här virtuella avbildningen (två filer) via Azure-portalen. Anteckna den plats dit du har kopierat avbildningen eftersom du använder den här avbildningen senare i proceduren.

2. Logga in på ESXi-servern via en webbläsare på följande URL: `https://<IP address of the ESXi server>`. Du måste ha administratörsbehörighet för att kunna skapa en virtuell dator.

   ![Inloggningssida](./media/data-box-gateway-deploy-provision-vmware/image1.png)
  
3. Ladda upp VMDK till ESXi-servern. Välj **Storage** (Lagring) i navigeringsfönstret.

   ![Skärm bild av en sida på ESXi Server-webbplatsen som visar fönstret navigatör med alternativet lagring valt.](./media/data-box-gateway-deploy-provision-vmware/image2.png)

4. I det högra fönstret, under **Datastores** (Datalager), väljer du det datalager där du vill ladda upp VMDK. 

    - Datalagret måste vara av typen VMFS5. 
    - Datalagret måste också ha tillräckligt med ledigt utrymme för OS- och datadiskarna.
   
5. Högerklicka och välj **Browse Datastore** (Bläddra i datalager).

   ![Bläddra i data lager](./media/data-box-gateway-deploy-provision-vmware/image3.png)

6. Ett **Datastore Browser**-fönster öppnas.

   ![Data lager webbläsare](./media/data-box-gateway-deploy-provision-vmware/image4.png)

7. Klicka på ikonen **Create directory** (Skapa katalog) i verktygsfältet för att skapa en ny mapp. Ange mappnamnet och anteckna det. Du använder det här mappnamnet senare när du skapar en virtuell dator (rekommenderad bästa praxis). Klicka på **Create directory** (Skapa katalog).

   ![Skapa katalog](./media/data-box-gateway-deploy-provision-vmware/image5.png)

8. Den nya mappen visas i det vänstrat fönstret i **Datastore Browser**. Klicka på ikonen **Upload** (Ladda upp) och välj **Upload File** (Ladda upp fil).

    ![Ladda upp filen](./media/data-box-gateway-deploy-provision-vmware/image6.png)

9. Bläddra till och peka på de VMDK-filer du har laddat ned. Det finns två filer. Välj en fil att ladda upp.

    ![Välj fil att ladda upp](./media/data-box-gateway-deploy-provision-vmware/image7.png)

10. Klicka på **Öppna**. Uppladdningen av VMDK-filen till det angivna datalagret startar. Det kan ta flera minuter för filen att laddas upp.
11. När uppladdningen är klar visas filen i datalagret i den mapp du har skapat. Ladda nu upp den andra VMDK-filen till samma datalager. När båda filerna har laddats upp slås de två filerna ihop till en enda fil. Då visas en enda fil i katalogen.

    ![Två VMDK-filer sammanfogas i en enda fil](./media/data-box-gateway-deploy-provision-vmware/image8.png)

12. Återgå till vSphere-klientfönstret. Välj **Virtual Machines** (Virtuella datorer) i navigeringsfönstret. Klicka på **Create/Register VM** (Skapa/registrera virtuell dator) i fönstret till höger.

    ![Skapa eller registrera VM](./media/data-box-gateway-deploy-provision-vmware/image9.png)

13. **New Virtual Machine** (Ny virtuell dator) visas. Under Select creation type (Välj skapandetyp) väljer du **Create a new virtual machine** (Skapa en ny virtuell dator) och klicka på **Next** (Nästa).
    ![Sidan Välj typ av skapande](./media/data-box-gateway-deploy-provision-vmware/image10.png)

14. På sidan **Select a Name and OS Name and Location** (Välj ett namn och OS-namn och plats) anger du **namnet** på den virtuella datorn. Det här namnet ska matcha mappnamnet (rekommenderad bästa praxis) du angav tidigare i steg 7. Välj Windows i **Guest OS family** (Gäst-OS-familj) och Microsoft Windows Server 2016 (64-bitars) i **Guest OS version** (Gäst-OS-version). Klicka på **Nästa**.

    ![Välj ett namn och ett namn och en plats för operativ system](./media/data-box-gateway-deploy-provision-vmware/image11.png)

15. På sidan **Select storage** (Välj lagring) väljer du ett datalager du vill använda för att etablera den virtuella datorn. Klicka på **Nästa**.

    ![Välj lagrings sida](./media/data-box-gateway-deploy-provision-vmware/image12.png)
16. På sidan **Customize settings** (Anpassa inställningar) anger du 4 i **CPU**, 8 192 MB (eller mer) i **Memory** (Minne), 2 TB (eller mer) i **Hard disk 1** (Hårddisk). Välj **SCSI-hårddisk** som ska läggas till. I det här fallet var det LSI Logic SAS. **De statiska IDE-diskarna stöds inte.** **Hard disk 1** (Hårddisk 1) är den virtuella datadisken. Observera att du inte kan komprimera disken när den har etablerats. Om du försöker minska disk resultatet försvinner alla lokala data på enheten. 

    ![Sidan anpassa inställningar](./media/data-box-gateway-deploy-provision-vmware/image13.png)

    På samma sidan klickar du på **Add hard disk** (Lägg till hårddisk) och väljer sedan **Existing hard disk** (Befintlig hårddisk). Välj VMDK-filen i datalagret. Detta lägger till en OS-disk. 

     ![Anpassa inställningar – Lägg till en hård disk](./media/data-box-gateway-deploy-provision-vmware/customize-settings-add-hard-disk.png)

    Rulla ned tills du ser **New hard disk** (Ny hårddisk) och expandera den för att visa inställningarna. Ställ in **Virtual Device Node** (Nod för virtuell enhet) på **IDE controller 0** (IDE-styrenhet 0).

     ![Anpassa inställningar – konfigurera en ny hård disk](./media/data-box-gateway-deploy-provision-vmware/customize-settings-new-disk.png)

17. (Valfritt) *Utför bara det här steget om du använder VMware ESXi Server 6.7*. På sidan **Customize settings** (Anpassa inställningar) klickar du på **VM options** (VM-alternativ). Gå till **Boot options > Firmware** (Startalternativ > Inbyggd programvara) och ändra den till **BIOS**. Som standard är värdet inställt på EFI. Klicka på **Nästa**.

    ![Sidan anpassa inställningar om du kör VMware ESXi Server 6,7](./media/data-box-gateway-deploy-provision-vmware/customize-settings-new-disk-esxi.png)

18. På sidan **Ready to Complete** (Redo att slutföra)granskar du alla inställningar associerade med den nya virtuella datorn. Kontrollera att CPU är 4, minnet är 8 192 MB, nätverksgränssnittet är 1 och hårddisk 2 har IDE-styrenhet 0. Klicka på **Finish**.
   
    ![Klar för att slutföra sidan ](./media/data-box-gateway-deploy-provision-vmware/image16.png)
     ![ 2](./media/data-box-gateway-deploy-provision-vmware/image17.png)

Den virtuella datorn är nu etablerad. Ett meddelande om detta visas och den nya virtuella datorn har lagts till i listan över virtuella datorer.

![Ny virtuell dator har lagts till i listan över virtuella datorer](./media/data-box-gateway-deploy-provision-vmware/image17.png)

Nästa steg är att starta den här virtuella datorn och hämta IP-adressen.

> [!NOTE]
> Vi rekommenderar att du inte installerar VMware-verktyg på din virtuella enhet (enligt etableringen ovan). Installation av VMware-verktyg resulterar i en konfiguration som inte stöds.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Starta den virtuella enheten och hämta IP-adressen

Utför följande steg för att starta den virtuella enheten och ansluta till den.

#### <a name="to-start-the-virtual-device"></a>Så startar du den virtuella enheten
1. Starta den virtuella enheten. I det högra fönstret väljer du din enhet i listan över virtuella datorer och högerklickar för att ta fram snabbmenyn. Välj **Power** (Av/på) och sedan **Power on** (På). Den virtuella datorn bör aktiveras. Du kan visa statusen i fönstret längst ned i webbklienten.

    ![Slå på den virtuella enheten](./media/data-box-gateway-deploy-provision-vmware/image19.png)

2. Välj din virtuella dator igen. Högerklicka och välj **Console** (Konsol) och välj sedan **Open in a new window** (Öppna i nytt fönster).

    ![Öppna den virtuella enhets konsolen](./media/data-box-gateway-deploy-provision-vmware/image20.png)

3. Konsolen för virtuell dator öppnas i ett nytt fönster. 

    ![Virtuell enhets konsol](./media/data-box-gateway-deploy-provision-vmware/image21.png)

4. När enheten körs pekar och klickar du på markören på fliken uppe i mitten i konsolfönstret. Välj **Guest OS (Gäst-OS) > Send keys (Skicka nycklar) > Ctrl+Alt+Delete**. Den virtuella datorn låses upp.

   ![Lås upp den virtuella enheten](./media/data-box-gateway-deploy-provision-vmware/image22.png)

5. Ange lösenordet för att logga in på datorn. Standard lösen ordet är *Password1*.

   ![Ange lösen ord för virtuell enhet](./media/data-box-gateway-deploy-provision-vmware/image23.png)

6. Steg 6–8 gäller bara när du startar i en icke-DHCP-miljö. Om du använder en DHCP-miljö hoppar du över dessa steg och går till steg 9. Om du har startat enheten i en icke-DHCP-miljö visas ett meddelande om detta: **Use the Set-HcsIPAddress cmdlet to configure the network** (Använd cmdleten Set-HcsIPAddress för att konfigurera nätverket). 
   
7. Konfigurera nätverket genom att i kommandotolken använda kommandot `Get-HcsIpAddress` för att lista de nätverksgränssnitt som har aktiverats på den virtuella enheten. Om enheten har ett enda nätverksgränssnitt aktiverad är det tilldelade standardnamnet för gränssnittet `Ethernet`.

8. Använd cmdleten `Set-HcsIpAddress` för att konfigurera nätverket. Ett exempel på detta visas nedan:

    `Set-HcsIpAddress –Name Ethernet0 –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

9. När den inledande installationen är klar och enheten har startats visas enhetens banderollstext. Anteckna den IP-adress och den URL som visas i banderollstexten för att hantera enheten. Du använder den här IP-adressen för att ansluta till webbgränssnittet för den virtuella enheten och slutföra den lokala installationen och aktiveringen.

   ![Banderoll-text och anslutnings-URL för virtuell enhet](./media/data-box-gateway-deploy-provision-vmware/image24.png)

Om enheten inte uppfyller minimikraven för konfiguration visas ett felmeddelande i banderollstexten (visas nedan). Du måste ändra enhetskonfigurationen så att den har tillräckliga resurser för att uppfylla minimikraven. Du kan sedan starta om och ansluta till enheten. Se till minikraven för konfiguration i [Kontrollera att värdsystemet uppfyller minimikraven för virtuella enheter](#check-the-host-system).

Om du möter andra fel under den inledande konfigurationen med hjälp av det lokala webb gränssnittet, se följande arbets flöden:

- [Kör diagnostiska tester för att felsöka konfiguration av webb gränssnitt](data-box-gateway-troubleshoot.md#run-diagnostics).
- [Generera logg paket och visa loggfiler](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Data Gateway-ämnen som att:

> [!div class="checklist"]
> * Se till att värden uppfyller minimikraven för enhet
> * Etablera en virtuell enhet i VMware
> * Starta den virtuella enheten och hämta IP-adressen

Gå vidare till nästa självstudie och lär dig hur du ansluter, konfigurerar och aktiverar din virtuella enhet.

* [Konfigurera och ansluta till resurser på Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)

