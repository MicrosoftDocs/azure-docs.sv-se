---
title: Skapa en NFSv3-och SMB-volym (Dual-Protocol) för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du skapar en volym som använder det dubbla protokollet i NFSv3 och SMB med stöd för LDAP-mappning av användare.
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
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: b6a2d7ad92c209a93d740d60808c2cbd2f90c6b4
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258426"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Skapa en NFSv3-och SMB-volym (Dual-Protocol) för Azure NetApp Files

Azure NetApp Files stöder skapande av volymer med NFS (NFSv3 och NFSv 4.1), SMB3 eller två protokoll. Den här artikeln visar hur du skapar en volym som använder det dubbla protokollet i NFSv3 och SMB med stöd för mappning av LDAP-användare. 

Information om hur du skapar NFS-volymer finns i [skapa en NFS-volym](azure-netapp-files-create-volumes.md). Information om hur du skapar SMB-volymer finns i [skapa en SMB-volym](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Innan du börjar 

* Du måste redan ha skapat en kapacitets grupp.  
    Se [Konfigurera en pool för kapacitet](azure-netapp-files-set-up-capacity-pool.md).   
* Ett undernät måste delegeras till Azure NetApp Files.  
    Se [delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Överväganden

* Se till att du uppfyller [kraven för Active Directory anslutningar](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* Skapa en zon för omvänd sökning på DNS-servern och Lägg sedan till en pekare (PTR) av AD host-datorn i den zonen för omvänd sökning. Annars går det inte att skapa dubbla protokoll volymer.
* Kontrollera att NFS-klienten är uppdaterad och att de senaste uppdateringarna för operativsystemet används.
* Kontrol lera att LDAP-servern Active Directory (AD) är igång och körs på AD. Du kan göra det genom att installera och konfigurera rollen [Active Directory Lightweight Directory Services (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) på AD-datorn.
* Dubbla protokoll volymer stöder för närvarande inte Azure Active Directory Domain Services (AADDS). LDAP över TLS får inte vara aktiverat om du använder AADDS.
* NFS-versionen som används av en dual-Protocol-volym är NFSv3. På så sätt gäller följande:
    * Dubbla protokoll stöder inte Windows ACL utökade attribut `set/get` från NFS-klienter.
    * NFS-klienter kan inte ändra behörigheter för säkerhets formatet NTFS och Windows-klienter kan inte ändra behörigheter för UNIX-typ med dubbla protokoll volymer.   

    I följande tabell beskrivs säkerhets stilarna och deras effekter:  
    
    | Säkerhets stil    | Klienter som kan ändra behörigheter   | Behörigheter som klienter kan använda  | Resulterande effektiv säkerhets stil    | Klienter som har åtkomst till filer     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | NFSv3 läges bitar   | UNIX  | NFS och Windows   |
    | `Ntfs`    | Windows   | NTFS ACL: er     | NTFS  |NFS och Windows|
* UNIX-användare som monterar säkerhets format volymen NTFS med NFS autentiseras som Windows-användare `root` för UNIX `root` och `pcuser` för alla andra användare. Se till att dessa användar konton finns i Active Directory innan du monterar volymen när du använder NFS. 
* Om du har stora topologier och använder `Unix` säkerhets formatet med en dubbel protokoll volym eller LDAP med utökade grupper, kanske Azure NetApp Files inte kan komma åt alla servrar i dina topologier.  Om den här situationen inträffar kontaktar du ditt konto team för att få hjälp.  <!-- NFSAAS-15123 --> 
* Du behöver inte ett Server rot certifikat för certifikat utfärdare för att skapa en volym med dubbla protokoll. Det krävs endast om LDAP över TLS är aktiverat.


## <a name="create-a-dual-protocol-volume"></a>Skapa en volym med dubbla protokoll

1.  Klicka på bladet **volymer** på bladet kapacitets pooler. Klicka på **+ Lägg till volym** för att skapa en volym. 

    ![Navigera till volymer](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  I fönstret Skapa en volym klickar du på **skapa** och anger information för följande fält under fliken grundläggande:   
    * **Volym namn**      
        Ange namnet på den volym du skapar.   

        Ett volym namn måste vara unikt inom varje pool för kapacitet. Det måste innehålla minst tre tecken. Du kan använda alla alfanumeriska tecken.   

        Du kan inte använda `default` eller `bin` som volym namn.

    * **Pool för kapacitet**  
        Ange den pool där du vill att volymen ska skapas.

    * **Kvot**  
        Ange mängden logisk lagring som tilldelas till volymen.  

        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.  

    * **Data flöde (MiB/S)**   
        Om volymen har skapats i en manuell pool för QoS-kapacitet anger du det data flöde som du vill använda för volymen.   

        Om volymen skapas i en pool med automatisk QoS-kapacitet är värdet som visas i det här fältet (kvot x service nivåns data flöde).   

    * **Virtuellt nätverk**  
        Ange det virtuella Azure-nätverk (VNet) som du vill ha åtkomst till volymen från.  

        Det virtuella nätverk som du anger måste ha ett undernät delegerat till Azure NetApp Files. Azure NetApp Files-tjänsten kan endast nås från samma virtuella nätverk eller från ett virtuellt nätverk som finns i samma region som volymen via VNet-peering. Du kan också komma åt volymen från det lokala nätverket via Express Route.   

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files. 
        
        Om du inte har delegerat ett undernät kan du klicka på **Skapa nytt** på sidan Skapa en volym. På sidan Skapa undernät anger du sedan undernätsinformationen och väljer **Microsoft.NetApp/volumes** för att delegera undernätet för Azure NetApp Files. I varje VNet kan endast ett undernät delegeras till Azure NetApp Files.   
 
        ![Skapa en volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Skapa undernät](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Om du vill använda en befintlig ögonblicks bilds princip på volymen klickar du på **Visa Avancerat avsnitt** för att expandera den, ange om du vill dölja sökvägen till ögonblicks bilden och välj en ögonblicks bilds princip på den nedrullningsbara menyn. 

        Information om hur du skapar en ögonblicks bilds princip finns i [Hantera ögonblicks bild principer](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Visa avancerad markering](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Klicka på **protokoll** och utför följande åtgärder:  
    * Välj **Dual-Protocol (NFSv3 och SMB)** som protokoll typ för volymen.   

    * Ange **volym Sök vägen** för volymen.   
    Den här volym Sök vägen är namnet på den delade volymen. Namnet måste börja med ett alfabetiskt Character och det måste vara unikt inom varje prenumeration och varje region.  

    * Ange det **säkerhets format** som ska användas: NTFS (standard) eller UNIX.

    * Du kan också [Konfigurera export princip för volymen](azure-netapp-files-configure-export-policy.md).

    ![Ange dubbla protokoll](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Granska volym informationen genom att klicka på **Granska + skapa** . Klicka sedan på **skapa** för att skapa volymen.

    Volymen som du skapade visas på sidan volymer. 
 
    En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>Tillåt lokala NFS-användare med LDAP att komma åt en volym med dubbla protokoll 

Du kan aktivera lokala NFS-klientdatorer som inte finns på Windows LDAP-servern för att få åtkomst till en dual-Protocol-volym som har LDAP med utökade grupper aktiverade. Det gör du genom att aktivera alternativet **Tillåt lokala NFS-användare med LDAP** enligt följande:

1. Klicka på **Active Directory anslutningar**.  På en befintlig Active Directory-anslutning klickar du på snabb menyn (de tre punkterna `…` ) och väljer **Redigera**.  

2. I fönstret **redigera Active Directory inställningar** som visas väljer du alternativet **Tillåt lokala NFS-användare med LDAP** .  

    ![Skärm bild som visar alternativet Tillåt lokala NFS-användare med LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>Hantera LDAP POSIX-attribut

Du kan hantera POSIX-attribut som UID, Hem Katalog och andra värden med hjälp av MMC-snapin-modulen Active Directory användare och datorer.  Följande exempel visar Redigeraren för Active Directory-attribut:  

![Redigeraren för Active Directory-attribut](../media/azure-netapp-files/active-directory-attribute-editor.png) 

Du måste ange följande attribut för LDAP-användare och LDAP-grupper: 
* Obligatoriska attribut för LDAP-användare:   
    `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: posixAccount`
* Obligatoriska attribut för LDAP-grupper:   
    `objectClass: posixGroup`, `gidNumber: 555`

## <a name="configure-the-nfs-client"></a>Konfigurera NFS-klienten 

Följ anvisningarna i [Konfigurera en NFS-klient för att Azure NetApp Files](configure-nfs-clients.md) konfigurera NFS-klienten.  

## <a name="next-steps"></a>Nästa steg  

* [Konfigurera en NFS-klient för Azure NetApp Files](configure-nfs-clients.md)
* [Felsöka SMB-eller Dual-Protocol-volymer](troubleshoot-dual-protocol-volumes.md)
* [Felsöka problem med LDAP-volymer](troubleshoot-ldap-volumes.md)
