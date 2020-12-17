---
title: Vad är nytt i Azure NetApp Files | Microsoft Docs
description: Innehåller en översikt över de senaste nya funktionerna och förbättringarna av Azure NetApp Files.
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
ms.date: 12/04/2020
ms.author: b-juche
ms.openlocfilehash: bba3dce2a2a18888cb88f4cf8b33cd48d6a4cd69
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631167"
---
# <a name="whats-new-in-azure-netapp-files"></a>Vad är nytt i Azure NetApp Files

Azure NetApp Files uppdateras regelbundet. Den här artikeln innehåller en översikt över de senaste nya funktionerna och förbättringarna. 

## <a name="december-2020"></a>December 2020

* [Azure Application enhetligt ögonblicks bild verktyget](azacsnap-introduction.md) (offentlig för hands version)    

    Azure Application enhetligt ögonblicks bild verktyget (AzAcSnap) är ett kommando rads verktyg som gör det möjligt att förenkla data skyddet för databaser från tredje part (SAP HANA) i Linux-miljöer (till exempel SUSE och RHEL).   

    AzAcSnap utnyttjar volym ögonblicks bilder och Replikerings funktioner i Azure NetApp Files och Azure stor instans. Det ger följande fördelar:

    * Programkonsekvent data skydd 
    * Databas katalog hantering 
    * *Ad hoc* -volymens skydd 
    * Kloning av lagrings volymer 
    * Stöd för haveri beredskap 

## <a name="november-2020"></a>November 2020

