---
title: Använd Azure Active Directory-Azure Database for MySQL
description: Lär dig hur du konfigurerar Azure Active Directory (Azure AD) för autentisering med Azure Database for MySQL
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 808c3589ba5b51b035ccc8165489c4d11203dd66
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612236"
---
# <a name="use-azure-active-directory-for-authentication-with-mysql"></a>Använda Azure Active Directory för autentisering med MySQL

I den här artikeln får du stegvisa anvisningar om hur du konfigurerar Azure Active Directory åtkomst med Azure Database for MySQL och hur du ansluter med hjälp av en Azure AD-token.

> [!IMPORTANT]
> Azure Active Directory autentisering är endast tillgängligt för MySQL 5,7 och senare.

## <a name="setting-the-azure-ad-admin-user"></a>Ange Azure AD admin-användare

Endast en Azure AD admin-användare kan skapa/Aktivera användare för Azure AD-baserad autentisering. Följ stegen nedan om du vill skapa en Azure AD admin-användare.

1. I Azure Portal väljer du den instans av Azure Database for MySQL som du vill aktivera för Azure AD.
2. Under Inställningar väljer du Active Directory administratör:

![Ange Azure AD-administratör][2]

3. Välj en giltig Azure AD-användare i kund klienten som Azure AD-administratör.

> [!IMPORTANT]
> När du anger administratören läggs en ny användare till i Azure Database for MySQL-servern med fullständig administratörs behörighet.

Det går bara att skapa en Azure AD-administratör per MySQL-server och valet av en annan för att skriva över den befintliga Azure AD-administratören som kon figurer ATS för servern.

När du har konfigurerat administratören kan du nu logga in:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Ansluta till Azure Database for MySQL med hjälp av Azure AD

Följande diagram på hög nivå sammanfattar arbets flödet för att använda Azure AD-autentisering med Azure Database for MySQL:

![autentiseringspaket][1]

Vi har utformat Azure AD-integreringen så att den fungerar med vanliga MySQL-verktyg som MySQL CLI, som inte är Azure AD-medvetna och som endast stöder användar namn och lösen ord vid anslutning till MySQL. Vi skickar Azure AD-token till det lösen ord som visas i bilden ovan.

Vi har för närvarande testat följande klienter:

- MySQLWorkbench 
- MySQL CLI

Vi har även testat de flesta vanliga program driv rutiner, men du kan se information i slutet av den här sidan.

Detta är de steg som en användare/ett program behöver för att autentisera med Azure AD som beskrivs nedan:

### <a name="prerequisites"></a>Förutsättningar

Du kan följa med i Azure Cloud Shell, en virtuell Azure-dator eller på den lokala datorn. Kontrol lera att du har [installerat Azure CLI](/cli/azure/install-azure-cli).

### <a name="step-1-authenticate-with-azure-ad"></a>Steg 1: autentisera med Azure AD

Börja med att autentisera med Azure AD med hjälp av Azure CLI-verktyget. Det här steget krävs inte i Azure Cloud Shell.

```
az login
```

Kommandot startar ett webbläsarfönster på sidan Azure AD-autentisering. Du måste ange ditt användar-ID och lösen ord för Azure AD.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Steg 2: Hämta Azure AD-åtkomsttoken

Anropa Azure CLI-verktyget för att hämta en åtkomsttoken för Azure AD-autentiserad användare från steg 1 för att få åtkomst till Azure Database for MySQL.

Exempel (för offentligt moln):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Resurs svärdet ovan måste anges exakt som det visas. För andra moln kan resurs värdet slås upp med:

```azurecli-interactive
az cloud show
```

För Azure CLI version 2.0.71 och senare kan kommandot anges i följande bekväma version för alla moln:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

När autentiseringen är klar returnerar Azure AD en åtkomsttoken:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Token är en bas 64-sträng som kodar all information om den autentiserade användaren och som är avsedd för den Azure Database for MySQL tjänsten.

> [!NOTE]
> Giltighet för åtkomsttoken är mellan 5 minuter och 60 minuter. Vi rekommenderar att du hämtar åtkomsttoken precis innan du initierar inloggningen till Azure Database for MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Steg 3: Använd token som lösen ord för att logga in med MySQL

