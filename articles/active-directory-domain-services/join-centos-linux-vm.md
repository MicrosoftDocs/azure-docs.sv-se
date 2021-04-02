---
title: Anslut en virtuell CentOS-dator till Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter till en virtuell CentOS Linux-dator till en Azure Active Directory Domain Services hanterad domän.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 76fc11384b55337f581a74239d4a40b90b284f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619665"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Anslut en virtuell CentOS Linux-dator till en Azure Active Directory Domain Services hanterad domän

För att användarna ska kunna logga in på virtuella datorer i Azure med en enda uppsättning autentiseringsuppgifter, kan du ansluta virtuella datorer till en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän. När du ansluter en virtuell dator till en Azure AD DS-hanterad domän kan användar konton och autentiseringsuppgifter från domänen användas för att logga in och hantera servrar. Grupp medlemskap från den hanterade domänen tillämpas också så att du kan kontrol lera åtkomsten till filer och tjänster på den virtuella datorn.

Den här artikeln visar hur du ansluter en virtuell CentOS Linux-dator till en hanterad domän.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Vid behov skapar och konfigurerar den första självstudien [en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* Ett användar konto som är en del av den hanterade domänen.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>Skapa och Anslut till en virtuell CentOS Linux-dator

Om du har en befintlig virtuell CentOS Linux-dator i Azure ansluter du till den med SSH och fortsätter sedan till nästa steg för att [börja konfigurera den virtuella datorn](#configure-the-hosts-file).

Om du behöver skapa en virtuell CentOS Linux-dator, eller om du vill skapa en virtuell test dator för användning med den här artikeln kan du använda någon av följande metoder:

* [Azure-portalen](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

När du skapar den virtuella datorn ska du tänka på inställningarna för det virtuella nätverket för att se till att den virtuella datorn kan kommunicera med den hanterade domänen:

* Distribuera den virtuella datorn till samma eller ett peer-kopplat virtuellt nätverk där du har aktiverat Azure AD Domain Services.
* Distribuera den virtuella datorn till ett annat undernät än din hanterade domän.

När den virtuella datorn har distribuerats följer du stegen för att ansluta till den virtuella datorn med SSH.

## <a name="configure-the-hosts-file"></a>Konfigurera värd filen

För att se till att namnet på den virtuella datorns värd är korrekt konfigurerat för den hanterade domänen redigerar du */etc/hosts* -filen och anger värd namnet:

```console
sudo vi /etc/hosts
```

I *hosts* -filen uppdaterar du adressen till *localhost* . Se följande exempel:

* *aaddscontoso.com* är DNS-domännamnet för din hanterade domän.
* *CentOS* är värd namnet för din CENTOS-VM som du ansluter till den hanterade domänen.

Uppdatera namnen med dina egna värden:

```console
127.0.0.1 centos.aaddscontoso.com centos
```

När du är färdig sparar du och avslutar *hosts* -filen med hjälp `:wq` av redigerings kommandot.

## <a name="install-required-packages"></a>Installera de paket som krävs

Den virtuella datorn behöver vissa ytterligare paket för att ansluta den virtuella datorn till den hanterade domänen. Installera och konfigurera de här paketen genom att uppdatera och installera verktygen för domän anslutning med `yum` :

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Anslut den virtuella datorn till den hanterade domänen

Nu när de nödvändiga paketen har installerats på den virtuella datorn ansluter du den till den hanterade domänen.

1. Använd `realm discover` kommandot för att identifiera den hanterade domänen. I följande exempel identifieras sfär- *AADDSCONTOSO.com*. Ange ditt eget hanterade domän namn med VERSALer:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Om `realm discover` kommandot inte kan hitta din hanterade domän kan du läsa följande fel söknings steg:

    * Kontrol lera att domänen kan kommas åt från den virtuella datorn. Försök `ping aaddscontoso.com` att se om ett positivt svar returneras.
    * Kontrol lera att den virtuella datorn har distribuerats till samma eller ett peer-kopplat virtuellt nätverk där den hanterade domänen är tillgänglig.
    * Bekräfta att DNS-serverinställningarna för det virtuella nätverket har uppdaterats så att de pekar på domän kontrol Lanterna i den hanterade domänen.

1. Initiera nu Kerberos med hjälp av `kinit` kommandot. Ange en användare som är en del av den hanterade domänen. Om det behövs [lägger du till ett användar konto i en grupp i Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Namnet på den hanterade domänen måste anges i alla VERSALer. I följande exempel `contosoadmin@aaddscontoso.com` används kontot som heter för att initiera Kerberos. Ange ditt eget användar konto som är en del av den hanterade domänen:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Slutligen ansluter du den virtuella datorn till den hanterade domänen med hjälp av `realm join` kommandot. Använd samma användar konto som är en del av den hanterade domän som du angav i föregående `kinit` kommando, till exempel `contosoadmin@AADDSCONTOSO.COM` :

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Det tar en stund att ansluta den virtuella datorn till den hanterade domänen. Följande exempel på utdata visar att den virtuella datorn har anslutits till den hanterade domänen:

```output
Successfully enrolled machine in realm
```

Om den virtuella datorn inte kan slutföra processen för domän anslutning, se till att den virtuella datorns nätverks säkerhets grupp tillåter utgående Kerberos-trafik på TCP + UDP-port 464 till det virtuella nätverkets undernät för din hanterade domän.

## <a name="allow-password-authentication-for-ssh"></a>Tillåt lösenordsautentisering för SSH

Som standard kan användare bara logga in på en virtuell dator med hjälp av offentlig SSH-baserad autentisering. Lösenordsbaserad autentisering Miss lyckas. När du ansluter den virtuella datorn till en hanterad domän måste dessa domän konton använda lösenordsbaserad autentisering. Uppdatera SSH-konfigurationen så att den tillåter lösenordsbaserad autentisering enligt följande.

1. Öppna *sshd_conf* -filen med en redigerare:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Uppdatera raden för *PasswordAuthentication* till *Ja*:

    ```console
    PasswordAuthentication yes
    ```

    När du är färdig sparar du och avslutar *sshd_conf* -filen med hjälp `:wq` av redigeraren för-kommandot.

1. Om du vill tillämpa ändringarna och låta användarna logga in med ett lösen ord, startar du om SSH-tjänsten:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Bevilja gruppen "AAD DC-administratörer" sudo behörigheter

Om du vill bevilja medlemmar i administratörs gruppen *AAD DC-administratörer* administrativa privilegier på den virtuella datorn CentOS lägger du till en post i */etc/sudoers*. När de har lagts till kan medlemmar i *Administratörs* gruppen för AAD-domänkontrollanter använda `sudo` kommandot på den virtuella datorn CentOS.

1. Öppna *sudoers* -filen för redigering:

    ```console
    sudo visudo
    ```

1. Lägg till följande post i slutet av */etc/sudoers* -filen. *Administratörs gruppen för AAD-domänkontrollanten* innehåller blank steg i namnet, så inkludera det omvända snedstreckets escape-tecken i grupp namnet. Lägg till ditt eget domän namn, till exempel *aaddscontoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    När du är färdig sparar du och stänger redigeraren med hjälp `:wq` av redigerings kommandot.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Logga in på den virtuella datorn med ett domän konto

Verifiera att den virtuella datorn har anslutits till den hanterade domänen genom att starta en ny SSH-anslutning med ett domän användar konto. Bekräfta att en arbets katalog har skapats och att grupp medlemskapet från domänen används.

1. Skapa en ny SSH-anslutning från-konsolen. Använd ett domän konto som tillhör den hanterade domänen med hjälp av `ssh -l` kommandot, till exempel `contosoadmin@aaddscontoso.com` och ange sedan den virtuella datorns adress, till exempel *CentOS.aaddscontoso.com*. Om du använder Azure Cloud Shell använder du den offentliga IP-adressen för den virtuella datorn i stället för det interna DNS-namnet.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
    ```

1. När du har anslutit till den virtuella datorn kontrollerar du att arbets katalogen har initierats korrekt:

    ```console
    pwd
    ```

    Du bör vara i katalogen */Home* med din egen katalog som matchar användar kontot.

1. Kontrol lera nu att grupp medlemskapen har lösts korrekt:

    ```console
    id
    ```

    Du bör se dina grupp medlemskap från den hanterade domänen.

1. Om du har loggat in på den virtuella datorn som medlem i gruppen *AAD DC-administratörer* kontrollerar du att du kan använda kommandot på rätt sätt `sudo` :

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Nästa steg

Om du har problem med att ansluta den virtuella datorn till den hanterade domänen eller logga in med ett domän konto, se [fel sökning av problem med domän anslutning](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
