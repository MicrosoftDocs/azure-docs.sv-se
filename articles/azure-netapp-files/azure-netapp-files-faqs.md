---
title: Vanliga frågor och svar om Azure NetApp Files | Microsoft Docs
description: Läs vanliga frågor och svar om Azure NetApp Files, till exempel nätverk, säkerhet, prestanda, kapacitets hantering och datamigrering/skydd.
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
ms.date: 01/21/2020
ms.author: b-juche
ms.openlocfilehash: f680b1ec76200cb4d3bfc90cd83a2c134f379245
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696133"
---
# <a name="faqs-about-azure-netapp-files"></a>Vanliga frågor och svar om Azure NetApp Files

I den här artikeln besvaras vanliga frågor och svar om Azure NetApp Files. 

## <a name="networking-faqs"></a>Vanliga frågor om nätverk

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Går NFS-datasökvägen över Internet?  

Nej. Data Sök vägen för NFS går inte via Internet. Azure NetApp Files är en Azure Native-tjänst som distribueras till Azure-Virtual Network (VNet) där tjänsten är tillgänglig. Azure NetApp Files använder ett delegerat undernät och etablerar ett nätverks gränssnitt direkt på det virtuella nätverket. 

Mer information finns i [rikt linjer för Azure NetApp Files nätverks planering](./azure-netapp-files-network-topologies.md) .  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Kan jag ansluta ett VNet som jag redan har skapat till Azure NetApp Files tjänsten?

Ja, du kan ansluta virtuella nätverk som du har skapat till tjänsten. 

Mer information finns i [rikt linjer för Azure NetApp Files nätverks planering](./azure-netapp-files-network-topologies.md) .  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Kan jag montera en NFS-volym Azure NetApp Files med DNS FQDN-namn?

Ja, du kan, om du skapar nödvändiga DNS-poster. Azure NetApp Files tillhandahåller tjänstens IP-adress för den etablerade volymen. 

> [!NOTE] 
> Azure NetApp Files kan distribuera ytterligare IP-adresser för tjänsten efter behov.  DNS-poster kan behöva uppdateras regelbundet.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Kan jag ange eller välja min egen IP-adress för en Azure NetApp Files volym?  

Nej. IP-tilldelningen till Azure NetApp Files volymer är dynamisk. Tilldelning av statisk IP-adress stöds inte. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Stöder Azure NetApp Files Dual Stack (IPv4 och IPv6) VNet?

Nej, Azure NetApp Files stöder för närvarande inte Dual Stack (IPv4 och IPv6) VNet.  
 
## <a name="security-faqs"></a>Vanliga frågor och svar

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Kan nätverks trafiken mellan den virtuella Azure-datorn och lagringen krypteras?

Data trafik mellan NFSv 4.1-klienter och Azure NetApp Files volymer kan krypteras med hjälp av Kerberos med AES-256-kryptering. Mer information finns i [Konfigurera nfsv 4.1 Kerberos-kryptering för Azure NetApp Files](configure-kerberos-encryption.md) .   

Data trafik mellan NFSv3-eller SMB3-klienter till Azure NetApp Files volymer är inte krypterad. Men trafiken från en virtuell Azure-dator (som kör en NFS-eller SMB-klient) till Azure NetApp Files är lika säker som vilken annan Azure-VM-till-VM-trafik som helst. Den här trafiken är lokal i Azure Data Center-nätverket. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Kan lagringen krypteras i vila?

Alla Azure NetApp Files volymer krypteras med hjälp av FIPS 140-2-standarden. Alla nycklar hanteras av Azure NetApp Filess tjänsten. 

### <a name="how-are-encryption-keys-managed"></a>Hur hanteras krypterings nycklar? 

Nyckel hantering för Azure NetApp Files hanteras av tjänsten. En unik data krypterings nyckel för XTS-AES-256 genereras för varje volym. En krypterings nyckel hierarki används för att kryptera och skydda alla volym nycklar. Dessa krypterings nycklar visas aldrig eller rapporteras inte i okrypterat format. Krypterings nycklarna tas bort omedelbart när en volym tas bort.

Stöd för Kundhanterade nycklar (Bring Your Own Key) med hjälp av Azure Dedicated HSM är tillgängligt på ett kontrollerat sätt i USA, södra centrala USA, västra USA 2 och US Gov, Virginia regioner. Du kan begära åtkomst på [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) . När kapaciteten blir tillgänglig kommer begäran att godkännas.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Kan jag konfigurera princip reglerna för NFS-export för att kontrol lera åtkomsten till Azure NetApp Files tjänstens monterings mål?

