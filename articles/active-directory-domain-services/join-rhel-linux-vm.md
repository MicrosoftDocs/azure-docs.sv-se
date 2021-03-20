---
title: Anslut en virtuell RHEL-dator till Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter till en Red Hat Enterprise Linux virtuell dator till en Azure AD Domain Services hanterad domän.
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
ms.openlocfilehash: 285a972936bfdf4b173e2a20223143883cd8b7d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619563"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Ansluta en Red Hat Enterprise Linux virtuell dator till en Azure Active Directory Domain Services hanterad domän

För att användarna ska kunna logga in på virtuella datorer i Azure med en enda uppsättning autentiseringsuppgifter, kan du ansluta virtuella datorer till en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän. När du ansluter en virtuell dator till en Azure AD DS-hanterad domän kan användar konton och autentiseringsuppgifter från domänen användas för att logga in och hantera servrar. Grupp medlemskap från den hanterade domänen tillämpas också så att du kan kontrol lera åtkomsten till filer och tjänster på den virtuella datorn.

Den här artikeln visar hur du ansluter en Red Hat Enterprise Linux (RHEL) virtuell dator till en hanterad domän.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Vid behov skapar och konfigurerar den första självstudien [en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* Ett användar konto som är en del av den hanterade domänen.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>Skapa och Anslut till en virtuell RHEL Linux-dator

Om du har en befintlig virtuell RHEL Linux-dator i Azure ansluter du till den med SSH och fortsätter sedan till nästa steg för att [börja konfigurera den virtuella datorn](#configure-the-hosts-file).

Om du behöver skapa en virtuell RHEL Linux-dator, eller om du vill skapa en virtuell test dator för användning med den här artikeln kan du använda någon av följande metoder:

* [Azure-portalen](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

När du skapar den virtuella datorn ska du tänka på inställningarna för det virtuella nätverket för att se till att den virtuella datorn kan kommunicera med den hanterade domänen:

* Distribuera den virtuella datorn till samma eller ett peer-kopplat virtuellt nätverk där du har aktiverat Azure AD Domain Services.
* Distribuera den virtuella datorn till ett annat undernät än din Azure AD Domain Services hanterade domän.

När den virtuella datorn har distribuerats följer du stegen för att ansluta till den virtuella datorn med SSH.

## <a name="configure-the-hosts-file"></a>Konfigurera värd filen

För att se till att namnet på den virtuella datorns värd är korrekt konfigurerat för den hanterade domänen redigerar du */etc/hosts* -filen och anger värd namnet:

```console
sudo vi /etc/hosts
```

I *hosts* -filen uppdaterar du adressen till *localhost* . Se följande exempel:

* *aaddscontoso.com* är DNS-domännamnet för din hanterade domän.
* *RHEL* är värd namnet för din RHEL-VM som du ansluter till den hanterade domänen.

Uppdatera namnen med dina egna värden:

```console
127.0.0.1 rhel rhel.aaddscontoso.com
```

När du är färdig sparar du och avslutar *hosts* -filen med hjälp `:wq` av redigerings kommandot.

## <a name="install-required-packages"></a>Installera de paket som krävs

Den virtuella datorn behöver vissa ytterligare paket för att ansluta den virtuella datorn till den hanterade domänen. Installera och konfigurera de här paketen genom att uppdatera och installera de verktyg för domän anslutning som använder `yum` . Det finns vissa skillnader mellan RHEL 7. x och RHEL 6. x, så Använd lämpliga kommandon för distribution-versionen i de återstående avsnitten i den här artikeln.

**RHEL 7**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**RHEL 6**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Anslut den virtuella datorn till den hanterade domänen

Nu när de nödvändiga paketen har installerats på den virtuella datorn ansluter du den till den hanterade domänen. Använd sedan lämpliga steg för din RHEL distribution-version.

### <a name="rhel-7"></a>RHEL 7

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

### <a name="rhel-6"></a>RHEL 6

1. Använd `adcli info` kommandot för att identifiera den hanterade domänen. I följande exempel identifieras sfär- *ADDDSCONTOSO.com*. Ange ditt eget hanterade domän namn med VERSALer:

    ```console
    sudo adcli info aaddscontoso.com
    ```

   Om `adcli info` kommandot inte kan hitta din hanterade domän kan du läsa följande fel söknings steg:

    * Kontrol lera att domänen kan kommas åt från den virtuella datorn. Försök `ping aaddscontoso.com` att se om ett positivt svar returneras.
    * Kontrol lera att den virtuella datorn har distribuerats till samma eller ett peer-kopplat virtuellt nätverk där den hanterade domänen är tillgänglig.
    * Bekräfta att DNS-serverinställningarna för det virtuella nätverket har uppdaterats så att de pekar på domän kontrol Lanterna i den hanterade domänen.

1. Börja med att ansluta till domänen med hjälp av `adcli join` kommandot, det här kommandot skapar även keytab för att autentisera datorn. Använd ett användar konto som är en del av den hanterade domänen.

    ```console
    sudo adcli join aaddscontoso.com -U contosoadmin
    ```

1. Nu ska du konfigurera `/ect/krb5.conf` och skapa `/etc/sssd/sssd.conf` filerna för att använda `aaddscontoso.com` Active Directory-domänen.
   Kontrol lera att `AADDSCONTOSO.COM` har ersatts av ditt eget domän namn:

    Öppna `/ect/krb5.conf` filen med en redigerare:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Uppdatera `krb5.conf` filen så att den matchar följande exempel:

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = AADDSCONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     AADDSCONTOSO.COM = {
     kdc = AADDSCONTOSO.COM
     admin_server = AADDSCONTOSO.COM
     }
    
    [domain_realm]
     .AADDSCONTOSO.COM = AADDSCONTOSO.COM
     AADDSCONTOSO.COM = AADDSCONTOSO.COM
    ```
    
   Skapa `/etc/sssd/sssd.conf` filen:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Uppdatera `sssd.conf` filen så att den matchar följande exempel:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDSCONTOSO.COM
    
    [domain/AADDSCONTOSO.COM]
    
     id_provider = ad
    ```

1. Se till att `/etc/sssd/sssd.conf` behörigheter är 600 och ägs av rot användaren:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. Används `authconfig` för att instruera den virtuella datorn om AD Linux-integrering:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. Starta och aktivera SSSD-tjänsten:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Om den virtuella datorn inte kan slutföra processen för domän anslutning, se till att den virtuella datorns nätverks säkerhets grupp tillåter utgående Kerberos-trafik på TCP + UDP-port 464 till det virtuella nätverkets undernät för din hanterade domän.

Kontrol lera nu om du kan fråga användarens AD-information med `getent`

```console
sudo getent passwd contosoadmin
```

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

1. Om du vill tillämpa ändringarna och låta användarna logga in med ett lösen ord, startar du om SSH-tjänsten för din RHEL distribution-version:

   **RHEL 7**

    ```console
    sudo systemctl restart sshd
    ```

   **RHEL 6**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Bevilja gruppen "AAD DC-administratörer" sudo behörigheter

Om du vill bevilja medlemmar i administratörs gruppen *AAD DC-administratörer* administrativa privilegier på den virtuella datorn RHEL lägger du till en post i */etc/sudoers*. När de har lagts till kan medlemmar i *Administratörs* gruppen för AAD-domänkontrollanter använda `sudo` kommandot på den virtuella datorn RHEL.

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

1. Skapa en ny SSH-anslutning från-konsolen. Använd ett domän konto som tillhör den hanterade domänen med hjälp av `ssh -l` kommandot, till exempel `contosoadmin@aaddscontoso.com` och ange sedan den virtuella datorns adress, till exempel *RHEL.aaddscontoso.com*. Om du använder Azure Cloud Shell använder du den offentliga IP-adressen för den virtuella datorn i stället för det interna DNS-namnet.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com rhel.aaddscontoso.com
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
