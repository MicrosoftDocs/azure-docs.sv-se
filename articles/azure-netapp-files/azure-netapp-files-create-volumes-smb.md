---
title: Skapa en SMB-volym för Azure NetApp Files | Microsoft Docs
description: Den här artikeln visar hur du skapar en SMB3-volym i Azure NetApp Files. Lär dig mer om kraven för Active Directory-anslutningar och Domain Services.
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
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: d3ca94524c334a20f5ee75e5300ad419fa1542c5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873279"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Skapa en SMB-volym för Azure NetApp Files

Azure NetApp Files har stöd för att skapa volymer med NFS (NFSv3 och NFSv4.1), SMB3 eller dubbla protokoll (NFSv3 och SMB). En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet. 

Den här artikeln visar hur du skapar en SMB3-volym. Information om NFS-volymer finns [i Skapa en NFS-volym.](azure-netapp-files-create-volumes.md) Information om volymer med dubbla protokoll finns [i Skapa en volym med dubbla protokoll.](create-volumes-dual-protocol.md)

## <a name="before-you-begin"></a>Innan du börjar 

* Du måste redan ha konfigurerat en kapacitetspool. Se [Konfigurera en kapacitetspool.](azure-netapp-files-set-up-capacity-pool.md)     
* Ett undernät måste delegeras till Azure NetApp Files. Se [Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="configure-active-directory-connections"></a>Konfigurera Active Directory-anslutningar 

Innan du skapar en SMB-volym måste du skapa en Active Directory-anslutning. Om du inte har konfigurerat Active Directory-anslutningar för Azure NetApp-filer följer du anvisningarna som beskrivs i [Skapa och hantera Active Directory-anslutningar.](create-active-directory-connections.md)

## <a name="add-an-smb-volume"></a>Lägga till en SMB-volym

1. Klicka på **bladet** Volymer på bladet Kapacitetspooler. 

    ![Navigera till volymer](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klicka på **+ Lägg till volym** för att skapa en volym.  
    Fönstret Skapa en volym visas.

3. I fönstret Skapa en volym klickar du **på Skapa** och anger information för följande fält under fliken Grundläggande:   
    * **Volymnamn**      
        Ange namnet på den volym du skapar.   

        Ett volymnamn måste vara unikt inom varje kapacitetspool. Det måste innehålla minst tre tecken. Du kan använda valfria alfanumeriska tecken.   

        Du kan inte använda `default` eller `bin` som volymnamn.

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

        Det virtuella nätverk som du anger måste ha ett undernät delegerat till Azure NetApp Files. Tjänsten Azure NetApp Files kan endast nås från samma VNet eller från ett VNet som finns i samma region som volymen via VNet-peering. Du kan också komma åt volymen från ditt lokala nätverk via Express Route.   

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files. 
        
        Om du inte har delegerat ett undernät kan du klicka **på Skapa nytt** på sidan Skapa en volym. På sidan Skapa undernät anger du sedan undernätsinformationen och väljer **Microsoft.NetApp/volumes** för att delegera undernätet för Azure NetApp Files. I varje VNet kan endast ett undernät delegeras till Azure NetApp Files.   
 
        ![Skapa en volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Skapa undernät](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Om du vill tillämpa en befintlig ögonblicksbildsprincip på volymen klickar du på Visa avancerat avsnitt för att expandera den, anger om du vill dölja sökvägen till ögonblicksbilden och väljer en ögonblicksbildsprincip i den nedströmsmenyn.  

        Information om hur du skapar en ögonblicksbildsprincip finns i [Hantera principer för ögonblicksbilder.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

        ![Visa avancerat val](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Klicka **på** Protokoll och fyll i följande information:  
    * Välj **SMB** som protokolltyp för volymen. 
    * Välj **din Active Directory-anslutning** i listrutan.
    * Ange namnet på den delade volymen i  **Resursnamn**.
    * Om du vill aktivera kryptering för SMB3 väljer du **Aktivera SMB3-protokollkryptering.**   
        Den här funktionen möjliggör kryptering för SMB3-data under flygning. SMB-klienter som inte använder SMB3-kryptering kan inte komma åt den här volymen.  Vilodata krypteras oavsett den här inställningen.  
        Mer information [finns i Vanliga frågor och svar om SMB-kryptering.](azure-netapp-files-faqs.md#smb-encryption-faqs) 

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
    * Om du vill aktivera kontinuerlig tillgänglighet för SMB-volymen väljer du **Aktivera kontinuerlig tillgänglighet.**    

        > [!IMPORTANT]   
        > Funktionen kontinuerlig SMB-tillgänglighet är för närvarande i offentlig förhandsversion. Du måste skicka en begäran om väntelista för att få åtkomst till funktionen via sidan för att **[skicka Azure NetApp Files SMB Continuous Availability Shares Public Preview.](https://aka.ms/anfsmbcasharespreviewsignup)** Vänta på ett officiellt e-postmeddelande från Azure NetApp Files innan du använder funktionen Kontinuerlig tillgänglighet.   
        > 
        > Du bör endast aktivera kontinuerlig tillgänglighet för SQL Server och [FSLogix-användarprofilcontainrar.](../virtual-desktop/create-fslogix-profile-container.md) Användning av SMB-kontinuerliga tillgänglighetsresurser för andra arbetsbelastningar än SQL Server och FSLogix-användarprofilcontainrar *stöds* inte. Den här funktionen stöds för närvarande i Windows SQL Server. Linux SQL Server stöds inte för närvarande. Om du använder ett icke-administratörskonto (domän) för att SQL Server måste du se till att kontot har den säkerhetsbehörighet som krävs. Om domänkontot inte har den säkerhetsbehörighet som krävs ( ), och behörigheten inte kan anges på domännivå, kan du bevilja kontot behörigheten med hjälp av fältet Säkerhetsprivilegier i `SeSecurityPrivilege` Active Directory-anslutningar.  Se [Skapa en Active Directory-anslutning.](create-active-directory-connections.md#create-an-active-directory-connection)

    <!-- [1/13/21] Commenting out command-based steps below, because the plan is to use form-based (URL) registration, similar to CRR feature registration -->
    <!-- 
        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
    --> 

    ![Skärmbild som beskriver fliken Protokoll för att skapa en SMB-volym.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Klicka **på Granska + skapa** för att granska volyminformationen.  Klicka sedan **på Skapa** för att skapa SMB-volymen.

    Volymen som du skapade visas på sidan Volymer. 
 
    En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.

## <a name="control-access-to-an-smb-volume"></a>Kontrollera åtkomst till en SMB-volym  

Åtkomst till en SMB-volym hanteras via behörigheter.  

### <a name="share-permissions"></a>Dela behörigheter  

Som standard har en ny volym **resursbehörigheterna Alla/Fullständig** behörighet. Medlemmar i gruppen Domänadministratörer kan ändra resursbehörigheterna på följande sätt:  

1. Mappa resursen till en enhet.  
2. Högerklicka på enheten, välj **Egenskaper** och gå sedan till **fliken** Säkerhet.

[![Ange resursbehörigheter](../media/azure-netapp-files/set-share-permissions.png)](../media/azure-netapp-files/set-share-permissions.png#lightbox)

### <a name="ntfs-file-and-folder-permissions"></a>NTFS-fil- och mappbehörigheter  

Du kan ange behörigheter för en  fil eller mapp med hjälp av fliken Säkerhet i objektets egenskaper i Windows SMB-klienten.
 
![Ange fil- och mappbehörigheter](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Nästa steg  

* [Montera eller demontera en volym för virtuella Windows- eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vanliga frågor och svar om SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Felsöka SMB- eller dubbla protokollvolymer](troubleshoot-dual-protocol-volumes.md)
* [Läs om integrering av virtuella nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
* [Installera en ny Active Directory-skog med Hjälp av Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
