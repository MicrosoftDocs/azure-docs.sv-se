---
title: Webb program mönster för flera innehavare | Microsoft Docs
description: Hitta arkitektur översikter och design mönster som beskriver hur du implementerar ett webb program för flera innehavare på Azure.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.custom: devx-track-dotnet
ms.openlocfilehash: d36a2804519e5728dd068cc6c06ad005244e8c95
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95524104"
---
# <a name="multitenant-applications-in-azure"></a>Program med flera klienter i Azure
Ett program för flera klient organisationer är en delad resurs som tillåter "användare i separata klienter" att visa programmet som om det var ett eget. Ett typiskt scenario som lånar sig själv till ett program med flera innehavare är ett där alla användare av programmet från olika klienter kanske vill anpassa användar upplevelsen, men på annat sätt har samma grundläggande affärs krav. Exempel på stora program med flera klient organisationer är Microsoft 365, Outlook.com och visualstudio.com.

Från en programproviders perspektiv är fördelarna med flera organisationer främst relaterade till drifts-och kostnads effektivitet. En version av programmet kan uppfylla behoven hos många klienter/kunder, vilket möjliggör konsolidering av system administrations uppgifter som övervakning, prestanda justering, program varu underhåll och säkerhets kopiering av data.

Nedan visas en lista över de viktigaste målen och kraven från en leverantörs perspektiv.

* **Etablering**: du måste kunna etablera nya klienter för programmet.  För program med flera klient organisationer med ett stort antal klienter är det vanligt vis nödvändigt att automatisera processen genom att aktivera självbetjänings etablering.
* **Hanterbarhet**: du måste kunna uppgradera programmet och utföra andra underhålls aktiviteter medan flera klienter använder det.
* **Övervakning**: du måste alltid kunna övervaka programmet för att identifiera eventuella problem och felsöka dem. Detta omfattar övervakning av hur varje klient använder programmet.

Ett korrekt implementerat program med flera klienter ger användarna följande fördelar.

* **Isolering**: aktiviteterna för enskilda klienter påverkar inte användningen av programmet av andra klienter. Klienterna har inte åtkomst till var and ras data. Den visas för klienten som om de har exklusiv användning av programmet.
* **Tillgänglighet**: enskilda klienter vill att programmet alltid ska vara tillgängligt, kanske med garantier som definierats i ett service avtal. Sedan bör aktiviteterna för andra klienter inte påverka programmets tillgänglighet.
* **Skalbarhet**: programmet skalas för att uppfylla efter frågan på enskilda klienter. Förekomst och åtgärder för andra klienter bör inte påverka programmets prestanda.
* **Kostnader**: kostnaderna är lägre än att köra ett dedikerat program med en enda klient eftersom flera innehavare möjliggör delning av resurser.
* **Anpassningsbarhet**. Möjligheten att anpassa programmet för en enskild klient på olika sätt, till exempel att lägga till eller ta bort funktioner, ändra färger och logo typer eller till och med att lägga till egen kod eller skript.

Kort, men det finns många saker som du måste tänka på för att tillhandahålla en mycket skalbar tjänst, men det finns också ett antal mål och krav som är gemensamma för många program med flera klienter. Vissa är kanske inte relevanta i vissa scenarier, och vikten av enskilda mål och krav kan skilja sig åt i varje scenario. Som leverantör av program för flera innehavare har du också mål och krav, till exempel för att uppfylla klientens mål och krav, lönsamhet, fakturering, flera tjänste nivåer, etablering, övervakning av övervakning och automatisering.

Mer information om ytterligare design aspekter av ett program med flera klienter finns i hantera [ett program för flera innehavare på Azure][Hosting a Multi-Tenant Application on Azure]. Information om vanliga mönster för dataarkitekturen i SaaS-databasprogram (Software-as-a-Service) med flera klienter finns i [Designmönster för SaaS-program med flera klienter i Azure SQL Database](./azure-sql/database/saas-tenancy-app-design-patterns.md). 

Azure innehåller många funktioner som gör att du kan åtgärda de viktiga problem som uppstår när du utformar ett system med flera innehavare.

**Gruppera**

* Segmentera webbplats klienter efter värdhuvuden med eller utan TLS-kommunikation
* Segmentera webbplats klienter efter frågeparametrar
* Webb tjänster i arbets roller
  * Arbets roller som vanligt vis bearbetar data på Server delen i ett program.
  * Webb roller som vanligt vis fungerar som klient del för program.

