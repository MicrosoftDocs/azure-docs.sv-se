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
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: 6ff87d046c60f588e133010895ec3e7ce08cb71f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740570"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Konfigurera NFSv4.1 Kerberos-kryptering för Azure NetApp Files

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

1.  Följ anvisningarna i [skapa en Active Directory-anslutning](create-active-directory-connections.md).  

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

    Exempel: 

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

Du bör förstå de säkerhets alternativ som är tillgängliga för NFSv 4.1-volymer, de testade prestanda vektorerna och den förväntade prestanda effekten av Kerberos. Mer information finns i [prestanda påverkan från Kerberos på nfsv 4.1-volymer](performance-impact-kerberos.md) .  

## <a name="next-steps"></a>Nästa steg  

* [Prestanda påverkan för Kerberos på NFSv 4.1-volymer](performance-impact-kerberos.md)
* [Felsök problem med NFSv 4.1 Kerberos-volymer](troubleshoot-nfsv41-kerberos-volumes.md)
* [Vanliga frågor och svar om Azure NetApp Files](azure-netapp-files-faqs.md)
* [Skapa en NFS-volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Skapa en Active Directory anslutning](create-active-directory-connections.md)
* [Konfigurera en NFS-klient för Azure NetApp Files](configure-nfs-clients.md) 
