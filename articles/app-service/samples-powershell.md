---
title: PowerShell-exempel
description: Hitta Azure PowerShell exempel för några vanliga App Service scenarier. Lär dig hur du automatiserar App Service distributions-eller hanterings uppgifter.
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 07/07/2020
ms.custom: mvc
ms.openlocfilehash: a1577d42de9a4452467a448a0de5cd5f9575a55f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169433"
---
# <a name="powershell-samples-for-azure-app-service"></a>PowerShell-exempel för Azure App Service

Följande tabell innehåller länkar till PowerShell-skript som skapats med Azure PowerShell.

| Skript | Beskrivning |
|-|-|
|**Skapa app**||
| [Skapa en app med distribution från GitHub](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en App Service-app som hämtar kod från GitHub. |
| [Skapa en app med kontinuerlig distribution från GitHub](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en App Service-app som kontinuerligt distribuerar kod från GitHub. |
| [Skapa en app och distribuera kod med FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app och laddar upp filer från en lokal katalog med hjälp av FTP. |
| [Skapa en app och distribuera kod från en lokal Git-lagringsplats](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app och konfigurerar kodöverföring från en lokal Git-lagringsplats. |
| [Skapa en app och distribuera kod till en mellanlagringsmiljö](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app med ett distributionsfack för att mellanlagra kodändringar. |
|  [Skapa en app och exponera din app med en privat slut punkt](./scripts/powershell-deploy-private-endpoint.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app med en privat slut punkt. |
|**Konfigurera app**||
| [Mappa en anpassad domän till en app](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en App Service-app och mappar ett anpassat domännamn till den. |
| [Binda ett anpassat TLS/SSL-certifikat till en app](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en App Service-app och binder TLS/SSL-certifikatet för ett anpassat domän namn till den. |
|**Skalningsapp**||
| [Skala en app manuellt](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app och skalar den över 2 instanser. |
| [Skala en app globalt med en arkitektur för hög tillgänglighet](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar två App Service-appar i två olika geografiska regioner och gör dem tillgängliga via en enskild slutpunkt med Azure Traffic Manager. |
|**Anslut app till resurser**||
| [Anslut en webbapp till en SQL-databas](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en App Service app och en databas i Azure SQL Database och lägger sedan till databas anslutnings strängen i appens inställningar. |
| [Anslut en app till ett lagringskonto](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en App Service-app och ett lagringskonto och lägger sedan till lagringsanslutningssträngen till appinställningarna. |
|**Säkerhetskopiera och återställ app**||
| [Säkerhetskopiera en app](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app och en engångssäkerhetskopia för den. |
| [Skapa en schemalagd säkerhetskopiering för en app](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app och skapar en schemalagd säkerhetskopia för den. |
| [Ta bort en säkerhetskopia för en app](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tar bort en befintlig säkerhetskopia för en app. |
| [Återställ en app från säkerhetskopia](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Återställer en app från en tidigare slutförd säkerhetskopia. |
| [Återställ en säkerhetskopia över prenumerationer](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Återställer en webbapp från en säkerhetskopia i en annan prenumeration. |
|**Övervaka app**||
| [Övervaka en app med webbserverloggar](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app, aktiverar loggning för den och hämtar loggarna till den lokala datorn. |
| | |
