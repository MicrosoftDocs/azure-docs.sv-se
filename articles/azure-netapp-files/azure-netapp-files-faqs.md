---
title: Vanliga frågor och svar om Azure NetApp Files | Microsoft Docs
description: Läs vanliga frågor om Azure NetApp Files, till exempel nätverk, säkerhet, prestanda, kapacitetshantering och datamigrering/skydd.
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
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: 6cef4860184b217e96e8967ab24a3befc632e316
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811858"
---
# <a name="faqs-about-azure-netapp-files"></a>Vanliga frågor och svar om Azure NetApp Files

Den här artikeln besvarar vanliga frågor och svar om Azure NetApp Files. 

## <a name="networking-faqs"></a>Vanliga frågor och svar om nätverk

### <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>Går datasökvägen för NFS eller SMB via Internet?  

Nej. Datasökvägen för NFS eller SMB går inte via Internet. Azure NetApp Files är en intern Azure-tjänst som distribueras till Azure Virtual Network (VNet) där tjänsten är tillgänglig. Azure NetApp Files använder ett delegerat undernät och tillser ett nätverksgränssnitt direkt i det virtuella nätverket. 

Se [Riktlinjer för Azure NetApp Files nätverksplanering för](./azure-netapp-files-network-topologies.md) mer information.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Kan jag ansluta ett VNet som jag redan har skapat till Azure NetApp Files tjänsten?

Ja, du kan ansluta virtuella nätverk som du har skapat till tjänsten. 

Se [Riktlinjer för Azure NetApp Files nätverksplanering för](./azure-netapp-files-network-topologies.md) mer information.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Kan jag montera en NFS-volym med Azure NetApp Files DNS FQDN-namn?

Ja, det kan du om du skapar de DNS-poster som krävs. Azure NetApp Files tillhandahåller tjänst-IP-adressen för den etablerade volymen. 

> [!NOTE] 
> Azure NetApp Files kan distribuera ytterligare IP-adresser för tjänsten efter behov.  DNS-poster kan behöva uppdateras regelbundet.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Kan jag ange eller välja min egen IP-adress för en Azure NetApp Files volym?  

Nej. IP-tilldelning till Azure NetApp Files volymer är dynamisk. Statisk IP-tilldelning stöds inte. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Stöder Azure NetApp Files virtuella nätverk med dubbla stackar (IPv4 och IPv6) ?

Nej, Azure NetApp Files stöder för närvarande inte virtuella nätverk med dubbla stackar (IPv4 och IPv6).  
 
## <a name="security-faqs"></a>Vanliga frågor och svar om säkerhet

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Kan nätverkstrafiken mellan den virtuella Azure-datorn och lagringen krypteras?

Datatrafik mellan NFSv4.1-klienter och Azure NetApp Files-volymer kan krypteras med Kerberos med AES-256-kryptering. Mer [information finns i Konfigurera NFSv4.1 Kerberos Azure NetApp Files kryptering.](configure-kerberos-encryption.md)   

Datatrafik mellan NFSv3- eller SMB3-klienter till Azure NetApp Files inte krypteras. Trafiken från en virtuell Azure-dator (som kör en NFS- eller SMB-klient) till Azure NetApp Files är dock lika säker som all annan Azure-VM-till-VM-trafik. Den här trafiken är lokal i Azure-datacenternätverket. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Kan lagringen krypteras i vila?

Alla Azure NetApp Files volymer krypteras med FIPS 140-2-standarden. Alla nycklar hanteras av Azure NetApp Files tjänsten. 

### <a name="how-are-encryption-keys-managed"></a>Hur hanteras krypteringsnycklar? 

Nyckelhantering för Azure NetApp Files hanteras av tjänsten. En unik XTS-AES-256-datakrypteringsnyckel genereras för varje volym. En krypteringsnyckelhierarki används för att kryptera och skydda alla volymnycklar. Dessa krypteringsnycklar visas eller rapporteras aldrig i okrypterat format. Krypteringsnycklar tas bort omedelbart när en volym tas bort.

