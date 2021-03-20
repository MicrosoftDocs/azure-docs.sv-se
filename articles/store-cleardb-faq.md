---
title: Vanliga frågor och svar om ClearDB MySql-databaser med Azure App Service
description: Svar på vanliga frågor om att använda ClearDB MySQL-databaser med Azure App Service.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: references_regions
ms.openlocfilehash: d9d4ae746c192e8b4afd1781c1e072fc0dc70422
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98599789"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Vanliga frågor och svar om ClearDB MySql-databaser med Azure App Service
Vanliga frågor och svar om att använda och köpa ClearDB MySQL-databaser för Azure Web Apps.

> [!IMPORTANT]
> Från och med den 13 juni 2018 faktureras ClearDB-baserade Azure-baserade kunder för närvarande av Microsoft till en direkt fakturerings modell med ClearDB. Informationen i den här artikeln är nu inaktuell. Du kommer inte längre att kunna skapa eller uppgradera en ClearDB-databas som skapats i Azure.
>
> Mer information och nästa steg finns i [ändringar av ClearDB Service-planer](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Vilka alternativ har jag för MySQL på Azure?
Se [ClearDB](https://w2.cleardb.net/) för den senaste informationen om tjänsten. ClearDB är en MySQL-värd tjänst och hanterar MySQL-infrastrukturen åt dig. 

Du har flera andra alternativ för att vara värd för MySQL i Azure:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [MySQL-kluster som körs på en virtuell Azure-dator](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [En enda instans av MySQL som körs på en virtuell Azure-dator](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Behöver jag ett kredit kort för Web App + MySQL-mallen på Azure Marketplace?
Detta beror på vilken typ av prenumeration du använder. Här följer några vanliga prenumerations typer:

* [Betala per](https://azure.microsoft.com/offers/ms-azr-0003p/)användning: kräver ett kredit kort och när du köper en betald MySQL-databas debiteras ditt kredit kort.
* [Kostnads fri utvärderings version](https://azure.microsoft.com/pricing/free-trial/): innehåller krediter för användning med Microsoft Azure tjänster, men tillåter inte köp av resurser från tredje part. Om du vill köpa tjänster från tredje part eller en betald MySQL-databas måste du använda en kredit korts aktive rad prenumeration. För Web Apps kan du skapa en kostnads fri ClearDB MySQL-databas.
* [MSDN-prenumeration](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) och **MSDN dev-test betala per** användning: på en MSDN-prenumeration måste du ha ett kredit kort för att köpa en betald MySQL-lösning från ClearDB.
* [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA-kunder debiteras mot deras EA varje kvartal för alla sina Azure Marketplace-inköp (tredje part) på en separat, konsol IDE rad faktura. Du debiteras utanför Azure-förskotts betalningen (tidigare kallat betalnings åtagande) för alla marknads plats inköp. Observera att för närvarande är Azure Store inte tillgängligt för kunder som har registrerats i Azerbajdzjan, Kroatien, Norge och Puerto Rico. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Varför debiterades jag $3,50 för en webbapp + MySQL från Azure Marketplace?
Standard databas alternativet är Titan, som är $3,50. Vi visar inte kostnaden när databasen skapas och du kan av misstag köpa en databas som du inte avsåg. Vi försöker hitta ett sätt att förbättra upplevelsen, men tills måste du kontrol lera alla valda pris nivåer för webbapp och databas innan du klickar på **skapa** och starta distributionen av resurserna.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Jag kör MySQL på min egen virtuella Azure-dator. Kan jag ansluta min Azure-webbapp till min databas?
Ja. Du kan ansluta din webbapp till databasen så länge din virtuella Azure-dator har fått fjärråtkomst till din webbapp. Mer information finns i [Installera MySQL på en virtuell dator](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>I vilka länder/regioner är ClearDB Premium MySQL-kluster som stöds?
ClearDB Premium MySQL-kluster är tillgängliga i alla Azure-regioner över hela världen med undantag för Indien, Australien, södra Brasilien och Kina.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Kan jag skapa ett nytt kluster innan jag skapar en databas med ClearDB Premium Cluster-lösningen?
Nej, det finns inte stöd för att skapa tomma ClearDB-kluster. Med Azure Portal kan du skapa databaser i ett kluster, vilket kan skapa ett nytt kluster på samma tid.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Kommer jag att bli varnad om jag försöker ta bort en ClearDB MySQL-databas som används i något av mina program?
Nej, Azure varnar dig inte om du tar bort ett marknads plats köp som ditt program är beroende av.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Vilka regioner kan jag skapa ClearDB-databaser i?
Azure Marketplace är inte tillgängligt för kunder som har registrerats i Azerbajdzjan, Kroatien, Norge eller Puerto Rico. ClearDB är inte tillgängligt i dessa regioner.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Vilken pris nivå ska jag välja för en webbapp och en databas för produktion?
Använd Basic eller en högre pris nivå för Web Apps. För ClearDB rekommenderar vi antingen en Saturnus-eller Jupiter-plan. Granska funktionerna & begränsningar för varje pris nivå för både [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) -och [ClearDB MySQL-databaser](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) för att välja den som passar dina behov.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Hur gör jag för att uppgraderar du ClearDB-databasen från en plan till en annan?
I [Azure Portal](https://portal.azure.com)kan du skala upp en delad värd databas för ClearDB. Läs den här [artikeln](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) om du vill veta mer. Vi stöder för närvarande inte uppgradering för ClearDB Premium-kluster i Azure Portal.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Jag kan inte se min ClearDB-databas i Azure Portal?
Om du har skapat en ClearDB-databas i klassisk kommer du inte att kunna se databasen i [Azure Portal](https://portal.azure.com). Det finns ingen lösning för det här scenariot.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Vem kontaktar jag för att få support när min databas är nere?
Kontakta [ClearDB-supporten](https://www.cleardb.com/developers/help/support) för alla databas problem. Var beredd på att förse dem med din Azure-prenumerations information.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Kan jag skapa ytterligare användare för min ClearDB MySQL Database Cluster-lösning?
Nej. Du kan inte skapa ytterligare användare, men du kan skapa fler databaser på ditt ClearDB-databas kluster.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Kan databaser för Basic/Pro-serien uppgraderas på plats som liknar planetär-planer idag på ClearDB-portalen?
Ja, Basic Series-databaser kan uppgraderas på plats (Basic 60 till och med Basic 500). Pro-serien kan uppgraderas på plats (Pro 125 till Pro 1000) med undantag för Pro 60. Vi stöder för närvarande inte uppgradering av Pro 60-databasen. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>När jag migrerar mina resurser från en prenumeration till en annan, kommer min ClearDB MySQL-databas också att migreras.
När du utför migreringen av en resurs över prenumerationer gäller vissa [begränsningar](azure-resource-manager/management/move-support-resources.md) . En ClearDB MySQL-databas är en tjänst från tredje part och migreras därför inte under migreringen av Azure-prenumerationen. Om du inte hanterar migreringen av MySQL-databasen innan du migrerar Azure-resurser kan dina ClearDB MySQL-databaser inaktive ras. Migrera dina databaser manuellt först och utför sedan migreringen av Azure-prenumeration för din webbapp. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Jag träffar utgifts gränsen för min prenumeration. Jag tog bort gränsen och mitt App Service är online, men databasen är inte tillgänglig. Hur gör jag för att återaktivera databasen ClearDB
Kontakta [ClearDB-supporten](https://www.cleardb.com/developers/help/support) för att återaktivera databasen. Ange din Azure-prenumerations information och ditt databas namn.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Kan jag överföra en ClearDB-databas från en kredit korts prenumeration till en EA-prenumeration?
Befintliga ClearDB-databaser använder det kredit kort som är associerat med befintliga prenumerationer. Om du vill använda en EA-prenumeration måste du migrera dina data till en ny databas:

* Köp en ny ClearDB-databas med din EA-prenumeration.
* Migrera dina data till den nya databasen.
* Uppdatera programmet så att det använder den nya databasen.
* Ta bort den gamla ClearDB-databasen.

När du skapar en ny webbapp med MySQL (ClearDB) eller skapar en MySQL-databas (ClearDB), avgör den prenumeration du väljer hur du betalar för tjänsten. Med en EA-prenumeration kommer vi inte att blockera inköp av tjänster från tredje part, till exempel ClearDB i Azure Portal. EA-prenumerationer debiteras utanför Azures förskotts betalning och faktureras kvartals vis och i förskott. EA-kunden måste skapa en betalnings metod, till exempel ett kredit kort för att betala för alla tredje parts Marketplace-tjänster.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Var kan jag se avgifterna för ClearDB-resurser i en EA-prenumeration?
För direkta EA-kunder visas Azure Marketplace-avgifter på Enterprise Portal. Observera att alla inköp av Marketplace och förbrukning debiteras utanför Azures förskotts betalning och faktureras kvartals vis och i förskott. EA-kunder måste betala direkt till tredjepartsleverantörer och kan göra detta genom att aktivera en betalnings metod, till exempel ett kredit kort med sitt EA-konto.

Indirekta EA-kunder kan hitta sina Azure Marketplace-prenumerationer på sidan **Hantera prenumerationer** i Enterprise Portal, men prissättningen är dold. Kunderna bör kontakta sin LSP för information om Marketplace-avgifter.

Åtkomst till Azure Marketplace för tjänster från tredje part kan hanteras av dina administratörer för din EA Azure-registrering. De kan inaktivera eller återaktivera åtkomst till tredje parts köp från butiken i Hantera konton och prenumerationer under avsnittet konton i Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Vem kontaktar jag för frågor om min faktura för ClearDB Services i EA-prenumerationen?
Kontakta [företagets kund support](https://aka.ms/AzureEntSupport) med avseende på fakturering under deras EA-registrering. Support teamet för EA-portalen besvarar din fråga eller hjälper dig att lösa problemet.

## <a name="more-information"></a>Mer information
[Vanliga frågor och svar om Azure Marketplace](https://azure.microsoft.com/marketplace/faq/)