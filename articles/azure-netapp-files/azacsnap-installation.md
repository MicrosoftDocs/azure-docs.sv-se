---
title: Installera Azure Application konsekvent ögonblicks bild verktyget för Azure NetApp Files | Microsoft Docs
description: Innehåller en guide för installation av Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 00aaa5bdc0d48adb735679fc4a71b3431970ef09
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737175"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a>Installera Azure Application enhetligt ögonblicks bild verktyget (förhands granskning)

Den här artikeln innehåller en guide för installation av Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.

## <a name="introduction"></a>Introduktion

Det nedladdnings bara själv-installations programmet är utformat för att göra det enkelt att konfigurera och köra ögonblicks bild verktyg med användar rättigheter som inte är rot (till exempel azacsnap). Installations programmet konfigurerar användaren och lägger till ögonblicks bild verktygen i under katalogen användare `$HOME/bin` (standard = `/home/azacsnap/bin` ).
Själv Installer försöker fastställa rätt inställningar och sökvägar för alla filer baserat på konfigurationen för den användare som utför installationen (till exempel root). Om de nödvändiga stegen (aktivera kommunikation med lagring och SAP HANA) kördes som rot, kommer installationen att kopiera den privata nyckeln och `hdbuserstore` till säkerhets kopierings användarens plats. Det är dock möjligt att utföra de steg som möjliggör kommunikation med lagrings Server delen och SAP HANA manuellt utföras av en kunnig administratör efter installationen.

## <a name="prerequisites-for-installation"></a>Krav för installation

Följ rikt linjerna för att ställa in och köra kommandona ögonblicks bilder och haveri beredskap. Vi rekommenderar att du utför följande steg som rot innan du installerar och använder verktyg för ögonblicks bilder.