Stöd för kund hanterade nycklar (Bring Your Own Key) som använder Azure Dedicated HSM är tillgängligt på kontrollerad basis i regionerna USA, östra, USA, södra centrala, USA, västra 2 US Gov, Virginia regioner. Du kan begära åtkomst på [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) . När kapaciteten blir tillgänglig godkänns begäranden.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Kan jag konfigurera NFS-exportprincipregler för att styra åtkomsten till Azure NetApp Files mål för tjänstmontering?

Ja, du kan konfigurera upp till fem regler i en enda NFS-exportprincip.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Stöder Azure NetApp Files nätverkssäkerhetsgrupper?

Nej, för närvarande kan du inte tillämpa nätverkssäkerhetsgrupper på det delegerade undernätet Azure NetApp Files eller de nätverksgränssnitt som skapats av tjänsten.

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Kan jag använda Azure RBAC med Azure NetApp Files?

Ja, Azure NetApp Files Azure RBAC-funktioner. Tillsammans med de inbyggda Azure-rollerna kan [du skapa anpassade roller](../role-based-access-control/custom-roles.md) för Azure NetApp Files. 

En fullständig lista över behörigheter Azure NetApp Files Azure-resursprovideråtgärder för [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp) .

### <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Stöds Azure-aktivitetsloggar på Azure NetApp Files?

Azure NetApp Files är en intern Azure-tjänst. Alla PUT-, POST- och DELETE-API:er mot Azure NetApp Files loggas. Loggarna visar till exempel aktiviteter som vem som skapade ögonblicksbilden, vem som ändrade volymen och så vidare.

En fullständig lista över API-åtgärder finns i [Azure NetApp Files REST API](/rest/api/netapp/).

### <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Kan jag använda Azure-principer med Azure NetApp Files?

Ja, du kan skapa [anpassade Azure-principer.](../governance/policy/tutorials/create-custom-policy-definition.md) 

