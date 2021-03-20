---
title: Felsöka ett Application Gateway i Azure – ILB ASE | Microsoft Docs
description: Lär dig hur du felsöker en Programgateway med hjälp av ett internt Load Balancer med ett App Service-miljön i Azure
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/09/2020
ms.author: genli
ms.openlocfilehash: 8861e850e168169762d95c44a54b6a88a036f396
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84628522"
---
# <a name="back-end-server-certificate-is-not-allow-listed-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Backend-servercertifikat tillåts inte i listan för en Programgateway med en intern Load Balancer med ett App Service-miljön

Den här artikeln innehåller fel sökning av följande problem: ett certifikat tillåts inte i listan när du skapar en Programgateway genom att använda en intern Load Balancer (ILB) tillsammans med en App Service-miljön (ASE) på Server sidan när du använder TLS från slut punkt till slut punkt i Azure.

## <a name="symptoms"></a>Symtom

När du skapar en Programgateway med hjälp av en ILB med en ASE på Server sidan kan backend-servern bli ohälsosam. Det här problemet uppstår om Authentication-certifikatet för programgatewayen inte matchar det konfigurerade certifikatet på backend-servern. Se följande scenario som exempel:

**Application Gateway konfiguration:**

- **Lyssnare:** Flera platser
- **Port:** 443
- **Värdnamn:** test.appgwtestase.com
- **SSL-certifikat:** CN = test. appgwtestase. com
- **Backend-pool:** IP-adress eller fullständigt domän namn
- **IP-adress:**: 10.1.5.11
- **Http-inställningar:** HTTPS
- **Port:**: 443
- **Anpassad avsökning:** Hostname – test.appgwtestase.com
- **Autentiseringscertifikat:** . cer av test.appgwtestase.com
- **Server dels hälsa:** Ej felfri – backend-servercertifikat tillåts inte i listan med Application Gateway.

**ASE-konfiguration:**

- **ILB IP:** 10.1.5.11
- **Domän namn:** appgwtestase.com
- **App Service:** test.appgwtestase.com
- **SSL-bindning:** SNI SSL – CN = test. appgwtestase. com

När du ansluter till programgatewayen visas följande fel meddelande, eftersom backend-servern inte är felfri:

**502 – webb servern tog emot ett ogiltigt svar när den fungerade som en gateway eller proxyserver.**

## <a name="solution"></a>Lösning

När du inte använder ett värdnamn för att komma åt en HTTPS-webbplats, returnerar backend-servern det konfigurerade certifikatet på standard webbplatsen, om SNI är inaktiverat. För en ILB-ASE kommer standard certifikatet från ILB-certifikatet. Om det inte finns några konfigurerade certifikat för ILB kommer certifikatet från ASE app-certifikatet.

När du använder ett fullständigt kvalificerat domän namn (FQDN) för att komma åt ILB, returnerar backend-servern rätt certifikat som laddas upp i HTTP-inställningarna. Om så inte är fallet, bör du överväga följande alternativ:

- Använd FQDN i backend-poolen för programgatewayen för att peka på IP-adressen för ILB. Det här alternativet fungerar bara om du har en privat DNS-zon eller en anpassad DNS-konfigurerad. Annars måste du skapa en "A"-post för en offentlig DNS.

- Använd det överförda certifikatet på ILB eller standard certifikatet (ILB-certifikat) i HTTP-inställningarna. Programgatewayen hämtar certifikatet när den får åtkomst till ILB IP för avsökningen.

- Använd ett certifikat med jokertecken på ILB och backend-servern, så att certifikatet är gemensamt för alla webbplatser. Den här lösningen är dock bara möjlig i händelse av under domäner och inte om var och en av dessa webbplatser kräver olika värdnamn.

- Avmarkera alternativet **Använd för App Service** för Application Gateway om du använder IP-adressen för ILB.

För att minska omkostnaderna kan du ladda upp ILB-certifikatet i HTTP-inställningarna för att göra avsöknings Sök vägen fungera. (Det här steget är bara för att tillåta List. Den används inte för TLS-kommunikation.) Du kan hämta ILB-certifikatet genom att komma åt ILB med dess IP-adress från din webbläsare på HTTPS och sedan exportera TLS/SSL-certifikatet i ett Base-64-kodat CER-format och ladda upp certifikatet på respektive HTTP-inställningar.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.
