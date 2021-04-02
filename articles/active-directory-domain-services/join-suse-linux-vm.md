---
title: Anslut en virtuell SLE-dator till Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter till en SUSE Linux Enterprise-dator till en Azure AD Domain Services hanterad domän.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: justinha
ms.openlocfilehash: f2f421d95dfc376aed373c718198db33a870d9dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619614"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Ansluta en SUSE Linux Enterprise-dator till en Azure Active Directory Domain Services hanterad domän

För att användarna ska kunna logga in på virtuella datorer i Azure med en enda uppsättning autentiseringsuppgifter, kan du ansluta virtuella datorer till en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän. När du ansluter en virtuell dator till en Azure AD DS-hanterad domän kan användar konton och autentiseringsuppgifter från domänen användas för att logga in och hantera servrar. Grupp medlemskap från den hanterade domänen tillämpas också så att du kan kontrol lera åtkomsten till filer och tjänster på den virtuella datorn.

Den här artikeln visar hur du ansluter en virtuell dator med SUSE Linux Enterprise (SLE) till en hanterad domän.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Vid behov skapar och konfigurerar den första självstudien [en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* Ett användar konto som är en del av den hanterade domänen.

## <a name="create-and-connect-to-a-sle-linux-vm"></a>Skapa och Anslut till en virtuell SLE Linux-dator

Om du har en befintlig virtuell SLE Linux-dator i Azure ansluter du till den med SSH och fortsätter sedan till nästa steg för att [börja konfigurera den virtuella datorn](#configure-the-hosts-file).

Om du behöver skapa en virtuell SLE Linux-dator, eller om du vill skapa en virtuell test dator för användning med den här artikeln kan du använda någon av följande metoder:

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
* *Linux-q2gr* är värd namnet för din SLE-VM som du ansluter till den hanterade domänen.

Uppdatera namnen med dina egna värden:

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

När du är färdig sparar du och avslutar *hosts* -filen med hjälp `:wq` av redigerings kommandot.

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>Anslut den virtuella datorn till den hanterade domänen med SSSD

Slutför följande steg för att ansluta till den hanterade domänen med **SSSD** och modulen *användar inloggnings hantering* i YaST:

1. Installera modulen för *användar inloggnings hantering* YaST:

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. Öppna YaST.

1. Om du vill använda automatisk identifiering av DNS senare konfigurerar du IP-adresserna för den hanterade domänen ( *Active Directory-servern*) som namnserver för din klient.

    I YaST väljer du **System > nätverks inställningar**.

1. Välj fliken *hostname/DNS* och ange IP-adressen för den hanterade domänen i text rutan *namn server 1*. De här IP-adresserna visas i fönstret *Egenskaper* i Azure Portal för din hanterade domän, till exempel *10.0.2.4* och *10.0.2.5*.

    Lägg till din egen IP-adress för hanterade domäner och välj sedan **OK**.

1. Välj *Network Services*  >  *hantering av användar inloggning* i huvud fönstret för YaST.

    Modulen öppnas med en översikt över olika nätverks egenskaper för datorn och den autentiseringsmetod som används för närvarande, som visas i följande exempel skärm bild:

    ![Exempel skärm bild av fönstret användar inloggnings hantering i YaST](./media/join-suse-linux-vm/overview-window.png)

    Om du vill börja redigera väljer du **ändra inställningar**.

Slutför följande steg för att ansluta den virtuella datorn till den hanterade domänen:

1. I dialog rutan väljer du **Lägg till domän**.

1. Ange rätt *domän namn*, till exempel *aaddscontoso.com*, och ange sedan de tjänster som ska användas för identitets data och autentisering. Välj *Microsoft Active Directory* för båda.

    Kontrol lera att alternativet för *att aktivera domänen* är markerat.

1. När du är klar väljer du **OK**.

1. Godkänn standardinställningarna i följande dialog ruta och välj sedan **OK**.

1. Den virtuella datorn installerar ytterligare program vara efter behov och kontrollerar sedan om den hanterade domänen är tillgänglig.

    Om allt är korrekt visas följande exempel dialog ruta för att indikera att den virtuella datorn har identifierat den hanterade domänen men att du *ännu inte har registrerats*.

    ![Exempel skärm bild av fönstret Active Directory-registrering i YaST](./media/join-suse-linux-vm/enroll-window.png)

1. I dialog rutan anger du *användar namn* och *lösen ord* för en användare som är en del av den hanterade domänen. Om det behövs [lägger du till ett användar konto i en grupp i Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    För att se till att den aktuella domänen är aktive rad för samba aktiverar *du överskrivning av Samba-konfigurationen för att arbeta med den här AD*.

1. Om du vill registrera dig väljer du **OK**.

1. Ett meddelande visas som bekräftar att du har registrerats. Slutför genom att välja **OK**.

När den virtuella datorn har registrerats i den hanterade domänen konfigurerar du klienten med hjälp av *Hantera domän användarens inloggning*, som du ser i följande exempel skärm bild:

![Exempel skärm bild av fönstret Hantera domän användar inloggning i YaST](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. Om du vill tillåta inloggningar med hjälp av data från den hanterade domänen markerar du kryss rutan för *Tillåt domän användar inloggning*.

1. Alternativt, under *Aktivera domän data källa*, kontrollerar du ytterligare data källor efter behov för din miljö. De här alternativen omfattar vilka användare som får använda **sudo** eller vilka nätverks enheter som är tillgängliga.

1. Om du vill tillåta att användare i den hanterade domänen har hem kataloger på den virtuella datorn markerar du kryss rutan för *skapa arbets kataloger*.

1. I sido fältet väljer du **tjänst alternativ › namn switch** och sedan *utökade alternativ*. Välj antingen *fallback_homedir* eller *override_homedir* i fönstret och välj sedan **Lägg till**.

1. Ange ett värde för hem katalogens plats. Om du vill att hem kataloger ska följa formatet på */home/user_name* använder du */Home/%u*. Mer information om möjliga variabler finns i SSSD. conf man Page ( `man 5 sssd.conf` ), avsnitt *override_homedir*.

1. Välj **OK**.

1. Klicka på **OK** för att spara ändringarna. Kontrol lera sedan att värdena som visas nu är korrekta. Välj **Avbryt** om du vill lämna dialog rutan.

1. Om du avser att köra SSSD och winbind samtidigt (t. ex. När du ansluter till via SSSD, men sedan kör en Samba-fil Server) ska *Kerberos-metoden* för samba vara inställd på *hemligheter och keytab* i SMB. conf. Alternativet SSSD *ad_update_samba_machine_account_password* ska också anges till *Sant* i SSSD. conf. De här alternativen förhindrar att systemets keytab inte går att synkronisera.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Anslut den virtuella datorn till den hanterade domänen med winbind

Slutför följande steg för att ansluta till den hanterade domänen med **winbind** och YaST för *Windows-domän medlemskap* :

1. I YaST väljer du **Network Services > domän medlemskap i Windows**.

1. Ange domänen som ska anslutas till *domänen eller arbets gruppen* på skärmen *Windows-domän medlemskap* . Ange namnet på den hanterade domänen, till exempel *aaddscontoso.com*.

    ![Exempel skärm bild av Windows-fönstret för domän medlemskap i YaST](./media/join-suse-linux-vm/samba-client-window.png)

1. Om du vill använda SMB-källan för Linux-autentisering, kontrollerar du alternativet för att *använda SMB-information för Linux-autentisering*.

1. Om du vill skapa en lokal arbets katalog automatiskt för hanterade domän användare på den virtuella datorn markerar du alternativet för att *skapa en Hem Katalog vid inloggning*.

1. Markera alternativet för *offline-autentisering* så att dina domän användare kan logga in även om den hanterade domänen inte är tillgänglig för tillfället.

1. Välj *Expert inställningar* om du vill ändra UID-och GID-intervall för samba-användare och-grupper.

1. Konfigurera NTP-tidssynkronisering (Network Time Protocol) för din hanterade domän genom att välja *NTP-konfiguration*. Ange IP-adresserna för den hanterade domänen. De här IP-adresserna visas i fönstret *Egenskaper* i Azure Portal för din hanterade domän, till exempel *10.0.2.4* och *10.0.2.5*.

1. Välj **OK** och bekräfta domän anslutningen när du uppmanas till det.

1. Ange lösen ordet för en administratör i den hanterade domänen och välj **OK**.

    ![Exempel skärm bild av dialog rutan för autentisering när du ansluter en virtuell SLE-dator till den hanterade domänen](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

När du har anslutit till den hanterade domänen kan du logga in på den från din arbets station med hjälp av din dators visnings hanterare eller-konsolen.

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-yast-command-line-interface"></a>Anslut den virtuella datorn till den hanterade domänen med winbind från kommando rads gränssnittet YaST

Så här ansluter du till den hanterade domänen med hjälp av **winbind** och *kommando rads gränssnittet för YaST*:

* Anslut till domänen:

  ```console
  sudo yast samba-client joindomain domain=aaddscontoso.com user=<admin> password=<admin password> machine=<(optional) machine account>
  ```

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-terminal"></a>Anslut den virtuella datorn till den hanterade domänen med winbind från terminalen

Så här ansluter du till den hanterade domänen med **winbind** och *`samba net` kommandot*:

1. Installera Kerberos-klienten och Samba-winbind:

   ```console
   sudo zypper in krb5-client samba-winbind
   ```

2. Redigera konfigurationsfilerna:

   * /etc/samba/smb.conf
   
     ```ini
     [global]
         workgroup = AADDSCONTOSO
         usershare allow guests = NO #disallow guests from sharing
         idmap config * : backend = tdb
         idmap config * : range = 1000000-1999999
         idmap config AADDSCONTOSO : backend = rid
         idmap config AADDSCONTOSO : range = 5000000-5999999
         kerberos method = secrets and keytab
         realm = AADDSCONTOSO.COM
         security = ADS
         template homedir = /home/%D/%U
         template shell = /bin/bash
         winbind offline logon = yes
         winbind refresh tickets = yes
     ```

   * /etc/krb5.conf
   
     ```ini
     [libdefaults]
         default_realm = AADDSCONTOSO.COM
         clockskew = 300
     [realms]
         AADDSCONTOSO.COM = {
             kdc = PDC.AADDSCONTOSO.COM
             default_domain = AADDSCONTOSO.COM
             admin_server = PDC.AADDSCONTOSO.COM
         }
     [domain_realm]
         .aaddscontoso.com = AADDSCONTOSO.COM
     [appdefaults]
         pam = {
             ticket_lifetime = 1d
             renew_lifetime = 1d
             forwardable = true
             proxiable = false
             minimum_uid = 1
         }
     ```

   * /etc/Security/pam_winbind. conf
   
     ```ini
     [global]
         cached_login = yes
         krb5_auth = yes
         krb5_ccache_type = FILE
         warn_pwd_expire = 14
     ```

   * /etc/nsswitch.conf
   
     ```ini
     passwd: compat winbind
     group: compat winbind
     ```

3. Kontrol lera att datum och tid i Azure AD och Linux är synkroniserade. Du kan göra detta genom att lägga till Azure AD-servern till NTP-tjänsten:
   
   1. Lägg till följande rad i/etc/NTP.conf:
     
      ```console
      server aaddscontoso.com
      ```

   1. Starta om NTP-tjänsten:
     
      ```console
      sudo systemctl restart ntpd
      ```

4. Anslut till domänen:

   ```console
   sudo net ads join -U Administrator%Mypassword
   ```

5. Aktivera winbind som inloggnings källa i PAM-modulerna (Pluggable Authentication Module) för Linux:

   ```console
   pam-config --add --winbind
   ```

6. Aktivera automatisk skapande av hem kataloger så att användarna kan logga in:

   ```console
   pam-config -a --mkhomedir
   ```

7. Starta och aktivera winbind-tjänsten:

   ```console
   sudo systemctl enable winbind
   sudo systemctl start winbind
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

1. Om du vill tillämpa ändringarna och låta användarna logga in med ett lösen ord, startar du om SSH-tjänsten:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Bevilja gruppen "AAD DC-administratörer" sudo behörigheter

Om du vill bevilja medlemmar i administratörs gruppen *AAD DC-administratörer* administratörs behörighet på den virtuella datorn SLE lägger du till en post i */etc/sudoers*. När de har lagts till kan medlemmar i *Administratörs* gruppen för AAD-domänkontrollanter använda `sudo` kommandot på den virtuella datorn SLE.

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

1. Skapa en ny SSH-anslutning från-konsolen. Använd ett domän konto som tillhör den hanterade domänen med hjälp av `ssh -l` kommandot, till exempel `contosoadmin@aaddscontoso.com` och ange sedan den virtuella datorns adress, till exempel *Linux-q2gr.aaddscontoso.com*. Om du använder Azure Cloud Shell använder du den offentliga IP-adressen för den virtuella datorn i stället för det interna DNS-namnet.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. När du har anslutit till den virtuella datorn kontrollerar du att arbets katalogen har initierats korrekt:

    ```console
    pwd
    ```

    Du bör vara i katalogen */Home* med din egen katalog som matchar användar kontot.

3. Kontrol lera nu att grupp medlemskapen har lösts korrekt:

    ```console
    id
    ```

    Du bör se dina grupp medlemskap från den hanterade domänen.

4. Om du har loggat in på den virtuella datorn som medlem i gruppen *AAD DC-administratörer* kontrollerar du att du kan använda kommandot på rätt sätt `sudo` :

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>Nästa steg

Om du har problem med att ansluta den virtuella datorn till den hanterade domänen eller logga in med ett domän konto, se [fel sökning av problem med domän anslutning](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
