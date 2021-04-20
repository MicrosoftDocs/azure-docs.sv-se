---
title: Skapa en volym med dubbla protokoll (NFSv3 och SMB) för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du skapar en volym som använder det dubbla protokollet för NFSv3 och SMB med stöd för LDAP-användarmappning.
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
ms.date: 04/19/2021
ms.author: b-juche
ms.openlocfilehash: c702c41228512eceebeaf45ccae709db38a85a51
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725692"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Skapa en volym med dubbla protokoll (NFSv3 och SMB) för Azure NetApp Files

Azure NetApp Files stöd för att skapa volymer med NFS (NFSv3 och NFSv4.1), SMB3 eller dubbla protokoll. Den här artikeln visar hur du skapar en volym som använder det dubbla protokollet för NFSv3 och SMB med stöd för LDAP-användarmappning. 

Information om hur du skapar NFS-volymer finns [i Skapa en NFS-volym.](azure-netapp-files-create-volumes.md) Information om hur du skapar SMB-volymer [finns i Skapa en SMB-volym.](azure-netapp-files-create-volumes-smb.md) 

## <a name="before-you-begin"></a>Innan du börjar 

* Du måste redan ha skapat en kapacitetspool.  
    Se [Konfigurera en kapacitetspool.](azure-netapp-files-set-up-capacity-pool.md)   
