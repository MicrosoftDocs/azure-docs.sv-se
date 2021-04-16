---
title: CLI-exempel
description: Hitta Azure CLI-exempel för några av de App Service scenarierna. Lär dig hur du automatiserar App Service distribution eller hanteringsuppgifter.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 07/07/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 51cdd470c9ae970d5945f8d5399cea67a44037f0
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483392"
---
# <a name="cli-samples-for-azure-app-service"></a>CLI-exempel för Azure App Service

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| Skript | Beskrivning |
|-|-|
|**Skapa app**||
| [Skapa en app och distribuera filer med FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app och distribuerar en fil till den via FTP. |
| [Skapa en app och distribuera kod från GitHub](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app och distribuerar kod från en offentlig GitHub-lagringsplats. |
| [Skapa en app med kontinuerlig distribution från GitHub](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app med kontinuerlig publicering från en GitHub-lagringsplats som du äger. |
| [Skapa en app och distribuera kod från en lokal Git-lagringsplats](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app och konfigurerar kodöverföring från en lokal Git-lagringsplats. |
| [Skapa en app och distribuera kod till en mellanlagringsmiljö](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app med ett distributionsfack för att mellanlagra kodändringar. |
| [Skapa en ASP.NET Core-app i en Docker-container](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app i Linux och läser in en Docker-avbildning från Docker Hub. |
| [Skapa en app och exponera den med en privat slutpunkt](./scripts/cli-deploy-privateendpoint.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service app och en privat slutpunkt |
|**Konfigurera app**||
| [Mappa en anpassad domän till en app](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app och mappar ett anpassat domännamn till den. |
| [Binda ett anpassat TLS/SSL-certifikat till en app](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service app och binder TLS/SSL-certifikatet för ett anpassat domännamn till den. |
|**Skalningsapp**||
| [Skala en app manuellt](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app och skalar den över 2 instanser. |
| [Skala en app globalt med en arkitektur för hög tillgänglighet](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar två App Service-appar i två olika geografiska regioner och gör dem tillgängliga via en enskild slutpunkt med Azure Traffic Manager. |
|**Skydda app**||
| [Integrera med Azure Application Gateway](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service app och integrerar den med hjälp Application Gateway tjänstslutpunkt och åtkomstbegränsningar. |
|**Anslut app till resurser**||
| [Anslut en webbapp till en SQL-databas](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service app och en databas i Azure SQL Database och lägger sedan till databasanslutningssträngen i appinställningarna. |
| [Anslut en app till ett lagringskonto](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app och ett lagringskonto och lägger sedan till lagringsanslutningssträngen till appinställningarna. |
| [Ansluta en app till en Azure Cache for Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app och en Azure Cache for Redis och lägger sedan till Redis-anslutningsinformationen till appinställningarna.) |
| [Ansluta en app till Azure Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app och en Cosmos DB och lägger sedan till Cosmos DB-anslutningsinformationen till appinställningarna. |
|**Säkerhetskopiera och återställa app**||
| [Säkerhetskopiera en app](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app och en engångssäkerhetskopia för den. |
| [Skapa en schemalagd säkerhetskopiering för en app](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app och skapar en schemalagd säkerhetskopia för den. |
| [Återställer en app från en säkerhetskopia](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Återställer en App Service-app från en säkerhetskopia. |
|**Övervaka app**||
| [Övervaka en appA med webbserverloggar](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app, aktiverar loggning för den och hämtar loggarna till den lokala datorn. |
| | |
