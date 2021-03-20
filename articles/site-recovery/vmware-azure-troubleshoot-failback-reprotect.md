---
title: Felsök återställning efter fel i haveri beredskap för virtuella VMware-datorer med Azure Site Recovery
description: I den här artikeln beskrivs olika sätt att felsöka problem med återställning efter fel och skydd under haveri beredskap för virtuella VMware-datorer till Azure med Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: ed4e52470264441a99c5ccf0a736bb00233510c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87423124"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Felsöka återställning efter fel till lokala enheter från Azure

Den här artikeln beskriver hur du felsöker problem som kan uppstå när du återställer virtuella Azure-datorer till din lokala VMware-infrastruktur efter redundansväxlingen till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Återställning efter fel omfattar huvudsakligen två huvud steg. För det första steget efter redundansväxlingen måste du skydda virtuella Azure-datorer till lokalt så att de börjar replikera. Det andra steget är att köra en redundansväxling från Azure för att växla tillbaka till din lokala plats.

## <a name="common-issues"></a>Vanliga problem

- Om du utför en skrivskyddad vCenter-identifiering och skyddar virtuella datorer, slutförs skyddet och redundansväxlingen fungerar. Vid återskydd Miss lyckas redundansväxlingen eftersom data lagret inte kan identifieras. Ett symtom är att data lager inte visas under återskydd. För att lösa det här problemet kan du uppdatera vCenter-autentiseringsuppgifterna med ett lämpligt konto som har behörigheter och sedan försöka utföra jobbet igen.
- När du växlar tillbaka till en virtuell Linux-dator och kör den lokalt kan du se att Network Manager-paketet har avinstallerats från datorn. Avinstallationen sker eftersom Network Manager-paketet tas bort när den virtuella datorn återställs i Azure.
- När en virtuell Linux-dator konfigureras med en statisk IP-adress och växlas över till Azure, hämtas IP-adressen från DHCP. När du växlar över till den lokala datorn fortsätter den virtuella datorn att använda DHCP för att hämta IP-adressen. Logga in manuellt på datorn och ange sedan IP-adressen tillbaka till en statisk adress vid behov. En virtuell Windows-dator kan hämta sin statiska IP-adress igen.
- Om du använder antingen ESXi 5,5 Free Edition eller den kostnads fria versionen av vSphere 6, lyckas redundansväxlingen, men failback fungerar inte. Om du vill aktivera återställning efter fel, uppgradera till antingen programmets utvärderings licens.
- Om du inte kan komma åt konfigurations servern från processervern använder du Telnet för att kontrol lera anslutningen till konfigurations servern på port 443. Du kan också prova att pinga konfigurations servern från processervern. En processerver bör också ha ett pulsslag när den är ansluten till konfigurations servern.
- En Windows Server 2008 R2 SP1-Server som skyddas som en fysisk lokal server kan inte återställas från Azure till en lokal plats.
- Du kan inte växla tillbaka i följande situationer:
    - Du migrerade datorer till Azure. 
    - Du har flyttat en virtuell dator till en annan resurs grupp.
    - Du har tagit bort den virtuella Azure-datorn.
    - Du har inaktiverat skyddet av den virtuella datorn.
    - Du skapade den virtuella datorn manuellt i Azure. Datorn bör först skyddas lokalt och ha redundansväxlats till Azure innan återskydden.
    - Det går bara att återställa en ESXi-värd. Du kan inte återställa virtuella VMware-datorer eller fysiska servrar till Hyper-V-värdar, fysiska datorer eller VMware-arbetsstationer.


## <a name="troubleshoot-reprotection-errors"></a>Felsöka återskydds fel

Det här avsnittet innehåller information om vanliga återskydds fel och hur du korrigerar dem.

### <a name="error-code-95226"></a>Felkod 95226

**Det gick inte att återaktivera skydd eftersom den virtuella Azure-datorn inte kunde komma åt den lokala konfigurations servern.**

Felet uppstår när:

* Den virtuella Azure-datorn kan inte ansluta till den lokala konfigurations servern. Den virtuella datorn kan inte identifieras och registreras på konfigurations servern.
* InMage Scout Application Service körs inte på den virtuella Azure-datorn efter redundansväxlingen. Tjänsten krävs för kommunikation med den lokala konfigurations servern.

Lös problemet så här:

* Kontrol lera att Azure VM Network gör det möjligt för den virtuella Azure-datorn att kommunicera med den lokala konfigurations servern. Du kan antingen konfigurera en plats-till-plats-VPN-anslutning till ditt lokala data Center eller konfigurera en Azure ExpressRoute-anslutning med privat peering på det virtuella Azure-DATORns virtuella nätverk.
* Om den virtuella datorn kan kommunicera med den lokala konfigurations servern loggar du in på den virtuella datorn. Kontrol lera sedan tjänsten InMage Scout Application. Om du ser att den inte körs startar du tjänsten manuellt. Kontrol lera att tjänstens start typ är inställd på **Automatisk**.

### <a name="error-code-78052"></a>Felkod 78052

**Det gick inte att slutföra skyddet för den virtuella datorn.**

Det här problemet kan inträffa om det redan finns en virtuell dator med samma namn på huvud mål servern som du växlar tillbaka till.

Lös problemet så här:

* Välj en annan huvud mål server på en annan värd så att skyddet skapar datorn på en annan värd, där namnen inte kolliderar.
* Du kan också använda VMotion för att flytta huvud målet till en annan värd där namn kollisionen inte inträffar. Om den befintliga virtuella datorn är en lösa dator byter du namn på den så att den nya virtuella datorn kan skapas på samma ESXi-värd.


### <a name="error-code-78093"></a>Felkod 78093

**Den virtuella datorn körs inte, svarar inte eller är inte tillgänglig.**

Lös problemet så här:

För att skydda en misslyckad virtuell dator måste den virtuella Azure-datorn köras så att mobilitets tjänsten registrerar sig för den lokala konfigurations servern och kan börja replikeras genom att kommunicera med processervern. Om datorn är i ett felaktigt nätverk eller inte körs (svarar inte eller stängs) kan konfigurations servern inte komma åt mobilitets tjänsten på den virtuella datorn för att börja återaktivering.

* Starta om den virtuella datorn så att den kan börja kommunicera igen lokalt.
* Starta om skydds jobbet när du har startat den virtuella Azure-datorn.

### <a name="error-code-8061"></a>Felkod 8061

**Det går inte att komma åt data lagret från ESXi-värden.**

Kontrol lera [kraven för huvud mål och data lager som stöds](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) för återställning efter fel.


## <a name="troubleshoot-failback-errors"></a>Felsök failback-fel

I det här avsnittet beskrivs vanliga fel som kan uppstå under återställning efter fel.

### <a name="error-code-8038"></a>Felkod 8038

**Det gick inte att öppna den lokala virtuella datorn på grund av felet.**

Det här problemet uppstår när den lokala virtuella datorn tas upp på en värd som inte har tillräckligt med minne. 

Lös problemet så här:

* Etablera mer minne på ESXi-värden.
* Dessutom kan du använda VMotion för att flytta den virtuella datorn till en annan ESXi-värd som har tillräckligt med minne för att starta den virtuella datorn.
