---
title: Förbered virtuella VMware-datorer för skydd och återställning efter fel med Azure Site Recovery
description: Förbered för redundansväxling av virtuella VMware-datorer efter redundansväxlingen med Azure Site Recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: f3e352577d65bda021f886d6612c8c0101922003
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727514"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Förbereda för återaktivering av skydd och återställning efter fel för virtuella VMware-datorer

Efter [redundansväxlingen](site-recovery-failover.md) av lokala virtuella VMware-datorer eller fysiska servrar till Azure skyddar du de virtuella Azure-datorer som skapats efter redundansväxlingen, så att de replikeras tillbaka till den lokala platsen. Med replikering från Azure till lokal plats kan du sedan växla tillbaka genom att köra en redundansväxling från Azure till lokalt när du är klar.

Innan du fortsätter får du en snabb överblick över den här videon om hur du växlar tillbaka från Azure till en lokal plats.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Komponenter för återskydd/återställning efter fel

Du behöver ett antal komponenter och inställningar på plats innan du kan återaktivera och återställa från Azure.

| **Komponent**| **Information** |
| --- | --- |
| **Lokal konfigurationsserver** | Den lokala konfigurations servern måste köras och vara ansluten till Azure.<br/><br/> Den virtuella dator som du växlar tillbaka till måste finnas i konfigurations serverns databas. Om haveriet påverkar konfigurations servern återställer du den med samma IP-adress för att se till att återställning efter fel fungerar.<br/><br/>  Om IP-adresser för replikerade datorer behålls vid redundans, ska plats-till-plats-anslutning (eller ExpressRoute-anslutning) upprättas mellan virtuella datorer i Azure och failback-NÄTVERKSKORTet på konfigurations servern. För kvarhållna IP-adresser behöver konfigurations servern två nätverkskort – ett för anslutning till käll datorer och en för Azures återställning efter fel. Detta förhindrar överlappande av adress intervall för under nätet för källan och misslyckades med virtuella datorer. |
| **Processerver i Azure** | Du behöver en processerver i Azure innan du kan växla tillbaka till din lokala plats.<br/><br/> Processervern tar emot data från den skyddade virtuella Azure-datorn och skickar den till den lokala platsen.<br/><br/> Du behöver ett nätverk med låg latens mellan processervern och den skyddade virtuella datorn, så vi rekommenderar att du distribuerar processervern i Azure för högre replikerings prestanda.<br/><br/> För koncept bevis kan du använda den lokala processervern och ExpressRoute med privat peering.<br/><br/> Processervern bör finnas i det Azure-nätverk där den misslyckade virtuella datorn finns. Processervern måste också kunna kommunicera med den lokala konfigurations servern och huvud mål servern. |
| **Separat huvud mål Server** | Huvud mål servern tar emot återställnings data och som standard körs en Windows huvud mål server på den lokala konfigurations servern.<br/><br/> En huvud mål server kan ha upp till 60 diskar anslutna till sig. De virtuella datorerna som återställs har mer än en samlad summa på 60 diskar, eller om du inte återställer stora volymer av trafik, skapar du en separat huvud mål server för återställning efter fel.<br/><br/> Om datorerna samlas in i en replikeringsgrupp för konsekvens för flera virtuella datorer måste de virtuella datorerna vara Windows, eller måste alla vara Linux. Varför? Eftersom alla virtuella datorer i en replikeringsgrupp måste använda samma huvud mål server, och huvud mål servern måste ha samma operativ system (med samma eller en senare version) än för de replikerade datorerna.<br/><br/> Huvud mål servern får inte ha några ögonblicks bilder på sina diskar, annars fungerar inte återställningen och återställning efter fel.<br/><br/> Huvud målet kan inte ha en paravirtuell SCSI-styrenhet. Kontrollanten kan bara vara en LSI Logic-styrenhet. Utan en LSI Logic-kontroll, Miss lyckas återskydd. |
| **Replikeringsprincip för återställning efter fel** | Om du vill replikera tillbaka till den lokala platsen behöver du en princip för återställning efter fel. Den här principen skapas automatiskt när du skapar en replikeringsprincip i Azure.<br/><br/> Principen associeras automatiskt med konfigurationsservern. Den ställs in på ett återställnings tröskelvärde på 15 minuter, kvarhållning av återställnings punkter i 24 timmar och frekvensen för programkonsekventa ögonblicks bilder är 60 minuter. Det går inte att redigera principen. |
| **Plats-till-plats-VPN/ExpressRoute privat peering** | Skydd och återställning efter fel kräver en VPN-anslutning från plats till plats eller ExpressRoute privata peering för att replikera data. |


## <a name="ports-for-reprotectionfailback"></a>Portar för skydd/återställning efter fel

Ett antal portar måste vara öppna för skydd/återställning efter fel. Följande bild visar portarna och återställningen av återställnings-och återställnings flödet.

