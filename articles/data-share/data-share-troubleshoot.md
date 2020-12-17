---
title: Felsöka Azure Data Share
description: Lär dig hur du felsöker problem med inbjudningar och fel när du skapar eller tar emot data resurser med Azure Data Share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: c93ce9c81ada3c30128846b43041603e132abd88
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617246"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Felsöka vanliga problem i Azure Data Share 

Den här artikeln visar hur du felsöker vanliga problem med Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Azure Data Share-inbjudningar 

I vissa fall, när en ny användare klickar på **acceptera inbjudan** från e-postinbjudan som skickades, kan de visas med en tom lista med inbjudningar. 

![Inga inbjudningar](media/no-invites.png)

Det här kan bero på något av följande:

* **Azure Data Share-tjänsten är inte registrerad som en resursprovider för någon Azure-prenumeration i klientorganisationen.** Du ser det här felet om det inte finns någon Data Share-resurs i Azure-klienten. När du skapar en Azure Data Share-resurs registrerar den automatiskt resursprovidern i din Azure-prenumeration. Du kan också registrera Data Share-tjänsten manuellt med följande steg. Du måste ha en deltagarroll i Azure för att utföra de här stegen.

    1. Gå till **Prenumerationer** i Azure-portalen
    1. Välj den prenumeration du vill använda till att skapa Azure Data Share-resursen
    1. Klicka på **Resursprovidrar**
    1. Sök efter **Microsoft.DataShare**
    1. Klicka på **Registrera** 

    Du måste ha [Azure Contributor-rollen](../role-based-access-control/built-in-roles.md#contributor) till Azure-prenumerationen för att slutföra de här stegen. 

* **Inbjudan skickas till ditt e-postalias snarare än din e-postadress för Azure-inloggning.** Om du har registrerat Azure Data Share-tjänsten eller redan har skapat en Data Share-resurs i Azure-klienten, men ändå inte kan se inbjudan, kan det bero på att providern har angett ditt e-postalias som mottagare snarare än din e-postadress för Azure-inloggning. Kontakta din dataprovider och kontrollera att de har skickat inbjudan till din e-postadress för Azure-inloggning och inte ditt e-postalias.

* **Inbjudan har redan accepterats.** Länken i e-postmeddelandet tar dig till sidan för Data Share-inbjudningar i Azure-portalen, och där visas bara väntande inbjudningar. Om du redan har accepterat inbjudan visas den inte längre på sidan för Data Share-inbjudningar. Gå vidare till din Data Share-resurs som du använde till att ta emot inbjudan för att visa mottagna resurser och konfigurera ditt Azure Data Explorer-klustermål.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Fel vid skapande eller mottagning av en ny resurs

"Det gick inte att lägga till data uppsättningar"

"Det gick inte att mappa data uppsättningar"

"Det gick inte att bevilja data resursen Resource x åtkomst till y"

"Du har inte rätt behörighet till x"

"Det gick inte att lägga till Skriv behörigheter för Azure Data Share-konto till en eller flera av de valda resurserna"

Om du får ovanstående fel när du skapar en ny resurs, lägger till data uppsättningar eller mappar data uppsättningar, kan det bero på otillräcklig behörighet för Azure Data Store. Se [roller och krav](concepts-roles-permissions.md) för nödvändiga behörigheter. 

Du måste ha Skriv behörighet för att dela eller ta emot data från ett Azure-datalager, som vanligt vis finns i **deltagar** rollen. 

Om det här är första gången du delar eller tar emot data från Azure Data Store, behöver du även *Microsoft. auktorisering/roll tilldelningar/Skriv* behörighet, som vanligt vis finns i **ägar** rollen. Även om du har skapat Azure Data Store-resursen så blir det inte automatiskt ägaren till resursen. Med rätt behörighet ger Azure Data Share service automatiskt den hanterade identitets åtkomsten för data resursen till data lagret. Den här processen kan ta några minuter att börja gälla. Om det uppstår ett problem på grund av den här fördröjningen, försök igen om några minuter.

SQL-baserad delning kräver ytterligare behörigheter. Se [Dela från SQL-källor](how-to-share-from-sql.md) för detaljerad lista över krav.

## <a name="snapshot-failed"></a>Ögonblicks bild misslyckades
Det gick inte att ta ögonblicks bilder på grund av olika orsaker. Du hittar ett detaljerat fel meddelande genom att klicka på Start tiden för ögonblicks bilden och sedan på status för varje data uppsättning. Följande är vanliga orsaker till att ögonblicks bilder Miss lyckas:

* Data resursen har inte behörighet att läsa från käll data lagret eller skriva till mål data lagret. Se [roller och krav](concepts-roles-permissions.md) för detaljerade behörighets krav. Om det här är första gången du tar en ögonblicks bild kan det ta några minuter för data resurs resursen att beviljas åtkomst till Azure Data Store. Vänta några minuter och försök igen.
* Data delnings anslutningen till käll-eller mål data lagret blockeras av brand väggen.
* Den delade data mängden eller käll-eller mål data lagret har tagits bort.

För lagrings kontot är följande ytterligare orsaker till fel i ögonblicks bilder.

* Filen uppdateras vid källan medan ögonblicks bilden sker. Detta kan resultera i 0 byte-fil på målet. Efterföljande ögonblicks bilder efter uppdateringen har slutförts på källan.

För SQL-källor är följande ytterligare orsaker till fel i ögonblicks bilder. 

* Käll-eller mål-SQL-skriptet för att bevilja behörighet för data resurs körs inte. Eller för Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW) körs den med SQL-autentisering i stället för Azure Active Directory autentisering.  
* Käll-eller mål-SQL data lagret har pausats.
* SQL-datatyper stöds inte av ögonblicks bild processen eller mål data lagret. Mer information finns i [Dela från SQL-källor](how-to-share-from-sql.md#supported-data-types) .
* Käll-eller mål-SQL data lagret har låsts av andra processer. Azure Data Share tillämpar inte lås på käll-och mål-SQL data lager. Befintliga lås på käll-och mål-SQL data lagret leder dock till att ögonblicks bilder Miss lyckas.
* Mål-SQL-tabellen refereras till av en sekundär nyckel begränsning. Om det finns en mål tabell med samma namn i ögonblicks bilden, tas tabellen bort och en ny tabell skapas i Azure Data Share. Om en sekundär nyckel begränsning refereras till mål-SQL-tabellen går det inte att släppa tabellen.
* Mål-CSV-filen genereras, men det går inte att läsa data i Excel. Detta kan inträffa när käll-SQL-tabellen innehåller data med tecken som inte är engelska. I Excel, Välj fliken Hämta data och välj CSV-filen, Välj fil ursprung som 65001: Unicode (UTF-8) och Läs in data.

## <a name="snapshot-issue-after-updating-snapshot-schedule"></a>Problem med ögonblicks bild efter uppdatering av ögonblicks bild schema
När dataprovidern har uppdaterat ögonblicks bilds schema för den skickade resursen måste data konsumenten inaktivera schemat för tidigare ögonblicks bilder och återaktivera det uppdaterade ögonblicks bilds schemat för den mottagna resursen. 

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) . 

Om du vill lära dig hur du tar emot data fortsätter du till kursen för att [godkänna och ta emot data](subscribe-to-data-share.md) .