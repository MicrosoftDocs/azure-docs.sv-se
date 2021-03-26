---
title: MABS (Azure Backup Server) v3 UR1-skydds mat ris
description: Den här artikeln innehåller en support mat ris som visar alla arbets belastningar, data typer och installationer som Azure Backup Server skyddar.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: cfdd227135a2124e22a604bad4bd41594a38fb37
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561278"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>MABS (Azure Backup Server) v3 UR1-skydds mat ris

Den här artikeln innehåller de olika servrar och arbets belastningar som du kan skydda med Azure Backup Server. I följande matris visas vad som kan skyddas med Azure Backup Server.

Använd följande matris för MABS v3-UR1:

* Arbets belastningar – teknikens typ av arbets belastning.

* Version – MABS version som stöds för arbets belastningarna.

* MABS-installation – den dator/plats där du vill installera MABS.

* Skydd och återställning – ange detaljerad information om arbets belastningarna, till exempel lagrings behållare som stöds eller distribution som stöds.

>[!NOTE]
>Stöd för 32-bitars skydds agenten är föråldrad med MABS v3-UR1. Se [32-bitars skydds agentens utfasning](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>Supportmatris för skydd

I följande avsnitt beskrivs matrisen för skydds support för MABS:

* [Säkerhets kopiering av program](#applications-backup)
* [VM-säkerhetskopiering](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Säkerhets kopiering av program

| **Arbetsbelastning**               | **Version**                                                  | **Azure Backup Server installation**                       | **Azure Backup Server som stöds** | **Skydd och återställning**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Klient datorer (64-bitars) | Windows 10                                                  | Fysisk server  <br><br>    Virtuell Hyper-V-dator   <br><br>   Virtuell VMware-dator | V3-UR1                            | Volym, dela, mapp, filer, deduplicerade volymer   <br><br>   Alla volymer måste vara av NTFS-typ FAT och FAT32 stöds inte.  <br><br>    Volymerna måste ha minst 1 GB. Azure Backup Server använder tjänsten Volume Shadow Copy (VSS) för att ta data ögonblicks bilden och ögonblicks bilden fungerar bara om volymen är minst 1 GB. |
| Servrar (64-bitars)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Virtuell Azure-dator (när arbets belastningen körs som virtuell Azure-dator)  <br><br>    Fysisk server  <br><br>    Virtuell Hyper-V-dator <br><br>     Virtuell VMware-dator  <br><br>    Azure Stack | V3-UR1                            | Volym, dela, mapp, fil <br><br>    Deduplicerade volymer (endast NTFS) <br><br>När du skyddar en WS 2016 NTFS-deduplicerad volym med MABS v3 som körs på Windows Server 2019 kan återställningarna påverkas. Vi har en korrigering för att utföra återställningar i icke-deduplicerat sätt som kommer att ingå i senare versioner av MABS. Kontakta MABS-supporten om du behöver den här korrigeringen på MABS v3 UR1.<br><br> När du skyddar en WS 2019 NTFS-deduplicerad volym med MABS v3 på Windows Server 2016, kommer säkerhets kopieringarna och återställningarna inte att dedupliceras. Det innebär att säkerhets kopieringarna förbrukar mer utrymme på MABS-servern än den ursprungliga NTFS-deduplicerade volymen.   <br><br>   System tillstånd och Bare Metal (stöds inte när arbets belastningen körs som virtuell Azure-dator) |
| Servrar (64-bitars)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (du måste installera [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)) | Fysisk server  <br><br>    Virtuell Hyper-V-dator  <br><br>      Virtuell VMware-dator  <br><br>   Azure Stack | V3-UR1                            | Volym, resurs, mapp, fil, system tillstånd/Bare Metal        |
| SQL Server                | SQL Server 2019, 2017, 2016 och [stöd för SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 och stödda [SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | Fysisk server  <br><br>     Virtuell Hyper-V-dator   <br><br>     Virtuell VMware-dator  <br><br>   Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)  <br><br>     Azure Stack | V3-UR1                            | Alla distributions scenarier: databas       <br><br>  MABS v3-UR1 har stöd för säkerhets kopiering av SQL-databaser över ReFS-volymer     <br><br>     MABS stöder inte SQL Server databaser som finns på Windows Server 2012 Scale-Out fil servrar (SOFS). <br><br>   MABS kan inte skydda SQL Server-distribuerad tillgänglighets grupp (DAG) eller tillgänglighets grupp (AG), där roll namnet i redundansklustret skiljer sig från det namngivna AG: t i SQL.       |
| Exchange                   | Exchange 2019, 2016                                         | Fysisk server   <br><br>   Virtuell Hyper-V-dator  <br><br>      Virtuell VMware-dator  <br><br>   Azure Stack  <br><br>    Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator) | V3-UR1                            | Skydda (alla distributions scenarier): fristående Exchange-Server, databas under en databas tillgänglighets grupp (DAG)  <br><br>    Återställa (alla distributionsscenarier): Postlåda, postlådedatabaser under en grupp för databastillgänglighet    <br><br>  Säkerhets kopiering av Exchange över ReFS stöds med MABS v3 UR1 |
| SharePoint                 | SharePoint 2019, 2016 med senaste SPs                       | Fysisk server  <br><br>    Virtuell Hyper-V-dator <br><br>    Virtuell VMware-dator  <br><br>   Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)   <br><br>   Azure Stack | V3-UR1                            | Skydda (alla distributions scenarier): Server grupp, webb server innehåll för klient del  <br><br>    Återställ (alla distributions scenarier): Server grupp, databas, webb program, fil eller List objekt, SharePoint-sökning, webb server för klient del  <br><br>    Det finns inte stöd för att skydda en SharePoint-grupp som använder AlwaysOn-funktionen SQL Server 2012 för innehålls databaserna. |

## <a name="vm-backup"></a>VM-säkerhetskopiering

| **Arbetsbelastning**                                                 | **Version**                                             | **Azure Backup Server installation**                      | **Azure Backup Server som stöds** | **Skydd och återställning**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V-MABS för skydds agent på Hyper-V-värdservern, kluster eller VM | Windows Server 2019, 2016, 2012 R2, 2012               | Fysisk server  <br><br>    Virtuell Hyper-V-dator <br><br>    Virtuell VMware-dator | V3-UR1                             | Skydda: Hyper-V-datorer, klusterdelade volymer (CSV: er)  <br><br>    Återställning: virtuell dator, återställning av filer och mappar på objekt nivå är endast tillgängliga för Windows, volymer, virtuella hård diskar |
| Virtuella VMware-datorer                                                  | VMware Server 5,5, 6,0 eller 6,5, 6,7 (licensierad version) | Virtuell Hyper-V-dator  <br><br>   Virtuell VMware-dator         | V3-UR1                             | Skydda: virtuella VMware-datorer på klusterdelade volymer (CSV: er), NFS och SAN-lagring   <br><br>     Återställning: virtuell dator, återställning av filer och mappar på objekt nivå är endast tillgängliga för Windows, volymer, virtuella hård diskar <br><br>    VMware-vApps stöds inte. |

>[!NOTE]
> MABS stöder inte säkerhets kopiering av virtuella datorer med direkt lagrings diskar eller de som använder en virtuell fjärrhårddisk. I dessa scenarier rekommenderar vi att du använder säkerhets kopiering på gästnivå med hjälp av MABS och installerar en agent på den virtuella datorn för att säkerhetskopiera data.

## <a name="linux"></a>Linux

| **Arbetsbelastning** | **Version**                               | **Azure Backup Server installation**                      | **Azure Backup Server som stöds** | **Skydd och återställning**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux körs som [Hyper-V-](back-up-hyper-v-virtual-machines-mabs.md) eller [VMware](backup-azure-backup-server-vmware.md) -gäst | Fysisk server, lokal virtuell Hyper-V-dator, virtuell Windows-dator i VMware | V3-UR1                             | Hyper-V måste köras på Windows Server 2012 R2, Windows Server 2016 eller Windows Server 2019. Skydda: hela den virtuella datorn   <br><br>   Återställ: Hela den virtuella datorn   <br><br>    Endast filkonsekventa ögonblicksbilder stöds.    <br><br>   En fullständig lista över Linux-distributioner och-versioner som stöds finns i artikeln [Linux on distributioner som har godkänts av Azure](../virtual-machines/linux/endorsed-distros.md). |

## <a name="azure-expressroute-support"></a>Stöd för Azure ExpressRoute

Du kan säkerhetskopiera dina data via Azure ExpressRoute med offentlig peering (tillgänglig för gamla kretsar) och Microsoft-peering. Säkerhets kopiering över privat peering stöds inte.

Med offentlig peering: säkerställa åtkomst till följande domäner/adresser:

* Webbadresser
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-adresser
  * 20.190.128.0/18
  * 40.126.0.0/18

Med Microsoft-peering väljer du följande tjänster/regioner och relevanta community-värden:

* Azure Active Directory (12076:5060)
* Microsoft Azure region (enligt platsen för ditt Recovery Services-valv)
* Azure Storage (enligt platsen för ditt Recovery Services-valv)

Mer information finns i krav för [ExpressRoute-routning](../expressroute/expressroute-routing.md).

>[!NOTE]
>Offentlig peering är föråldrad för nya kretsar.

## <a name="cluster-support"></a>Stöd för kluster

Azure Backup Server kan skydda data i följande klustrade program:

* Filservrar

* SQL Server

* Hyper-V – om du skyddar ett Hyper-V-kluster med en skalbar MABS skydds agent kan du inte lägga till sekundärt skydd för de skyddade Hyper-V-arbetsbelastningarna.

* Exchange Server – Azure Backup Server kan skydda icke-delade disk kluster för Exchange Server-versioner som stöds (kluster kontinuerlig replikering) och kan även skydda Exchange Server som kon figurer ATS för lokal kontinuerlig replikering.

* SQL Server-Azure Backup Server stöder inte säkerhets kopiering av SQL Server databaser som finns på klusterdelade volymer (CSV: er).

>[!NOTE]
>MABS stöder endast skydd av virtuella Hyper-V-datorer på klusterdelade volymer (CSV: er). Att skydda andra arbetsbelastningar som finns på en CSV stöds inte.

Azure Backup Server kan skydda kluster arbets belastningar som finns i samma domän som MABS-servern och i en underordnad eller betrodd domän. Om du vill skydda data källor i ej betrodda domäner eller arbets grupper, använder du NTLM eller certifikatautentisering för en enskild server, eller certifikatautentisering endast för ett kluster.

## <a name="data-protection-issues"></a>Problem med dataskydd

* MABS kan inte säkerhetskopiera virtuella datorer med delade enheter (som kan kopplas till andra virtuella datorer) eftersom Hyper-V VSS-skrivaren inte kan säkerhetskopiera volymer som säkerhets kopie ras av delade virtuella hård diskar.

* När du skyddar en delad mapp innehåller sökvägen till den delade mappen den logiska sökvägen på volymen. Om du flyttar den delade mappen kommer skyddet inte att fungera. Om du måste flytta en skyddad delad mapp tar du bort den från skydds gruppen och lägger sedan till den i skyddet efter flytten.  Om du ändrar sökvägen till en skyddad data källa på en volym som använder krypterande filsystem (EFS) och den nya fil Sök vägen överskrider 5120 tecken kommer data skyddet att Miss.

* Du kan inte ändra domänen för en skyddad dator och behålla skyddet utan avbrott. Du kan inte heller ändra domänen för en skyddad dator och associera befintliga repliker och återställnings punkter med datorn när den skyddas på nytt. Om du måste ändra domänen för en skyddad dator tar du först bort data källorna på datorn från skyddet. Skydda sedan data källan på datorn när den har en ny domän.

* Du kan inte ändra namnet på en skyddad dator och behålla skyddet utan avbrott. Du kan inte heller ändra namnet på en skyddad dator och associera befintliga repliker och återställnings punkter med datorn när den skyddas på nytt. Ta först bort datakällorna på datorn från skyddet om du måste ändra namnet på en skyddad dator. Skydda sedan datakällan på datorn efter att du angett det nya namnet.

* MABS identifierar automatiskt tids zonen för en skyddad dator under installationen av skydds agenten. Om en skyddad dator flyttas till en annan tidszon efter konfigurationen av skyddet ska du se till att ändra datorns klocka i Kontrollpanelen. Uppdatera sedan tids zonen i MABS-databasen.

* MABS kan skydda arbets belastningar i samma domän som MABS-servern, eller i underordnade och betrodda domäner. Du kan också skydda följande arbets belastningar i arbets grupper och obetrodda domäner med hjälp av NTLM eller certifikatautentisering:

  * SQL Server
  * Filserver
  * Hyper-V

  De här arbetsbelastningarna kan köras på en enskild server eller i en klusterkonfiguration. Om du vill skydda en arbets belastning som inte finns i en betrodd domän, se [förbereda datorer i arbets grupper och ej betrodda domäner](/system-center/dpm/prepare-environment-for-dpm) för exakt information om vad som stöds och vilken autentisering som krävs.

## <a name="unsupported-data-types"></a>Data typer som inte stöds

MABS stöder inte skydd av följande data typer:

* Hårda länkar

* Referenspunkter, inklusive DFS-länkar och knutpunkter

* Metadata för monteringspunkter – en skyddsgrupp kan innehålla data med anslutningspunkter. I det här fallet skyddar DPM den monterade volymen som är mål för monteringspunkten, men DPM skyddar inte monteringspunktens metadata. När du återställer data som innehåller monteringspunkter måste du manuellt återskapa hierarkin för monteringspunkter.

* Data i monterade volymer inom monterade volymer

* Papperskorgen

* Växla filer

* Informationsmapp för systemvolym. För att skydda system informationen för en dator måste du välja datorns system tillstånd som skydds grupp medlem.

* Icke-NTFS-volymer

* Filer som innehåller hårda länkar eller symboliska länkar från Windows Vista.

* Data på fil resurser som är värdar för UPD: ar (användar profil diskar)

* Filer med någon av följande kombinationer av attribut:

  * Kryptering och referenser

  * Kryptering och Single Instance Store (SIS)

  * Kryptering och skiftlägeskänslighet

  * Kryptering och parsning

  * Skiftlägeskänslighet och SIS

  * Komprimering och SIS

## <a name="next-steps"></a>Nästa steg

* [Support mat ris för säkerhets kopiering med Microsoft Azure Backup Server eller System Center DPM](backup-support-matrix-mabs-dpm.md)