1. **Operativ systemet har korrigerats**: information [om hur du installerar och konfigurerar SAP HANA (stora instanser) i Azure](../virtual-machines/workloads/sap/hana-installation.md#operating-system)finns i avsnittet korrigering och SMT-installation.
1. **Tidssynkronisering har kon figurer ATS**. Kunden måste tillhandahålla en NTP-kompatibel tids Server och konfigurera operativ systemet på lämpligt sätt.
1. **Hana har installerats** : se Hana-Installationsinstruktioner i [SAP NetWeaver-installation på Hana-databas](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database).
1. **[Aktivera kommunikation med lagring](#enable-communication-with-storage)** (mer information finns i separata avsnitt): kunden måste konfigurera SSH med ett privat/offentligt nyckel par och ange den offentliga nyckeln för varje nod där verktyg för ögonblicks bilder planeras att köras till Microsoft-åtgärder för installation på lagrings Server delen.
   1. **För Azure NetApp Files (se separata avsnitt för mer information)**: kunden måste generera autentiserings filen för tjänstens huvud namn.
   1. **För Azure stor Instance (se separata avsnitt för mer information)**: kunden måste konfigurera SSH med ett privat/offentligt nyckel par och ange den offentliga nyckeln för varje nod där verktyg för ögonblicks bilder planeras att köras till Microsoft-åtgärder för installation på lagrings Server delen.

      Testa detta genom att använda SSH för att ansluta till en av noderna (till exempel `ssh -l <Storage UserName> <Storage IP Address>` ).
      Skriv `exit` för att logga ut från lagrings meddelandet.

      Microsoft-åtgärder tillhandahåller IP-adress för lagrings användare och lagring vid etablerings tillfället.
  
1. **[Aktivera kommunikation med SAP HANA](#enable-communication-with-sap-hana)** (mer information finns i separata avsnitt): kunden måste konfigurera en lämplig SAP HANA användare med de behörigheter som krävs för att utföra ögonblicks bilden.
   1. Den här inställningen kan testas från kommando raden på följande sätt med hjälp av texten i `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - I exemplen ovan används icke-SSL-kommunikation för att SAP HANA.

## <a name="enable-communication-with-storage"></a>Aktivera kommunikation med lagring

I det här avsnittet beskrivs hur du aktiverar kommunikation med lagring.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Skapa RBAC-tjänstens huvud namn

1. I en Azure Cloud Shell-session kontrollerar du att du är inloggad på den prenumeration där du vill associera med tjänstens huvud namn som standard:

    ```azurecli-interactive
    az account show
    ```

1. Om prenumerationen inte är korrekt använder du

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. Skapa ett huvud namn för tjänsten med Azure CLI enligt följande exempel

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. Detta bör generera utdata som i följande exempel:

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > Det här kommandot tilldelar rollen RBAC-deltagare automatiskt till tjänstens huvud namn på prenumerations nivå. du kan begränsa omfattningen till den specifika resurs gruppen där dina tester kommer att skapa resurserna.

1. Klipp ut och klistra in innehållet i en fil `azureauth.json` som kallas lagrad på samma system som `azacsnap` kommandot och skyddar filen med lämpliga system behörigheter.

### <a name="azure-large-instance"></a>Stor Azure-instans

Kommunikation med lagrings Server delen körs via en krypterad SSH-kanal. Följande exempel steg är till för att ge vägledning om hur du konfigurerar SSH för den här kommunikationen.

1. Ändra `/etc/ssh/ssh_config` filen

    Se följande utdata där `MACs hmac-sha1` raden har lagts till:

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. Skapa ett privat/offentligt nyckel par

    Använd följande exempel kommando för att generera nyckel paret, ange inget lösen ord när du genererar en nyckel.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Skicka den offentliga nyckeln till Microsoft-åtgärder

    Skicka `cat /root/.ssh/id_rsa.pub` kommandots utdata (exempel nedan) till Microsoft-åtgärder för att aktivera ögonblicks bilds verktygen för att kommunicera med underlag rings systemet.

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>Aktivera kommunikation med SAP HANA

Ögonblicks bilds verktygen kommunicerar med SAP HANA och behöver en användare med rätt behörighet för att initiera och släppa databasen Spara-punkt. I följande exempel visas installationen av SAP HANA v2-användaren och `hdbuserstore` för kommunikation till SAP HANA-databasen.

Följande exempel kommandon ställer in en användare (AZACSNAP) i SYSTEMDB på SAP HANA 2.
databas, ändra IP-adress, användar namn och lösen ord efter behov:

1. Anslut till SYSTEMDB för att skapa användaren

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. Skapa användaren

    I det här exemplet skapas AZACSNAP-användaren i SYSTEMDB.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Bevilja användar behörighet

    I det här exemplet anges behörigheten för AZACSNAP-användaren för att utföra en databas konsekvent lagrings ögonblicks bild.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *Valfri* – förhindra att användarens lösen ord upphör att gälla

    > [!NOTE]
    > Kontrol lera med företags principen innan du gör den här ändringen.

   I det här exemplet inaktive ras lösen ordets giltighets tid för AZACSNAP-användaren, utan den här ändringen kommer användarens lösen ord att upphöra att fungera korrekt.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. Konfigurera SAP HANA säkra användar arkivet (ändra lösen ordet) det här exemplet använder `hdbuserstore` kommandot från Linux-gränssnittet för att konfigurera SAP HANA säkra användar lagringen.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. Kontrol lera SAP HANA säkra användar arkivet för att kontrol lera om det säkra användar arkivet är korrekt konfigurerat, Använd `hdbuserstore` kommandot för att lista utdata som liknar följande exempel. Mer information om hur `hdbuserstore` du använder finns på SAP-webbplatsen.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="additional-instructions-for-using-the-log-trimmer-sap-hana-20-and-later"></a>Ytterligare instruktioner för att använda logg trimmern (SAP HANA 2,0 och senare)

Om du använder logg trimmern kan du använda följande exempel kommandon för att skapa en användare (AZACSNAP) i klient databaserna på ett databas system för SAP HANA 2,0. Kom ihåg att ändra IP-adress, användar namn och lösen ord efter behov:

1. Anslut till klient databasen för att skapa användaren, klient information är `<IP_address_of_host>` och `<SYSTEM_USER_PASSWORD>` .  Observera också den port ( `30015` ) som krävs för att kommunicera med klient databasen.

    ```bash
    hdbsql -n <IP_address_of_host>:30015 - i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output  
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql TENANTDB=>
    ```

1. Skapa användaren

    I det här exemplet skapas AZACSNAP-användaren i SYSTEMDB.

    ```sql
    hdbsql TENANTDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Bevilja användar behörighet

    I det här exemplet anges behörigheten för AZACSNAP-användaren för att utföra en databas konsekvent lagrings ögonblicks bild.

    ```sql
    hdbsql TENANTDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *Valfri* – förhindra att användarens lösen ord upphör att gälla

    > [!NOTE]
    > Kontrol lera med företags principen innan du gör den här ändringen.

   I det här exemplet inaktive ras lösen ordets giltighets tid för AZACSNAP-användaren, utan den här ändringen kommer användarens lösen ord att upphöra att fungera korrekt.  

   ```sql
   hdbsql TENANTDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

> [!NOTE]  
> Upprepa de här stegen för alla klient databaser. Det går att hämta anslutnings information för alla klienter som använder följande SQL-fråga mot SYSTEMDB.

```sql
SELECT HOST, SQL_PORT, DATABASE_NAME FROM SYS_DATABASES.M_SERVICES WHERE SQL_PORT LIKE '3%'
```

Se följande exempel fråga och utdata.

```bash
hdbsql -jaxC -n 10.90.0.31:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> " SELECT HOST,SQL_PORT, DATABASE_NAME FROM SYS_DATABASES.M_SERVICES WHERE SQL_PORT LIKE '3%' "
```

```output
sapprdhdb80,30013,SYSTEMDB
sapprdhdb80,30015,H81
sapprdhdb80,30041,H82
```

### <a name="using-ssl-for-communication-with-sap-hana"></a>Använda SSL för kommunikation med SAP HANA

`azacsnap`Verktyget använder SAP HANA `hdbsql` kommando för att kommunicera med SAP HANA. Detta inkluderar användning av SSL-alternativ vid kryptering av kommunikation med SAP HANA. `azacsnap` använder `hdbsql` kommandots SSL-alternativ enligt följande.

Följande används alltid när du använder `azacsnap --ssl` alternativet:

- `-e` -Aktiverar TLS-encryptionTLS/SSL-kryptering. Servern väljer den högsta tillgängliga.
- `-ssltrustcert` -Anger om serverns certifikat ska verifieras.
- `-sslhostnameincert "*"` -Anger värd namnet som används för att verifiera serverns identitet. Om du anger `"*"` som värd namn verifieras inte serverns värdnamn.

SSL-kommunikation kräver också nyckel lager och betrodda Arkiv-filer.  Även om det är möjligt att de här filerna lagras på standard platser på en Linux-installation, för att säkerställa att rätt nyckel material används för de olika SAP HANA-systemen (det vill säga i de fall där olika fil lagrings-och förtroende lager-filer används för varje SAP HANA-system) `azacsnap` förväntar sig att filerna för nyckel lagring och förtroende arkiv lagras på den `securityPath` plats som anges i `azacsnap` konfigurations filen.

#### <a name="key-store-files"></a>Filer för nyckel Arkiv

- Om du använder flera sid med samma nyckel material, är det enklare att skapa länkar till securityPath-platsen som definierats i `azacsnap` konfigurations filen.  Se till att dessa värden finns för varje SID med hjälp av SSL.
  - För OpenSSL:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - För commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- Om du använder flera sid: er med det olika nyckel materialet per SID, kopierar du (eller flyttar och byter namn) filerna till platsen securityPath enligt definitionen i sid- `azacsnap` konfigurationsfilen.
  - För OpenSSL:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - För commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

Vid `azacsnap` anrop läggs `hdbsql` den till på `-sslkeystore=<securityPath>/<SID>_keystore` kommando raden.

#### <a name="trust-store-files"></a>Betrodda Store-filer

- Om du använder flera sid med samma nyckel material skapar du hårda länkar till securityPath-platsen som definieras i `azacsnap` konfigurations filen.  Se till att dessa värden finns för varje SID med hjälp av SSL.
  - För OpenSSL:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - För commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- Om du använder flera sid: er med det olika nyckel materialet per SID, kopierar du (eller flyttar och byter namn) filerna till platsen securityPath enligt definitionen i sid- `azacsnap` konfigurationsfilen.
  - För OpenSSL:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - För commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> `<SID>`Komponenten i fil namnen måste vara SAP HANA system identifierare i versaler (till exempel,, `H80` `PR1` osv.)

Vid `azacsnap` anrop läggs `hdbsql` den till på `-ssltruststore=<securityPath>/<SID>_truststore` kommando raden.

`azacsnap -c test --test hana --ssl openssl`Det innebär att om du kör var `SID` `H80` i konfigurations filen körs `hdbsql` anslutningarna på följande sätt:

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> `\`Specialtecknet är en kommando rad rad brytning för att förbättra skärpan för flera parametrar som skickas på kommando raden.

## <a name="installing-the-snapshot-tools"></a>Installera verktyg för ögonblicks bilder

Det nedladdnings bara själv-installations programmet är utformat för att göra det enkelt att konfigurera och köra ögonblicks bild verktyg med användar rättigheter som inte är rot (till exempel azacsnap). Installations programmet konfigurerar användaren och lägger till ögonblicks bild verktygen i under katalogen användare `$HOME/bin` (standard = `/home/azacsnap/bin` ).

Själv Installer försöker fastställa rätt inställningar och sökvägar för alla filer baserat på konfigurationen för den användare som utför installationen (till exempel root). Om föregående konfigurations steg (aktivera kommunikation med lagring och SAP HANA) kördes som rot, kommer installationen att kopiera den privata nyckeln och `hdbuserstore` till säkerhets kopierings användarens plats. Det är dock möjligt att utföra de steg som möjliggör kommunikation med lagrings Server delen och SAP HANA manuellt utföras av en kunnig administratör efter installationen.

> [!NOTE]
> För tidigare SAP HANA på Azures stora instans installationer var katalogen för förinstallerade ögonblicks bild verktyg `/hana/shared/<SID>/exe/linuxx86_64/hdb` .

När du har slutfört de [nödvändiga stegen](#prerequisites-for-installation) är det nu möjligt att installera verktyg för ögonblicks bilder med hjälp av själv Installer enligt följande:

1. Kopiera den nedladdade själv installationen till mål systemet.
1. Kör själv Installer som `root` användaren, se följande exempel. Om det behövs kan du göra filen körbar med `chmod +x *.run` kommandot.

Om du kör kommandot själv Installer utan några argument visas hjälp om hur du använder installations programmet för att installera verktyg för ögonblicks bilder enligt följande:

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> Själv Installer har ett alternativ för att extrahera (-X) ögonblicks bild verktyg från paketet utan att göra några användare att skapa och konfigurera. På så sätt kan en erfaren administratör slutföra installations stegen manuellt, eller kopiera kommandon för att uppgradera en befintlig installation.

### <a name="easy-installation-of-snapshot-tools-default"></a>Enkel installation av verktyg för ögonblicks bilder (standard)

Installations programmet har utformats för att snabbt installera verktyg för ögonblicks bilder för SAP HANA på Azure. Om installations programmet bara körs med alternativet-I, utförs som standard följande steg:

1. Skapa ögonblicks bild användare ' azacsnap ', Hem Katalog och ange grupp medlemskap.
1. Konfigurera azacsnap-användarens inloggning `~/.profile` .
1. Sök efter kataloger för kataloger som ska läggas till i azacsnap `$PATH` , dessa är vanligt vis Sök vägarna till SAP HANA verktyg, till exempel `hdbsql` och `hdbuserstore` .
1. Sök i fil systemet efter kataloger som ska läggas till i azacsnap `$LD_LIBRARY_PATH` . Många kommandon kräver att en biblioteks Sök väg anges för att kunna köras korrekt, vilket konfigurerar den för den installerade användaren.
1. Kopiera SSH-nycklar för Server dels lagring för azacsnap från rot användaren (användaren som kör installationen). Detta förutsätter att rot användaren redan har konfigurerat anslutningen till lagrings platsen
    - Se avsnittet "[Aktivera kommunikation med lagring](#enable-communication-with-storage)".
1. Kopiera SAP HANA säker användar lagring för mål användaren, azacsnap. Detta förutsätter att rot användaren redan har konfigurerat säker användar lagring – se avsnittet Aktivera kommunikation med SAP HANA.
1. Verktyg för ögonblicks bilder extraheras till `/home/azacsnap/bin/` .
1. Kommandona i `/home/azacsnap/bin/` har sina behörighets uppsättningar (ägarskap och körbara bitar osv.).

I följande exempel visas rätt utdata från installations programmet när du kör med standard alternativet för installation.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>Avinstallera verktyg för ögonblicks bilder

Om verktyg för ögonblicks bilder har installerats med standardinställningarna, behöver avinstallationen bara ta bort användaren att kommandona har installerats för (standard = azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>Manuell installation av verktyg för ögonblicks bilder

I vissa fall är det nödvändigt att installera verktygen manuellt, men rekommendationen är att använda installations programmets standard alternativ för att under lätta den här processen.

Varje rad som börjar med ett `#` Character visar exempel kommandona som följer efter att tecknen körs av rot användaren. I `\` slutet av en rad är standard rad fortsättnings tecken för ett Shell-kommando.

Som rot-superanvändare kan en manuell installation uppnås på följande sätt:

1. Hämta grupp-ID: t för "sapsys", i det här fallet grupp-ID = 1010

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. Skapa ögonblicks bild användare ' azacsnap ', Hem Katalog och ange grupp medlemskap med grupp-ID: t från steg 1.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. Se till att användarens inloggnings-azacsnap `.profile` finns.

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. Sök efter kataloger för kataloger som ska läggas till i azacsnap $PATH, vanligt vis Sök vägarna till SAP HANA verktyg, till exempel `hdbsql` och `hdbuserstore` .

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Lägg till den uppdaterade sökvägen till användarens profil

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Sök i fil systemet efter kataloger som ska läggas till i azacsnap $LD _LIBRARY_PATH.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Lägg till den uppdaterade biblioteks Sök vägen till användarens profil

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. På stora Azure-instanser
    1. Kopiera SSH-nycklar för Server dels lagring för azacsnap från rot användaren (användaren som kör installationen). Detta förutsätter att rot användaren redan har konfigurerat anslutningen till lagrings platsen
       > Se avsnittet "[Aktivera kommunikation med lagring](#enable-communication-with-storage)".

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. Ange användar behörigheterna korrekt för SSH-filerna

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. På Azure NetApp Files
    1. Konfigurera användarens `DOTNET_BUNDLE_EXTRACT_BASE_DIR` sökväg enligt vägledningen för extrahering av filer i .net Core.
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. Kopiera SAP HANA säker användar lagring för mål användaren, azacsnap. Detta förutsätter att rot användaren redan har konfigurerat det säkra användar arkivet.
    > Se avsnittet "[Aktivera kommunikation med SAP HANA](#enable-communication-with-sap-hana)".

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. Ange användar behörigheterna korrekt för `hdbuserstore` filerna

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. Extrahera verktyg för ögonblicks bilder till/Home/azacsnap/bin/.

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. Gör kommando filen körbar

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. Se till att rätt ägande behörighet har angetts för användarens arbets katalog

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>Slutför installationen av verktyg för ögonblicks bilder

Installations programmet innehåller steg för att slutföra när installationen av verktyg för ögonblicks bilder har genomförts.
Följ dessa steg om du vill konfigurera och testa verktyg för ögonblicks bilder.  Efter lyckad testning utför du den första databasen konsekvent lagrings ögonblicks bild.

Följande utdata visar de steg som ska utföras när du har kört installations programmet med standard alternativen för installation:

1. Ändra till ögonblicks bild användar kontot
    1. `su - azacsnap`
1. Konfigurera HANA-säkrat användar Arkiv
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. Ändra till plats för kommandon
   1. `cd /home/azacsnap/bin/`
1. Konfigurera kund informations filen
   1. `azacsnap -c configure –-configuration new`
1. Testa anslutningen till lagringen....
   1. `azacsnap -c test –-test storage`
1. Testa anslutningen till HANA....
    1. utan SSL
       1. `azacsnap -c test –-test hana`
    1. med SSL måste du välja rätt SSL-alternativ
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. Kör din första säkerhets kopia av ögonblicks bilden
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

Steg 2 behövs om "[Aktivera kommunikation med SAP HANA](#enable-communication-with-sap-hana)" inte genomfördes före installationen.

> [!NOTE]
> Test kommandona bör köras på rätt sätt. Annars kan kommandona Miss lyckas.

## <a name="configuring-the-database"></a>Konfigurera databasen

I det här avsnittet beskrivs hur du konfigurerar data basen.

### <a name="sap-hana-configuration"></a>SAP HANA konfiguration

Det finns vissa rekommenderade ändringar som ska tillämpas på SAP HANA för att säkerställa skyddet av logg säkerhets kopior och katalog. Som standard `basepath_logbackup` kommer och att `basepath_catalogbackup` mata ut sina filer till `$(DIR_INSTANCE)/backup/log` katalogen och det är osannolikt att den här sökvägen finns på en volym som `azacsnap` är konfigurerad för ögonblicks bilder. dessa filer skyddas inte av ögonblicks bilder av lagring.

Följande `hdbsql` kommando exempel är avsedda att demonstrera inställningen logg-och katalog Sök vägar till platser som finns på lagrings volymer som kan ögonblicks bilder av `azacsnap` . Se till att kontrol lera värdena på kommando raden matchar den lokala SAP HANAs konfigurationen.

### <a name="configure-log-backup-location"></a>Konfigurera logg säkerhets kopierings plats

I det här exemplet görs ändringen till- `basepath_logbackup` parametern.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>Konfigurera plats för katalog säkerhets kopiering

I det här exemplet görs ändringen till- `basepath_catalogbackup` parametern. Kontrol lera först att `basepath_catalogbackup` sökvägen finns på fil systemet, om du inte skapar sökvägen med samma ägare som katalogen.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Om sökvägen behöver skapas, skapar följande exempel sökvägen och anger rätt ägarskap och behörigheter. De här kommandona måste köras som rot.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

I följande exempel ändras inställningen SAP HANA.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>Sök efter logg-och katalog säkerhets kopierings platser

När du har gjort ändringarna ovan bekräftar du att inställningarna är korrekta med följande kommando.
I det här exemplet visas de inställningar som har angetts enligt anvisningarna ovan som Systeminställningar.

> Den här frågan returnerar även STANDARDINSTÄLLNINGARNA för jämförelse.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>Konfigurera tids gräns för logg säkerhets kopiering

Standardvärdet för SAP HANA att utföra en logg säkerhets kopiering är 900 sekunder (15 minuter). Vi rekommenderar att du minskar värdet till 300 sekunder (det vill säga 5 minuter).  Sedan är det möjligt att köra vanliga säkerhets kopieringar (till exempel var 10: e minut) genom att lägga till log_backups volym i avsnittet annan volym i konfigurations filen.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>Kontrol lera tids gräns för logg säkerhets kopiering

När du har ändrat tids gränsen för logg säkerhets kopieringen kontrollerar du att den har ställts in enligt följande.
I det här exemplet visas de inställningar som har angetts som Systeminställningar, men den här frågan returnerar även STANDARDINSTÄLLNINGARNA för jämförelse.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure Application enhetligt ögonblicks bild verktyget](azacsnap-cmd-ref-configure.md)