Ja, du kan konfigurera upp till fem regler i en enda NFS-export princip.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Stöder Azure NetApp Files nätverks säkerhets grupper?

Nej, för närvarande kan du inte använda nätverks säkerhets grupper för det delegerade under nätet för Azure NetApp Files eller nätverks gränssnitten som skapas av tjänsten.

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Kan jag använda Azure RBAC med Azure NetApp Files?

Ja, Azure NetApp Files stöder Azure RBAC-funktioner.

## <a name="performance-faqs"></a>Vanliga frågor och svar om prestanda

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Vad gör jag för att optimera eller finjustera Azure NetApp Files prestanda?

Du kan utföra följande åtgärder enligt prestanda kraven: 
- Kontrol lera att den virtuella datorn har rätt storlek.
- Aktivera accelererat nätverk för den virtuella datorn.
- Välj önskad tjänste nivå och storlek för kapacitets gruppen.
- Skapa en volym med önskad kvot storlek för kapacitet och prestanda.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Hur gör jag för att Convert-baserade service nivåer för Azure NetApp Files till IOPS?

Du kan konvertera MB/s till IOPS med hjälp av följande formel:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Hur gör jag för att ändra service nivån för en volym?

Du kan ändra service nivån för en befintlig volym genom att flytta volymen till en annan kapacitets pool som använder den [tjänste nivå](azure-netapp-files-service-levels.md) som du vill använda för volymen. Se [dynamisk ändring av volymens service nivå](dynamic-change-volume-service-level.md). 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Hur gör jag för att övervaka Azure NetApp Files prestanda?

Azure NetApp Files tillhandahåller volym prestanda mått. Du kan också använda Azure Monitor för att övervaka användnings statistik för Azure NetApp Files.  Se [mått för Azure NetApp Files](azure-netapp-files-metrics.md) för att få en lista över prestanda mått för Azure NetApp Files.

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Vilken prestanda påverkas av Kerberos på NFSv 4.1?