* [Återställ ögonblicks bild](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    Med funktionen för återställning av ögonblicks bilder kan du snabbt återställa en volym till det tillstånd den var i när en viss ögonblicks bild togs. I de flesta fall går det mycket snabbare att återställa en volym än att återställa enskilda filer från en ögonblicks bild till det aktiva fil systemet. Det är också mer utrymmes effektivt jämfört med att återställa en ögonblicks bild till en ny volym.

## <a name="september-2020"></a>September 2020

* [Azure NetApp Files replikering över flera regioner](cross-region-replication-introduction.md) (offentlig för hands version)

  Azure NetApp Files stöder nu replikering över flera regioner. Med den här nya funktionen för haveri beredskap kan du replikera dina Azure NetApp Files volymer från en Azure-region till en annan på ett snabbt och kostnads effektivt sätt och skydda dina data från oförutsedda regionala haverier. Azure NetApp Files replikering mellan regioner utnyttjar NetApp SnapMirror® Technology; endast ändrade block skickas över nätverket i ett komprimerat, effektivt format. Den här tillverkarspecifika tekniken minimerar mängden data som krävs för att replikera över regionerna, och därför sparar kostnader för data överföring. Den förkortar också replikerings tiden, så att du kan få ett mindre återställnings punkt mål (återställnings punkt mål).

* [Manuell pool för QoS-kapacitet](manual-qos-capacity-pool-introduction.md) (för hands version)  

    I en manuell pool för QoS-kapacitet kan du tilldela kapaciteten och data flödet för en volym oberoende av varandra. Det totala data flödet för alla volymer som skapats med en manuell QoS-kapacitet begränsas av poolens totala genomflöde. Det bestäms av kombinationen av poolens storlek och data flödet på tjänst nivå. Alternativt kan en Pools [QoS-typ](azure-netapp-files-understand-storage-hierarchy.md#qos_types) vara automatisk (automatisk), vilket är standardvärdet. I en pool med automatisk QoS-kapacitet tilldelas data flöde automatiskt till volymerna i poolen, proportionella mot storleks kvoten som tilldelats volymerna.

* [LDAP-signering](azure-netapp-files-create-volumes-smb.md) (för hands version)   

    Azure NetApp Files stöder nu LDAP-signering för säkra LDAP-sökningar mellan Azure NetApp Files-tjänsten och de användardefinierade Active Directory Domain Services domän kontrol Lanterna. Den här funktionen finns för närvarande som en förhandsversion.

* [AES-kryptering för AD-autentisering](azure-netapp-files-create-volumes-smb.md) (för hands version)

    Azure NetApp Files stöder nu AES-kryptering på LDAP-anslutning till DOMÄNKONTROLLANT för att aktivera AES-kryptering för en SMB-volym. Den här funktionen finns för närvarande som en förhandsversion. 

* Nya [mått](azure-netapp-files-metrics.md):   

    * Nya volym mått: 
        * *Storlek på allokerad volym: den allokerade* storleken på en volym
    * Mått för nya pooler: 
        * *Allokerad storlek för pool*: poolens allokerade storlek 
        * *Total ögonblicks bild storlek för poolen*: summan av ögonblicks bildernas storlek från alla volymer i poolen

## <a name="july-2020"></a>Juli 2020

* [NFSv3-och SMB-volym (Dual-Protocol)](create-volumes-dual-protocol.md)

    Nu kan du skapa en Azure NetApp Files volym som tillåter samtidig åtkomst med dubbla protokoll (NFS v3 och SMB) med stöd för mappning av LDAP-användare. Den här funktionen möjliggör användnings fall där du kan ha en Linux-baserad arbets belastning som genererar och lagrar data i en Azure NetApp Files volym. På samma gång måste personalen använda Windows-baserade klienter och program vara för att analysera de nyligen genererade data från samma Azure NetApp Files volym. Med funktionen samtidig åtkomst med dubbla protokoll raderas behovet av att kopiera data för arbets belastnings data till en separat volym med ett annat protokoll för efter analys, sparande av lagrings kostnad och drift tid. Den här funktionen är kostnads fri (normal [Azure NetApp Files lagrings kostnad](https://azure.microsoft.com/pricing/details/netapp/) gäller fortfarande) och är allmänt tillgänglig. Läs mer i [dokumentationen för samtidig åtkomst till dubbla protokoll](create-volumes-dual-protocol.MD).

* [NFS v 4.1 Kerberos-kryptering under överföring](configure-kerberos-encryption.MD)

    Azure NetApp Files stöder nu NFS-klient kryptering i Kerberos-lägen (krb5, krb5i och krb5p) med AES-256-kryptering, vilket ger dig ytterligare data säkerhet. Den här funktionen är kostnads fri (normal [Azure NetApp Files lagrings kostnad](https://azure.microsoft.com/pricing/details/netapp/) gäller fortfarande) och är allmänt tillgänglig. Läs mer i [dokumentationen för Kerberos-kryptering i NFS v 4.1](configure-kerberos-encryption.MD).

* [Ändring av service nivå för dynamisk volym](dynamic-change-volume-service-level.MD)

    Cloud lovar flexibilitet i IT-utgifterna. Du kan nu ändra service nivån för en befintlig Azure NetApp Files volym genom att flytta volymen till en annan kapacitets pool som använder den tjänste nivå som du vill använda för volymen. Den här ändringar på plats på service nivå för volymen kräver inte att du migrerar data. Det påverkar inte heller data planets åtkomst till volymen. Du kan ändra en befintlig volym för att använda en högre service nivå för bättre prestanda eller för att använda en lägre service nivå för kostnads optimering. Den här funktionen är kostnads fri (normal [Azure NetApp Files lagrings kostnad](https://azure.microsoft.com/pricing/details/netapp/) gäller fortfarande) och är för närvarande en offentlig för hands version. Du kan registrera dig för för hands versionen av funktionen genom att följa den [dynamiska volymens ändrings dokumentation på tjänst nivå](dynamic-change-volume-service-level.md).

* [Princip för ögonblicks bild av volym](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (för hands version) 

    Med Azure NetApp Files kan du skapa tidpunkts ögonblicks bilder av dina volymer. Nu kan du skapa en ögonblicks bild princip om du vill att Azure NetApp Files automatiskt ska skapa ögonblicks bilder av volymen enligt valfri frekvens. Du kan schemalägga att ögonblicks bilderna ska tas i varje timme, varje dag, varje vecka eller månads vis. Du kan också ange det maximala antal ögonblicks bilder som ska sparas som en del av ögonblicks bild principen. Den här funktionen är kostnads fri (normal [Azure NetApp Files lagrings kostnad](https://azure.microsoft.com/pricing/details/netapp/) gäller fortfarande) och är för närvarande en för hands version. Du kan registrera dig för för hands versionen av funktionen genom att följa [dokumentationen för volym ögonblicks bilds principen](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

* [Export princip för NFS-rot åtkomst](azure-netapp-files-configure-export-policy.md)

    Med Azure NetApp Files kan du nu ange om rot kontot ska ha åtkomst till volymen. 

* [Dölj ögonblicks bilds Sök väg](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Med Azure NetApp Files kan du nu ange om en användare ska kunna se och komma åt `.snapshot` katalogen (NFS-klienter) eller `~snapshot` mappen (SMB-klienter) på en monterad volym.

## <a name="may-2020"></a>Maj 2020

* [Användare av säkerhets kopierings princip](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection) (förhands granskning)

    Med Azure NetApp Files kan du inkludera ytterligare konton som kräver förhöjd behörighet för det dator konto som skapats för användning med Azure NetApp Files. De angivna kontona kommer att kunna ändra NTFS-behörigheter på fil-eller mappnivå. Du kan till exempel ange ett icke-privilegierat tjänst konto som används för att migrera data till en SMB-filresurs i Azure NetApp Files. Funktionen säkerhets kopierings princip användare är för närvarande en för hands version.

## <a name="next-steps"></a>Nästa steg
* [Vad är Azure NetApp Files](azure-netapp-files-introduction.md)
* [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 
