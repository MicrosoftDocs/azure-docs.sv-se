---
title: Konfigurera NFSv 4.1 Kerberos-kryptering för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du konfigurerar NFSv 4.1 Kerberos-kryptering för Azure NetApp Files och prestanda påverkan.
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
ms.topic: how-to
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: 69168060cbce4a904c53d7f79895e909c8c42e01
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935231"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Konfigurera NFSv 4.1 Kerberos-kryptering för Azure NetApp Files

Azure NetApp Files stöder NFS-klient kryptering i Kerberos-lägen (krb5, krb5i och krb5p) med AES-256-kryptering. I den här artikeln beskrivs de konfigurationer som krävs för att använda en NFSv 4.1-volym med Kerberos-kryptering.

## <a name="requirements"></a>Krav

Följande krav gäller för NFSv 4.1-klient kryptering: 

* Active Directory Domain Services (AD DS)-anslutning för att under lätta Kerberos-biljetter 
* Skapa en/PTR-post för både klient-och Azure NetApp Files NFS-serverns IP-adresser
* En Linux-klient  
    Den här artikeln innehåller rikt linjer för RHEL-och Ubuntu-klienter.  Andra klienter kommer att arbeta med liknande konfigurations steg. 
* Åtkomst till NTP-server  
    Du kan använda en av de ofta använda domän kontrol Lanterna för Active Directory-domän domänkontrollanten (AD DC).

## <a name="create-an-nfs-kerberos-volume"></a>Skapa en NFS Kerberos-volym

1.  Följ stegen i [skapa en NFS-volym för Azure NetApp Files](azure-netapp-files-create-volumes.md) för att skapa nfsv 4.1-volymen.   

    På sidan Skapa en volym anger du NFS-versionen till **nfsv 4.1** och ställer in Kerberos på **aktive rad**.

    > [!IMPORTANT] 
    > Du kan inte ändra val av Kerberos-aktivering när volymen har skapats.

    ![Skapa NFSv 4.1 Kerberos-volym](../media/azure-netapp-files/create-kerberos-volume.png)  

