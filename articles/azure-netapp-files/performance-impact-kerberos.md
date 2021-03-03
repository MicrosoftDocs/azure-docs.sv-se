---
title: Prestanda påverkan för Kerberos på Azure NetApp Files NFSv 4.1-volymer | Microsoft Docs
description: Beskriver de tillgängliga säkerhets alternativen, de testade prestanda vektorerna och den förväntade prestanda effekten hos Kerberos på Azure NetApp Files NFSv 4.1-volymer.
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
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: e9054f11c8f55e9fe00266840a9f6e257f14e659
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747212"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>Prestanda påverkan för Kerberos på Azure NetApp Files NFSv 4.1-volymer

Azure NetApp Files stöder [NFS-klient kryptering i Kerberos](configure-kerberos-encryption.md) -lägen (krb5, krb5i och krb5p) med AES-256-kryptering. Den här artikeln beskriver prestanda påverkan för Kerberos på NFSv 4.1-volymer. 

## <a name="available-security-options"></a>Tillgängliga säkerhets alternativ 

De säkerhets alternativ som för närvarande är tillgängliga för NFSv 4.1-volymer är följande: 

* **SEC = sys** använder lokala UNIX-UID och GIDs med hjälp av AUTH_SYS för att autentisera NFS-åtgärder.
* **SEC = krb5** använder Kerberos V5 i stället för lokala UNIX-UID och GIDs för att autentisera användare.
* **SEC = krb5i** använder Kerberos V5 för användarautentisering och utför integritets kontroll av NFS-åtgärder med hjälp av säkra kontroll summor för att förhindra manipulering av data.
* **SEC = krb5p** använder Kerberos V5 för användarautentisering och integritets kontroll. Den krypterar NFS-trafik för att förhindra trafik avlyssning. Det här alternativet är den säkraste inställningen, men det innebär också högsta prestanda.

## <a name="performance-vectors-tested"></a>Prestanda vektorer har testats

I det här avsnittet beskrivs den enda prestanda påverkan på klient sidan för de olika `sec=*` alternativen.

* Prestanda påverkan har testats på två nivåer: låg concurrency (låg belastning) och hög samtidighet (övre gräns för I/O och data flöde).  
* Tre typer av arbets belastningar har testats:  
    * Liten åtgärd slumpmässig läsning/skrivning (med FIO)
    * Sekventiell läsning/skrivning av stor åtgärd (med FIO)
    * Metadata, tungt arbets belastning som genereras av program som git

## <a name="expected-performance-impact"></a>Förväntad prestanda påverkan 

Det finns två fokus områden: låg belastning och övre gräns. I följande listor beskrivs säkerhets inställningen prestanda påverkan per säkerhets inställning och scenario efter scenario. Alla jämförelser görs mot `sec=sys` säkerhets parametern. Testet utfördes på en enda volym med hjälp av en enda klient. 

Prestanda påverkan för krb5:

* Låg samtidighet (r/w):
    * En sekventiell fördröjning ökade 0,3 MS.
    * Slumpmässig I/O-latens ökade 0,2 MS.
    * I/O-latens för metadata ökade 0,2 MS.
* Hög samtidighet (r/w): 
    * Högsta sekventiella data flöde har påverkats av krb5.
    * Högsta slumpmässiga I/O minskas med 30% för rena Läs arbets belastningar med den totala påverkan som släpps på noll när arbets belastningen flyttas till ren skrivning. 
    * Maximal arbets belastning för metadata minskade 30%.

Prestanda påverkan för krb5i: 

* Låg samtidighet (r/w):
    * En sekventiell fördröjning ökade 0,5 ms.
    * Slumpmässig I/O-latens ökade 0,2 MS.
    * I/O-latens för metadata ökade 0,2 MS.
* Hög samtidighet (r/w): 
    * Det maximala sekventiella data flödet minskade med 70% övergripande oberoende av arbets belastnings blandningen.
    * Högsta slumpmässiga I/O minskade med 50% för rena Läs arbets belastningar med den totala påverkan som minskar till 25% när arbets belastningen flyttas till ren skrivning. 
    * Maximal arbets belastning för metadata minskade 30%.

Prestanda påverkan för krb5p:

* Låg samtidighet (r/w):
    * En sekventiell fördröjning ökade 0,8 ms.
    * Slumpmässig I/O-latens ökade 0,2 MS.
    * I/O-latens för metadata ökade 0,2 MS.
* Hög samtidighet (r/w): 
    * Det maximala sekventiella data flödet minskade med 85% övergripande oberoende av arbets belastnings blandningen. 
    * Högsta slumpmässiga I/O minskade med 65% för rena Läs arbets belastningar med den totala påverkan som minskar till 43% när arbets belastningen flyttas till ren skrivning. 
    * Maximal arbets belastning för metadata minskade 30%.

## <a name="next-steps"></a>Nästa steg  

* [Konfigurera NFSv4.1 Kerberos-kryptering för Azure NetApp Files](configure-kerberos-encryption.md) 
