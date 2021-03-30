---
title: Konfigurera katastrof återställning Active Directory/DNS med Azure Site Recovery
description: Den här artikeln beskriver hur du implementerar en katastrof återställnings lösning för Active Directory och DNS med Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mayg
ms.openlocfilehash: 528a24bb64aa8d323b5d63a27af0a52ccdf1abb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86132318"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Konfigurera katastrof återställning för Active Directory och DNS

Företags program som SharePoint, Dynamics AX och SAP är beroende av Active Directory och en DNS-infrastruktur för att fungera korrekt. När du konfigurerar haveri beredskap för program, behöver du ofta återställa Active Directory och Domain Name System (DNS) innan du återställer andra program komponenter för att säkerställa rätt program funktioner.

Du kan använda [Site Recovery](site-recovery-overview.md) för att skapa en katastrof återställnings plan för Active Directory. När ett avbrott inträffar kan du initiera en redundansväxling. Active Directory kan vara igång på några minuter. Om du har distribuerat Active Directory för flera program på den primära platsen, till exempel för SharePoint och SAP, kanske du vill växla över hela platsen. Du kan först redundansväxla Active Directory att använda Site Recovery. Sedan växlar du över de andra programmen med hjälp av programspecifika återställnings planer.

Den här artikeln beskriver hur du skapar en katastrof återställnings lösning för Active Directory. Det innehåller nödvändiga komponenter och instruktioner för redundans. Du bör vara bekant med Active Directory och Site Recovery innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- Om du replikerar till Azure förbereder du [Azure-resurser](tutorial-prepare-azure.md), inklusive en prenumeration, ett Azure-Virtual Network, ett lagrings konto och ett Recovery Services-valv.
- Granska [support kraven](./vmware-physical-azure-support-matrix.md) för alla komponenter.

## <a name="replicate-the-domain-controller"></a>Replikera domänkontrollanten

