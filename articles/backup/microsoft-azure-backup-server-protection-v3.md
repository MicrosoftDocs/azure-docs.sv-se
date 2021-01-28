---
title: Vad Azure Backup Server v3 RTM kan säkerhetskopiera
description: Den här artikeln innehåller en skydds mat ris som visar alla arbets belastningar, data typer och installationer som Azure Backup som hanterar v3 RTM-skydd.
ms.date: 11/13/2018
ms.topic: conceptual
ms.openlocfilehash: 1ec8240844061b9b250a3cbf92ffcc5f2b3f474b
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986895"
---
# <a name="azure-backup-server-v3-rtm-protection-matrix"></a>Azure Backup Server v3 RTM-skydds mat ris

I följande matris visas vad som kan skyddas med Azure Backup Server v3 RTM och tidigare versioner.

## <a name="protection-support-matrix"></a>Supportmatris för skydd

|Arbetsbelastning|Version|Azure Backup Server</br> installation|Azure Backup Server som stöds|Skydd och återställning|
|------------|-----------|---------------|--------------|--------------|
|Klientdatorer (64-bitars och 32-bitars)|Windows 10|Fysisk server<br /><br />Virtuell Hyper-V-dator<br /><br />Virtuell VMware-dator|V3, V2|Volym, dela, mapp, filer, deduplicerade volymer<br /><br />Alla volymer måste vara av NTFS-typ FAT och FAT32 stöds inte.<br /><br />Volymerna måste ha minst 1 GB. Azure Backup Server använder tjänsten Volume Shadow Copy (VSS) för att ta data ögonblicks bilden och ögonblicks bilden fungerar bara om volymen är minst 1 GB.|
|Klientdatorer (64-bitars och 32-bitars)|Windows 8,1|Fysisk server<br /><br />Virtuell Hyper-V-dator|V3, V2|Filer<br /><br />Alla volymer måste vara av NTFS-typ FAT och FAT32 stöds inte.<br /><br />Volymerna måste ha minst 1 GB. Azure Backup Server använder tjänsten Volume Shadow Copy (VSS) för att ta data ögonblicks bilden och ögonblicks bilden fungerar bara om volymen är minst 1 GB.|
|Klientdatorer (64-bitars och 32-bitars)|Windows 8,1|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)|V3, V2|Volym, dela, mapp, filer, deduplicerade volymer<br /><br />Alla volymer måste vara av NTFS-typ FAT och FAT32 stöds inte.<br /><br />Volymerna måste ha minst 1 GB. Azure Backup Server använder tjänsten Volume Shadow Copy (VSS) för att ta data ögonblicks bilden och ögonblicks bilden fungerar bara om volymen är minst 1 GB.|
|Klientdatorer (64-bitars och 32-bitars)|Windows 8|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator|V3, V2|Volym, dela, mapp, filer, deduplicerade volymer<br /><br />Alla volymer måste vara av NTFS-typ FAT och FAT32 stöds inte.<br /><br />Volymerna måste ha minst 1 GB. Azure Backup Server använder tjänsten Volume Shadow Copy (VSS) för att ta data ögonblicks bilden och ögonblicks bilden fungerar bara om volymen är minst 1 GB.|
|Klientdatorer (64-bitars och 32-bitars)|Windows 8|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)|V3, V2|Volym, dela, mapp, filer, deduplicerade volymer<br /><br />Alla volymer måste vara av NTFS-typ FAT och FAT32 stöds inte.<br /><br />Volymerna måste ha minst 1 GB. Azure Backup Server använder tjänsten Volume Shadow Copy (VSS) för att ta data ögonblicks bilden och ögonblicks bilden fungerar bara om volymen är minst 1 GB.|
|Klientdatorer (64-bitars och 32-bitars)|Windows 7|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator|V3, V2|Volym, dela, mapp, filer, deduplicerade volymer<br /><br />Alla volymer måste vara av NTFS-typ FAT och FAT32 stöds inte.<br /><br />Volymerna måste ha minst 1 GB. Azure Backup Server använder tjänsten Volume Shadow Copy (VSS) för att ta data ögonblicks bilden och ögonblicks bilden fungerar bara om volymen är minst 1 GB.|
|Klientdatorer (64-bitars och 32-bitars)|Windows 7|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)|V3, V2|Volym, dela, mapp, filer, deduplicerade volymer<br /><br />Alla volymer måste vara av NTFS-typ FAT och FAT32 stöds inte.<br /><br />Volymerna måste ha minst 1 GB. Azure Backup Server använder tjänsten Volume Shadow Copy (VSS) för att ta data ögonblicks bilden och ögonblicks bilden fungerar bara om volymen är minst 1 GB.|
|Servrar (64-bitars)|Windows Server 2019|Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)<br /><br />Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /><br />Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3 <br />Inte Nano-server|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem), deduplicerade volymer|
|Servrar (32-bitars och 64-bitars)|Windows Server 2016|Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)<br /><br />Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /><br />Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3, V2<br />Inte Nano-server|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem), deduplicerade volymer|
|Servrar (32-bitars och 64-bitars)|Windows Server 2012 R2 – Datacenter och Standard|Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)<br /> <br /> Azure Stack|V3, V2|Volym, resurs, mapp, fil<br /><br />Azure Backup Server måste köras på minst Windows Server 2012 R2 för att skydda Windows Server 2012 deduplicerade volymer.|
|Servrar (32-bitars och 64-bitars)|Windows Server 2012 R2 – Datacenter och Standard|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem)<br /><br />Azure Backup Server måste köras på Windows Server 2012 eller 2012 R2 för att skydda Windows Server 2012 deduplicerade volymer.|
|Servrar (32-bitars och 64-bitars)|Windows Server 2012/2012 med SP1 – Datacenter och Standard|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3, V2|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem<br /><br />Azure Backup Server måste köras på minst Windows Server 2012 R2 för att skydda Windows Server 2012 deduplicerade volymer.|
|Servrar (32-bitars och 64-bitars)|Windows Server 2012/2012 med SP1 – Datacenter och Standard|Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)<br /> <br /> Azure Stack|V3, V2|Volym, resurs, mapp, fil<br /><br />Azure Backup Server måste köras på minst Windows Server 2012 R2 för att skydda Windows Server 2012 deduplicerade volymer.|
|Servrar (32-bitars och 64-bitars)|Windows Server 2012/2012 med SP1 – Datacenter och Standard|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem<br /><br />Azure Backup Server måste köras på minst Windows Server 2012 R2 för att skydda Windows Server 2012 deduplicerade volymer.|
|Servrar (32-bitars och 64-bitars)|Windows Server 2008 R2 SP1 – Standard och Enterprise|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3, V2<br />Du måste köra SP1 och installera [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem|
|Servrar (32-bitars och 64-bitars)|Windows Server 2008 R2 SP1 – Standard och Enterprise|Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)<br /> <br /> Azure Stack|V3, V2<br />Du måste köra SP1 och installera [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)|Volym, resurs, mapp, fil|
|Servrar (32-bitars och 64-bitars)|Windows Server 2008 R2 SP1 – Standard och Enterprise|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMWare)<br /> <br /> Azure Stack|V3, V2<br />Du måste köra SP1 och installera [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem|
|Servrar (32-bitars och 64-bitars)|Windows Server 2008 SP2|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|Stöds inte|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem|
|Servrar (32-bitars och 64-bitars)|Windows Server 2008 SP2|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem|
|Servrar (32-bitars och 64-bitars)|Windows Storage Server 2008|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3, V2|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem|
|SQL Server|SQL Server 2019|Fysisk server <br /><br /> Lokal virtuell Hyper-V-dator <br /> <br /> Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator) <br /><br /> Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2017|Fysisk server <br /><br /> Lokal virtuell Hyper-V-dator <br /> <br /> Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator) <br /><br /> Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2016 SP2|Fysisk server <br /><br /> Lokal virtuell Hyper-V-dator <br /> <br /> Virtuell Azure-dator <br /><br /> Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2016 SP1|Fysisk server <br /><br /> Lokal virtuell Hyper-V-dator <br /> <br /> Virtuell Azure-dator <br /><br /> Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2016|Fysisk server <br /><br /> Lokal virtuell Hyper-V-dator <br /> <br /> Virtuell Azure-dator <br /><br /> Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2014|Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2014|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2012 med SP2|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2012 med SP2|Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2012 med SP2|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2012, SQL Server 2012 med SP1|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2012, SQL Server 2012 med SP1|Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2012, SQL Server 2012 med SP1|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2008 R2|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2008 R2|Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2008 R2|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2008|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2008|Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|SQL Server|SQL Server 2008|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Alla distributionsscenarier: Databas|
|Exchange|Exchange 2016|Fysisk server<br/><br/> Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack<br /> <br />Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)|V3, V2|Skydda (alla distributionsscenarier): Fristående Exchange-server, databas under en grupp för databastillgänglighet (DAG)<br /><br />Återställa (alla distributionsscenarier): Postlåda, postlådedatabaser under en grupp för databastillgänglighet<br/><br/> Säkerhets kopiering av Exchange över ReFS stöds inte |
|Exchange|Exchange 2016|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Skydda (alla distributionsscenarier): Fristående Exchange-server, databas under en grupp för databastillgänglighet (DAG)<br /><br />Återställa (alla distributionsscenarier): Postlåda, postlådedatabaser under en grupp för databastillgänglighet<br/><br/> Säkerhets kopiering av Exchange över ReFS stöds inte |
|Exchange|Exchange 2013|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3, V2|Skydda (alla distributionsscenarier): Fristående Exchange-server, databas under en grupp för databastillgänglighet (DAG)<br /><br />Återställa (alla distributionsscenarier): Postlåda, postlådedatabaser under en grupp för databastillgänglighet<br/><br/> Säkerhets kopiering av Exchange över ReFS stöds inte |
|Exchange|Exchange 2013|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Skydda (alla distributionsscenarier): Fristående Exchange-server, databas under en grupp för databastillgänglighet (DAG)<br /><br />Återställa (alla distributionsscenarier): Postlåda, postlådedatabaser under en grupp för databastillgänglighet<br/><br/> Säkerhets kopiering av Exchange över ReFS stöds inte |
|Exchange|Exchange 2010|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3, V2|Skydda (alla distributionsscenarier): Fristående Exchange-server, databas under en grupp för databastillgänglighet (DAG)<br /><br />Återställa (alla distributionsscenarier): Postlåda, postlådedatabaser under en grupp för databastillgänglighet<br/><br/> Säkerhets kopiering av Exchange över ReFS stöds inte |
|Exchange|Exchange 2010|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Skydda (alla distributionsscenarier): Fristående Exchange-server, databas under en grupp för databastillgänglighet (DAG)<br /><br />Återställa (alla distributionsscenarier): Postlåda, postlådedatabaser under en grupp för databastillgänglighet<br/><br/> Säkerhets kopiering av Exchange över ReFS stöds inte |
|SharePoint|SharePoint 2016|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /><br />Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)<br /><br />Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Skydda (alla distributionsscenarier): Servergrupp, klientwebbserverinnehåll<br /><br />Återställ (alla distributionsscenarier): Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, klientwebbserver<br /><br />Tänk på att skydd av en Windows SharePoint-servergrupp som använder AlwaysOn-funktionen i SQL Server 2012 för innehållsdatabaser inte stöds.|
|SharePoint|SharePoint 2013|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3, V2|Skydda (alla distributionsscenarier): Servergrupp, klientwebbserverinnehåll<br /><br />Återställ (alla distributionsscenarier): Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, klientwebbserver<br /><br />Tänk på att skydd av en Windows SharePoint-servergrupp som använder AlwaysOn-funktionen i SQL Server 2012 för innehållsdatabaser inte stöds.|
|SharePoint|SharePoint 2013|Virtuell Azure-dator (när arbets belastningen körs som virtuell Azure-dator) – <br /> <br /> Azure Stack|V3, V2|Skydda (alla distributionsscenarier): Servergrupp, SharePoint-sökning, klientwebbserverinnehåll<br /><br />Återställ (alla distributionsscenarier): Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, klientwebbserver<br /><br />Tänk på att skydd av en Windows SharePoint-servergrupp som använder AlwaysOn-funktionen i SQL Server 2012 för innehållsdatabaser inte stöds.|
|SharePoint|SharePoint 2013|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Skydda (alla distributionsscenarier): Servergrupp, SharePoint-sökning, klientwebbserverinnehåll<br /><br />Återställ (alla distributionsscenarier): Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, klientwebbserver<br /><br />Tänk på att skydd av en Windows SharePoint-servergrupp som använder AlwaysOn-funktionen i SQL Server 2012 för innehållsdatabaser inte stöds.|
|SharePoint|SharePoint 2010|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator<br /> <br /> Azure Stack|V3, V2|Skydda (alla distributionsscenarier): Servergrupp, SharePoint-sökning, klientwebbserverinnehåll<br /><br />Återställ (alla distributionsscenarier): Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, klientwebbserver|
|SharePoint|SharePoint 2010|Virtuell Azure-dator (när arbetsbelastningen körs som virtuell Azure-dator)<br /> <br /> Azure Stack|V3, V2|Skydda (alla distributionsscenarier): Servergrupp, SharePoint-sökning, klientwebbserverinnehåll<br /><br />Återställ (alla distributionsscenarier): Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, klientwebbserver|
|SharePoint|SharePoint 2010|Virtuell Windows-dator i VMware (skyddar arbets belastningar som körs på virtuella Windows-datorer i VMware)<br /> <br /> Azure Stack|V3, V2|Skydda (alla distributionsscenarier): Servergrupp, SharePoint-sökning, klientwebbserverinnehåll<br /><br />Återställ (alla distributionsscenarier): Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, klientwebbserver|
|Hyper-V-MABS för skydds agent på Hyper-V-värdservern, kluster eller VM|Windows Server 2019|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator|V3|Skydda: Hyper-V-datorer, klusterdelade volymer (CSV)<br /><br />Återställ: Virtuell dator, återställning av filer och mappar på objektnivå, volymer, virtuella hårddiskar|
|Hyper-V-MABS för skydds agent på Hyper-V-värdservern, kluster eller VM|Windows Server 2016|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator|V3, V2|Skydda: Hyper-V-datorer, klusterdelade volymer (CSV)<br /><br />Återställ: Virtuell dator, återställning av filer och mappar på objektnivå, volymer, virtuella hårddiskar|
|Hyper-V-MABS för skydds agent på Hyper-V-värdservern, kluster eller VM|Windows Server 2012 R2 – Datacenter och Standard|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator|V3, V2|Skydda: Hyper-V-datorer, klusterdelade volymer (CSV)<br /><br />Återställ: Virtuell dator, återställning av filer och mappar på objektnivå, volymer, virtuella hårddiskar|
|Hyper-V-MABS för skydds agent på Hyper-V-värdservern, kluster eller VM|Windows Server 2012 – Datacenter och Standard|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator|V3, V2|Skydda: Hyper-V-datorer, klusterdelade volymer (CSV)<br /><br />Återställ: Virtuell dator, återställning av filer och mappar på objektnivå, volymer, virtuella hårddiskar|
|Hyper-V-MABS för skydds agent på Hyper-V-värdservern, kluster eller VM|Windows Server 2008 R2 SP1 – Enterprise och Standard|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator|V3, V2|Skydda: Hyper-V-datorer, klusterdelade volymer (CSV)<br /><br />Återställ: Virtuell dator, återställning av filer och mappar på objektnivå, volymer, virtuella hårddiskar|
|Hyper-V-MABS för skydds agent på Hyper-V-värdservern, kluster eller VM|Windows Server 2008 SP2|Fysisk server<br /><br />Lokal virtuell Hyper-V-dator|Stöds inte|Skydda: Hyper-V-datorer, klusterdelade volymer (CSV)<br /><br />Återställ: Virtuell dator, återställning av filer och mappar på objektnivå, volymer, virtuella hårddiskar|
|VMwares virtuella datorer|VMware vCenter/vSphere ESX/ESXi licensierad version 5.5/6.0/6.5 |Fysisk server, <br/>Lokal virtuell Hyper-V-dator, <br/> Virtuell Windows-dator i VMware|V3, V2|Virtuella VMware-datorer på klusterdelade volymer (CSV: er), NFS och SAN-lagring<br /> Objekt nivå återställning av filer och mappar är bara tillgängligt för virtuella Windows-datorer, VMware vApps stöds inte.|
|VMwares virtuella datorer|[VMware vSphere licensierad version 6,7](backup-azure-backup-server-vmware.md#vmware-vsphere-67) |Fysisk server, <br/>Lokal virtuell Hyper-V-dator, <br/> Virtuell Windows-dator i VMware|V3|Virtuella VMware-datorer på klusterdelade volymer (CSV: er), NFS och SAN-lagring<br /> Objekt nivå återställning av filer och mappar är bara tillgängligt för virtuella Windows-datorer, VMware vApps stöds inte.|
|Linux|Linux körs som [Hyper-V-](back-up-hyper-v-virtual-machines-mabs.md) eller [VMware](backup-azure-backup-server-vmware.md) -gäst|Fysisk server, <br/>Lokal virtuell Hyper-V-dator, <br/> Virtuell Windows-dator i VMware|V3, V2|Hyper-V måste köra på Windows Server 2012 R2 eller Windows Server 2016. Skydda: Hela den virtuella datorn<br /><br />Återställ: Hela den virtuella datorn <br/><br/> Endast filkonsekventa ögonblicksbilder stöds. <br/><br/> En fullständig lista över Linux-distributioner och-versioner som stöds finns i artikeln [Linux on distributioner som har godkänts av Azure](../virtual-machines/linux/endorsed-distros.md).|

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

    Om du kör Hyper-V på Windows Server 2008 R2, se till att installera uppdateringen som beskrivs i KB [975354](https://support.microsoft.com/kb/975354).
    Om du kör Hyper-V på Windows Server 2008 R2 i en kluster konfiguration ska du kontrol lera att du installerar SP2 och KB [971394](https://support.microsoft.com/kb/971394).

* Exchange Server – Azure Backup Server kan skydda icke-delade disk kluster för Exchange Server-versioner som stöds (kluster kontinuerlig replikering) och kan även skydda Exchange Server som kon figurer ATS för lokal kontinuerlig replikering.

* SQL Server-Azure Backup Server stöder inte säkerhets kopiering av SQL Server databaser som finns på klusterdelade volymer (CSV: er).

Azure Backup Server kan skydda kluster arbets belastningar som finns i samma domän som MABS-servern och i en underordnad eller betrodd domän. Om du vill skydda data källor i ej betrodda domäner eller arbets grupper, använder du NTLM eller certifikatautentisering för en enskild server, eller certifikatautentisering endast för ett kluster.
