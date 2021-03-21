---
title: Hantering av Azures produktions nätverk – Microsoft Azure
description: Den här artikeln beskriver hur Microsoft hanterar och arbetar med Azures produktions nätverk för att skydda Azure-datacentren.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "68727120"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Hantering och drift av Azures produktions nätverk    
Den här artikeln beskriver hur Microsoft hanterar och arbetar med Azures produktions nätverk för att skydda Azure-datacentren.

## <a name="monitor-log-and-report"></a>Övervaka, logga och rapportera

Hantering och drift av Azures produktions nätverk är en samordnad ansträngning mellan drift teamen för Azure och Azure SQL Database. Teamen använder flera verktyg för prestanda övervakning av system och program i miljön. Och de använder lämpliga verktyg för att övervaka nätverks enheter, servrar, tjänster och program processer.

För att säkerställa säker utförande av tjänster som körs i Azure-miljön implementerar drift teamen flera nivåer av övervakning, loggning och rapportering, inklusive följande åtgärder:

- I huvudsak samlar Microsoft Monitoring Agent (MMA) in information om övervakning och diagnostik från många platser, inklusive Fabric Controller (FC) och rot operativ systemet (OS) och skriver dem till loggfiler. Agenten skickar slutligen en sammanfattad delmängd av informationen till ett förkonfigurerat Azure Storage-konto. Dessutom läser den fristående övervaknings-och diagnos tjänsten olika övervaknings-och diagnostiska loggdata och sammanfattar informationen. Övervaknings-och diagnos tjänsten skriver informationen till en integrerad logg. Azure använder den anpassade Azure-säkerhetsövervakningen, som är ett tillägg till Azure Monitoring System. Den innehåller komponenter som ser, analyserar och rapporterar om säkerhetsrelaterade händelser från olika platser i plattformen.

- Azure SQL Database Windows Fabric-plattformen tillhandahåller hantering, distribution, utveckling och tjänster för drifts granskning för Azure SQL Database. Plattformen erbjuder distribuerade distributions tjänster för flera steg, hälso övervakning, automatiska reparationer och service versions krav. Den innehåller följande tjänster:

   - Service modellerings funktioner med stor åter givnings miljö (Data Center kluster är dyra och begränsade).
   - Distributions-och uppgraderings arbets flöden med ett klick för start och underhåll av tjänsten.
   - Hälso rapportering med automatiserade reparations arbets flöden för att aktivera själv återställning.
   - Real tids övervakning, avisering och fel söknings funktioner över noderna i ett distribuerat system.
   - Centraliserad insamling av drift data och mått för distribution av distribuerad rotor Saks analys och tjänst insikter.
   - Drift verktyg för distribution, ändrings hantering och övervakning.
   - Azure SQL Database Windows Fabric plattform och övervaknings skript körs kontinuerligt och övervakar i real tid.

Om avvikelser inträffar aktive ras incident svars processen följt av Azure incident prioritering-teamet. Lämplig support personal för Azure meddelas att svara på incidenten. Problem spårning och lösning dokumenteras och hanteras i ett centraliserat biljett system. System drifts måtten är tillgängliga enligt sekretess avtalet och på begäran.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Företags nätverk och Multi-Factor Access till produktion
Användar basen för företags nätverk innehåller support personal för Azure. Företags nätverket har stöd för interna företags funktioner och har åtkomst till interna program som används för Azure-kundsupport. Företags nätverket är både logiskt och fysiskt åtskilda från Azures produktions nätverk. Azure-personal får åtkomst till företags nätverket med hjälp av Azure-arbetsstationer och bärbara datorer. Alla användare måste ha ett Azure Active Directory (Azure AD)-konto, inklusive användar namn och lösen ord, för att få åtkomst till företags nätverks resurser. Åtkomst till företags nätverk använder Azure AD-konton, som utfärdas till alla Microsoft-personal, entreprenörer och leverantörer och hanteras av Microsoft Information Technology. Unika användar identifierare särskiljer personal utifrån deras anställnings status hos Microsoft.

Åtkomst till interna Azure-program styrs genom autentisering med Active Directory Federation Services (AD FS) (AD FS). AD FS är en tjänst som tillhandahålls av Microsoft informations teknik och som tillhandahåller autentisering av företags nätverks användare genom att använda en säker token och användar anspråk. AD FS gör det möjligt för interna Azure-program att autentisera användare mot Microsoft Corporate Active Directory-domänen. Användarna måste autentiseras med hjälp av Multi-Factor Authentication för att få åtkomst till produktions nätverket från företags nätverkets miljö.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokal och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure information system](infrastructure-components.md)
- [Azure nätverks arkitektur](infrastructure-network.md)
- [Azures produktions nätverk](production-network.md)
- [Azure SQL Database säkerhetsfunktioner](infrastructure-sql.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Integritet för Azure-infrastruktur](infrastructure-integrity.md)
- [Data skydd för Azure-kunder](protection-customer-data.md)