Se [prestanda påverkan för Kerberos på nfsv 4.1](configure-kerberos-encryption.md#kerberos_performance) för information om säkerhets alternativ för nfsv 4.1, prestanda vektorer som testas och den förväntade prestanda påverkan. 

## <a name="nfs-faqs"></a>Vanliga frågor och svar om NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Jag vill att en volym ska monteras automatiskt när en virtuell Azure-dator startas eller startas om.  Hur gör jag för att konfigurerar du min värd för beständiga NFS-volymer?

Om en NFS-volym ska monteras automatiskt vid start eller omstart av virtuella datorer lägger du till en post i `/etc/fstab` filen på värden. 

Mer information finns i [montera eller demontera en volym för virtuella Windows-eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) .  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Varför visar DF-kommandot på NFS-klienten inte den etablerade volym storleken?

Volym storleken som rapporteras i DF är den maximala storlek som Azure NetApp Files volymen kan växa till. Storleken på Azure NetApp Files volymen i DF-kommandot reflekterar inte volymens kvot eller storlek.  Du kan få Azure NetApp Files volym storlek eller kvot genom Azure Portal eller API: et.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Vilken NFS-version stöder Azure NetApp Files?

Azure NetApp Files stöder NFSv3 och NFSv 4.1. Du kan [skapa en volym](azure-netapp-files-create-volumes.md) med hjälp av antingen NFS-versionen. 

### <a name="how-do-i-enable-root-squashing"></a>Hur gör jag för att aktivera rot-squashing?

Du kan ange om rot kontot ska ha åtkomst till volymen eller inte genom att använda volymens export princip. Mer information finns i [Konfigurera export princip för en NFS-volym](azure-netapp-files-configure-export-policy.md) .

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Kan jag använda samma fil Sök väg (token för skapande av volym) för flera volymer?

Ja, det kan du. Fil Sök vägen måste dock användas i antingen en annan prenumeration eller i en annan region.   

Du kan till exempel skapa en volym som heter `vol1` . Och sedan skapar du en annan volym som också kallas `vol1` i en annan pool med kapacitet men i samma prenumeration och region. I det här fallet orsakar det ett fel när du använder samma volym namn `vol1` . Om du vill använda samma fil Sök väg måste namnet vara i en annan region eller prenumeration.

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Varför tar klienten längre tid att söka i mappar och undermappar när jag försöker komma åt NFS-volymer via en Windows-klient?

Kontrol lera att `CaseSensitiveLookup` är aktiverat på Windows-klienten för att snabba upp sökningen av mappar och undermappar:

1. Använd följande PowerShell-kommando för att aktivera CaseSensitiveLookup:   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Montera volymen på Windows-servern.   
    Exempel:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

## <a name="smb-faqs"></a>Vanliga frågor och svar om SMB

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Vilka SMB-versioner stöds av Azure NetApp Files?

Azure NetApp Files stöder SMB 2,1 och SMB 3,1 (som har stöd för SMB 3,0).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Krävs en Active Directory anslutning för SMB-åtkomst? 

Ja, du måste skapa en Active Directory anslutning innan du distribuerar en SMB-volym. De angivna domän kontrol Lanterna måste vara tillgängliga för det delegerade under nätet för Azure NetApp Files för lyckad anslutning.  Mer information finns i [skapa en SMB-volym](./azure-netapp-files-create-volumes-smb.md) . 

### <a name="how-many-active-directory-connections-are-supported"></a>Hur många Active Directory-anslutningar stöds?

Azure NetApp Files har inte stöd för flera Active Directory (AD)-anslutningar i en *region*, även om AD-anslutningarna finns i olika NetApp-konton. Du kan dock ha flera AD-anslutningar i en enda *prenumeration*, förutsatt att AD-anslutningarna finns i olika regioner. Om du behöver flera AD-anslutningar i en enda region kan du använda separata prenumerationer för att göra det. 

En AD-anslutning har kon figurer ATS per NetApp-konto. AD-anslutningen visas bara via det NetApp-konto som den skapas i.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Stöder Azure NetApp Files Azure Active Directory? 

Både [Azure Active Directory (AD) Domain Services](../active-directory-domain-services/overview.md) och [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) stöds. Du kan använda befintliga Active Directory domänkontrollanter med Azure NetApp Files. Domänkontrollanter kan finnas i Azure som virtuella datorer eller lokalt via ExpressRoute eller S2S VPN. Azure NetApp Files stöder inte AD Join för [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) för tillfället.

Om du använder Azure NetApp Files med Azure Active Directory Domain Services är organisationsenhetens sökväg `OU=AADDC Computers` när du konfigurerar Active Directory för ditt NetApp-konto.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Vilka versioner av Windows Server Active Directory stöds?

Azure NetApp Files stöder Windows Server 2008r2SP1-2019-versioner av Active Directory Domain Services.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Varför visar det tillgängliga utrymmet på min SMB-klient inte den etablerade storleken?

Den volym storlek som rapporteras av SMB-klienten är den maximala storlek som Azure NetApp Files volymen kan växa till. Storleken på Azure NetApp Files volymen som det visas på SMB-klienten reflekterar inte volymens kvot eller storlek. Du kan få Azure NetApp Files volym storlek eller kvot genom Azure Portal eller API: et.

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Jag har problem med att ansluta till min SMB-resurs. Vad ska jag göra?

Vi rekommenderar att du anger den maximala toleransen för synkronisering av dator klockor till fem minuter. Mer information finns i [Max tolerans för synkronisering av dator klockor](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)). 

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Vanliga frågor och svar om kapacitets hantering

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Vill du Hur gör jag för att övervaka användning för kapacitets gruppen och volymen av Azure NetApp Files? 

Azure NetApp Files tillhandahåller statistik för kapacitets bassäng och volym användning. Du kan också använda Azure Monitor för att övervaka användningen av Azure NetApp Files. Mer information finns i [mått för Azure NetApp Files](azure-netapp-files-metrics.md) . 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kan jag hantera Azure NetApp Files via Azure Storage Explorer?

Nej. Azure NetApp Files stöds inte av Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Hur gör jag för att avgöra om en katalog närmar sig gräns storleken?

Du kan använda `stat` kommandot från en klient för att se om en katalog närmar sig den maximala storleks gränsen för katalogens metadata (320 MB).

För en katalog på 320 MB är antalet block 655360, med varje block storlek 512 byte.  (Det vill säga 320x1024x1024/512.)  

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
Azure NetApp Files tillhandahåller NFS-och SMB-volymer.  Du kan använda valfritt filbaserat kopierings verktyg för att migrera data till tjänsten. 