När du ansluter måste du använda åtkomsttoken som MySQL-användarens lösen ord. När du använder GUI-klienter som MySQLWorkbench kan du använda metoden ovan för att hämta token. 

När du använder CLI kan du använda den här kort handen för att ansluta: 

**Exempel (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Viktiga överväganden vid anslutning:

* `user@tenant.onmicrosoft.com` är namnet på den Azure AD-användare eller-grupp som du försöker ansluta som
* Lägg alltid till Server namnet efter namn på Azure AD-användare/grupp (t. ex. `@mydb` )
* Se till att använda det exakta sättet som användar-eller grupp namnet i Azure AD är stavat
* Användar-och grupp namn i Azure AD är Skift läges känsliga
* Använd endast grupp namnet (t. ex.) när du ansluter som en grupp. `GroupName@mydb`
* Om namnet innehåller blank steg ska du använda `\` före varje blank steg för att kringgå det

Observera inställningen Aktivera-klartext-plugin-program – du måste använda en liknande konfiguration med andra klienter för att se till att token skickas till servern utan att hashas.

Du autentiseras nu till MySQL-servern med Azure AD-autentisering.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Skapa Azure AD-användare i Azure Database for MySQL

Om du vill lägga till en Azure AD-användare till din Azure Database for MySQL databas utför du följande steg efter att du har anslutit (se senare avsnitt om hur du ansluter):

1. Se först till att Azure AD-användaren `<user>@yourtenant.onmicrosoft.com` är en giltig användare i Azure AD-klienten.
2. Logga in på Azure Database for MySQL-instansen som administratörs användare för Azure AD.
3. Skapa användare `<user>@yourtenant.onmicrosoft.com` i Azure Database for MySQL.

**Exempel:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

För användar namn som överstiger 32 tecken rekommenderar vi att du använder ett alias i stället för att användas vid anslutning: 

Exempel:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Att autentisera en användare via Azure AD ger inte användaren behörighet att komma åt objekt i den Azure Database for MySQL databasen. Du måste bevilja användaren de behörigheter som krävs manuellt.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Skapa Azure AD-grupper i Azure Database for MySQL

Om du vill aktivera en Azure AD-grupp för åtkomst till databasen använder du samma mekanism som för användarna, men anger i stället grupp namnet:

**Exempel:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

När du loggar in kommer medlemmarna i gruppen att använda sina personliga åtkomsttoken, men signera med det grupp namn som anges som användar namn.

## <a name="token-validation"></a>Verifiering av token

Azure AD-autentisering i Azure Database for MySQL säkerställer att användaren finns i MySQL-servern och kontrollerar giltigheten för token genom att verifiera innehållet i token. Följande verifierings steg för token utförs:

-   Token har signerats av Azure AD och har inte manipulerats
-   Token utfärdades av Azure AD för den klient som är associerad med servern
-   Token har inte gått ut
-   Token för Azure Database for MySQL resurs (och inte en annan Azure-resurs)

## <a name="compatibility-with-application-drivers"></a>Kompatibilitet med program driv rutiner

De flesta driv rutiner stöds, men se till att använda inställningarna för att skicka lösen ordet i klartext, så att token skickas utan ändringar.

* C/C++
  * libmysqlclient: stöds
  * MySQL-Connector-c + +: stöds
* Java
  * Connector/J (MySQL-Connector-Java): stöds, måste använda `useSSL` inställning
* Python
  * Koppling/python: stöds
* Ruby
  * mysql2: stöds
* .NET
  * MySQL-Connector-net: stöds, du måste lägga till plugin-programmet för mysql_clear_password
  * MySQL-net/MySqlConnector: stöds
* Node.js
  * mysqljs: stöds inte (skickar inte token i klartext utan korrigering)
  * Node-mysql2: stöds
* Perl
  * DBD:: MySQL: stöds
  * NET:: MySQL: stöds inte
* Go
  * Go-SQL-driv rutin: stöds, Lägg till `?tls=true&allowCleartextPasswords=true` i anslutnings sträng

## <a name="next-steps"></a>Nästa steg

* Granska de övergripande begreppen för [Azure Active Directory autentisering med Azure Database for MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