Du kan dock inte skapa Azure-principer (anpassade namngivningsprinciper) i Azure NetApp Files gränssnittet. Se [Riktlinjer för Azure NetApp Files nätverksplanering.](azure-netapp-files-network-topologies.md#considerations)

## <a name="performance-faqs"></a>Vanliga frågor och svar om prestanda

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Vad ska jag göra för att optimera eller finjustera Azure NetApp Files prestanda?

Du kan vidta följande åtgärder enligt prestandakraven: 
- Kontrollera att den virtuella datorn har rätt storlek.
- Aktivera accelererat nätverk för den virtuella datorn.
- Välj önskad servicenivå och storlek för kapacitetspoolen.
- Skapa en volym med önskad kvotstorlek för kapacitet och prestanda.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Hur gör jag för att konvertera dataflödesbaserade tjänstnivåer för Azure NetApp Files till IOPS?

Du kan konvertera MB/s till IOPS med hjälp av följande formel:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Hur gör jag för att ändra tjänstnivå för en volym?

Du kan ändra servicenivån för en befintlig volym genom att flytta volymen till en annan kapacitetspool som använder [den servicenivå](azure-netapp-files-service-levels.md) som du vill använda för volymen. Se [Ändra tjänstnivån för en volym dynamiskt.](dynamic-change-volume-service-level.md) 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Hur gör jag för att övervaka Azure NetApp Files prestanda?

Azure NetApp Files ger mätvärden för volymprestanda. Du kan också använda Azure Monitor för att övervaka användningsstatistik för Azure NetApp Files.  Se [Mått för Azure NetApp Files](azure-netapp-files-metrics.md) för listan över prestandamått för Azure NetApp Files.

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Hur påverkas prestandan av Kerberos på NFSv4.1?

Se [Prestandapåverkan av Kerberos på NFSv4.1-volymer](performance-impact-kerberos.md) för information om säkerhetsalternativ för NFSv4.1, testade prestandavektorer och förväntad prestandapåverkan. 

## <a name="nfs-faqs"></a>Vanliga frågor och svar om NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Jag vill ha en volym monterad automatiskt när en virtuell Azure-dator startas eller startas om.  Hur gör jag för att konfigurera värden för beständiga NFS-volymer?

För att en NFS-volym automatiskt ska monteras vid start eller omstart av den virtuella datorn lägger du till en `/etc/fstab` post i filen på värden. 

Mer [information finns i Montera eller demontera en volym för virtuella Windows- eller Linux-datorer.](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Varför visar inte DF-kommandot på NFS-klienten den etablerade volymstorleken?

Den volymstorlek som rapporteras i DF är den maximala storlek Azure NetApp Files volymen kan växa till. Storleken på den Azure NetApp Files volymen i DF-kommandot återspeglar inte kvoten eller storleken på volymen.  Du kan hämta Azure NetApp Files volymstorlek eller kvot via Azure Portal eller API:et.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Vilken NFS-version Azure NetApp Files stöd för?

Azure NetApp Files stöder NFSv3 och NFSv4.1. Du kan [skapa en volym med](azure-netapp-files-create-volumes.md) någon av NFS-versionerna. 

### <a name="how-do-i-enable-root-squashing"></a>Hur gör jag för att aktivera rot mosning?

Du kan ange om rotkontot kan komma åt volymen eller inte med hjälp av volymens exportprincip. Mer [information finns i Konfigurera exportprincip för en NFS-volym.](azure-netapp-files-configure-export-policy.md)

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Kan jag använda samma filsökväg (token för volymskapande) för flera volymer?

Ja, det kan du. Filsökvägen måste dock användas i antingen en annan prenumeration eller i en annan region.   

Du kan till exempel skapa en volym med namnet `vol1` . Sedan skapar du en till volym som också `vol1` anropas i en annan kapacitetspool, men i samma prenumeration och region. I det här fallet orsakar användning av samma `vol1` volymnamn ett fel. Om du vill använda samma filsökväg måste namnet finnas i en annan region eller prenumeration.

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Varför tar det lång tid för klienten att söka i mappar och undermappar när jag försöker komma åt NFS-volymer via en Windows-klient?

Kontrollera att är `CaseSensitiveLookup` aktiverat på Windows-klienten för att påskynda uppslag av mappar och undermappar:

1. Använd följande PowerShell-kommando för att aktivera CaseSensitiveLookup:   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Montera volymen på Windows-servern.   
    Exempel:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Hur stöder Azure NetApp Files NFSv4.1-fillåsning? 

För NFSv4.1-klienter stöder Azure NetApp Files NFSv4.1-mekanismen för fillåsning som upprätthåller tillståndet för alla fillås i en lånebaserad modell. 

Per RFC 3530 definierar Azure NetApp Files enda låneperiod för alla tillstånd som en NFS-klient har. Om klienten inte förnyar sitt lån inom den angivna perioden kommer alla tillstånd som är associerade med klientens lån att släppas av servern.  

Om en klient som monterar en volym till exempel slutar svara eller kraschar utöver tidsgränserna, släpps låsen. Klienten kan förnya sitt lån explicit eller implicit genom att utföra åtgärder som att läsa en fil.   

En respitperiod definierar en period av särskild bearbetning där klienter kan försöka återta sitt låsningstillstånd under en serveråterställning. Standardtidsutfallet för lånen är 30 sekunder med en respitperiod på 45 sekunder. Efter det frisläpps klientens lån.   

## <a name="smb-faqs"></a>Vanliga frågor och svar om SMB

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Vilka SMB-versioner stöds av Azure NetApp Files?

Azure NetApp Files stöder SMB 2.1 och SMB 3.1 (som innehåller stöd för SMB 3.0).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Krävs en Active Directory-anslutning för SMB-åtkomst? 

Ja, du måste skapa en Active Directory-anslutning innan du distribuerar en SMB-volym. De angivna domänkontrollanterna måste vara tillgängliga för det delegerade undernätet Azure NetApp Files för att anslutningen ska lyckas.  Mer [information finns i Skapa en SMB-volym.](./azure-netapp-files-create-volumes-smb.md) 

### <a name="how-many-active-directory-connections-are-supported"></a>Hur många Active Directory-anslutningar stöds?

Azure NetApp Files stöder inte flera Active Directory-anslutningar (AD) i en *enda region,* även om AD-anslutningarna finns i olika NetApp-konton. Du kan dock ha flera AD-anslutningar i en enda *prenumeration,* så länge AD-anslutningarna finns i olika regioner. Om du behöver flera AD-anslutningar i en enda region kan du använda separata prenumerationer för att göra det. 

En AD-anslutning konfigureras per NetApp-konto. AD-anslutningen visas bara via det NetApp-konto som den skapas i.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Stöder Azure NetApp Files stöd Azure Active Directory? 

Både [Azure Active Directory (AD) Domain Services](../active-directory-domain-services/overview.md) och Active Directory Domain Services [(AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) stöds. Du kan använda befintliga Active Directory-domänkontrollanter med Azure NetApp Files. Domänkontrollanter kan finnas i Azure som virtuella datorer eller lokalt via ExpressRoute eller S2S VPN. Azure NetApp Files stöder inte AD-anslutning [för Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) just nu.

Om du använder Azure NetApp Files med Azure Active Directory Domain Services är organisationsenhetssökvägen när du `OU=AADDC Computers` konfigurerar Active Directory för ditt NetApp-konto.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Vilka versioner Windows Server Active Directory stöds?

Azure NetApp Files stöder Windows Server 2008r2SP1-2019-versioner av Active Directory Domain Services.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Varför visar inte det tillgängliga utrymmet på SMB-klienten den etablerade storleken?

Den volymstorlek som rapporteras av SMB-klienten är den maximala storlek Azure NetApp Files volymen kan växa till. Storleken på den Azure NetApp Files volymen som visas på SMB-klienten återspeglar inte kvoten eller storleken på volymen. Du kan hämta Azure NetApp Files volymstorlek eller kvot via Azure Portal eller API:et.

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Jag har problem med att ansluta till min SMB-resurs. Vad ska jag göra?

Som bästa praxis bör du ange den maximala toleransen för synkronisering av datorklocka till fem minuter. Mer information finns i Maximal [tolerans för synkronisering av datorklocka.](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)) 

### <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>Kan jag hantera `SMB Shares` `Sessions` , och via `Open Files` datorhanteringskonsolen (MMC)?

Hantering av `SMB Shares` , `Sessions` och via `Open Files` datorhanteringskonsolen (MMC) stöds inte för närvarande.

### <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>Hur kan jag hämta IP-adressen för en SMB-volym via portalen?

Använd **länken JSON-vy** i volymöversiktsfönstret och leta efter  **startIp-identifieraren** under  ->  **properties mountTargets**.

### <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>Kan en Azure NetApp Files SMB-resurs fungera som en DFS-namnrymdsrot (DFS-N)?

Nej. Men Azure NetApp Files SMB-resurser kan fungera som ett mappmål för DFS-namnrymd (DFS-N).   
Om du vill använda en Azure NetApp Files SMB-resurs som ett DFS-N-mappmål anger du monteringssökvägen för Universal Naming Convention (UNC) för SMB-resursen i Azure NetApp Files med hjälp av [proceduren DFS Add Folder Target.](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target)  

### <a name="smb-encryption-faqs"></a>Vanliga frågor och svar om SMB-kryptering

I det här avsnittet besvaras vanliga frågor om SMB-kryptering (SMB 3.0 och SMB 3.1.1).

#### <a name="what-is-smb-encryption"></a>Vad är SMB-kryptering?  

[SMB-kryptering](/windows-server/storage/file-server/smb-security) ger slutpunkt till slutpunkt-kryptering av SMB-data och skyddar data från avlyssning av förekomster i ej betrodda nätverk. SMB-kryptering stöds på SMB 3.0 och högre. 

#### <a name="how-does-smb-encryption-work"></a>Hur fungerar SMB-kryptering?

När en begäran skickas till lagringen krypterar klienten begäran, som lagringen sedan dekrypterar. Svar krypteras på liknande sätt av servern och dekrypteras av klienten.

#### <a name="which-clients-support-smb-encryption"></a>Vilka klienter stöder SMB-kryptering?

Windows 10, Windows 2012 och senare versioner stöder SMB-kryptering.

#### <a name="with-azure-netapp-files-at-what-layer-is-smb-encryption-enabled"></a>Vilket lager Azure NetApp Files SMB-kryptering aktiverat med hjälp av Azure NetApp Files lager?  

SMB-kryptering är aktiverat på resursnivå.

#### <a name="what-forms-of-smb-encryption-are-used-by-azure-netapp-files"></a>Vilka former av SMB-kryptering används av Azure NetApp Files?

SMB 3.0 använder AES-CCM-algoritmen, medan SMB 3.1.1 använder AES-GCM-algoritmen

#### <a name="is-smb-encryption-required"></a>Krävs SMB-kryptering?

SMB-kryptering krävs inte. Därför är den endast aktiverad för en viss resurs om användaren begär att Azure NetApp Files aktivera den. Azure NetApp Files-resurser exponeras aldrig för Internet. De är bara tillgängliga inifrån ett visst VNet, via VPN eller Express Route, så Azure NetApp Files-resurser är säkra. Valet att aktivera SMB-kryptering är helt upp till användaren. Tänk på den förväntade prestandaförseningen innan du aktiverar den här funktionen.

#### <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a><a name="smb_encryption_impact"></a>Vad förväntas påverka SMB-kryptering på klientarbetsbelastningar?

Även om SMB-kryptering påverkar både klienten (PROCESSORkostnader för kryptering och dekryptering av meddelanden) och lagringen (minskning av dataflödet), visar följande tabell endast lagringspåverkan. Du bör testa krypteringsprestandapåverkan mot dina egna program innan du distribuerar arbetsbelastningar till produktion.

|     I/O-profil       |     Påverkan        |
|-  |-  |
|     Läs- och skrivarbetsbelastningar      |     10 % till 15 %        |
|     Metadataintensiv        |     5 %    |

## <a name="capacity-management-faqs"></a>Vanliga frågor och svar om kapacitetshantering

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Hur gör jag för att övervaka användningen för kapacitetspoolen och volymen av Azure NetApp Files? 

Azure NetApp Files tillhandahåller kapacitetspooler och mått för volymanvändning. Du kan också använda Azure Monitor för att övervaka Azure NetApp Files. Mer [information finns Azure NetApp Files](azure-netapp-files-metrics.md) mått. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kan jag hantera Azure NetApp Files via Azure Storage Explorer?

Nej. Azure NetApp Files stöds inte av Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Hur gör jag för att avgöra om en katalog närmar sig gränsen?

Du kan använda kommandot från en klient för att se om en katalog närmar sig `stat` den maximala storleksgränsen för katalogmetadata (320 MB).   

För en katalog på 320 MB är antalet block 655360, där varje blockstorlek är 512 byte.  (Det vill säga 320x1024x1024/512.)  Det här antalet översätts till högst cirka 4 miljoner filer för en katalog på 320 MB. Det faktiska antalet maximalt antal filer kan dock vara lägre, beroende på faktorer som antalet filer som innehåller icke-ASCII-tecken i katalogen. Därför bör du använda kommandot på `stat` följande sätt för att avgöra om katalogen närmar sig gränsen.  

Exempel:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>Vanliga frågor och svar om datamigrering och skydd

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Hur gör jag för att migrera data till Azure NetApp Files?
Azure NetApp Files tillhandahåller NFS- och SMB-volymer.  Du kan använda val annat filbaserat kopieringsverktyg för att migrera data till tjänsten. 

NetApp erbjuder en SaaS-baserad lösning, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  Med lösningen kan du replikera NFS- eller SMB-data till Azure NetApp Files NFS-exporter eller SMB-resurser. 

Du kan också använda en mängd olika kostnadsfria verktyg för att kopiera data. För NFS kan du använda verktyg för arbetsbelastningar som [rsync](https://rsync.samba.org/examples.html) för att kopiera och synkronisera källdata till en Azure NetApp Files volym. För SMB kan du använda arbetsbelastningar [robocopy](/windows-server/administration/windows-commands/robocopy) på samma sätt.  Dessa verktyg kan också replikera fil- eller mappbehörigheter. 

Kraven för datamigrering från lokal plats till Azure NetApp Files är följande: 

- Se Azure NetApp Files är tillgängligt i Azure-målregionen.
- Verifiera nätverksanslutningen mellan källan och den Azure NetApp Files målvolymens IP-adress. Dataöverföring mellan den lokala platsen och Azure NetApp Files-tjänsten stöds via ExpressRoute.
- Skapa målvolymen Azure NetApp Files målvolymen.
- Överför källdata till målvolymen med önskat filkopieringsverktyg.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Hur gör jag för att skapa en kopia av en Azure NetApp Files i en annan Azure-region?
    
Azure NetApp Files tillhandahåller NFS- och SMB-volymer.  Alla filbaserade kopieringsverktyg kan användas för att replikera data mellan Azure-regioner. 

NetApp erbjuder en SaaS-baserad lösning, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  Med lösningen kan du replikera NFS- eller SMB-data till Azure NetApp Files NFS-exporter eller SMB-resurser. 

Du kan också använda en mängd olika kostnadsfria verktyg för att kopiera data. För NFS kan du använda arbetsbelastningsverktyg som [rsync](https://rsync.samba.org/examples.html) för att kopiera och synkronisera källdata till en Azure NetApp Files volym. För SMB kan du använda arbetsbelastningar [robocopy](/windows-server/administration/windows-commands/robocopy) på samma sätt.  Dessa verktyg kan också replikera fil- eller mappbehörigheter. 

Kraven för att replikera en Azure NetApp Files till en annan Azure-region är följande: 
- Se Azure NetApp Files är tillgänglig i Azure-målregionen.
- Verifiera nätverksanslutningen mellan virtuella nätverk i varje region. För närvarande global peering mellan virtuella nätverk inte.  Du kan upprätta en anslutning mellan virtuella nätverk genom att länka till en ExpressRoute-krets eller använda en S2S VPN-anslutning. 
- Skapa målvolymen Azure NetApp Files volymen.
- Överför källdata till målvolymen med hjälp av det filkopieringsverktyg du föredrar.

### <a name="is-migration-with-azure-data-box-supported"></a>Stöds migrering med Azure Data Box stöd?

Nej. Azure Data Box stöder inte Azure NetApp Files för närvarande. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Stöds migrering med Azure Import/Export-tjänsten?

Nej. Azure Import/Export-tjänsten stöder inte Azure NetApp Files för närvarande.

## <a name="product-faqs"></a>Vanliga frågor och svar om produkter

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Kan jag använda Azure NetApp Files NFS- eller SMB-volymer med Azure VMware Solution (AVS)?

Du kan montera Azure NetApp Files NFS-volymer på virtuella Windows-datorer med AVS eller virtuella Linux-datorer. Du kan mappa Azure NetApp Files SMB-resurser på virtuella Windows-datorer i AVS. Mer information finns i [Azure NetApp Files med Azure VMware Solution]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md).  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Vilka regioner stöds för att använda Azure NetApp Files NFS- eller SMB-volymer med Azure VMware Solution (AVS)?

Användning Azure NetApp Files NFS- eller SMB-volymer med AVS stöds i följande regioner – USA, östra, USA, västra, Europa, västra och Australien, östra.

## <a name="next-steps"></a>Nästa steg  

- [Microsoft Azure vanliga frågor och svar om ExpressRoute](../expressroute/expressroute-faqs.md)
- [Vanliga frågor och svar Microsoft Azure Virtual Network Microsoft Azure Virtual Network om Microsoft Azure Virtual Network](../virtual-network/virtual-networks-faq.md)
- [Skapa en supportbegäran för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Vanliga frågor och svar om SMB-prestanda för Azure NetApp Files](azure-netapp-files-smb-performance.md)
