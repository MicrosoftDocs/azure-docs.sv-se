---
title: Självstudiekurs om etablering av Azure Data Box Gateway i Hyper-V | Microsoft Docs
description: Den andra självstudiekursen om att distribuera Azure Data Box Gateway innebär att etablera en virtuell enhet i Hyper-V.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 2e54b8e58e8139c46639187957906790ffb0fe54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727693"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-hyper-v"></a>Självstudie: etablera Azure Data Box Gateway i Hyper-V

## <a name="overview"></a>Översikt

Den här självstudien beskriver hur du etablerar en Data Box Gateway i ett värdsystem som kör Hyper-V i Windows Server 2016, Windows Server 2012 R2 eller Windows Server 2012.

Du behöver ha administratörsbehörighet för att etablera och konfigurera en virtuell enhet. Etableringen och den inledande installationen kan ta ungefär 10 minuter att slutföra. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Se till att värden uppfyller minimikraven för enhet
> * Etablera en virtuell enhet i hypervisor-program
> * Starta den virtuella enheten och hämta IP-adressen

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

De nödvändiga komponenterna för att etablera en virtuell enhet på ett värdsystem som kör Hyper-V i Windows Server 2016 eller Windows Server 2012 R2 följer nedan.

### <a name="for-the-data-box-gateway-resource"></a>För Data Box Gateway-resursen

Innan du börjar ska du kontrollera att:

* Du har slutfört alla stegen i [Förbereda portalen för Data Box Gateway](data-box-gateway-deploy-prep.md).
* Du har laddat ned avbildningen av den virtuella enheten för Hyper-V från Azure-portalen enligt beskrivningen i [Förbereda portalen för Data Box Gateway](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > Programvaran som körs på Data Box Gateway kan bara användas med Data Box Gateway-resursen.

### <a name="for-the-data-box-gateway-virtual-device"></a>För den virtuella Data Box Gateway-enheten

Innan du distribuerar en enhet kontrollerar du att:

* Du har åtkomst till ett värdsystem som kör Hyper-V i Windows Server 2012 R2 eller senare som kan användas för att etablera en enhet.
* Värdsystemet kan dedikera följande resurser för att etablera den virtuella enheten:

  * Minst 4 kärnor.
  * Minst 8 GB RAM. Vi rekommenderar starkt minst 16 GB RAM-minne.
  * Ett nätverksgränssnitt.
  * En 250 GB OS-disk.
  * En virtuell disk på 2 TB för data.

### <a name="for-the-network-in-the-datacenter"></a>För nätverket i datacentret

Innan du börjar:

* Granska nätverkskraven för att distribuera en Data Box Gateway och konfigurera datacenternätverket enligt kraven. Mer information finns i [Nätverkskrav för Data Box Gateway](data-box-gateway-system-requirements.md#networking-port-requirements).
* Se till att den minsta Internetbandbredden är 20 Mbps för att enheten ska fungera optimalt.

## <a name="check-the-host-system"></a>Kontrollera värdsystemet

För att skapa en virtuell enhet behöver du följande:

* Hyper-V-rollen installerad i Windows Server 2016, Windows Server 2012 R2 eller Windows Server 2012.
* Microsoft Hyper-V Manager på en Microsoft Windows-klient som är ansluten till värden.
* Kontrollera att den underliggande maskinvaran (värdsystemet) där du skapar den virtuella enheten kan dedikera följande resurser till din virtuella enhet:

  * Minst 4 virtuella processorer.
  * Minst 8 GB RAM.
  * Ett nätverksgränssnitt anslutet till det nätverk som kan dirigera trafik till Internet.
  * En 250 GB OS-disk.
  * En virtuell disk på 2 TB för system data.

## <a name="bitlocker-considerations"></a>BitLocker-överväganden

* Vi rekommenderar att du aktiverar BitLocker på din Data Box Gateway virtuella dator. BitLocker är inte aktiverat som standard. Mer information finns i:
  * [Inställningar för krypterings stöd i Hyper-V Manager](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
  * [Stöd för BitLocker i en virtuell dator](https://kb.vmware.com/s/article/2036142)

## <a name="provision-a-virtual-device-in-hypervisor"></a>Etablera en virtuell enhet i hypervisor-program

Utför följande steg för att etablera en enhet i ditt hypervisor-program.

1. På din Windows Server-värd kopierar du avbildningen av den virtuella enheten till en lokal disk. Du har laddat ned den här VHDX-avbildningen via Azure-portalen. Anteckna den plats dit du har kopierat avbildningen eftersom du använder den här avbildningen senare i proceduren.

2. Öppna **Serverhanteraren**. Klicka på **verktyg** i det övre högra hörnet och välj **Hyper-V Manager**.

    ![Välj Hyper-V Manager i Serverhanteraren](./media/data-box-gateway-deploy-provision-hyperv/image1.png)

3. I **Hyper-V Manager** går du till fönsterrutan för omfång, högerklickar på din systemnod för att öppna snabbmenyn och klickar sedan på **Ny** > **Virtuell dator**.

   ![Skapa en ny virtuell dator i Hyper-V Manager](./media/data-box-gateway-deploy-provision-hyperv/image2.png)
4. På sidan **Innan du börjar** i guiden för ny virtuell dator klickar du på **Nästa**.
5. På sidan **Ange namn och plats** anger du ett **Namn** för din virtuella enhet. Klicka på **Nästa**.

   ![Sidan Ange namn och plats](./media/data-box-gateway-deploy-provision-hyperv/image3.png)
6. På sidan **Ange generation** väljer du **Generation 2** för .vhdx-enhetsavbildningstypen och klickar sedan på **Nästa**.    

   ![Sidan Ange generation](./media/data-box-gateway-deploy-provision-hyperv/image4.png)
7. På sidan **Tilldela minne** anger du ett **Startminne** på minst **8 192 MB**. Aktivera inte dynamiskt minne. Klicka sedan på **Nästa**.

   ![Sidan tilldela minne](./media/data-box-gateway-deploy-provision-hyperv/image5.png) 
8. På sidan **Konfigurera nätverk** anger du den virtuella växel som är ansluten till Internet och klickar sedan på **Nästa**.

   ![Sidan Konfigurera nätverk](./media/data-box-gateway-deploy-provision-hyperv/image6.png)
9. På sidan **Anslut virtuell hårddisk** väljer du **Använd en befintlig virtuell hårddisk**, anger platsen för den virtuella enhetsavbildningen och klickar sedan på **Nästa**.

   ![Sidan Anslut virtuell hård disk](./media/data-box-gateway-deploy-provision-hyperv/image7.png)
10. Granska **sammanfattningen** och klicka sedan på **Slutför** för att skapa den virtuella datorn.

    ![Slutför guide sidan ny virtuell dator](./media/data-box-gateway-deploy-provision-hyperv/image8.png)
11. Du behöver fyra virtuella processorer för att uppfylla minimi kraven. Om du vill lägga till fyra virtuella processorer väljer du ditt värd system i fönstret **Hyper-V Manager** . I den högra fönsterrutan går du till listan över **virtuella datorer** och letar upp den virtuella dator som du nyss skapade. Markera och högerklicka på datornamnet och välj **Inställningar**.

    ![Inställningar för virtuella datorer](./media/data-box-gateway-deploy-provision-hyperv/image9.png)
12. På sidan **Inställningar** går du till den vänstra fönsterruta och klickar på **Processor**. I den högra fönsterrutan ställer du in **antal virtuella processorer** på 4 (eller fler). Klicka på **Applicera**.

    ![Ange antal virtuella processorer på sidan Inställningar](./media/data-box-gateway-deploy-provision-hyperv/image10.png)
13. För att uppfylla minimi kraven måste du också lägga till en virtuell data disk på 2 TB. På sidan **Inställningar**:

    1. I den vänstra fönsterrutan väljer du **SCSI Controller**.
    2. I den högra fönsterrutan väljer du **Hårddisk** och klickar på **Lägg till**.

    ![Sidan Lägg till hård disk på Inställningar](./media/data-box-gateway-deploy-provision-hyperv/image11.png)
14. På sidan **Hårddisk** väljer du alternativet **Virtuell hårddisk** och klickar på **Ny**. **Guiden för ny virtuell hårddisk** startar.

    ![Guiden ny virtuell hård disk](./media/data-box-gateway-deploy-provision-hyperv/image12.png)
15. På sidan **Innan du börjar** i guiden för ny virtuell hårddisk klickar du på **Nästa**.
16. På **sidan för att välja diskformat** godkänner du standardalternativet för **VHDX**-format. Klicka på **Nästa**.
17. På **sidan för att välja disktyp** ställer du in typen av virtuell hårddisk till **Dynamiskt expanderande** (rekommenderas). En disk med **Fast storlek** skulle också fungera, men då kan du behöva vänta länge. Vi rekommenderar att du inte använder alternativet **Differentierande**. Klicka på **Nästa**.

    ![Sidan Välj disktyp](./media/data-box-gateway-deploy-provision-hyperv/image13.png)
18. På sidan **Ange namn och plats** anger du ett **namn** och en **plats** (du kan bläddra till en) för data disken. Klicka på **Nästa**.

    ![Sidan Ange namn och plats](./media/data-box-gateway-deploy-provision-hyperv/image14.png)
19. På sidan **Konfigurera disk** väljer du alternativet **skapa en ny tom virtuell hård disk** och anger storleken som **2 TB** (eller mer).

    2 TB är minimikravet, men du kan alltid etablera en större disk. Observera att det inte går att krympa disken när den har tillhandahållits. Om du försöker minska disk resultatet försvinner alla lokala data på enheten. Det finns inte stöd för att utöka data disken. Klicka på **Nästa**.

    ![Sidan Konfigurera disk](./media/data-box-gateway-deploy-provision-hyperv/image15.png)
20. På sidan **Sammanfattning** läser du informationen om din virtuella datadisk, och om allt stämmer klickar du på **Slutför** för att skapa disken. Guiden stängs och en virtuell hårddisk läggs till i datorn.

    ![Sidan guiden Ny virtuell hård disk slutförs](./media/data-box-gateway-deploy-provision-hyperv/image16.png)
21. Gå tillbaka till sidan **Inställningar**. Klicka på **OK** för att stänga sidan **Inställningar** och gå tillbaka till Hyper-V Manager-fönstret.

    ![Sidan Inställningar](./media/data-box-gateway-deploy-provision-hyperv/image17.png)

Den virtuella datorn har nu kon figurer ATS helt.

> [!NOTE]
> Du kan inte etablera en ny Data Box Gateway genom att kopiera den konfigurerade virtuella hård disken. Varje ny Data Box Gateway virtuell enhet måste tillhandahållas från en virtuell enhets avbildning för Hyper-V som hämtas från Azure Portal.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Starta den virtuella enheten och hämta IP-adressen

Utför följande steg för att starta den virtuella enheten och ansluta till den.

#### <a name="to-start-the-virtual-device"></a>Så startar du den virtuella enheten

1. Starta den virtuella enheten.

   ![Starta virtuell enhet](./media/data-box-gateway-deploy-provision-hyperv/image18.png)
2. När enheten körs väljer du enheten, högerklickar och väljer **Anslut**.

3. Du kan behöva vänta 10–15 minuter innan enheten blir klar. Ett statusmeddelande visas på konsolen som visar förloppet. När enheten är klar går du till **Åtgärd**. Tryck `Ctrl + Alt + Delete` för att logga in på den virtuella enheten. Standardanvändaren är *EdgeUser* och standardlösenordet är *Password1*.

   ![Logga in på den virtuella enheten](./media/data-box-gateway-deploy-provision-hyperv/image21.png)

4. Steg 5–7 gäller bara när du startar i en icke-DHCP-miljö. Om du använder en DHCP-miljö hoppar du över dessa steg. Om du startade enheten i en icke-DHCP-miljö visas ett meddelande som reflekterar det.

5. Konfigurera nätverket genom att använda kommandot `Get-HcsIpAddress` för att lista de nätverksgränssnitt som har aktiverats på den virtuella enheten. Om enheten har ett enda nätverksgränssnitt aktiverad är det tilldelade standardnamnet för gränssnittet `Ethernet`.

6. Använd cmdleten `Set-HcsIpAddress` för att konfigurera nätverket. Se följande exempel:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

7. När den inledande installationen är klar och enheten har startats visas enhetens banderollstext. Anteckna den IP-adress och den URL som visas i banderollstexten för att hantera enheten. Använd den här IP-adressen för att ansluta till webbgränssnittet för den virtuella enheten och slutföra den lokala installationen och aktiveringen.

   ![Banderoll för virtuell enhet med IP-adress och anslutnings-URL](./media/data-box-gateway-deploy-provision-hyperv/image23.png)

Om enheten inte uppfyller minimikraven för konfiguration visas ett felmeddelande i banderollstexten. Ändra enhetskonfigurationen så att datorn har tillräckliga resurser för att uppfylla minimikraven. Du kan sedan starta om och ansluta till enheten. Se de lägsta konfigurations kraven i [kontrol lera att värd systemet uppfyller minimi kraven för virtuella enheter](#check-the-host-system).

Om du möter andra fel under den inledande konfigurationen med hjälp av det lokala webb gränssnittet, se följande arbets flöden:

* [Kör diagnostiska tester för att felsöka konfiguration av webb gränssnitt](data-box-gateway-troubleshoot.md#run-diagnostics).
* [Generera logg paket och visa loggfiler](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Data Gateway-ämnen som att:

> [!div class="checklist"]
> * Se till att värden uppfyller minimikraven för enhet
> * Etablera en virtuell enhet i hypervisor-program
> * Starta den virtuella enheten och hämta IP-adressen

Gå vidare till nästa självstudie och lär dig hur du ansluter, konfigurerar och aktiverar din virtuella enhet.

> [!div class="nextstepaction"]
> [Ansluta och konfigurera din Data Box Gateway](./data-box-gateway-deploy-connect-setup-activate.md)