* Ett undernät måste delegeras till Azure NetApp Files.  
    Se [Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Överväganden

* Kontrollera att du uppfyller [kraven för Active Directory-anslutningar](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* Skapa en zon för omvänd sökning på DNS-servern och lägg sedan till en pekarpost (PTR) för AD-värddatorn i den omvända sökningszonen. Annars går det inte att skapa volymen med dubbla protokoll.
* Kontrollera att NFS-klienten är uppdaterad och att de senaste uppdateringarna för operativsystemet används.
* Kontrollera att LDAP-servern (Active Directory) är igång på AD. Du kan göra det genom att installera och [konfigurera Active Directory Lightweight Directory Services (AD LDS)-rollen](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) på AD-datorn.
* Volymer med dubbla protokoll stöder för närvarande inte Azure Active Directory Domain Services (AADDS). LDAP över TLS får inte aktiveras om du använder AADDS.
* NFS-versionen som används av en volym med dubbla protokoll är NFSv3. Därför gäller följande överväganden:
    * Dubbla protokoll stöder inte utökade Windows ACLS-attribut från `set/get` NFS-klienter.
    * NFS-klienter kan inte ändra behörigheter för NTFS-säkerhetsformatet och Windows-klienter kan inte ändra behörigheter för volymer med dubbla protokoll i UNIX-format.   

    I följande tabell beskrivs säkerhetsformaten och deras effekter:  
    
    | Säkerhetsstil    | Klienter som kan ändra behörigheter   | Behörigheter som klienter kan använda  | Resulterande effektiv säkerhetsstil    | Klienter som har åtkomst till filer     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | NFSv3-läge bitar   | UNIX  | NFS och Windows   |
    | `Ntfs`    | Windows   | NTFS-ACL:er     | NTFS  |NFS och Windows|
* UNIX-användare som monterar volymen för NTFS-säkerhetsformat med NFS autentiseras som Windows-användare `root` för UNIX och för alla andra `root` `pcuser` användare. Kontrollera att dessa användarkonton finns i Active Directory innan du monterar volymen när du använder NFS. 
* Om du har stora topologier och använder säkerhetsformatet med en volym med dubbla protokoll eller LDAP med utökade grupper kanske Azure NetApp Files inte kan komma åt alla servrar i `Unix` dina topologier.  Om den här situationen inträffar kontaktar du ditt kontoteam för att få hjälp.  <!-- NFSAAS-15123 --> 
* Du behöver inte ett rotcertifikatutfärdarcertifikat för servern för att skapa en volym med dubbla protokoll. Det krävs bara om LDAP över TLS är aktiverat.


## <a name="create-a-dual-protocol-volume"></a>Skapa en volym med dubbla protokoll

1.  Klicka på **bladet** Volymer på bladet Kapacitetspooler. Klicka på **+ Lägg till volym** för att skapa en volym. 

    ![Navigera till volymer](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  I fönstret Skapa en volym klickar du **på Skapa** och anger information för följande fält under fliken Grundläggande:   
    * **Volymnamn**      
        Ange namnet på den volym du skapar.   

        Ett volymnamn måste vara unikt inom varje kapacitetspool. Det måste innehålla minst tre tecken. Du kan använda valfria alfanumeriska tecken.   

        Du kan inte `default` använda `bin` eller som volymnamn.

    * **Kapacitetspool**  
        Ange den kapacitetspool där du vill att volymen ska skapas.

    * **Kvoten**  
        Ange mängden logisk lagring som tilldelas till volymen.  

        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.  

    * **Dataflöde (MiB/S)**   
        Om volymen skapas i en manuell QoS-kapacitetspool anger du det dataflöde som du vill använda för volymen.   

        Om volymen skapas i en automatisk QoS-kapacitetspool visas värdet i det här fältet (kvot x dataflöde på tjänstnivå).   

    * **Virtuellt nätverk**  
        Ange det virtuella Azure-nätverk (VNet) som du vill komma åt volymen från.  

        Det virtuella nätverk som du anger måste ha ett undernät delegerat till Azure NetApp Files. Azure NetApp Files-tjänsten kan endast nås från samma virtuella nätverk eller från ett virtuellt nätverk som finns i samma region som volymen via VNet-peering. Du kan också komma åt volymen från ditt lokala nätverk via Express Route.   

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files. 
        
        Om du inte har delegerat ett undernät kan du klicka på **Skapa nytt** på sidan Skapa en volym. På sidan Skapa undernät anger du sedan undernätsinformationen och väljer **Microsoft.NetApp/volumes** för att delegera undernätet för Azure NetApp Files. I varje Vnet kan endast ett undernät delegeras till Azure NetApp Files.   
 
        ![Skapa en volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Skapa undernät](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Om du vill tillämpa en befintlig ögonblicksbildsprincip på volymen klickar du på Visa avancerat avsnitt för att expandera den, anger om du vill dölja sökvägen till ögonblicksbilden och väljer en princip för ögonblicksbilder i den nedströmsmenyn.  

        Information om hur du skapar en princip för ögonblicksbilder finns i [Hantera principer för ögonblicksbilder.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

        ![Visa avancerat val](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Klicka **på** Protokoll och utför sedan följande åtgärder:  
    * Välj **dual-protocol (NFSv3 och SMB)** som protokolltyp för volymen.   

    * Ange **volymsökvägen** för volymen.   
    Den här volymsökvägen är namnet på den delade volymen. Namnet måste börja med ett alfabetiskt tecken och det måste vara unikt inom varje prenumeration och varje region.  

    * Ange **säkerhetsformatet som** ska användas: NTFS (standard) eller UNIX.

    * Om du vill aktivera SMB3-protokollkryptering för volymen med dubbla protokoll väljer du **Aktivera SMB3-protokollkryptering.**   

        Den här funktionen möjliggör kryptering endast för SMB3-data under flygning. Den krypterar inte NFSv3-data under flygning. SMB-klienter som inte använder SMB3-kryptering kan inte komma åt den här volymen. Vilodata krypteras oavsett den här inställningen. Mer information [finns i Vanliga frågor och svar om SMB-kryptering.](azure-netapp-files-faqs.md#smb-encryption-faqs) 

        Funktionen **SMB3-protokollkryptering** är för närvarande i förhandsversion. Om det är första gången du använder den här funktionen registrerar du funktionen innan du använder den: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Kontrollera status för funktionsregistreringen: 

        > [!NOTE]
        > **RegistrationState kan** vara i tillståndet `Registering` i upp till 60 minuter innan du ändrar till `Registered` . Vänta tills statusen är `Registered` innan du fortsätter.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        Du kan också använda [Azure CLI-kommandon](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` och registrera funktionen och visa `az feature show` registreringsstatusen.  

    * Du kan också [konfigurera exportprincipen för volymen](azure-netapp-files-configure-export-policy.md).

    ![Ange dubbla protokoll](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Klicka **på Granska + skapa** för att granska volyminformationen. Klicka sedan **på Skapa** för att skapa volymen.

    Volymen som du skapade visas på sidan Volymer. 
 
    En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>Tillåt att lokala NFS-användare med LDAP får åtkomst till en volym med dubbla protokoll 

Du kan aktivera lokala NFS-klientanvändare som inte finns på Windows LDAP-servern för att få åtkomst till en volym med dubbla protokoll som har LDAP med utökade grupper aktiverade. Det gör du genom att aktivera **alternativet Tillåt lokala NFS-användare med LDAP** enligt följande:

1. Klicka **på Active Directory-anslutningar**.  På en befintlig Active Directory-anslutning klickar du på snabbmenyn (de tre punkterna `…` ) och väljer **Redigera**.  

2. I fönstret **Redigera Active Directory-inställningar** som visas väljer du **alternativet Tillåt lokala NFS-användare med LDAP.**  

    ![Skärmbild som visar alternativet Tillåt lokala NFS-användare med LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>Hantera LDAP POSIX-attribut

Du kan hantera POSIX-attribut som UID, Hemkatalog och andra värden med hjälp Active Directory - användare och datorer MMC-snapin-modulen.  I följande exempel visas Active Directory-attributredigeraren:  

![Active Directory-attributredigeraren](../media/azure-netapp-files/active-directory-attribute-editor.png) 

Du måste ange följande attribut för LDAP-användare och LDAP-grupper: 
* Obligatoriska attribut för LDAP-användare:   
    `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: posixAccount`
* Obligatoriska attribut för LDAP-grupper:   
    `objectClass: posixGroup`, `gidNumber: 555`

## <a name="configure-the-nfs-client"></a>Konfigurera NFS-klienten 

Följ anvisningarna [i Konfigurera en NFS-klient Azure NetApp Files](configure-nfs-clients.md) konfigurera NFS-klienten.  

## <a name="next-steps"></a>Nästa steg  

* [Konfigurera en NFS-klient för Azure NetApp Files](configure-nfs-clients.md)
* [Felsöka SMB- eller dubbla protokollvolymer](troubleshoot-dual-protocol-volumes.md)
* [Felsöka problem med LDAP-volymer](troubleshoot-ldap-volumes.md)