- Du måste konfigurera Site Recovery replikering på minst en virtuell dator (VM) som är värd för en domänkontrollant eller DNS.
- Om du har flera domänkontrollanter i din miljö måste du också konfigurera ytterligare en domänkontrollant på mål platsen. Den ytterligare domänkontrollanten kan vara i Azure eller i ett sekundärt lokalt Data Center.
- Om du bara har några få program och en domänkontrollant kanske du vill växla över hela platsen tillsammans. I det här fallet rekommenderar vi att du använder Site Recovery för att replikera domänkontrollanten till mål platsen, antingen i Azure eller i ett sekundärt lokalt Data Center. Du kan använda samma replikerade domänkontrollant eller virtuell DNS-dator för [redundanstest](#test-failover-considerations).
- Om du har många program och fler än en domänkontrollant i din miljö, eller om du planerar att redundansväxla ett fåtal program i taget Site Recovery, rekommenderar vi att du konfigurerar ytterligare en domänkontrollant på mål platsen (antingen i Azure eller i ett sekundärt lokalt Data Center) om du planerar att redundansväxla ett fåtal program samtidigt. För [redundanstest](#test-failover-considerations)kan du använda en domänkontrollant som replikeras av Site Recovery. För redundans kan du använda ytterligare en domänkontrollant på mål platsen.

## <a name="enable-protection-with-site-recovery"></a>Aktivera skydd med Site Recovery

Du kan använda Site Recovery för att skydda den virtuella datorn som är värd för domänkontrollanten eller DNS.

### <a name="protect-the-vm"></a>Skydda den virtuella datorn

Den domänkontrollant som replikeras med hjälp av Site Recovery används för [redundanstest](#test-failover-considerations). Se till att den uppfyller följande krav:

1. Domänkontrollanten är en global katalog server.
1. Domänkontrollanten bör vara rollen som FSMO (Flexible Single Master Operations) för roller som behövs under redundanstest. Annars måste dessa roller tas [över efter redundansväxlingen](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) .

### <a name="configure-vm-network-settings"></a>Konfigurera inställningar för virtuellt dator nätverk

För den virtuella dator som är värd för domänkontrollanten eller DNS, i Site Recovery konfigurerar du nätverks inställningar under **beräknings-och nätverks** inställningarna för den replikerade virtuella datorn. Detta säkerställer att den virtuella datorn är ansluten till rätt nätverk efter redundansväxlingen.

## <a name="protect-active-directory"></a>Skydda Active Directory

### <a name="site-to-site-protection"></a>Plats-till-plats-skydd

Skapa en domänkontrollant på den sekundära platsen. När du befordrar servern till en roll för domänkontrollant anger du namnet på samma domän som används på den primära platsen. Du kan använda snapin-modulen **Active Directory platser och tjänster** för att konfigurera inställningar för plats länk objektet som platserna läggs till i. Genom att konfigurera inställningar på en plats länk kan du styra när replikeringen sker mellan två eller flera platser och hur ofta det inträffar. Mer information finns i [Schemalägga replikering mellan platser](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731862(v=ws.11)).

### <a name="site-to-azure-protection"></a>Skydd från plats till Azure

Skapa först en domänkontrollant i ett virtuellt Azure-nätverk. När du befordrar servern till en roll för domänkontrollant anger du samma domän namn som används på den primära platsen.

Konfigurera sedan om DNS-servern för det virtuella nätverket så att den använder DNS-servern i Azure.

:::image type="content" source="./media/site-recovery-active-directory/azure-network.png" alt-text="Azure-nätverk":::

### <a name="azure-to-azure-protection"></a>Azure-till-Azure-skydd

Skapa först en domänkontrollant i ett virtuellt Azure-nätverk. När du befordrar servern till en roll för domänkontrollant anger du samma domän namn som används på den primära platsen.

Konfigurera sedan om DNS-servern för det virtuella nätverket så att den använder DNS-servern i Azure.

## <a name="test-failover-considerations"></a>Överväganden vid testning av redundans

För att undvika påverkan på produktions arbets belastningar sker redundanstestningen i ett nätverk som är isolerat från produktions nätverket.

De flesta program kräver närvaro av en domänkontrollant eller en DNS-server. Innan programmet växlar över måste du därför skapa en domänkontrollant i det isolerade nätverket som ska användas för redundanstest. Det enklaste sättet att göra detta är att använda Site Recovery för att replikera en virtuell dator som är värd för en domänkontrollant eller DNS. Kör sedan ett redundanstest för den virtuella domänkontrollanten på den virtuella datorn innan du kör ett redundanstest för återställnings planen för programmet.

1. Använd Site Recovery för att [Replikera](vmware-azure-tutorial.md) den virtuella datorn som är värd för domänkontrollanten eller DNS.
1. Skapa ett isolerat nätverk. Alla virtuella nätverk som du skapar i Azure är isolerade från andra nätverk som standard. Vi rekommenderar att du använder samma IP-adressintervall för det här nätverket som du använder i produktions nätverket. Aktivera inte plats-till-plats-anslutning i det här nätverket.
1. Ange en DNS-IP-adress i det isolerade nätverket. Använd IP-adressen som du förväntar dig att den virtuella DNS-datorn ska hämta. Om du replikerar till Azure anger du IP-adressen för den virtuella dator som används vid redundansväxling. Ange IP-adressen i den replikerade virtuella datorn i inställningarna för **beräkning och nätverk** genom att välja **mål-IP-** inställningar.

   :::image type="content" source="./media/site-recovery-active-directory/azure-test-network.png" alt-text="Azure test Network":::

   > [!TIP]
   > Site Recovery försöker skapa virtuella test datorer i ett undernät med samma namn och genom att använda samma IP-adress som anges i **beräknings-och nätverks** inställningarna för den virtuella datorn. Om ett undernät med samma namn inte är tillgängligt i det virtuella Azure-nätverket som anges för redundanstest, skapas den virtuella test datorn i det alfabetiskt första under nätet.
   >
   > Om mål-IP-adressen är en del av det valda under nätet försöker Site Recovery skapa den virtuella datorn för redundanstest genom att använda mål-IP-adressen. Om mål-IP-adressen inte är en del av det valda under nätet skapas den virtuella datorn för redundanstest genom att använda nästa tillgängliga IP-adress i det valda under nätet.

### <a name="test-failover-to-a-secondary-site"></a>Testa redundans till en sekundär plats

1. Om du replikerar till en annan lokal plats och använder DHCP [konfigurerar du DNS och DHCP för redundanstest](hyper-v-vmm-test-failover.md#prepare-dhcp).
1. Gör ett redundanstest för den virtuella domänkontrollanten på den virtuella datorn som körs i det isolerade nätverket. Använd den senaste tillgängliga _programkonsekventa_ återställnings punkten för den virtuella domänkontrollanten för att utföra redundanstest.
1. Kör ett redundanstest för återställnings planen som innehåller virtuella datorer som programmet körs på.
1. När testningen är klar _rensar du redundanstest_ på den virtuella domänkontrollanten. Det här steget tar bort domänkontrollanten som skapades för redundanstest.

### <a name="remove-references-to-other-domain-controllers"></a>Ta bort referenser till andra domänkontrollanter

När du startar en redundanstest ska du inte ta med alla domänkontrollanter i test nätverket. Om du vill ta bort referenser till andra domänkontrollanter som finns i produktions miljön kan du behöva ta [fsmo Active Directory-roller](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) och göra [metadata-rensning](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816907(v=ws.10)) för domän kontrollers som saknas.

### <a name="issues-caused-by-virtualization-safeguards"></a>Problem som orsakas av virtualiseringsbaserad skydd

> [!IMPORTANT]
> Några av de konfigurationer som beskrivs i det här avsnittet är inte standardkonfigurationer för domänkontrollanten eller standard. Om du inte vill göra dessa ändringar i en produktionsavdelning kan du skapa en domänkontrollant som är dedikerad för Site Recovery ska användas för redundanstest. Gör dessa ändringar endast på den domänkontrollanten.

Från och med Windows Server 2012 [är ytterligare skydd inbyggda i Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Dessa skydd bidrar till att skydda virtualiserade domänkontrollanter mot återställningar av USN (Update Sequence Number) om den underliggande hypervisor **-plattformen stöder VM-GenerationID**. Azure stöder **VM-GenerationID**. Därför har domänkontrollanter som kör Windows Server 2012 eller senare på virtuella Azure-datorer ytterligare skydd.

När **VM-GenerationID** återställs återställs också **InvocationID** -värdet för AD DS-databasen. Dessutom tas RID-poolen bort och `SYSVOL` mappen markeras som icke-auktoritativ. Mer information finns i [Introduktion till Active Directory Domain Services virtualisering](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) och [säker virtualisera DISTRIBUTED File System replikering (DFSR)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/safely-virtualizing-dfsr/ba-p/424671).

Växling till Azure kan orsaka att **VM-GenerationID** återställs. Om du återställer **VM-GenerationID** utlöses ytterligare skydd när domänkontrollantens virtuella dator startas i Azure. Detta kan leda till en betydande fördröjning i att kunna logga in på den virtuella domänkontrollanten.

Eftersom den här domänkontrollanten endast används i en redundanstest, är det inte nödvändigt att virtualisera skyddet. För att säkerställa att värdet för **VM-GenerationID** för den virtuella domänkontrollantens virtuella dator inte ändras kan du ändra värdet för följande `DWORD` till **4** i den lokala domänkontrollanten:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`

#### <a name="symptoms-of-virtualization-safeguards"></a>Symptom på skydd av virtualisering

Om Virtualization-skydd utlöses efter ett redundanstest, kan ett eller flera av följande symptom visas:

- **GenerationID** -värdet ändras:

  :::image type="content" source="./media/site-recovery-active-directory/Event2170.png" alt-text="Ändring av generations-ID":::

- **InvocationID** -värdet ändras:

  :::image type="content" source="./media/site-recovery-active-directory/Event1109.png" alt-text="Ändring av anrops-ID":::

- `SYSVOL` mappen och `NETLOGON` resurserna är inte tillgängliga.

  :::image type="content" source="./media/site-recovery-active-directory/sysvolshare.png" alt-text="Resurs för SYSVOL-mapp":::

  :::image type="content" source="./media/site-recovery-active-directory/Event13565.png" alt-text="NtFrs SYSVOL-mapp":::

- DFSR-databaser tas bort.

  :::image type="content" source="./media/site-recovery-active-directory/Event2208.png" alt-text="DFSR-databaser tas bort":::

### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Felsöka problem med domänkontrollanten under testning av redundans

> [!IMPORTANT]
> Några av de konfigurationer som beskrivs i det här avsnittet är inte standardkonfigurationer för domänkontrollanter eller standard. Om du inte vill göra dessa ändringar i en produktionsavdelning kan du skapa en domänkontrollant som är dedikerad för Site Recovery redundanstest. Gör ändringarna endast på den dedikerade domänkontrollanten.

1. I kommando tolken kör du följande kommando för att kontrol lera om `SYSVOL` mappen och `NETLOGON` mappen delas:

    `NET SHARE`

1. Kör följande kommando i kommando tolken för att kontrol lera att domänkontrollanten fungerar korrekt:

    `dcdiag /v > dcdiag.txt`

1. I utgående loggen letar du efter följande text. Texten bekräftar att domänkontrollanten fungerar korrekt.

    - `passed test Connectivity`
    - `passed test Advertising`
    - `passed test MachineAccount`

Om föregående villkor är uppfyllda är det troligt att domänkontrollanten fungerar korrekt. Om den inte gör det utför du följande steg:

1. Gör en auktoritativ återställning av domänkontrollanten. Tänk på följande:

    - Även om vi inte rekommenderar replikering med [tjänsten File Replication (FRS)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-end-is-nigh-for-frs-8211-updated-for-ws2016/ba-p/425379)bör du följa stegen för en auktoritativ återställning om du använder FRS-replikering. Processen beskrivs i [använda register nyckeln BURFLAGS för att initiera om tjänsten File Replication](https://support.microsoft.com/kb/290762).

      Mer information om BurFlags finns i blogg inlägget [D2 och D4: Vad är det för?](/archive/blogs/janelewis/d2-and-d4-what-is-it-for).

    - Om du använder DFSR-replikering slutför du stegen för en auktoritativ återställning. Processen beskrivs i [tvinga en auktoritativ och icke-auktoritativ synkronisering för DFSR-replikerad SYSVOL-mapp (till exempel "D4/D2" för FRS)](https://support.microsoft.com/kb/2218556).

      Du kan också använda PowerShell-funktionerna. Mer information finns i [DFSR-SYSVOL auktoritär/icke-auktoritativ Restore PowerShell Functions](/archive/blogs/thbouche/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions).

1. Kringgå det inledande kravet för synkronisering genom att ange följande register nyckel till **0** i den lokala domänkontrollanten. Om `DWORD` inte finns kan du skapa den under noden **parametrar** .

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

   Mer information finns i [FELSÖKA DNS-händelse-ID 4013: DNS-servern kunde inte läsa in AD-integrerade DNS-zoner](https://support.microsoft.com/kb/2001093).

1. Inaktivera kravet på att en global katalog server ska vara tillgänglig för att verifiera användar inloggningen. Det gör du genom att ange följande register nyckel i den lokala domänkontrollanten till **1**. Om `DWORD` inte finns kan du skapa den under **LSA** -noden.

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

   Mer information finns i [hur den globala katalogen fungerar](/previous-versions/windows/it-pro/windows-server-2003/cc737410(v=ws.10)).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS och domänkontrollant på olika datorer

Om du kör domänkontrollanten och DNs på samma virtuella dator kan du hoppa över den här proceduren.

Om DNS inte finns på samma virtuella dator som domänkontrollanten måste du skapa en virtuell DNS-dator för redundanstest. Du kan använda en ny DNS-server och skapa alla zoner som krävs. Om din Active Directory domän exempelvis `contoso.com` , kan du skapa en DNS-zon med namnet `contoso.com` . De poster som motsvarar Active Directory måste uppdateras i DNS på följande sätt:

1. Se till att dessa inställningar är på plats innan någon annan virtuell dator i återställnings planen startar:

   - Zonen måste ha namnet efter skogens rot namn.
   - Zonen måste vara filbaserad.
   - Zonen måste vara aktive rad för säkra och oskyddade uppdateringar.
   - Matcharen för den virtuella datorn som är värd för domänkontrollanten ska peka på IP-adressen för den virtuella DNS-datorn.

1. Kör följande kommando på den virtuella datorn som är värd för domänkontrollanten:

   `nltest /dsregdns`

1. Kör följande kommandon för att lägga till en zon på DNS-servern, tillåta oskyddade uppdateringar och lägga till en post för zonen i DNS:

   ```cmd
   dnscmd /zoneadd contoso.com  /Primary

   dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1

   dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>

   dnscmd /config contoso.com /allowupdate 1
   ```

## <a name="next-steps"></a>Nästa steg

[Lär dig mer](site-recovery-workload.md) om att skydda företags arbets belastningar med Azure Site Recovery.
