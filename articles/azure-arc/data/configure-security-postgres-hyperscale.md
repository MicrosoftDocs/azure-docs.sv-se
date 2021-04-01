---
title: Konfigurera säkerhet för din Azure-Arc-aktiverade PostgreSQL-hyperskala-servergrupp
description: Konfigurera säkerhet för din Azure-Arc-aktiverade PostgreSQL-hyperskala-servergrupp
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d6e27fddceb69efbb2c1697c09ee9b61d7f38ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687982"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Konfigurera säkerhet för din Azure-Arc-aktiverade PostgreSQL-hyperskala-servergrupp

I det här dokumentet beskrivs olika aspekter relaterade till säkerheten i Server gruppen:
- Kryptering i vila
- Användarhantering
   - Allmänna perspektiv
   - Ändra lösen ordet för den administrativa användaren _postgres_
- Granska

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>Kryptering i vila
Du kan implementera kryptering i vila antingen genom att kryptera diskarna där du lagrar databaserna och/eller genom att använda databas funktioner för att kryptera de data som du infogar eller uppdaterar.

### <a name="hardware-linux-host-volume-encryption"></a>Maskin vara: värd volym kryptering i Linux
Implementera system data kryptering för att skydda alla data som finns på diskarna som används av Azure-bågen som är aktiverat Data Services installationen. Du kan läsa mer om det här avsnittet:
- [Data kryptering i vila](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) i Linux i allmänhet 
- Disk kryptering med LUKS- `cryptsetup` kryptering (Linux) ( https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) särskilt sedan Azure Arc-aktiverad Data Services körs på den fysiska infrastruktur som du anger är du ansvarig för att skydda infrastrukturen.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>Program vara: Använd PostgreSQL- `pgcrypto` tillägget i Server gruppen
Förutom att kryptera diskarna som används som värd för din Azure Arc-konfiguration, kan du konfigurera din Azure Arc-aktiverad PostgreSQL-Server grupp för att exponera mekanismer som dina program kan använda för att kryptera data i dina databaser. `pgcrypto`Tillägget är en del av `contrib` postgres-tilläggen och är tillgänglig i den postgresql för den storskaliga gruppen i Azure-bågen. Du hittar information om `pgcrypto` tillägget [här](https://www.postgresql.org/docs/current/pgcrypto.html).
I sammanfattning, med följande kommandon, aktiverar du tillägget, du skapar det och använder det:


#### <a name="create-the-pgcrypto-extension"></a>Skapa `pgcrypto` tillägget
Anslut till din server grupp med det klient verktyg du väljer och kör standard frågan för PostgreSQL:
```console
CREATE EXTENSION pgcrypto;
```

> Du hittar information [här](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) om hur du ansluter.

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>Kontrol lera listan över de tillägg som är klara att användas i Server gruppen
Du kan kontrol lera att `pgcrypto` tillägget är klart att användas genom att lista de tillägg som är tillgängliga i Server gruppen.
Anslut till din server grupp med det klient verktyg du väljer och kör standard frågan för PostgreSQL:
```console
select * from pg_extension;
```
Du bör se `pgcrypto` om du har aktiverat och skapat det med de kommandon som anges ovan.

#### <a name="use-the-pgcrypto-extension"></a>Använd `pgcrypto` tillägget
Nu kan du justera koden för dina program så att de använder någon av funktionerna som erbjuds av `pgcrypto` :
- Allmänna hash-funktioner
- Hash-funktioner för lösen ord
- PGP-krypterings funktioner
- Funktioner för RAW-kryptering
- Slumpmässiga data funktioner

Till exempel för att generera hash-värden. Kör kommandot:

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

Returnerar följande hash:

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

Eller, till exempel:

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

returnerar följande hash:

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

Eller till exempel för att lagra krypterade data som ett lösen ord:

Vi antar att mitt program lagrar hemligheter i följande tabell:

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

Och jag krypterar lösen ordet när de skapar en användare:

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

Nu ser jag att mitt lösen ord är krypterat:

```console
select * from mysecrets;
```

Utdata:

- USERid: 1
- Användar namn: mig
- USERpassword: $1 $ Uc7jzZOp $ NTfcGo7F10zGOkXOwjHy31

När jag ansluter till mitt program och anger ett lösen ord, kommer det att sökas i `mysecrets` tabellen och returnerar namnet på användaren om det finns en matchning mellan det lösen ord som har angetts för programmet och de lösen ord som lagras i tabellen. Exempel:

- Jag skickar fel lösen ord:
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Utdata 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- Jag skickar rätt lösen ord:

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   Utdata:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

Det här lilla exemplet visar att du kan kryptera data i vila (lagra krypterade data) i Azure Arc-aktiverade PostgreSQL med postgres- `pgcrypto` tillägget och dina program kan använda funktioner som erbjuds av `pgcrypto` för att manipulera dessa krypterade data.

## <a name="user-management"></a>Användarhantering
### <a name="general-perspectives"></a>Allmänna perspektiv
Du kan använda standard metoden postgres för att skapa användare eller roller. Men om du gör det kommer de här artefakterna bara att vara tillgängliga i koordinatorrollen. I förhandsversionen kommer dessa användare/roller inte ännu att kunna komma åt data som distribueras utanför koordinatornoden och i arbetsnoderna i din servergrupp. Orsaken är att användardefinitionen inte replikeras till arbetsnoderna i förhandsversionen.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>Ändra lösen ordet för den administrativa användaren _postgres_
Azure Arc Enabled PostgreSQL-skalning levereras med den postgres administrativa användar _postgres_ som du anger lösen ordet för när du skapar din server grupp.
Det allmänna formatet för kommandot att ändra lösen ordet är:
```console
azdata arc postgres server edit --name <server group name> --admin-password
```

Där `--admin-password` är ett booleskt värde som relaterar till förekomsten av ett värde i miljövariabeln AZDATA_PASSWORD **session** .
Om variabeln AZDATA_PASSWORD **session** -miljön finns och har ett värde, anger du lösen ordet för postgres-användaren till värdet för den här miljövariabeln om du kör kommandot ovan.

Om variabeln **AZDATA_PASSWORD-** miljövariabeln finns men inte har värdet eller om variabeln AZDATA_PASSWORD **session** Environment inte finns, uppmanas användaren att ange ett lösen ord interaktivt med kommandot ovan.

#### <a name="change-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>Ändra lösen ordet för den administrativa postgres-användaren på ett interaktivt sätt

1. Ta bort variabeln AZDATA_PASSWORD **sessions** miljö eller ta bort dess värde
2. Kör kommandot:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   Till exempel
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   Du uppmanas att ange lösen ordet och bekräfta det:
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   När lösen ordet uppdateras visar kommandots utdata:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="change-the-password-of-the-postgres-administrative-user-using-the-azdata_password-session-environment-variable"></a>Ändra lösen ordet för den administrativa postgres-användaren med hjälp av variabeln AZDATA_PASSWORD **session** -miljö:
1. Ange värdet för den AZDATA_PASSWORD **sessionens** miljö variabel som du vill att lösen ordet ska vara.
2. Kör kommandot:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   Till exempel
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   
   När lösen ordet uppdateras visar kommandots utdata:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> För att kontrol lera om den AZDATA_PASSWORD sessionens miljö variabel finns och vilket värde den har, kör du:
> - På en Linux-klient:
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - På en Windows-klient med PowerShell:
> ```console
> echo $env:AZDATA_PASSWORD
> ```

## <a name="audit"></a>Granska

För gransknings scenarier konfigurerar du Server gruppen att använda `pgaudit` tilläggen för postgres. För mer information om `pgaudit` se [ `pgAudit` GitHub-projekt](https://github.com/pgaudit/pgaudit/blob/master/README.md). Om du vill aktivera `pgaudit` tillägget i Server gruppen läser du [Använd postgresql-tillägg](using-extensions-in-postgresql-hyperscale-server-group.md).


## <a name="next-steps"></a>Nästa steg
- Se [ `pgcrypto` tillägg](https://www.postgresql.org/docs/current/pgcrypto.html)
- Se [använda postgresql-tillägg](using-extensions-in-postgresql-hyperscale-server-group.md)