2. Välj **Exportera princip** för att matcha den önskade nivån av åtkomst-och säkerhets alternativ (Kerberos 5, Kerberos-5i eller Kerberos-5P) för volymen.   

    För prestanda påverkan av Kerberos, se [prestanda påverkan av Kerberos på nfsv 4.1](#kerberos_performance).  

    Du kan också ändra Kerberos-säkerhetsmetoderna för volymen genom att klicka på Exportera princip i Azure NetApp Files navigerings fönstret.

3.  Klicka på **Granska + skapa** för att skapa nfsv 4.1-volymen.

## <a name="configure-the-azure-portal"></a>Konfigurera Azure Portal 

1.  Följ anvisningarna i [skapa en Active Directory-anslutning](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection).  

    Kerberos kräver att du skapar minst ett dator konto i Active Directory. Den konto information som du anger används för att skapa konton för både SMB- *och* Nfsv 4.1 Kerberos-volymer. Den här datorn skapas automatiskt när volymen skapas.

2.  Under **Kerberos-sfär** anger du **namnet på AD-servern** och IP-adressen för **KDC** .

    AD server och KDC IP kan vara samma server. Den här informationen används för att skapa SPN-dator kontot som används av Azure NetApp Files. När dator kontot har skapats kommer Azure NetApp Files använda DNS-servertjänsten för att hitta ytterligare KDC-servrar efter behov. 

    ![Kerberos-sfär](../media/azure-netapp-files/kerberos-realm.png)
 
3.  Klicka på **Anslut** för att spara konfigurationen.

## <a name="configure-active-directory-connection"></a>Konfigurera Active Directory anslutning 

Konfiguration av NFSv 4.1 Kerberos skapar två dator konton i Active Directory:
* Ett dator konto för SMB-resurser
* Ett dator konto för NFSv 4.1 – du kan identifiera det här kontot med hjälp av prefixet `NFS-` . 

När du har skapat den första NFSv 4.1 Kerberos-volymen anger du krypterings typ för dator kontot med hjälp av följande PowerShell-kommando:

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>Konfigurera NFS-klienten 

Följ anvisningarna i [Konfigurera en NFS-klient för att Azure NetApp Files](configure-nfs-clients.md) konfigurera NFS-klienten.  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>Montera NFS-Kerberos-volymen

1. På sidan **volymer** väljer du den NFS-volym som du vill montera.

2. Välj **monterings instruktioner** från volymen för att Visa anvisningarna.

    Ett exempel: 

    ![Monterings instruktioner för Kerberos-volymer](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. Skapa katalogen (monterings punkt) för den nya volymen.  

4. Ange standard krypterings typen AES 256 för dator kontot:  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * Du behöver bara köra det här kommandot en gång för varje dator konto.
    * Du kan köra det här kommandot från en domänkontrollant eller från en dator med [rsat](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) installerat. 
    * `$NFSCOMPUTERACCOUNT`Variabeln är det dator konto som skapades i Active Directory när du distribuerar Kerberos-volymen. Detta är det konto som har prefixet `NFS-` . 
    * `$ANFSERVICEACCOUNT`Variabeln är ett användar konto som inte är privilegie rad Active Directory med delegerade kontroller över den organisationsenhet där dator kontot har skapats. 

5. Montera volymen på värden: 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * `$ANFEXPORT`Variabeln är den `host:/export` sökväg som finns i monterings anvisningarna.
    * `$ANFMOUNTPOINT`Variabeln är mappen som skapas av användaren på Linux-värden.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Prestanda påverkan för Kerberos på NFSv 4.1 

Det här avsnittet hjälper dig att förstå prestanda effekten hos Kerberos på NFSv 4.1.

### <a name="available-security-options"></a>Tillgängliga säkerhets alternativ 

De säkerhets alternativ som för närvarande är tillgängliga för NFSv 4.1-volymer är följande: 

* **SEC = sys** använder lokala UNIX-UID och GIDs med hjälp av AUTH_SYS för att autentisera NFS-åtgärder.
* **SEC = krb5** använder Kerberos V5 i stället för lokala UNIX-UID och GIDs för att autentisera användare.
* **SEC = krb5i** använder Kerberos V5 för användarautentisering och utför integritets kontroll av NFS-åtgärder med hjälp av säkra kontroll summor för att förhindra manipulering av data.
* **SEC = krb5p** använder Kerberos V5 för användarautentisering och integritets kontroll. Den krypterar NFS-trafik för att förhindra trafik avlyssning. Det här alternativet är den säkraste inställningen, men det innebär också högsta prestanda.

### <a name="performance-vectors-tested"></a>Prestanda vektorer har testats

I det här avsnittet beskrivs den enda prestanda påverkan på klient sidan för de olika `sec=*` alternativen.

* Prestanda påverkan har testats på två nivåer: låg concurrency (låg belastning) och hög samtidighet (övre gräns för I/O och data flöde).  
* Tre typer av arbets belastningar har testats:  
    * Liten åtgärd slumpmässig läsning/skrivning (med FIO)
    * Sekventiell läsning/skrivning av stor åtgärd (med FIO)
    * Metadata, tungt arbets belastning som genereras av program som git

### <a name="expected-performance-impact"></a>Förväntad prestanda påverkan 

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

* [Felsök problem med NFSv 4.1 Kerberos-volymer](troubleshoot-nfsv41-kerberos-volumes.md)
* [Vanliga frågor och svar om Azure NetApp Files](azure-netapp-files-faqs.md)
* [Skapa en NFS-volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Skapa en Active Directory anslutning](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)
* [Konfigurera en NFS-klient för Azure NetApp Files](configure-nfs-clients.md) 
