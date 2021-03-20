---
title: Använd Azure Active Directory-Azure Database for PostgreSQL-enskild server
description: Lär dig hur du konfigurerar Azure Active Directory (AAD) för autentisering med Azure Database for PostgreSQL-enskild server
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 178c339f6f47569160a9a748794678c610f35734
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87171644"
---
# <a name="use-azure-active-directory-for-authentication-with-postgresql"></a>Använd Azure Active Directory för autentisering med PostgreSQL

I den här artikeln får du stegvisa anvisningar om hur du konfigurerar Azure Active Directory åtkomst med Azure Database for PostgreSQL och hur du ansluter med hjälp av en Azure AD-token.

## <a name="setting-the-azure-ad-admin-user"></a>Ange Azure AD admin-användare

Endast Azure AD Administrator-användare kan skapa/Aktivera användare för Azure AD-baserad autentisering. Vi rekommenderar att du inte använder Azure AD-administratören för vanliga databas åtgärder eftersom den har utökade användar behörigheter (t. ex. CREATEDB).

Om du vill ställa in Azure AD-administratören (du kan använda en användare eller en grupp) följer du stegen nedan

1. I Azure Portal väljer du den instans av Azure Database for PostgreSQL som du vill aktivera för Azure AD.
2. Under Inställningar väljer du Active Directory administratör:

![Ange Azure AD-administratör][2]

3. Välj en giltig Azure AD-användare i kund klienten som Azure AD-administratör.

> [!IMPORTANT]
> När du anger administratören läggs en ny användare till i Azure Database for PostgreSQL-servern med fullständig administratörs behörighet. Azure AD admin-användaren i Azure Database for PostgreSQL kommer att ha rollen `azure_ad_admin` .

Det går bara att skapa en Azure AD-administratör per PostgreSQL-Server och valet av en annan för att skriva över den befintliga Azure AD-administratören som kon figurer ATS för servern. Du kan ange en Azure AD-grupp i stället för en enskild användare som har flera administratörer. Observera att du sedan loggar in med grupp namnet i administrations syfte.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Ansluta till Azure Database for PostgreSQL med hjälp av Azure AD

Följande diagram på hög nivå sammanfattar arbets flödet för att använda Azure AD-autentisering med Azure Database for PostgreSQL:

![autentiseringspaket][1]

Vi har utformat Azure AD-integrationen för att arbeta med vanliga PostgreSQL-verktyg som psql, som inte är Azure AD-medvetna och som endast stöder användar namn och lösen ord när du ansluter till PostgreSQL. Vi skickar Azure AD-token till det lösen ord som visas i bilden ovan.

Vi har för närvarande testat följande klienter:

- psql kommando rad (Använd PGPASSWORD-variabeln för att skicka token, se nedan)
- Azure Data Studio (med PostgreSQL-tillägget)
- Andra libpq-baserade klienter (t. ex. vanliga program ramverk och ORMs)

> [!NOTE]
> Tänk på att det inte finns stöd för att använda Azure AD-token med pgAdmin eftersom den har en hårdkodad begränsning på 256 tecken för lösen ord (som token överskrider).

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

Anropa Azure CLI-verktyget för att hämta en åtkomsttoken för Azure AD-autentiserad användare från steg 1 för att få åtkomst till Azure Database for PostgreSQL.

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

Token är en bas 64-sträng som kodar all information om den autentiserade användaren och som är avsedd för den Azure Database for PostgreSQL tjänsten.

> [!NOTE]
> Giltighet för åtkomsttoken är mellan 5 minuter och 60 minuter. Vi rekommenderar att du hämtar åtkomsttoken precis innan du initierar inloggningen till Azure Database for PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Steg 3: Använd token som lösen ord för att logga in med PostgreSQL

När du ansluter måste du använda åtkomsttoken som användar lösen ord för PostgreSQL.

När du använder `psql` kommando rads klienten måste åtkomsttoken skickas via `PGPASSWORD` miljövariabeln, eftersom åtkomsttoken överskrider lösen ords längden som `psql` kan acceptera direkt:

Windows-exempel:

