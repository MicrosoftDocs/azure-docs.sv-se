---
title: Säkerhetsrekommendationer
description: Implementera säkerhets rekommendationerna så att du kan uppfylla dina säkerhets skyldigheter enligt vad som anges i vår delade ansvars modell. Förbättra säkerheten för din app.
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 09dbe0fbefb8b90b4c4e1ddef57abf3b13856183
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148092"
---
# <a name="security-recommendations-for-app-service"></a>Säkerhets rekommendationer för App Service

Den här artikeln innehåller säkerhets rekommendationer för Azure App Service. Genom att implementera dessa rekommendationer kan du uppfylla dina säkerhets krav enligt beskrivningen i vår delade ansvars modell och förbättrar den övergripande säkerheten för dina webb programs lösningar. Mer information om vad Microsoft gör för att uppfylla ansvaret för tjänst leverantören finns i [säkerhet för Azure-infrastruktur](../security/fundamentals/infrastructure.md).

## <a name="general"></a>Allmänt

| Rekommendation | Kommentarer |
|-|-|----|
| Håll dig uppdaterad | Använd de senaste versionerna av plattformar som stöds, programmeringsspråk, protokoll och ramverk. |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Rekommendation | Kommentarer |
|-|----|
| Inaktivera anonym åtkomst | Om du inte behöver stöd för anonyma begär Anden inaktiverar du anonym åtkomst. Mer information om Azure App Service autentiseringsalternativ finns [i autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md).|
| Kräv autentisering | När det är möjligt använder du modulen App Service autentisering i stället för att skriva kod för att hantera autentisering och auktorisering. Se [autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md). |
| Skydda Server dels resurser med autentiserad åtkomst | Du kan antingen använda användarens identitet eller använda en program identitet för att autentisera till en backend-resurs. När du väljer att använda en program identitet använder du en [hanterad identitet](overview-managed-identity.md).
| Kräv autentisering av klient certifikat | Autentisering med klient certifikat förbättrar säkerheten genom att bara tillåta anslutningar från klienter som kan autentiseras med hjälp av certifikat som du anger. |

## <a name="data-protection"></a>Dataskydd

| Rekommendation | Kommentarer |
|-|-|
| Omdirigera HTTP till HTTPs | Som standard kan-klienter ansluta till webbappar med hjälp av både HTTP eller HTTPS. Vi rekommenderar att du omdirigerar HTTP till HTTPs eftersom HTTPS använder SSL/TLS-protokollet för att tillhandahålla en säker anslutning, som både är krypterad och autentiserad. |
| Kryptera kommunikation med Azure-resurser | När appen ansluter till Azure-resurser, till exempel [SQL Database](https://azure.microsoft.com/services/sql-database/) eller [Azure Storage](../storage/index.yml), finns anslutningen kvar i Azure. Eftersom anslutningen går igenom det delade nätverket i Azure bör du alltid kryptera all kommunikation. |
| Kräv att den senaste TLS-versionen är möjlig | Sedan 2018 nya Azure App Service-appar använda TLS 1,2. I nyare versioner av TLS ingår säkerhets förbättringar över äldre protokoll versioner. |
| Använd FTPS | App Service stöder både FTP-och FTPS för att distribuera dina filer. Använd FTPS i stället för FTP när det är möjligt. När ett eller båda av dessa protokoll inte används bör du [inaktivera dem](deploy-ftp.md#enforce-ftps). |
| Skydda programdata | Lagra inte program hemligheter, t. ex. autentiseringsuppgifter för databaser, API-token eller privata nycklar i koden eller konfigurationsfilerna. Den vanligaste metoden är att få åtkomst till dem som [miljövariabler](https://wikipedia.org/wiki/Environment_variable) som använder standard mönstret på det språk som du väljer. I Azure App Service kan du definiera miljövariabler genom [appinställningar](./configure-common.md) och [anslutnings strängar](./configure-common.md). App-inställningar och anslutnings strängar lagras krypterade i Azure. Inställningarna för appar dekrypteras bara innan de matas in i appens process minne när appen startas. Krypterings nycklarna roteras regelbundet. Du kan också integrera din Azure App Service-app med [Azure Key Vault](../key-vault/index.yml) för hantering av avancerade hemligheter. Genom att [komma åt Key Vault med en hanterad identitet](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)kan din app service-app på ett säkert sätt komma åt de hemligheter du behöver. |

## <a name="networking"></a>Nätverk

| Rekommendation | Kommentarer |
|-|-|
| Använda statiska IP-begränsningar | Azure App Service i Windows kan du definiera en lista med IP-adresser som får åtkomst till din app. Listan över tillåtna kan innehålla enskilda IP-adresser eller ett intervall med IP-adresser som definieras av en under nät mask. Mer information finns i [Azure App Service statiska IP-begränsningar](app-service-ip-restrictions.md).  |
| Använd den isolerade pris nivån | Förutom den isolerade pris nivån kör alla nivåer dina appar i den delade nätverks infrastrukturen i Azure App Service. Den isolerade nivån ger dig fullständig nätverks isolering genom att köra apparna i en dedikerad [app Services miljö](environment/intro.md). En App Service-miljö körs i din egen instans av [Azure Virtual Network](../virtual-network/index.yml).|
| Använda säkra anslutningar vid åtkomst till lokala resurser | Du kan använda [hybrid anslutningar](app-service-hybrid-connections.md), [Virtual Network-integrering](web-sites-integrate-with-vnet.md)eller [App Service miljö](environment/intro.md) för att ansluta till lokala resurser. |
| Begränsa exponering för inkommande nätverks trafik | Med nätverks säkerhets grupper kan du begränsa nätverks åtkomst och kontrol lera antalet exponerade slut punkter. Mer information finns i [så här styr du inkommande trafik till en app service-miljön](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Övervakning

| Rekommendation | Kommentarer |
|-|-|
|Använd Azure Security Center standard nivå | [Azure Security Center](../security-center/defender-for-app-service-introduction.md) integreras internt med Azure App Service. Den kan köra utvärderingar och tillhandahålla säkerhets rekommendationer. |

## <a name="next-steps"></a>Nästa steg

Kontakta din programprovider för att se om det finns ytterligare säkerhets krav. Mer information om hur du utvecklar säkra program finns i [dokumentationen om säker utveckling](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).