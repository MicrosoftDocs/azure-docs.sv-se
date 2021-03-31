---
title: Förbered för haveri beredskap för virtuella Hyper-V-datorer till Azure med Azure Site Recovery
description: Lär dig hur du förbereder lokala virtuella Hyper-V-datorer för haveri beredskap till Azure med Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6f24a259d2d71aa6599f6dd417d5e9fc99734e99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135653"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Förbereda lokala Hyper-V-servrar för haveriberedskap till Azure

Den här artikeln beskriver hur du förbereder en lokal Hyper-V-infrastruktur när du vill konfigurera haveri beredskap för Hyper-VMs till Azure med [Azure Site Recovery](site-recovery-overview.md).


Det här är den andra självstudien i en serie som visar hur du konfigurerar haveri beredskap till Azure för lokala virtuella Hyper-V-datorer. I den första självstudien [ställer vi in de Azure-komponenter](tutorial-prepare-azure.md) som krävs för haveri beredskap för Hyper-V.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Granska Hyper-V-krav och VMM-krav om dina Hyper-V-värdar hanteras av System Center VMM.
> * Förbered VMM om det är tillämpligt.
> * Kontrol lera Internet åtkomst till Azure-platser.
> * Förbered virtuella datorer så att du kan komma åt dem efter en redundansväxling till Azure.

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i artikeln i avsnittet så här i Site Recovery innehålls förteckningen.

## <a name="before-you-start"></a>Innan du börjar

Se till att du har för berett Azure enligt beskrivningen i den [första självstudien i den här serien](tutorial-prepare-azure.md).

## <a name="review-requirements-and-prerequisites"></a>Granska krav och krav

Kontrol lera att Hyper-V-värdar och virtuella datorer uppfyller kraven.

1. [Kontrol lera](hyper-v-azure-support-matrix.md#on-premises-servers) krav för lokal server.
2. [Kontrol lera kraven](hyper-v-azure-support-matrix.md#replicated-vms) för virtuella Hyper-V-datorer som du vill replikera till Azure.
3. Kontrol lera [nätverk](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)för Hyper-V-värden; och stöd för värd-och gäst [lagring](hyper-v-azure-support-matrix.md#hyper-v-host-storage) för lokala Hyper-V-värdar.
4. Kontrollera vad som stöds när det gäller [Azure-nätverk](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [lagring](hyper-v-azure-support-matrix.md#azure-storage) och [compute](hyper-v-azure-support-matrix.md#azure-compute-features) efter redundansväxling.
5. Dina lokala virtuella datorer som du replikerar till Azure måste uppfylla [kraven för virtuella Azure-datorer](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Förbered VMM (valfritt)

Om Hyper-V-värdar hanteras av VMM måste du förbereda den lokala VMM-servern. 

- Se till att VMM-servern har minst ett moln, med en eller flera värd grupper. Den Hyper-V-värd som de virtuella datorerna körs på bör finnas i molnet.
- Förbered VMM-servern för nätverks mappning.

### <a name="prepare-vmm-for-network-mapping"></a>Förbereda VMM för nätverks mappning

Om du använder VMM mappas [nätverks mappning](./hyper-v-vmm-network-mapping.md) mellan lokala VMM VM-nätverk och virtuella Azure-nätverk. Mappning garanterar att virtuella Azure-datorer är anslutna till rätt nätverk när de skapas efter en redundansväxling.

Förbered VMM för nätverks mappning enligt följande:

1. Se till att du har ett [logiskt VMM-nätverk](/system-center/vmm/network-logical) som är associerat med molnet där Hyper-V-värdarna finns.
2. Se till att du har ett [virtuellt dator nätverk](/system-center/vmm/network-virtual) som är kopplat till det logiska nätverket.
3. I VMM ansluter du de virtuella datorerna till det virtuella dator nätverket.

## <a name="verify-internet-access"></a>Verifiera Internet åtkomst

1. I självstudien är den enklaste konfigurationen för Hyper-V-värdar och VMM-servern att ha direkt åtkomst till Internet utan att använda en proxyserver. 
2. Se till att Hyper-V-värdar och VMM-servern om det behövs kan komma åt de URL: er som krävs nedan.   
3. Om du styr åtkomst efter IP-adress kontrollerar du att:
    - IP-adressbaserade brand Väggs regler kan ansluta till [Azure datacenter IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653)och HTTPS-porten (443).
    - Tillåt IP-adressintervall för Azure-regionen för din prenumeration.
    
### <a name="required-urls"></a>Obligatoriska URL: er


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Under ett failover-scenario kanske du vill ansluta till det replikerade lokala nätverket.

För att ansluta till virtuella Windows-datorer med RDP efter redundans, Tillåt åtkomst enligt följande:

1. För att få åtkomst via Internet aktiverar du RDP på den lokala virtuella datorn före redundansen. Se till att TCP-och UDP-regler läggs till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen**  >  **tillåtna appar** för alla profiler.
2. För åtkomst via plats-till-plats-VPN aktiverar du RDP på den lokala datorn. RDP bör tillåtas i **Windows-brandväggen**  ->  **tillåtna appar och funktioner** för **domän nätverk och privata** nätverk.
   Kontrollera att operativsystemets SAN-princip har angetts till **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135). Det får inte finnas några väntande Windows-uppdateringar på den virtuella datorn när du utlöser en redundans. I så fall kan du inte logga in på den virtuella datorn förrän uppdateringen är klar.
3. Efter en redundans av en virtuell Windows Azure-dator, kontrollerar du att **Startdiagnostik** visar en skärmbild av den virtuella datorn. Om du inte kan ansluta kontrollerar du att den virtuella datorn körs. Granska sedan dessa [felsökningstips](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Efter redundansväxlingen kan du komma åt virtuella Azure-datorer med samma IP-adress som den replikerade lokala virtuella datorn eller en annan IP-adress. [Läs mer](concepts-on-premises-to-azure-networking.md) om hur du konfigurerar IP-adresser för redundans.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera katastrof återställning till Azure för virtuella Hyper-V-datorer](./hyper-v-azure-tutorial.md) 
>  [Konfigurera katastrof återställning till Azure för virtuella Hyper-V-datorer i VMM-moln](./hyper-v-vmm-azure-tutorial.md)