![Portar för redundans och återställning efter fel](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Distribuera en processerver i Azure

1. [Konfigurera en processerver](vmware-azure-set-up-process-server-azure.md) i Azure för återställning efter fel.
2. Se till att de virtuella Azure-datorerna kan komma åt processervern. 
3. Kontrol lera att plats-till-plats-VPN-anslutningen eller ExpressRoute privata peering-nätverket har tillräckligt med bandbredd för att skicka data från processervern till den lokala platsen.

## <a name="deploy-a-separate-master-target-server"></a>Distribuera en separat huvud mål Server

1. Observera [kraven och begränsningarna](#reprotectionfailback-components)för huvud mål servern.
2. Skapa en [Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) -eller [Linux](vmware-azure-install-linux-master-target.md) -huvud mål server för att matcha operativ systemet för de virtuella datorer som du vill återaktivera och återställa.
3. Kontrol lera att du inte använder Storage-vMotion för huvud mål servern, eller att återställning efter fel inte kan utföras. Det går inte att starta den virtuella dator datorn eftersom diskarna inte är tillgängliga.
    - Undvik detta genom att utesluta huvud mål servern från vMotion-listan.
    - Om ett huvud mål bevarar en lagrings vMotion-aktivitet efter återaktivering, migreras de skyddade VM-diskarna som är anslutna till huvud mål servern till målet för vMotion-aktiviteten. Om du försöker återställa efter detta Miss lyckas disk-från koppling eftersom diskarna inte hittas. Det är sedan svårt att hitta diskarna i dina lagrings konton. Om detta inträffar kan du söka efter dem manuellt och koppla dem till den virtuella datorn. Sedan kan den lokala virtuella datorn startas.

4. Lägg till en lagrings enhet till den befintliga huvud mål servern i Windows. Lägg till en ny disk och formatera enheten. Lagrings enheten används för att stoppa tidpunkterna då den virtuella datorn replikerar tillbaka till den lokala platsen. Observera dessa kriterier. Om de inte är uppfyllda visas inte enheten för huvud mål servern:
    - Volymen används inte i något annat syfte, till exempel ett målvärde, och är inte i låst läge.
    - Volymen är inte en cache-volym. Den anpassade installations volymen för processervern och huvud mål servern är inte giltig för en kvarhållningsperiod. När processervern och huvud målet är installerade på en volym, är volymen en cache-volym på huvud mål servern.
    - Volymens fil system typ är inte FAT eller FAT32.
    - Volym kapaciteten är inte noll.
    - Standard Retentions volymen för Windows är R-volymen.
    - Standard lagrings volymen för Linux är/mnt/retention.

5. Lägg till en enhet om du använder en befintlig processerver. Den nya enheten måste uppfylla kraven i det sista steget. Om lagrings enheten inte finns visas den inte i list rutan för val i portalen. När du har lagt till en enhet i det lokala huvud målet tar det upp till 15 minuter innan enheten visas i valet på portalen. Du kan uppdatera konfigurations servern om enheten inte visas efter 15 minuter.
6. Installera VMware-verktyg eller öppna-VM-verktyg på huvud mål servern. Utan verktygen går det inte att identifiera data lagringen på huvud mål serverns ESXi-värd.
7. Ange disken. Inställningen Enableuuid är = true i konfigurations parametrarna för den virtuella huvud mål datorn i VMware. Om den här raden inte finns lägger du till den. Den här inställningen krävs för att tillhandahålla ett konsekvent UUID till VMDK så att den monteras på rätt sätt.
8. Kontrol lera vCenter Server åtkomst krav:
    - Om den virtuella datorn som du växlar tillbaka till finns på en ESXi-värd som hanteras av VMware vCenter Server, behöver huvud mål servern åtkomst till den lokala VMDK-filen (Virtual Machine disk) för den lokala datorn, för att kunna skriva replikerade data till den virtuella datorns diskar. Kontrol lera att det lokala VM-datalagret är monterat på huvud mål värden med Läs-/Skriv behörighet.
    - Om den virtuella datorn inte finns på en ESXi-värd som hanteras av en VMware vCenter Server, skapar Site Recovery en ny virtuell dator under återskyddet. Den här virtuella datorn skapas på den ESXi-värd som du skapar den virtuella datorn för huvud mål servern på. Välj ESXi-värden noggrant för att skapa den virtuella datorn på den värd som du vill ha. Hårddisken på den virtuella datorn måste finnas i ett datalager som kan nås av den värd där huvudmålservern körs.
    - Ett annat alternativ om den lokala virtuella datorn redan finns för återställning efter fel, är att ta bort den innan du gör en återställning efter fel. Failback skapar sedan en ny virtuell dator på samma värd som huvud målets ESXi-värd. När du växlar tillbaka till en annan plats återställs data till samma data lager och samma ESXi-värd som används av den lokala huvud mål servern.
9. För att fysiska datorer ska kunna återställas till virtuella VMware-datorer bör du slutföra identifieringen av värden som huvud mål servern körs på, innan du kan skydda datorn igen.
10. Kontrol lera att den ESXi-värd på vilken den virtuella huvud datorn har minst ett VMFS-datalager som är kopplat till den. Om inga VMFS-datalager är kopplade är data lagrets indata i skydds inställningarna tomma och du kan inte fortsätta.


## <a name="next-steps"></a>Nästa steg

[Återaktivera skydd](vmware-azure-reprotect.md) för en virtuell dator.
