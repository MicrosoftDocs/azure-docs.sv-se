---
title: Konfigurera lägger till LDAP med utökade grupper för Azure NetApp Files NFS-volym åtkomst | Microsoft Docs
description: Beskriver överväganden och steg för att aktivera LDAP med utökade grupper när du skapar en NFS-volym med hjälp av Azure NetApp Files.
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
ms.openlocfilehash: 2031cbf07d700307ae1e11c516f9fc736bce5080
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106499018"
---
# <a name="configure-adds-ldap-with-extended-groups-for-nfs-volume-access"></a>Konfigurera lägger till LDAP med utökade grupper för NFS-volym åtkomst

När du [skapar en NFS-volym](azure-netapp-files-create-volumes.md)har du möjlighet att aktivera LDAP med utökade grupp funktioner ( **LDAP-** alternativet) för volymen. Den här funktionen aktiverar Active Directory LDAP-användare och utökade grupper (upp till 1024 grupper) för att få åtkomst till volymen.  

I den här artikeln beskrivs överväganden och steg för att aktivera LDAP med utökade grupper när du skapar en NFS-volym.  

## <a name="considerations"></a>Överväganden

* LDAP över TLS får *inte* vara aktiverat om du använder Azure Active Directory Domain Services (AADDS).  

* Om du aktiverar LDAP med utökade grupp funktioner kommer LDAP-aktiverade [Kerberos-volymer](configure-kerberos-encryption.md) inte att visa fil ägarskapet korrekt för LDAP-användare. En fil eller katalog som skapas av en LDAP-användare kommer `root` som standard att vara ägare i stället för den faktiska LDAP-användaren. `root`Kontot kan dock manuellt ändra ägarskapet för filen med hjälp av kommandot `chown <username> <filename>` . 

* Du kan inte ändra inställningen för LDAP-alternativ (aktive rad eller inaktive rad) när du har skapat volymen.  

* I följande tabell beskrivs inställningarna för Time to Live (TTL) för LDAP-cachen. Du måste vänta tills cachen har uppdaterats innan du försöker komma åt en fil eller katalog via en klient. Annars visas ett meddelande om nekad åtkomst på klienten. 

    |     Feltillstånd    |     Lösning    |
    |-|-|
    | Cache |  Standardvärde för tidsgräns |
    | Lista över grupp medlemskap  | 24-timmars TTL  |
    | UNIX-grupper  | 24-timmars TTL, 1 minut negativ TTL  |
    | UNIX-användare  | 24-timmars TTL, 1 minut negativ TTL  |

    Cacheminnen har en viss tids gräns som kallas för *Time to Live*. Efter tids gränsen uppnådde poster så att inaktuella poster inte Linger. Det *negativa TTL* -värdet är där en sökning som har misslyckats finns för att bidra till att undvika prestanda problem på grund av LDAP-frågor för objekt som kanske inte finns. "        

## <a name="steps"></a>Steg

1. Funktionen LDAP med utökade grupper är för närvarande en för hands version. Innan du använder den här funktionen för första gången måste du registrera funktionen:  

    1. Registrera funktionen:   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```

    2. Kontrol lera status för funktions registreringen: 

        > [!NOTE]
        > **RegistrationState** kan vara i ett `Registering` tillstånd i upp till 60 minuter innan den ändras till `Registered` . Vänta tills statusen är `Registered` innan du fortsätter.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```
        
    Du kan också använda [Azure CLI-kommandon](/cli/azure/feature) `az feature register` och `az feature show` Registrera funktionen och Visa registrerings status. 

2. LDAP-volymer kräver en Active Directory konfiguration för inställningarna för LDAP-servern. Följ anvisningarna i [krav för Active Directory anslutningar](create-active-directory-connections.md#requirements-for-active-directory-connections) och [skapa en Active Directory anslutning](create-active-directory-connections.md#create-an-active-directory-connection) för att konfigurera Active Directory anslutningar på Azure Portal.  

3. Se till att Active Directory LDAP-servern är igång och körs på Active Directory. Du kan göra det genom att installera och konfigurera rollen [Active Directory Lightweight Directory Services (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) på AD-datorn.

4. LDAP-NFS-användare måste ha vissa POSIX-attribut på LDAP-servern. Följ [Hantera LDAP POSIX-attribut](create-volumes-dual-protocol.md#manage-ldap-posix-attributes) för att ange de attribut som krävs.  

5. Om du vill konfigurera en LDAP-integrerad Linux-klient, se [Konfigurera en NFS-klient för Azure NetApp Files](configure-nfs-clients.md).

6.  Skapa en NFS-volym genom att följa stegen i [skapa en NFS-volym för Azure NetApp Files](azure-netapp-files-create-volumes.md) . När du skapar en volym aktiverar du alternativet **LDAP** under fliken **protokoll** .   

    ![Skärm bild som visar skapa en volym sida med LDAP-alternativ.](../media/azure-netapp-files/create-nfs-ldap.png)  

7. Valfritt – du kan aktivera lokala NFS-klientdatorer som inte finns på Windows LDAP-servern för att få åtkomst till en NFS-volym som har LDAP med utökade grupper aktiverade. Det gör du genom att aktivera alternativet **Tillåt lokala NFS-användare med LDAP** enligt följande:
    1. Klicka på **Active Directory anslutningar**.  På en befintlig Active Directory-anslutning klickar du på snabb menyn (de tre punkterna `…` ) och väljer **Redigera**.  
    2. I fönstret **redigera Active Directory inställningar** som visas väljer du alternativet **Tillåt lokala NFS-användare med LDAP** .  

    ![Skärm bild som visar alternativet Tillåt lokala NFS-användare med LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  

## <a name="next-steps"></a>Nästa steg  

* [Skapa en NFS-volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Skapa och hantera Active Directory anslutningar](create-active-directory-connections.md)
* [Felsöka problem med LDAP-volymer](troubleshoot-ldap-volumes.md)
