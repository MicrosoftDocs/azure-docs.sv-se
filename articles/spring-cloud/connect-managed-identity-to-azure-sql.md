---
title: Använda hanterad identitet för att ansluta Azure SQL till Azure Spring Cloud appen
description: Konfigurera hanterad identitet för att ansluta Azure SQL till en Azure Spring Cloud app.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: ed729dde51316b9a67f396e3f7de3d7d9f6d4568
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378796"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Använda en hanterad identitet för att ansluta Azure SQL Database till en Azure Spring Cloud app

**Den här artikeln gäller för:** ✔️ Java

Den här artikeln visar hur du skapar en hanterad identitet för en Azure Spring Cloud-app och använder den för att komma Azure SQL Database.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) är den intelligenta, skalbara relationsdatabastjänsten som skapats för molnet. Den är alltid uppdaterad med AI-baserade och automatiserade funktioner som optimerar prestanda och hållbarhet. Serverlös beräkning och hyperskalalagringsalternativ skalar automatiskt resurser på begäran, så att du kan fokusera på att skapa nya program utan att behöva bekymra dig om lagringsstorlek eller resurshantering.

## <a name="prerequisites"></a>Förutsättningar
I det här exemplet används följande resurser.
* Följ [självstudien om Spring Data JPA](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) för att etablera Azure SQL Database och få det att fungera med en Java-app lokalt
* Följ [självstudien Azure Spring Cloud system tilldelad hanterad identitet för](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) att etablera en Azure Spring Cloud-app med MI aktiverat

## <a name="grant-permission-to-the-managed-identity"></a>Bevilja behörighet till den hanterade identiteten
Anslut till SQL-servern och kör följande SQL-fråga:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

Detta <MIName> följer regeln: `<service instance name>/apps/<app name>` , t.ex. myspringcloud/apps/sqldemo. Du kan också fråga MIName med Azure CLI:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Konfigurera din Java-app för att använda hanterad identitet
Öppna `src/main/resources/application.properties` filen och lägg till `Authentication=ActiveDirectoryMSI;` i slutet av följande rad. Se till att använda rätt värde för variabeln $AZ_DATABASE_NAME.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Skapa och distribuera appen till Azure Spring Cloud
Återskapa appen och distribuera den till Azure Spring Cloud som etablerats i den andra punkten under Krav. Nu har du ett Spring Boot-program, autentiserat av en hanterad identitet, som använder JPA för att lagra och hämta data från en Azure SQL Database i Azure Spring Cloud.

## <a name="next-steps"></a>Nästa steg

* [Så här kommer du åt Lagringsblob med hanterad identitet i Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Så här aktiverar du system tilldelad hanterad identitet för Azure Spring Cloud program](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Läs mer om hanterade identiteter för Azure-resurser](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Autentisera Azure Spring Cloud med Key Vault i GitHub Actions](./spring-cloud-github-actions-key-vault.md)