**Storage**

Data hantering som Azure SQL Database eller Azure Storage tjänster som till exempel Table service, som tillhandahåller tjänster för lagring av stora mängder ostrukturerade data och Blob Service, som tillhandahåller tjänster för att lagra stora mängder ostrukturerad text eller binära data som video, ljud och bilder.

* Skydda flera klient data i SQL Database per klient SQL Server inloggningar.
* Med Azure-tabeller för program resurser genom att ange en åtkomst princip för behållar nivå kan du ha möjlighet att ändra behörigheter utan att behöva utfärda nya URL: er för de resurser som skyddas med signaturer för delad åtkomst.
* Azure-köer för program resurser Azure-köer används ofta för att bearbeta bearbetning för klienter, men kan även användas för att distribuera arbete som krävs för etablering eller hantering.
* Service Bus köer för program resurser som skickar ett arbete till en delad tjänst kan du använda en enda kö där varje klient avsändare endast har behörighet (som härletts från anspråk som utfärdats från ACS) för att push-överföra till kön, medan endast mottagare från tjänsten har behörighet att hämta från kön till data som kommer från flera klienter.

**Anslutnings-och säkerhets tjänster**

* Azure Service Bus en infrastruktur för meddelanden som finns mellan program och som gör det möjligt för dem att utbyta meddelanden på ett löst sätt för förbättrad skalning och återhämtning.

**Nätverkstjänster**

Azure tillhandahåller flera nätverks tjänster som stöder autentisering och förbättrar hanteringen av dina värdbaserade program. Dessa tjänster omfattar följande:

* Med Azure Virtual Network kan du etablera och hantera virtuella privata nätverk (VPN) i Azure och länka dem på ett säkert sätt till den lokala IT-infrastrukturen.
* Virtual Network Traffic Manager låter dig belastningsutjämna inkommande trafik över flera värdbaserade Azure-tjänster, oavsett om de körs i samma data Center eller i olika data center runtom i världen.
* Azure Active Directory (Azure AD) är en modern, REST-baserad tjänst som tillhandahåller funktioner för identitets hantering och åtkomst kontroll för dina moln program. Med hjälp av Azure AD för program resurser får du ett enkelt sätt att autentisera och auktorisera användare för att få åtkomst till dina webb program och-tjänster samtidigt som funktionerna för autentisering och auktorisering kan delas ut från din kod.
* Azure Service Bus ger en säker meddelande-och data flödes kapacitet för distribuerade och hybrid program, till exempel kommunikation mellan Azure-värdbaserade program och lokala program och tjänster, utan att kräva komplexa brand väggar och säkerhets infrastrukturer. Att använda Service Bus Relay för program resurser för att få åtkomst till de tjänster som exponeras som slut punkter kan tillhöra klienten (t. ex. på plats utanför systemet, t. ex. lokalt), eller så kan de vara tjänster som tillhandahålls specifikt för klient organisationen (eftersom känsliga, klient specifika data överförs över dem).

**Etablering av resurser**

Azure erbjuder ett antal olika sätt att etablera nya klienter för programmet. För program med flera klient organisationer med ett stort antal klienter är det vanligt vis nödvändigt att automatisera processen genom att aktivera självbetjänings etablering.

* Med arbets roller kan du etablera och avetablera per klient resurser (t. ex. När en ny klient loggar in eller avbryter), samla in Mät värden för mätnings användning och hantera skala efter ett visst schema eller som svar på passerandet av tröskelvärden för viktiga prestanda indikatorer. Samma roll kan också användas för att skicka ut uppdateringar och uppgraderingar till lösningen.
* Azure blobs kan användas för att etablera beräknings-eller förinitierade lagrings resurser för nya klienter samtidigt som du tillhandahåller åtkomst principer för behållar nivå för att skydda beräknings tjänst paket, VHD-avbildningar och andra resurser.
* Alternativen för etablering SQL Database resurser för en klient är:
  
  * DDL i skript eller inbäddade som resurser i sammansättningar.
  * SQL Server 2008 R2 DAC-paket distribueras program mässigt.
  * Kopierar från en huvud referens databas.
  * Använda import och export av databasen för att etablera nya databaser från en fil.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: /previous-versions/msp-n-p/hh534480(v=pandp.10)
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716