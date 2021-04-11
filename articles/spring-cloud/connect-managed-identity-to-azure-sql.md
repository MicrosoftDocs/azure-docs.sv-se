---
title: Använd hanterad identitet för att ansluta Azure SQL till Azure våren Cloud-appen
description: Konfigurera hanterad identitet för att ansluta Azure SQL till en Azure våren Cloud-App.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 3f1cde0bf233c67d02b9b7266ce3b3c8a3696db8
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123457"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Använda en hanterad identitet för att ansluta Azure SQL Database till en Azure våren Cloud-App

**Den här artikeln gäller för:** ✔️ Java

Den här artikeln visar hur du skapar en hanterad identitet för en Azure våren Cloud-App och använder den för att få åtkomst till Azure SQL Database.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) är den intelligenta, skalbara, Relations databas tjänst som är byggd för molnet. Det är alltid uppdaterat, med AI-drivna och automatiserade funktioner som optimerar prestanda och hållbarhet. Lagrings alternativ utan serverns beräknings-och skalnings alternativ skalar automatiskt resurser på begäran, så att du kan fokusera på att skapa nya program utan att behöva bekymra dig om lagrings storlek eller resurs hantering.

## <a name="prerequisites"></a>Förutsättningar
I det här exemplet används följande resurser.
* Följ [själv studie kursen om vår JPA](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) för att etablera ett Azure SQL Database och få det att fungera med en Java-app lokalt
* Följ [självstudierna Azure våren Cloud system-tilldelade hanterade identiteter](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) för att etablera en Azure våren Cloud-App med mi aktiverat

## <a name="grant-permission-to-the-managed-identity"></a>Bevilja behörighet till den hanterade identiteten
Anslut till SQL Server och kör följande SQL-fr åga:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

Detta <MIName> följer regeln: `<service instance name>/apps/<app name>` , t. ex. myspringcloud/Apps/sqldemo. Du kan också fråga MIName med Azure CLI:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Konfigurera Java-appen så att den använder hanterad identitet
Öppna `src/main/resources/application.properties` filen och Lägg till `Authentication=ActiveDirectoryMSI;` i slutet av följande rad. Se till att du använder rätt värde för $AZ _DATABASE_NAME variabel.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Bygg och distribuera appen till Azure våren Cloud
Återskapa appen och distribuera den till Azure våren Cloud-appen som tillhandahålls på den andra punkten under krav. Nu har du ett våren Boot-program som autentiserats av en hanterad identitet som använder JPA för att lagra och hämta data från en Azure SQL Database i Azure våren Cloud.

## <a name="next-steps"></a>Nästa steg

* [Så här kommer du åt Storage BLOB med hanterad identitet i Azure våren Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Så här aktiverar du systemtilldelad hanterad identitet för Azure våren Cloud Application](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Lär dig mer om hanterade identiteter för Azure-resurser](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Autentisera Azure våren Cloud med Key Vault i GitHub-åtgärder](./spring-cloud-github-actions-key-vault.md)