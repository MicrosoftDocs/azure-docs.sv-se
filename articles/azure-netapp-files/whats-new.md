---
title: Vad är nytt i Azure NetApp Files | Microsoft Docs
description: Innehåller en sammanfattning av de senaste nya funktionerna och förbättringarna av Azure NetApp Files.
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
ms.topic: overview
ms.date: 04/21/2021
ms.author: b-juche
ms.openlocfilehash: fa028d8fffd2a4097b5bf7d7326d355ae56aebd7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862821"
---
# <a name="whats-new-in-azure-netapp-files"></a>Vad är nytt i Azure NetApp Files

Azure NetApp Files uppdateras regelbundet. Den här artikeln innehåller en sammanfattning av de senaste nya funktionerna och förbättringarna. 

## <a name="april-2021"></a>April 2021

* [SMB-kontinuerlig tillgänglighet (CA) delar stöd för FSLogix-användarprofilcontainrar](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (förhandsversion)  

    [FSLogix](/fslogix/overview) är en uppsättning lösningar som förbättrar, aktiverar och förenklar icke-beständiga Windows-beräkningsmiljöer. FSLogix-lösningar är lämpliga för virtuella miljöer i både offentliga och privata moln. FSLogix-lösningar kan också användas för att skapa fler portabla beräkningssessioner när du använder fysiska enheter. FSLogix kan användas för att ge dynamisk åtkomst till beständiga användarprofilcontainrar som lagras på SMB-delad nätverkslagring, inklusive Azure NetApp Files. För att ytterligare förbättra FSLogix motståndskraft mot underhållshändelser för lagringstjänst har Azure NetApp Files utökat stöd för SMB Transparent redundans via [SMB-resurser för](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) kontinuerlig tillgänglighet (CA) på Azure NetApp Files för användarprofilcontainrar. Mer information Azure NetApp Files [Windows Virtual Desktop finns](azure-netapp-files-solution-architectures.md#windows-virtual-desktop) i Azure NetApp Files Windows Virtual Desktop lösningar.  

* [SMB3-protokollkryptering](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (förhandsversion) 

    Du kan nu aktivera SMB3-protokollkryptering på Azure NetApp Files SMB- och dual-protocol-volymer. Den här funktionen möjliggör kryptering för aktuella SMB3-data med hjälp av [AES-CCM-algoritmen på SMB 3.0 och AES-GCM-algoritmen på SMB 3.1.1-anslutningar.](/windows-server/storage/file-server/file-server-smb-overview#features-added-in-smb-311-with-windows-server-2016-and-windows-10-version-1607) SMB-klienter som inte använder SMB3-kryptering kan inte komma åt den här volymen. Vilodata krypteras oavsett den här inställningen. SMB-kryptering förbättrar säkerheten ytterligare. Det kan dock påverka klienten (PROCESSORkostnader för kryptering och dekryptering av meddelanden). Det kan också påverka användningen av lagringsresurser (minskningar av dataflödet). Du bör testa krypteringsprestandapåverkan mot dina program innan du distribuerar arbetsbelastningar till produktion.

* [Active Directory Domain Services (ADDS) LDAP-användarmappning med utökade NFS-grupper](configure-ldap-extended-groups.md) (förhandsversion)   

    Som standard stöder Azure NetApp Files upp till 16 grupp-ID:n vid hantering av NFS-autentiseringsuppgifter, enligt definitionen i [RFC 5531](https://tools.ietf.org/html/rfc5531). Med den här nya funktionen kan du nu öka maxvärdet till 1 024 om du har användare som är medlemmar i fler än standardantalet grupper. För att stödja den här funktionen kan NFS-volymer nu också läggas till i ADDS LDAP, vilket gör att Active Directory LDAP-användare med utökade gruppposter (med upp till 1 024 grupper) kan komma åt volymen. 

## <a name="march-2021"></a>Mars 2021
 
* [SMB-resurser för kontinuerlig tillgänglighet (CA)](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (förhandsversion)  

    SMB transparent redundans möjliggör underhållsåtgärder på Azure NetApp Files-tjänsten utan att avbryta anslutningen till serverprogram som lagrar och kommer åt data på SMB-volymer. För att stödja SMB transparent redundans Azure NetApp Files stöder nu alternativet SMB-resurser för kontinuerlig tillgänglighet för användning med SQL Server-program över SMB som körs på virtuella Azure-datorer. Den här funktionen stöds för närvarande i Windows SQL Server. Linux SQL Server stöds inte för närvarande. Aktivering av den här funktionen ger betydande SQL Server prestandaförbättringar och skalnings- och kostnadsfördelar för enskild [instans, Always-On-redundansklusterinstans och Always-On-distributioner av tillgänglighetsgrupp.](azure-netapp-files-solution-architectures.md#sql-server) Se [Fördelar med att använda Azure NetApp Files för SQL Server distribution.](solutions-benefits-azure-netapp-files-sql-server.md)

* [Automatisk storleksändring av en målvolym för replikering mellan regioner](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    I en replikeringsrelation mellan regioner ändras storleken på målvolymen automatiskt baserat på källvolymens storlek. Därför behöver du inte ändra storlek på målvolymen separat. Det här beteendet för automatisk storleksändring gäller när volymerna finns i en aktiv replikeringsrelation eller när replikerings-peering bryts med omsynkroniseringsåtgärden. För att den här funktionen ska fungera måste du se till att det finns tillräckligt med utrymme i kapacitetspoolerna för både käll- och målvolymerna.

## <a name="december-2020"></a>December 2020

* [Azure Application konsekvent ögonblicksbild (förhandsversion)](azacsnap-introduction.md)    

    Azure Application Consistent Snapshot Tool (AzAcSnap) är ett kommandoradsverktyg som gör att du kan förenkla dataskyddet för databaser från tredje part (SAP HANA) i Linux-miljöer (till exempel SUSE och RHEL).   

    AzAcSnap utnyttjar funktionerna för ögonblicksbilder och replikering av volymer i Azure NetApp Files och Azure Large Instance. Det ger följande fördelar:

    * Program konsekvent dataskydd 
    * Hantering av databaskatalog 
    * *Ad hoc-volymskydd* 
    * Kloning av lagringsvolymer 
    * Stöd för haveriberedskap 

## <a name="november-2020"></a>November 2020

* [Återställning av ögonblicksbild](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    Med funktionen för återställning av ögonblicksbilder kan du snabbt återställa en volym till det tillstånd som den var i när en viss ögonblicksbild togs. I de flesta fall går det mycket snabbare att återställa en volym än att återställa enskilda filer från en ögonblicksbild till det aktiva filsystemet. Det är också mer utrymmeseffektivt jämfört med att återställa en ögonblicksbild till en ny volym.

## <a name="september-2020"></a>September 2020

* [Azure NetApp Files replikering mellan regioner](cross-region-replication-introduction.md) (förhandsversion)

  Azure NetApp Files stöder nu replikering mellan regioner. Med den här nya funktionen för haveriberedskap kan du replikera dina Azure NetApp Files-volymer från en Azure-region till en annan på ett snabbt och kostnadseffektivt sätt, vilket skyddar dina data från oförutsedda regionala fel. Azure NetApp Files replikering mellan regioner utnyttjar NetApp SnapMirror®teknik; endast ändrade block skickas via nätverket i ett komprimerat, effektivt format. Den här egenutvecklade tekniken minimerar mängden data som krävs för att replikera mellan regionerna, vilket sparar kostnader för dataöverföring. Det förkortar också replikeringstiden, så att du kan uppnå ett mindre mål för återställningspunkt (RPO).

* [Manuell QoS-kapacitetspool](manual-qos-capacity-pool-introduction.md) (förhandsversion)  

    I en manuell QoS-kapacitetspool kan du tilldela kapacitet och dataflöde för en volym oberoende av varandra. Det totala dataflödet för alla volymer som skapats med en manuell QoS-kapacitetspool begränsas av poolens totala dataflöde. Den bestäms av kombinationen av poolstorleken och dataflödet på tjänstnivå. Alternativt kan [QoS-typen för en kapacitetspool](azure-netapp-files-understand-storage-hierarchy.md#qos_types) vara automatisk ( automatisk), vilket är standard. I en automatisk QoS-kapacitetspool tilldelas dataflödet automatiskt till volymerna i poolen, proportionellt mot storlekskvoten som tilldelats volymerna.

* [LDAP-signering](azure-netapp-files-create-volumes-smb.md) (förhandsversion)   

    Azure NetApp Files stöder nu LDAP-signering för säkra LDAP-uppslag mellan Azure NetApp Files-tjänsten och Active Directory Domain Services-domänkontrollanterna. Den här funktionen finns för närvarande som en förhandsversion.

* [AES-kryptering för AD-autentisering](azure-netapp-files-create-volumes-smb.md) (förhandsversion)

    Azure NetApp Files stöder nu AES-kryptering på LDAP-anslutning till domänkontrollant för att aktivera AES-kryptering för en SMB-volym. Den här funktionen finns för närvarande som en förhandsversion. 

* Nya [mått:](azure-netapp-files-metrics.md)   

    * Nya volymmått: 
        * *Allokerad volymstorlek:* Den etablerade storleken på en volym
    * Nya poolmått: 
        * *Allokerad storlek* för pool: Poolens etablerade storlek 
        * *Total ögonblicksbildstorlek för poolen:* Summan av ögonblicksbildstorleken från alla volymer i poolen

## <a name="july-2020"></a>Juli 2020

* [Volym med dubbla protokoll (NFSv3 och SMB)](create-volumes-dual-protocol.md)

    Nu kan du skapa en Azure NetApp Files-volym som tillåter samtidig åtkomst med dubbla protokoll (NFS v3 och SMB) med stöd för LDAP-användarmappning. Den här funktionen möjliggör användningsfall där du kan ha en Linux-baserad arbetsbelastning som genererar och lagrar data i en Azure NetApp Files volym. Samtidigt måste personalen använda Windows-baserade klienter och programvara för att analysera nyligen genererade data från samma Azure NetApp Files volym. Funktionen samtidig åtkomst med dubbla protokoll gör att du inte behöver kopiera arbetsbelastningsgenererade data till en separat volym med ett annat protokoll för efteranalys, vilket sparar lagringskostnad och drifttid. Den här funktionen är kostnadsfri (normalt Azure NetApp Files [fortfarande](https://azure.microsoft.com/pricing/details/netapp/) gäller) och är allmänt tillgänglig. Läs mer i dokumentationen [om samtidig åtkomst med dubbla protokoll.](create-volumes-dual-protocol.MD)

* [NFS v4.1 Kerberos-kryptering under överföring](configure-kerberos-encryption.MD)

    Azure NetApp Files stöder nu NFS-klientkryptering i Kerberos-lägen (krb5, krb5i och krb5p) med AES-256-kryptering, vilket ger ytterligare datasäkerhet. Den här funktionen är kostnadsfri (normalt Azure NetApp Files [fortfarande gäller)](https://azure.microsoft.com/pricing/details/netapp/) och är allmänt tillgänglig. Läs mer i [dokumentationen om Kerberos-kryptering i NFS v4.1.](configure-kerberos-encryption.MD)

* [Ändring av dynamisk volymtjänstnivå](dynamic-change-volume-service-level.MD)

    Molnet utlovar flexibilitet i IT-utgifter. Du kan nu ändra servicenivån för en befintlig Azure NetApp Files volym genom att flytta volymen till en annan kapacitetspool som använder den servicenivå som du vill ha för volymen. Den här ändringen på servicenivå på plats för volymen kräver inte att du migrerar data. Det påverkar inte heller dataplanets åtkomst till volymen. Du kan ändra en befintlig volym om du vill använda en högre servicenivå för bättre prestanda eller använda en lägre servicenivå för kostnadsoptimering. Den här funktionen är kostnadsfri (normal Azure NetApp Files [fortfarande](https://azure.microsoft.com/pricing/details/netapp/) gäller) och är för närvarande i offentlig förhandsversion. Du kan registrera dig för funktionsförhandsvisningen genom att följa [dokumentationen för dynamisk volymändring på tjänstnivå.](dynamic-change-volume-service-level.md)

* [Princip för volymögonblicksbild](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (förhandsversion) 

    Azure NetApp Files kan du skapa ögonblicksbilder av dina volymer vid en tidpunkt. Nu kan du skapa en ögonblicksbildsprincip för att Azure NetApp Files skapa ögonblicksbilder av volymer automatiskt med valfri frekvens. Du kan schemalägga ögonblicksbilderna så att de tas i tim-, dags-, vecko- eller månadscykler. Du kan också ange det maximala antalet ögonblicksbilder som ska behållas som en del av ögonblicksbildsprincipen. Den här funktionen är kostnadsfri (normal [Azure NetApp Files fortfarande](https://azure.microsoft.com/pricing/details/netapp/) gäller) och är för närvarande i förhandsversion. Du kan registrera dig för funktionsförhandsvisningen genom att följa [principdokumentationen för ögonblicksbilder av volymer.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

* [Exportprincip för NFS-rotåtkomst](azure-netapp-files-configure-export-policy.md)

    Azure NetApp Files nu kan du ange om rotkontot kan komma åt volymen. 

* [Dölj sökväg för ögonblicksbilder](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Azure NetApp Files nu kan du ange om en användare kan se och komma åt katalogen `.snapshot` (NFS-klienter) eller mappen `~snapshot` (SMB-klienter) på en monterad volym.

## <a name="may-2020"></a>Maj 2020

* [Användare av säkerhetskopieringspolicy](create-active-directory-connections.md) (förhandsversion)

    Azure NetApp Files kan du inkludera ytterligare konton som kräver utökade privilegier till datorkontot som skapats för användning med Azure NetApp Files. De angivna kontona kommer att kunna ändra NTFS-behörigheterna på fil- eller mappnivå. Du kan till exempel ange ett icke-privilegierat tjänstkonto som används för att migrera data till en SMB-filresurs i Azure NetApp Files. Funktionen Användare av säkerhetskopieringspolicy finns för närvarande i förhandsversion.

## <a name="next-steps"></a>Nästa steg
* [Vad är Azure NetApp Files](azure-netapp-files-introduction.md)
* [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 
