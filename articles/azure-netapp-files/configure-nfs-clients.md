---
title: Konfigurera en NFS-klient för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du konfigurerar NFS-klienter som ska användas med Azure NetApp Files.
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
ms.openlocfilehash: c1cdeaa41dda11f2ab520cf8d31ddb2116587082
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94409577"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Konfigurera en NFS-klient för Azure NetApp Files

Konfigurationen av NFS-klienten som beskrivs i den här artikeln är en del av installationen när du [konfigurerar nfsv 4.1 Kerberos-kryptering](configure-kerberos-encryption.md) eller [skapar en volym med dubbla protokoll](create-volumes-dual-protocol.md). Det finns många olika Linux-distributioner som du kan använda med Azure NetApp Files. I den här artikeln beskrivs konfigurationer för två av de ofta använda miljöerna: RHEL 8 och Ubuntu 18,04. 

Oavsett vilken Linux-variant du använder, krävs följande konfigurationer:
* Konfigurera en NTP-klient för att undvika problem med tids skevning.
* Konfigurera DNS-poster för Linux-klienten för namn matchning.  
    Den här konfigurationen måste innehålla posten "A" (forward) och PTR (omvänd). 
* För domän anslutning skapar du ett dator konto för Linux-klienten i mål Active Directory (som skapas vid kommandot sfär anslutning). 
    > [!NOTE] 
    > `$SERVICEACCOUNT`Variabeln som används i kommandona nedan måste vara ett användar konto med behörighet eller delegering för att skapa ett dator konto i den aktuella organisationsenheten.

## <a name="rhel-8-configuration"></a>RHEL 8-konfiguration 

I det här avsnittet beskrivs RHEL-konfigurationer som krävs för NFSv 4.1 Kerberos-kryptering och dubbelt protokoll.  

I exemplen i det här avsnittet används följande domän namn och IP-adress:  

* Domän namn: `contoso.com`
* Privat IP: `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>RHEL 8-konfiguration om du använder NFSv 4.1 Kerberos-kryptering

1. Konfigurera `/etc/resolv.conf` med rätt DNS-server.  

    Exempel:  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. Lägg till NFS-klientprogramvaran i DNS-servern för DNS-vidarebefordran och zon för omvänd sökning.

3. Verifiera DNS genom att använda följande kommandon från NFS-klienten:   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. Installera paket:   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  Konfigurera NTP-klienten.  

    RHEL 8 använder chrony som standard. Följ konfigurations rikt linjerna i [använda `Chrony` paketet för att konfigurera NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

6.  Anslut Active Directory-domänen:  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    Exempel: 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`
    
    Se till att anges `default_realm` till den angivna sfären i `/etc/krb5.conf` .  Om inte, lägger du till det under `[libdefaults]` avsnittet i filen som visas i följande exempel:
    
    `default_realm = CONTOSO.COM`

7. Starta om alla NFS-tjänster:  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    Omstarten förhindrar fel tillståndet `“mount.nfs: an incorrect mount option was specified”` under Kerberos-montering.

8. Kör `kinit` kommandot med användar kontot för att hämta biljetter: 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    Exempel:   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>RHEL 8-konfiguration om du använder dubbla protokoll

Följande steg är valfria. Du behöver bara utföra stegen om du använder användar mappning på NFS-klienten: 

1. Slutför alla steg som beskrivs i [RHEL 8-konfigurationen om du använder nfsv 4.1 Kerberos Encryption](#rhel8_nfsv41_kerberos) -avsnittet.   

2. Lägg till en statisk DNS-post i/etc/hosts-filen för att använda fullständigt kvalificerat domän namn (FQDN) för din AD, i stället för att använda IP-adressen i SSSD-konfigurations filen:  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. Lägg till ett extra avsnitt för domäner för att lösa identifierare från AD LDAP-server:    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   

4. Se till att du `/etc/nsswitch.conf` har `sss` posten:   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. Starta om `sssd` tjänsten och rensa cacheminnet:   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. Testa för att säkerställa att klienten är integrerad med LDAP-servern:   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Ubuntu-konfiguration   

I det här avsnittet beskrivs Ubuntu-konfigurationer som krävs för NFSv 4.1 Kerberos-kryptering och dubbelt protokoll. 

I exemplen i det här avsnittet används följande domän namn och IP-adress:  

* Domän namn: `contoso.com`
* Privat IP: `10.6.1.4`

1. Konfigurera `/etc/resolv.conf` med rätt DNS-Server:

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. Lägg till NFS-klientprogramvaran i DNS-servern för DNS-vidarebefordran och zon för omvänd sökning.
 
    Verifiera DNS genom att använda följande kommandon från NFS-klienten:

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. Installera paket:
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    När du uppmanas till det inmatade `$DOMAIN.NAME` alternativet (med versaler, till exempel `CONTOSO.COM` ) som standard-Kerberos-sfär.

4. Starta om tjänsten `rpc-gssd.service` : 

    `sudo systemctl start rpc-gssd.service`

5. Ubuntu 18,04 använder chrony som standard. Följa konfigurations rikt linjerna i [Ubuntu Bionic: använda chrony för att konfigurera NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

6. Anslut Active Directory-domänen:   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    Exempel:    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. Utför `kinit` med användaren för att hämta biljetter: 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    Exempel:    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>Ubuntu-konfiguration om du använder dubbla protokoll  

Följande steg är valfria.  Du behöver bara utföra stegen om du vill använda användar mappning på NFS-klienten:  

1. Kör följande kommando för att uppgradera de installerade paketen:   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    I följande exempel används exempel värden. När du uppmanas att ange inmatade kommandon bör du tillhandahålla ininformation baserat på din miljö. 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. Kör följande kommando för att starta om och aktivera tjänsten:

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

I följande exempel frågar AD LDAP-servern från LDAP-Ubuntu LDAP-klient för en LDAP-användare `‘hari1’` : 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   


## <a name="next-steps"></a>Nästa steg  

* [Skapa en NFS-volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Skapa en dual-Protocol-volym för Azure NetApp Files](create-volumes-dual-protocol.md)

