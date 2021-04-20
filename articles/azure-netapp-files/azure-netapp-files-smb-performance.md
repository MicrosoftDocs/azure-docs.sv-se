---
title: Vanliga frågor och svar om SMB-prestanda för Azure NetApp Files| Microsoft Docs
description: Svar på vanliga frågor om SMB-prestanda för Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: 1ec58b056bd610773500c8ace1fb12d268b980e0
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726726"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Vanliga frågor och svar om SMB-prestanda för Azure NetApp Files

Den här artikeln besvarar vanliga frågor och svar om metodtips för SMB-prestanda för Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Är SMB Multichannel aktiverat i SMB-resurser? 

Ja, SMB Multichannel är aktiverat som standard, en ändring som skedde i början av januari 2020. Alla SMB-resurser redan befintliga SMB-volymer har haft funktionen aktiverad, och alla nyligen skapade volymer kommer också att ha funktionen aktiverad när den skapas. 

Alla SMB-anslutningar som upprättas innan funktionen aktiveras måste återställas för att dra nytta av SMB Multichannel-funktionerna. Om du vill återställa kan du koppla från och återansluta SMB-resursen.

## <a name="is-rss-supported"></a>Stöds RSS?

Ja, Azure NetApp Files har stöd för RSS (receive-side-scaling).

När SMB Multichannel är aktiverat upprättar en SMB3-klient flera TCP-anslutningar till Azure NetApp Files SMB-servern via ett nätverkskort (NIC) som har enkel RSS-kapacitet. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Vilka Windows-versioner stöder SMB Multichannel?

Windows har stöd för SMB Multichannel sedan Windows 2012 för bästa prestanda.  Mer [information finns i Distribuera SMB Multichannel](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) och Grunderna i [SMB Multichannel.](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0) 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Stöder min virtuella Azure-dator RSS?

Om du vill se om dina nätverkskort för virtuella Azure-datorer stöder RSS kör du `Get-SmbClientNetworkInterface` kommandot på följande sätt och markerar fältet `RSS Capable` : 