```cmd
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

```PowerShell
$env:PGPASSWORD='<copy/pasted TOKEN value from step 2>'
```

Linux/macOS-exempel:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Nu kan du starta en anslutning med Azure Database for PostgreSQL som du normalt skulle göra:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Viktiga överväganden vid anslutning:

* `user@tenant.onmicrosoft.com` är namnet på den Azure AD-användare eller-grupp som du försöker ansluta som
* Lägg alltid till Server namnet efter namn på Azure AD-användare/grupp (t. ex. `@mydb` )
* Se till att använda det exakta sättet som användar-eller grupp namnet i Azure AD är stavat
* Användar-och grupp namn i Azure AD är Skift läges känsliga
* Använd endast grupp namnet (t. ex.) när du ansluter som en grupp. `GroupName@mydb`
* Om namnet innehåller blank steg ska du använda `\` före varje blank steg för att kringgå det

Du autentiseras nu till din PostgreSQL-server med Azure AD-autentisering.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Skapa Azure AD-användare i Azure Database for PostgreSQL

Om du vill lägga till en Azure AD-användare till din Azure Database for PostgreSQL databas utför du följande steg efter att du har anslutit (se senare avsnitt om hur du ansluter):

1. Se först till att Azure AD-användaren `<user>@yourtenant.onmicrosoft.com` är en giltig användare i Azure AD-klienten.
2. Logga in på Azure Database for PostgreSQL-instansen som administratörs användare för Azure AD.
3. Skapa roll `<user>@yourtenant.onmicrosoft.com` i Azure Database for PostgreSQL.
4. Gör `<user>@yourtenant.onmicrosoft.com` en medlem i roll azure_ad_user. Detta måste bara ges till Azure AD-användare.

**Exempel:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Att autentisera en användare via Azure AD ger inte användaren behörighet att komma åt objekt i den Azure Database for PostgreSQL databasen. Du måste bevilja användaren de behörigheter som krävs manuellt.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Skapa Azure AD-grupper i Azure Database for PostgreSQL

Om du vill aktivera en Azure AD-grupp för åtkomst till databasen använder du samma mekanism som för användarna, men anger i stället grupp namnet:

**Exempel:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

När du loggar in kommer medlemmarna i gruppen att använda sina personliga åtkomsttoken, men signera med det grupp namn som anges som användar namn.

## <a name="token-validation"></a>Verifiering av token

Azure AD-autentisering i Azure Database for PostgreSQL säkerställer att användaren finns på PostgreSQL-servern och kontrollerar giltigheten för token genom att verifiera innehållet i token. Följande verifierings steg för token utförs:

- Token har signerats av Azure AD och har inte manipulerats
- Token utfärdades av Azure AD för den klient som är associerad med servern
- Token har inte gått ut
- Token för Azure Database for PostgreSQL resurs (och inte en annan Azure-resurs)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migrera befintliga PostgreSQL-användare till Azure AD-baserad autentisering

Du kan aktivera Azure AD-autentisering för befintliga användare. Det finns två fall att tänka på:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Fall 1: PostgreSQL användar namn matchar användarens huvud namn för Azure AD

I det osannolika fallet att dina befintliga användare redan matchar användar namnen i Azure AD kan du ge dem `azure_ad_user` rollen för att aktivera dem för Azure AD-autentisering:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

De kan nu logga in med autentiseringsuppgifter för Azure AD i stället för att använda deras tidigare konfigurerade PostgreSQL-lösenord.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Fall 2: PostgreSQL användar namn skiljer sig från användar huvud namnet för Azure AD

Om en PostgreSQL-användare antingen inte finns i Azure AD eller har ett annat användar namn kan du använda Azure AD-grupper för att autentisera som den här PostgreSQL-användaren. Du kan migrera befintliga Azure Database for PostgreSQL användare till Azure AD genom att skapa en Azure AD-grupp med ett namn som matchar PostgreSQL-användaren och sedan bevilja roll azure_ad_user till den befintliga PostgreSQL-användaren:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Detta förutsätter att du har skapat en grupp "DBReadUser" i din Azure AD. Användare som tillhör gruppen kommer nu att kunna logga in på databasen som den här användaren.

## <a name="next-steps"></a>Nästa steg

* Granska de övergripande begreppen för [Azure Active Directory-autentisering med Azure Database for PostgreSQL-enskild server](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
