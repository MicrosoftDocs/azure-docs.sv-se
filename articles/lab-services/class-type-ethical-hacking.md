---
title: Konfigurera ett etiska hackning-labb med Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb med Azure Lab Services för att under Visa etiska hackning.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 97cdf13f39cc73ee7f35fb402229469195f1456c
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616430"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Konfigurera ett labb för att lära dig etiska hackning-klassen

Den här artikeln visar hur du konfigurerar en klass som fokuserar på data utredning sida av etiska hackning. Testning av inträngning, en övning som används av den etiska hackning-communityn, inträffar när någon försöker få åtkomst till systemet eller nätverket för att Visa sårbarheter som en skadlig angripare kan utnyttja.

I en etisk hackning-klass kan studenter lära sig moderna tekniker för att försvara mot sårbarheter. Varje elev får en virtuell Windows Server-värd dator som har två kapslade virtuella datorer – en virtuell dator med [Metasploitable3](https://github.com/rapid7/metasploitable3) -avbildning och en annan dator med [Kali Linux](https://www.kali.org/) -avbildning. Den virtuella Metasploitable-datorn används för att utnyttja och Kali virtuell dator ger till gång till de verktyg som behövs för att köra kriminal tekniska-uppgifter.

Den här artikeln innehåller två huvud avsnitt. Det första avsnittet beskriver hur du skapar klass rummets labb. I det andra avsnittet beskrivs hur du skapar en mall med kapslad virtualisering aktive rad och de verktyg och bilder som behövs. I det här fallet en Metasploitable avbildning och en Kali Linux-avbildning på en dator som har Hyper-V aktiverat som värd för avbildningarna.

## <a name="lab-configuration"></a>Labb konfiguration

Du måste ha en Azure-prenumeration för att komma igång för att kunna konfigurera det här labbet. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har skaffat en Azure-prenumeration kan du antingen skapa ett nytt labb konto i Azure Lab Services eller använda ett befintligt konto. Se följande självstudie för att skapa ett nytt labb konto: [självstudier för att konfigurera ett labb konto](tutorial-setup-lab-account.md).

Följ [den här självstudien](tutorial-setup-classroom-lab.md) för att skapa ett nytt labb och Använd sedan följande inställningar:

| Storlek för virtuell dator | Bild |
| -------------------- | ----- |
| Medium (kapslad virtualisering) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Mall dator

När du har skapat mallen startar du datorn och ansluter till den för att slutföra följande tre huvudsakliga uppgifter.

1. Konfigurera datorn för kapslad virtualisering. Det möjliggör alla lämpliga Windows-funktioner, till exempel Hyper-V, och konfigurerar nätverk för Hyper-V-avbildningar för att kunna kommunicera med varandra och Internet.
2. Konfigurera [Kali](https://www.kali.org/) Linux-avbildningen. Kali är en Linux-distribution som innehåller verktyg för inträngande testning och säkerhets granskning.
3. Konfigurera Metasploitable-avbildningen. I det här exemplet används [Metasploitable3](https://github.com/rapid7/metasploitable3) -avbildningen. Den här avbildningen skapas för att syftet ska kunna innehålla säkerhets risker.

Resten av den här artikeln beskriver de manuella stegen för att slutföra de uppgifter som visas ovan.  Du kan också köra [labb tjänster för Hyper-V-skript](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) och [labb tjänster etiska hackning-skript](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Förbered mall för kapslad virtualisering

Följ instruktionerna för att [Aktivera kapslad virtualisering](how-to-enable-nested-virtualization-template-vm.md) för att förbereda din virtuella mall för inkapslad virtualisering.

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Konfigurera en kapslad virtuell dator med Kali Linux-avbildning

Kali är en Linux-distribution som innehåller verktyg för inträngande testning och säkerhets granskning.

1. Ladda ned avbildning från [stötande säkerhets Kali Linux VM-avbildningar](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/).  Kom ihåg standard användar namn och lösen ord som antecknas på hämtnings sidan.
    1. Ladda ned **Kali Linux VMware 64-bit-avbildningen (7z)** för VMware.
    1. Extrahera. 7z-filen.  Om du inte redan har 7 zip laddar du ned det från [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) . Kom ihåg platsen för den extraherade mappen eftersom du behöver den senare.
1. Konvertera den extraherade VMDK-filen till en VHDX-fil så att du kan använda VHDX-filen med Hyper-V. Det finns flera tillgängliga verktyg för att konvertera VMware-avbildningar till Hyper-V-avbildningar.  Vi använder [STARWIND V2V-konverteraren](https://www.starwindsoftware.com/starwind-v2v-converter).  För att ladda ned, se [hämtnings sidan för STARWIND V2V Converter](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Starta **STARWIND V2V-konverteraren**.
    1. På sidan **Välj platsen för bilden som ska konverteras väljer du** **lokal fil**.  Välj **Nästa**.
    1. På sidan **käll avbildning** navigerar du till och väljer den Kali Linux VMDK-fil som extraherades i föregående steg för inställningen **fil namn** .  Filen kommer att ha formatet Kali-Linux-{version}-VMware-amd64. vmdk.  Välj **Nästa**.
    1. På sidan **Välj plats för mål avbildningen** väljer du **lokal fil**.  Välj **Nästa**.
    1. På sidan **Välj mål avbildnings format** väljer du **VHD/VHDX**.  Välj **Nästa**.
    1. På sidan **Välj alternativ för VHD/VHDX-avbildnings format väljer du** **VHDX-förstorings Bart bild**.  Välj **Nästa**.
    1. På sidan **Välj mål fil namn** accepterar du standard fil namnet.  Välj **konvertera**.
    1. På sidan **konvertera** väntar du på att bilden ska konverteras.  Det här kan ta flera minuter.  Välj **Slutför** när konverteringen är klar.
1. Skapa en ny virtuell Hyper-V-dator.
    1. Öppna **Hyper-V Manager**.
    1. Välj **åtgärd**  ->  **ny**  ->  **virtuell dator**.
    1. På sidan **innan du börjar** i **guiden Ny virtuell dator** väljer du **Nästa**.
    1. På sidan **Ange namn och plats** anger du **Kali-Linux** som **namn** och väljer **Nästa**.
    1. På sidan **Ange generation** accepterar du standardvärdena och väljer **Nästa**.
    1. På sidan **tilldela minne** anger du **2048 MB** som **Start minne** och väljer **sedan nästa**.
    1. På sidan **Konfigurera nätverk** låter du anslutningen vara **ansluten**. Du konfigurerar nätverkskortet senare.
    1. På sidan **Anslut virtuell hård disk** väljer du **Använd en befintlig virtuell hård disk**. Bläddra till platsen för **Kali-Linux-{version}-VMware-amd64. vmdk-** filen som skapades i föregående steg och välj **Nästa**.
    1. På sidan **Slutför guiden Ny virtuell dator** och välj **Slutför**.
    1. När den virtuella datorn har skapats väljer du den i Hyper-V Manager. Aktivera inte datorn ännu.  
    1. Välj **Åtgärds**  ->  **Inställningar**.
    1. I dialog rutan **Inställningar för Kali-Linux** för väljer du **Lägg till maskin vara**.
    1. Välj **bakåtkompatibelt** nätverkskort och välj **Lägg till**.
    1. På sidan **bakåtkompatibelt** nätverkskort väljer du **LabServicesSwitch** för den **virtuella växel** inställningen och väljer **OK**. LabServicesSwitch skapades när du förbereder mallen för Hyper-V i avsnittet **Förbered mall för kapslad virtualisering** .
    1. Kali-Linux avbildningen är nu redo att användas. Från **Hyper-V Manager** väljer du **Åtgärds**  ->  **Start** och väljer sedan **åtgärd**  ->  **Anslut** för att ansluta till den virtuella datorn.  Standard användar namnet är **Kali** och lösen ordet är **Kali**.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Konfigurera en kapslad virtuell dator med Metasploitable-avbildning  

Rapid7 Metasploitable-avbildningen är en avbildning som har kon figurer ATS med säkerhets problem. Du använder den här bilden för att testa och hitta problem. Följande instruktioner visar hur du använder en redan skapad Metasploitable-avbildning. Men om en nyare version av Metasploitable-avbildningen behövs, se [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) .

1. Ladda ned Metasploitable-avbildningen.
    1. Navigera till [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html) . Fyll i formuläret för att ladda ned avbildningen och välj knappen **Skicka** .
    2. Välj knappen **Hämta Metasploitable nu** .
    3. När zip-filen har laddats ned extraherar du zip-filen och kommer ihåg platsen för filen Metasploitable. vmdk.
1. Konvertera den extraherade VMDK-filen till en VHDX-fil så att du kan använda VHDX-filen med Hyper-V. Det finns flera tillgängliga verktyg för att konvertera VMware-avbildningar till Hyper-V-avbildningar.  Vi använder [STARWIND V2V-konverteraren](https://www.starwindsoftware.com/starwind-v2v-converter) igen.  För att ladda ned, se [hämtnings sidan för STARWIND V2V Converter](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Starta **STARWIND V2V-konverteraren**.
    1. På sidan **Välj platsen för bilden som ska konverteras väljer du** **lokal fil**.  Välj **Nästa**.
    1. På sidan **käll avbildning** navigerar du till och väljer den Metasploitable. vmdk som extraherades i föregående steg för inställningen **fil namn** .  Välj **Nästa**.
    1. På sidan **Välj plats för mål avbildningen** väljer du **lokal fil**.  Välj **Nästa**.
    1. På sidan **Välj mål avbildnings format** väljer du **VHD/VHDX**.  Välj **Nästa**.
    1. På sidan **Välj alternativ för VHD/VHDX-avbildnings format väljer du** **VHDX-förstorings Bart bild**.  Välj **Nästa**.
    1. På sidan **Välj mål fil namn** accepterar du standard fil namnet.  Välj **konvertera**.
    1. På sidan **konvertera** väntar du på att bilden ska konverteras.  Det här kan ta flera minuter.  Välj **Slutför** när konverteringen är klar.
1. Skapa en ny virtuell Hyper-V-dator.
    1. Öppna **Hyper-V Manager**.
    1. Välj **åtgärd**  ->  **ny**  ->  **virtuell dator**.
    1. På sidan **innan du börjar** i **guiden Ny virtuell dator** väljer du **Nästa**.
    1. På sidan **Ange namn och plats** anger du **Metasploitable** som **namn** och väljer **Nästa**.

        ![Guiden ny virtuell dator avbildning](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. På sidan **Ange generation** accepterar du standardvärdena och väljer **Nästa**.
    1. På sidan **tilldela minne** anger du **512 MB** som **Start minne** och väljer **sedan nästa**.

        ![Sidan tilldela minne](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. På sidan **Konfigurera nätverk** låter du anslutningen vara **ansluten**. Du konfigurerar nätverkskortet senare.
    1. På sidan **Anslut virtuell hård disk** väljer du **Använd en befintlig virtuell hård disk**. Bläddra till platsen för **metasploitable. vhdx** -filen som skapades i föregående steg och välj **Nästa**.

        ![Sidan Anslut virtuell nätverks disk](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. På sidan **Slutför guiden Ny virtuell dator** och välj **Slutför**.
    1. När den virtuella datorn har skapats väljer du den i Hyper-V Manager. Aktivera inte datorn ännu.  
    1. Välj **Åtgärds**  ->  **Inställningar**.
    1. I dialog rutan **Inställningar för Metasploitable** väljer du **Lägg till maskin vara**.
    1. Välj **bakåtkompatibelt** nätverkskort och välj **Lägg till**.

        ![Sidan nätverkskort](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. På sidan **bakåtkompatibelt** nätverkskort väljer du **LabServicesSwitch** för den **virtuella växel** inställningen och väljer **OK**. LabServicesSwitch skapades när du förbereder mallen för Hyper-V i avsnittet **Förbered mall för kapslad virtualisering** .

        ![Sidan bakåtkompatibelt nätverkskort](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Metasploitable-avbildningen är nu redo att användas. Från **Hyper-V Manager** väljer du **Åtgärds**  ->  **Start** och väljer sedan **åtgärd**  ->  **Anslut** för att ansluta till den virtuella datorn.  Standard användar namnet är **msfadmin** och lösen ordet är **msfadmin**.

Mallen har nu uppdaterats och innehåller bilder som behövs för en etisk hackning-inträngning test klass, en bild med verktyg för att göra inträngande testning och en annan bild med säkerhets problem som kan upptäckas. Nu kan du publicera mallfilen till-klassen. Klicka på knappen **publicera** på mallsida för att publicera mallen i labbet.

## <a name="cost"></a>Cost  

Om du vill uppskatta kostnaden för det här labbet kan du använda följande exempel:

För en klass av 25 studenter med 20 timmar schemalagda klass tider och 10 timmars kvot för läxor eller tilldelningar, skulle priset för labbet vara:

25 studenter \* (20 + 10) timmar \* 55 labb enheter \* 0,01 USD per timme = 412,50 USD

>[!IMPORTANT]
>Kostnads uppskattning är till exempel endast syfte. Aktuell information om priser finns i [Azure Lab Services priser](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats

Den här artikeln gick dig genom stegen för att skapa ett labb för en etisk hackning-klass. Den innehåller steg för att konfigurera kapslad virtualisering för att skapa två virtuella datorer i den virtuella värd datorn för att tränga in test.

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in labb:

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users).