![Skärmbild som visar RSS-utdata för virtuella Azure-datorer.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Stöder Azure NetApp Files SMB Direct?

Nej, Azure NetApp Files stöder inte SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Vad är fördelen med SMB Multichannel? 

SMB Multichannel-funktionen gör det möjligt för en SMB3-klient att upprätta en pool med anslutningar via ett enda nätverkskort (NIC) eller flera nätverkskort och använda dem för att skicka begäranden för en enda SMB-session. SMB1 och SMB2 kräver däremot att klienten upprättar en anslutning och skickar all SMB-trafik för en viss session via anslutningen. Den här enskilda anslutningen begränsar den övergripande protokollprestanda som kan uppnås från en enda klient.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Ska jag konfigurera flera nätverkskort på min klient för SMB?

Nej. SMB-klienten matchar antalet nätverkskort som returneras av SMB-servern.  Varje lagringsvolym är tillgänglig från en och endast en lagringsslutpunkt.  Det innebär att endast ett nätverkskort används för en given SMB-relation.  

Som utdata `Get-SmbClientNetworkInterace` från nedan visar har den virtuella datorn 2 nätverksgränssnitt – 15 och 12.  Som du ser i följande kommando används endast gränssnitt 12 i anslutning till SMB-resursen, även om det finns två `Get-SmbMultichannelConnection` RSS-kompatibla nätverkskort. Gränssnitt 15 används inte.

![Screeshot som visar utdata för RSS-kompatibla nätverkskort.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Stöds NIC-team team i Azure?

NIC-teaming stöds inte i Azure. Även om flera nätverksgränssnitt stöds på virtuella Azure-datorer representerar de en logisk snarare än en fysisk konstruktion. Därför ger de ingen feltolerans.  Dessutom beräknas den tillgängliga bandbredden för en virtuell Azure-dator för själva datorn och inte för något enskilt nätverksgränssnitt.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Hur ser prestanda ut för SMB Multichannel?

Följande tester och diagram visar kraften i SMB Multichannel på arbetsbelastningar med en instans.

### <a name="random-io"></a>Slumpmässig i/o  

Med SMB Multichannel inaktiverat på klienten utfördes endast 4 KiB-läs- och skrivtester med FIO och en arbetsuppsättning på 40 GiB.  SMB-resursen har frigjorts mellan varje test, med ökningar av antalet SMB-klientanslutning per RSS-nätverksgränssnittsinställningar `1` för , , , , `4` `8` `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` . Testerna visar att standardinställningen för `4` är tillräcklig för I/O-intensiva arbetsbelastningar. Inkrementering till `8` och hade minimal `16` effekt. 

Kommandot visade `netstat -na | findstr 445` att ytterligare anslutningar har upprättats med ökningar från till till och till `1` `4` `8` `16` .  Fyra CPU-kärnor har använts fullt ut för SMB under varje test, vilket bekräftats av statistiken för perfmon `Per Processor Network Activity Cycles` (ingår inte i den här artikeln.)

![Diagram som visar en slumpmässig I/O-jämförelse av SMB Multichannel.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Den virtuella Azure-datorn påverkar inte I/O-gränserna för SMB-lagring (eller NFS).  Som du ser i följande diagram har instanstypen D32ds en begränsad hastighet på 308 000 för cachelagrad lagrings-IOPS och 51 200 för IOPS för frikopplad lagring.  Diagrammet ovan visar dock betydligt mer I/O över SMB.

![Diagram som visar ett slumpmässigt I/O-jämförelsetest.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sekventiell I/O 

Tester som liknar de slumpmässiga I/O-testerna som beskrevs tidigare utfördes med sekventiell I/O med 64 KiB. Även om ökningarna i antalet klientanslutningar per RSS-nätverksgränssnitt utöver 4' inte hade någon märkbar effekt på slumpmässiga I/O, gäller samma sak inte för sekventiella I/O. Som följande diagram visar är varje ökning associerad med en motsvarande ökning av läsgenomflödet. Skrivgenomflödet var platt på grund av begränsningar i nätverksbandbredden som Azure hade för varje instanstyp/-storlek. 

![Diagram som visar jämförelse av dataflödestest.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure begränsar nätverkshastigheten för varje typ/storlek på den virtuella datorn. Hastighetsbegränsningen gäller endast för utgående trafik. Antalet nätverkskort som finns på en virtuell dator påverkar inte den totala mängden bandbredd som är tillgänglig för datorn.  Till exempel har instanstypen D32ds en införad nätverksgräns på 16 000 Mbit/s (2 000 MiB/s).  Som det sekventiella diagrammet ovan visar påverkar gränsen utgående trafik (skrivningar) men inte läsningar i flera kanaler.

![Diagram som visar sekventiella I/O-jämförelsetest.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>Vilken prestanda förväntas med en enda instans med en datauppsättning på 1 TB?

För att ge mer detaljerad inblick i arbetsbelastningar med läs-/skriv-blandningar visar följande två diagram prestanda för en enda ultratjänstnivåmolnvolym på 50 TB med en datauppsättning på 1 TB och med SMB multichannel på 4. En optimal IODepth av 16 användes och FIO-parametrar (Flexible IO) användes för att säkerställa fullständig användning av nätverksbandbredden ( `numjobs=16` ).

Följande diagram visar resultatet för 4k slumpmässig i/o, med en enskild VM-instans och en läs-/skriv-blandning med 10 % intervall:

![Diagram som visar Windows 2019-standard _D32ds_v4 4K slumpmässigt I/S-test.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

Följande diagram visar resultatet för sekventiell I/O:

![Diagram som visar Windows 2019-standard _D32ds_v4 64K sekventiellt dataflöde.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>Vilken prestanda förväntas när du skalar ut med 5 virtuella datorer med en datauppsättning på 1 TB?

Dessa tester med 5 virtuella datorer använder samma testmiljö som den enskilda virtuella datorn, där varje process skrivs till en egen fil.

I följande diagram visas resultatet för slumpmässiga I/O:

![Diagram som visar Windows 2019-standard _D32ds_v4 4K 5-instans randio IO-test.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

Följande diagram visar resultatet för sekventiell I/O:

![Diagram som visar Windows 2019-standard _D32ds_v4 64K 5-instans sekventiellt dataflöde.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>Hur övervakar du Hyper-V Ethernet-kort och ser till att maximera nätverkskapaciteten?  

En strategi som används vid testning med FIO är att ange `numjobs=16` . När du gör det förgafflar du varje jobb till 16 specifika instanser för att maximera Microsoft Hyper-V nätverkskortet.

Du kan söka efter aktivitet på vart och ett av korten i Windows Prestandaövervakaren genom att välja Prestandaövervakaren **> Lägg till räknare > nätverksgränssnittet > Microsoft Hyper-V nätverkskortet**.

![Skärmbild som visar gränssnittet Lägg till räknare för prestandaövervakaren.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

När du har datatrafik som körs i dina volymer kan du övervaka dina kort i Windows Prestandaövervakaren. Om du inte använder alla dessa 16 virtuella kort kanske du inte maximerar nätverkets bandbreddskapacitet.

![Skärmbild som visar utdata från Prestandaövervakaren.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>Rekommenderas accelererat nätverk?

För maximal prestanda rekommenderar vi att du konfigurerar [accelererat nätverk där](../virtual-network/create-vm-accelerated-networking-powershell.md) det är möjligt. Tänk på följande:  

* Funktionen Azure Portal accelererat nätverk som standard för virtuella datorer som stöder den här funktionen.  Andra distributionsmetoder som Ansible och liknande konfigurationsverktyg kanske dock inte gör det.  Om du inte aktiverar accelererat nätverk kan det öka prestandan för en dator.  
* Om accelererat nätverk inte är aktiverat i nätverksgränssnittet för en virtuell dator på grund av bristande stöd för en instanstyp eller storlek, förblir det inaktiverat med större instanstyper. Du behöver manuella åtgärder i dessa fall.

## <a name="are-jumbo-frames-supported"></a>Stöds tavelramar?

Det finns inte stöd för tavelramar med virtuella Azure-datorer.

## <a name="is-smb-signing-supported"></a>Stöds SMB-signering? 

SMB-protokollet utgör grunden för fil- och utskriftsdelning och andra nätverksåtgärder, till exempel fjärradministration i Windows. För att förhindra man-i-mitten-attacker som ändrar SMB-paket under överföring, stöder SMB-protokollet digital signering av SMB-paket. 

SMB-signering stöds för alla SMB-protokollversioner som stöds av Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Hur påverkas prestandan av SMB-signering?  

SMB-signering har en skadliga effekt på SMB-prestanda. Bland andra potentiella orsaker till prestandaförsämringen förbrukar den digitala signeringen av varje paket ytterligare processorkraft på klientsidan som perfmon-utdata nedan visar. I det här fallet verkar Core 0 ansvara för SMB, inklusive SMB-signering.  En jämförelse med de sekventiella dataflödessiffrorna för sekventiell läsning utan flera kanaler i föregående avsnitt visar att SMB-signering minskar det totala dataflödet från 875MiB/s till cirka 250MiB/s. 

![Diagram som visar prestandapåverkan för SMB-signering.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)

## <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a>Vilken påverkan förväntas av SMB-kryptering på klientarbetsbelastningar?

Se [Vanliga frågor och svar om SMB-kryptering.](azure-netapp-files-faqs.md#smb_encryption_impact)

## <a name="next-steps"></a>Nästa steg  

- [Vanliga frågor och svar om Azure NetApp Files](azure-netapp-files-faqs.md)
- Se följande [Azure NetApp Files: Hanterade företagsfilresurser](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) för SMB-arbetsbelastningar om att använda SMB-filresurser med Azure NetApp Files.