NetApp erbjuder en SaaS lösning, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Med lösningen kan du replikera NFS-eller SMB-data till Azure NetApp Files NFS-exporter eller SMB-resurser. 

Du kan också använda en bred uppsättning kostnads fria verktyg för att kopiera data. För NFS kan du använda arbets belastnings verktyg som [rsync](https://rsync.samba.org/examples.html) för att kopiera och synkronisera käll data till en Azure NetApp Files volym. För SMB kan du använda arbets belastningar [Robocopy](/windows-server/administration/windows-commands/robocopy) på samma sätt.  Dessa verktyg kan också replikera fil-eller mappbehörigheter. 

Kraven för datamigrering från lokalt till Azure NetApp Files är följande: 

- Se till att Azure NetApp Files är tillgängligt i Azures mål region.
- Verifiera nätverks anslutningen mellan källan och Azure NetApp Files mål volymens IP-adress. Data överföring mellan lokalt och tjänsten Azure NetApp Files stöds över ExpressRoute.
- Skapa mål Azure NetApp Files volym.
- Överför källdata till mål volymen med hjälp av det önskade fil kopierings verktyget.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Hur gör jag för att skapa en kopia av en Azure NetApp Files volym i en annan Azure-region?
    
Azure NetApp Files tillhandahåller NFS-och SMB-volymer.  Alla filbaserade kopierings verktyg kan användas för att replikera data mellan Azure-regioner. 

NetApp erbjuder en SaaS-baserad lösning, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Med lösningen kan du replikera NFS-eller SMB-data till Azure NetApp Files NFS-exporter eller SMB-resurser. 

Du kan också använda en bred uppsättning kostnads fria verktyg för att kopiera data. För NFS kan du använda arbets belastnings verktyg som [rsync](https://rsync.samba.org/examples.html) för att kopiera och synkronisera käll data till en Azure NetApp Files volym. För SMB kan du använda arbets belastningar [Robocopy](/windows-server/administration/windows-commands/robocopy) på samma sätt.  Dessa verktyg kan också replikera fil-eller mappbehörigheter. 

Kraven för att replikera en Azure NetApp Files volym till en annan Azure-region är följande: 
- Se till att Azure NetApp Files är tillgängligt i Azures mål region.
- Verifiera nätverks anslutningen mellan virtuella nätverk i varje region. Global peering mellan virtuella nätverk stöds för närvarande inte.  Du kan upprätta anslutningar mellan virtuella nätverk genom att länka till en ExpressRoute-krets eller använda en S2S VPN-anslutning. 
- Skapa mål Azure NetApp Files volym.
- Överför källdata till mål volymen med hjälp av det önskade fil kopierings verktyget.

### <a name="is-migration-with-azure-data-box-supported"></a>Finns det stöd för migrering med Azure Data Box?

Nej. Azure Data Box stöder för närvarande inte Azure NetApp Files. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Stöds migrering med Azure import/export-tjänsten?

Nej. Azure import/export-tjänsten stöder inte Azure NetApp Files för närvarande.

## <a name="product-faqs"></a>Vanliga frågor om produkter

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Kan jag använda Azure NetApp Files NFS eller SMB-volymer med Azure VMware-lösningen (AVS)?

Du kan montera Azure NetApp Files NFS-volymer på virtuella Windows-datorer eller virtuella Linux-datorer i AVS. Du kan mappa Azure NetApp Files SMB-resurser i AVS-virtuella Windows-datorer. Mer information finns i [Azure NetApp Files med Azure VMware-lösningen]( ../azure-vmware/net-app-files-with-azure-vmware-solution.md).  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Vilka regioner stöds för att använda Azure NetApp Files NFS-eller SMB-volymer med Azure VMware-lösningen (AVS)?

Användning av Azure NetApp Files NFS-eller SMB-volymer med AVS stöds i följande regioner – östra USA, västra USA, västra Europa och östra Australien.

## <a name="next-steps"></a>Nästa steg  

- [Microsoft Azure ExpressRoute vanliga frågor](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Virtual Network vanliga frågor och svar](../virtual-network/virtual-networks-faq.md)
- [Skapa en supportbegäran för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Vanliga frågor och svar om SMB-prestanda för Azure NetApp Files](azure-netapp-files-smb-performance.md)
