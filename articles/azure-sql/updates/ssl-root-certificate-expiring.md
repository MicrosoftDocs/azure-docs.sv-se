---
title: Certifikat rotation för Azure SQL Database & SQL-hanterad instans
description: Lär dig mer om kommande ändringar av rot certifikat ändringar som påverkar Azure SQL Database och Azure SQL-hanterad instans
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: 96367b143711c4ec5f3f8d609f048c72c6fded16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590859"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>Förstå ändringarna i rot certifikat utfärdarens ändring för Azure SQL Database & SQL-hanterad instans

Azure SQL Database & SQL-hanterad instans ändrar rot certifikatet för klient programmet/driv rutinen som Aktiver ATS med SSL, som används för att upprätta säker TDS-anslutning. Det [aktuella rot certifikatet](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) är inställt på att upphöra att gälla den 26 oktober 2020 som en del av standard-och bästa metoder för underhåll och säkerhet. Den här artikeln innehåller mer information om kommande ändringar, vilka resurser som kommer att påverkas och de steg som krävs för att säkerställa att programmet upprätthåller anslutningen till databas servern.

## <a name="what-update-is-going-to-happen"></a>Vilken uppdatering ska ske?

[Webb läsar forum för certifikat utfärdare (ca)](https://cabforum.org/) har nyligen publicerat rapporter om flera certifikat som utfärdats av ca-leverantörer som icke-kompatibla.

Som enligt branschens krav på efterlevnad började CA-leverantörer återkallade CA-certifikat för icke-kompatibla ca: er, vilket kräver att servrar använder certifikat som utfärdats av kompatibla ca: er och signeras av CA-certifikat från dessa kompatibla certifikat utfärdare. Eftersom Azure SQL Database & SQL-hanterad instans för närvarande använder en av dessa icke-kompatibla certifikat, som klient program använder för att verifiera sina SSL-anslutningar, måste vi se till att lämpliga åtgärder vidtas (beskrivs nedan) för att minimera den potentiella påverkan på dina Azure SQL-servrar.

Det nya certifikatet kommer att användas från och med den 26 oktober 2020. Om du använder fullständig validering av Server certifikatet vid anslutning från en SQL-klient (TrustServerCertificate = true) måste du se till att din SQL-klient kan verifiera nytt rot certifikat före den 26 oktober 2020.

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>Hur gör jag för att vet du om mitt program kan påverkas?

Alla program som använder SSL/TLS och kontrollerar att rot certifikatet måste uppdatera rot certifikatet för att kunna ansluta till Azure SQL Database & SQL-hanterad instans. 

Om du inte använder SSL/TLS för närvarande påverkas inte din program tillgänglighet. Du kan kontrol lera om klient programmet försöker verifiera rot certifikatet genom att titta på anslutnings strängen. Om TrustServerCertificate uttryckligen är inställt på Sant påverkas inte det.

Om klient driv rutinen använder operativ systemets certifikat Arkiv, som merparten av driv rutinerna gör, och ditt operativ system regelbundet upprätthåller, kommer den här ändringen troligen inte att påverka dig, eftersom rot certifikatet som vi växlar till ska redan vara tillgängligt i ditt betrodda rot certifikat arkiv. Sök efter Baltimore CyberDigiCert GlobalRoot G2 och kontrol lera att den finns.

Om klient driv rutinen använder det lokala fil certifikat arkivet för att undvika att programmets tillgänglighet avbryts på grund av att certifikaten har återkallats, eller om du vill uppdatera ett certifikat som har återkallats, kan du läsa avsnittet [**Vad behöver jag för att underhålla anslutningen**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Vad behöver jag för att underhålla anslutningen

Om du vill undvika att programmets tillgänglighet avbryts på grund av att certifikaten har återkallats, eller om du vill uppdatera ett certifikat som har återkallats, följer du stegen nedan:

*   Hämta Baltimore CyberTrust root & DigiCert GlobalRoot G2 rot certifikat utfärdare från länkarna nedan:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Generera ett kombinerat CA-certifikatarkiv med både **BaltimoreCyberTrustRoot** -och **DigiCertGlobalRootG2** -certifikat ingår.

## <a name="what-can-be-the-impact"></a>Vad kan vara påverkan på?
Om du verifierar Server certifikat som dokumenteras här kan programmets tillgänglighet avbrytas eftersom databasen inte går att komma åt. Beroende på ditt program kan du få en rad fel meddelanden, inklusive men inte begränsat till:
*   Ogiltigt certifikat/återkallat certifikat
*   Anslutningens tidsgräns uppnåddes
*   Fel om tillämpligt

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Om jag inte använder SSL/TLS måste jag fortfarande uppdatera rot certifikat utfärdaren?
Det krävs inga åtgärder för den här ändringen om du inte använder SSL/TLS. Du bör ändå ange en plan för att börja använda den senaste TLS-versionen när vi planerar för TLS-tvång i nära framtid.

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>Vad händer om jag inte uppdaterar rot certifikatet före den 26 oktober 2020?
Om du inte uppdaterar rot certifikatet före den 30 november 2020 kommer dina program som ansluter via SSL/TLS och verifiering för rot certifikatet inte att kunna kommunicera med den Azure SQL Database & SQL-hanterade instansen och programmet kommer att uppleva anslutnings problem till din Azure SQL Database & SQL-hanterade instans.

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>Måste jag planera ett underhålls avbrott för den här ändringen?<BR>
Nej. Eftersom ändringen bara finns på klient sidan för att ansluta till servern, finns det inga underhålls avbrott som krävs för den här ändringen.

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>Vad händer om jag inte kan få en schemalagd stillestånds tid för den här ändringen före den 26 oktober 2020?
Eftersom klienterna som används för att ansluta till servern måste uppdatera certifikat informationen enligt beskrivningen i avsnittet åtgärda [här](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity), behöver vi inte något drift stopp för-servern i det här fallet.

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>Kommer jag att påverkas om jag skapar en ny server efter den 30 november 2020?
För servrar som skapats efter den 26 oktober 2020 kan du använda det nyligen utfärdade certifikatet för dina program för att ansluta med SSL.

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Hur ofta uppdaterar Microsoft sina certifikat eller vad är förfallo principen?
Dessa certifikat som används av Azure SQL Database & SQL-hanterad instans tillhandahålls av betrodda certifikat utfärdare (CA). Därför är stöd för dessa certifikat på Azure SQL Database & SQL-hanterad instans knutet till stöd för dessa certifikat av CA. Men i så fall kan det vara oförutsedda buggar i dessa fördefinierade certifikat, vilket måste åtgärdas tidigast.

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-primary-server-or-all-the-read-replicas"></a>Behöver jag bara utföra den här uppdateringen på den primära servern eller alla Läs repliker om jag använder Läs repliker?
Eftersom den här uppdateringen är en ändring på klient sidan, om klienten som används för att läsa data från replik servern, måste du även tillämpa ändringarna för dessa klienter. 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>Har vi frågor på Server sidan för att kontrol lera om SSL används?
Eftersom den här konfigurationen är på klient sidan är information inte tillgänglig på Server sidan.

### <a name="what-if-i-have-further-questions"></a>Vad händer om jag har fler frågor?
Om du har ett support avtal och behöver teknisk hjälp, skapa support förfrågan för Azure, se [så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).