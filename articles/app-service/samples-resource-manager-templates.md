---
title: Azure Resource Manager-mallexempel
description: Hitta Azure Resource Manager mal sampel för några vanliga App Service scenarier. Lär dig hur du automatiserar App Service distributions-eller hanterings uppgifter.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 08/26/2020
ms.author: tomfitz
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 5129fccceb633991767cdd3b52bbb5b6af067270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88891053"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Azure Resource Manager-mallar för App Service

Följande tabell innehåller länkar till Azure Resource Manager-mallar för Azure App Service. Rekommendationer om hur du undviker vanliga fel när du skapar appmallar finns i avsnittet med [vägledning för att distribuera appar med Azure Resource Manager-mallar](deploy-resource-manager-template.md).

Mer information om JSON-syntaxen och egenskaper för App Services-resurser finns på sidan med [Microsoft.Web-resurstyper](/azure/templates/microsoft.web/allversions).

| Distribuera en app | Beskrivning |
|-|-|
| [App Service plan och enkel Linux-app](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Distribuerar en App Service-app som är konfigurerad för Linux. |
| [App Service plan och enkel Windows-app](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Distribuerar en App Service-app som är konfigurerad för Windows. |
| [App som är länkad till en GitHub-lagringsplats](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Distribuerar en App Service-app som hämtar kod från GitHub. |
| [App med anpassade distributionsfack](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Distribuerar en App Service-app med anpassade distributionsfack/miljöer. |
| [App med privat slut punkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-private-endpoint-webapp)| Distribuerar en App Service-app med en privat slut punkt. |
|**Konfigurera en app**| **Beskrivning** |
| [Appcertifikat från Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Distribuerar ett App Service app-certifikat från en Azure Key Vault hemlighet och använder det för TLS/SSL-bindning. |
| [App med en anpassad domän och SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Distribuerar en App Service-app med ett anpassat värdnamn och hämtar ett app-certifikat från Key Vault för TLS/SSL-bindning. |
| [App med ett GoLang-tillägg](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Distribuerar en App Service-app med Golang-platstillägget. Du kan sedan köra webbprogram som har utvecklats för Golang på Azure. |
| [App med Java 8 och Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Distribuerar en App Service-app med Java 8 och Tomcat 8 aktiverat. Du kan sedan köra Java-program i Azure. |
| [App med regional VNet-integrering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-service-regional-vnet-integration)| Distribuerar en App Service-app med regional VNet-integrering aktive rad. |
|**Skydda en app**| **Beskrivning** |
| [App-integrerad med Azure Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| Distribuerar en App Service app och en Application Gateway och isolerar trafiken med hjälp av tjänst slut punkt och åtkomst begränsningar. |
|**Linux-app med anslutna resurser**| **Beskrivning** |
| [App i Linux med MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Distribuerar en App Service-app i Linux med Azure Database for MySQL. |
| [App i Linux med PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Distribuerar en App Service-app i Linux med Azure Database for PostgreSQL. |
|**App med anslutna resurser**| **Beskrivning** |
| [App med MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Distribuerar en App Service-app i Windows med Azure Database for MySQL. |
| [App med PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Distribuerar en App Service-app i Windows med Azure Database for PostgreSQL. |
| [App med en databas i Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Distribuerar en App Service app och en databas i Azure SQL Database på den grundläggande service nivån. |
| [App med Blob-lagringsanslutning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Distribuerar en App Service-app med en Azure Blob Storage-anslutningssträng. Du kan sedan använda Blob-lagring från appen. |
| [App med en Azure Cache for Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Distribuerar en App Service-app med en Azure Cache for Redis. |
| [App ansluten till en server dels webapp](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection)| Distribuerar två webbappar (klient dels-och Server del) på ett säkert sätt tillsammans med VNet-injektering och privat slut punkt. |
|**App Service Environment**| **Beskrivning** |
| [Skapa en App Service-miljö v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Skapar en App Service-miljö v2 i det virtuella nätverket. |
| [Skapa en App Service-miljö v2 med en ILB-adress](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Skapar en App Service-miljö v2 i det virtuella nätverket med en privat intern adress för lastbalanseraren. |
| [Konfigurera SSL-standardcertifikatet för en ILB App Service-miljö eller ILB App Service-miljö v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Konfigurerar TLS/SSL-standardcertifikatet för en ILB-App Service miljö eller en ILB App Service-miljö v2. |
| | |
