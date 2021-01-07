---
title: Felsöka Azure Data Share
description: Lär dig hur du felsöker problem med inbjudningar och fel när du skapar eller tar emot data resurser i Azure Data Share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: 3aa1c0b8579bd37d2bb51cbde70997131c696813
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964515"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Felsök vanliga problem i Azure Data Share 

Den här artikeln förklarar hur du felsöker vanliga problem i Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Azure Data Share-inbjudningar 

I vissa fall när nya användare väljer **acceptera inbjudan** i en e-postinbjudan kan de se en tom lista med inbjudningar. 

:::image type="content" source="media/no-invites.png" alt-text="Skärm bild som visar en tom lista med inbjudningar.":::

Det här problemet kan bero på någon av följande orsaker:

* **Azure Data Share-tjänsten är inte registrerad som en resurs leverantör för någon Azure-prenumeration i Azure-klienten.** Det här problemet uppstår när din Azure-klient saknar data resurs resurs. 

    När du skapar en Azure Data Share-resurs registrerar den automatiskt resursprovidern i din Azure-prenumeration. Du kan registrera data delnings tjänsten manuellt med hjälp av följande steg. För att slutföra de här stegen behöver du [deltagar rollen](../role-based-access-control/built-in-roles.md#contributor) för Azure-prenumerationen. 

    1. I Azure-portalen går du till **Prenumerationer**.
    1. Välj den prenumeration som du vill använda för att skapa Azure Data Share-resursen.
    1. Välj **resurs leverantörer**.
    1. Sök efter **Microsoft. DataShare**.
    1. Välj **Register** (Registrera).

* **Inbjudan skickas till ditt e-postalias i stället för din e-postadress för Azure-inloggning.** Om du redan har registrerat Azure Data Share-tjänsten eller skapat en data resurs resurs i Azure-klienten, men du fortfarande inte kan se inbjudan, kan ditt e-postalias visas som mottagare. Kontakta din data leverantör och se till att inbjudan skickas till din e-postadress för Azure-inloggning och inte ditt e-postalias.

* **Inbjudan har redan accepterats.** Länken i e-postmeddelandet tar dig till sidan med **inbjudningar till data resursen** i Azure Portal. Den här sidan listar bara väntande inbjudningar. Accepterade inbjudningar visas inte på sidan. Om du vill visa mottagna resurser och konfigurera Azure Datautforskaren Cluster-inställningen går du till den data resurs resurs som du använde för att acceptera inbjudan.

## <a name="creating-and-receiving-shares"></a>Skapa och ta emot resurser

Följande fel kan uppstå när du skapar en ny resurs, lägger till data uppsättningar eller mappar data uppsättningar:

* Det gick inte att lägga till data uppsättningar.
* Det gick inte att mappa data uppsättningar.
* Det gick inte att bevilja data dela resurs-x-åtkomst till y.
* Du har inte rätt behörighet till x.
* Det gick inte att lägga till Skriv behörigheter för Azure Data Share-kontot till en eller flera av de valda resurserna.

Du kan se något av dessa fel om du har otillräckliga behörigheter för Azure Data Store. Mer information finns i [roller och krav](concepts-roles-permissions.md). 

Du behöver Skriv behörighet för att dela eller ta emot data från ett Azure-datalager. Den här behörigheten är vanligt vis en del av deltagar rollen. 

Om du delar data eller tar emot data från Azure Data Store för första gången behöver du även *Microsoft. Authorization/roll-tilldelningar/Skriv* behörighet. Den här behörigheten är vanligt vis en del av ägar rollen. Även om du har skapat Azure Data Store-resursen är du inte nödvändigt vis ägare till resursen. 

När du har rätt behörigheter tillåter Azure Data Share-tjänsten automatiskt data resurs resursens hanterade identitet för åtkomst till data lagret. Den här processen kan ta några minuter. Om du får problem på grund av den här fördröjningen, försök igen om några minuter.

SQL-baserad delning kräver extra behörigheter. Information om krav finns i [Dela från SQL-källor](how-to-share-from-sql.md).

## <a name="snapshots"></a>Ögonblicksbilder
En ögonblicks bild kan inte utföras av olika orsaker. Öppna ett detaljerat fel meddelande genom att välja Start tid för ögonblicks bilden och sedan status för varje data uppsättning. 

Ögonblicks bilder fungerar ofta inte av följande anledningar:

* Data dela saknar behörighet att läsa från käll data lagret eller skriva till mål data lagret. Mer information finns i [roller och krav](concepts-roles-permissions.md). Om du tar en ögonblicks bild för första gången kan data resurs resursen behöva några minuter för att få åtkomst till Azure Data Store. Försök igen om några minuter.
* Data delnings anslutningen till käll data lagret eller mål data lagret blockeras av en brand vägg.
* En delad data mängd, käll data lager eller mål data lager har tagits bort.

För lagrings konton kan en ögonblicks bild inte utföras eftersom en fil uppdateras vid källan medan ögonblicks bilden sker. Därför kan en 0-byte-fil visas på målet. Efter uppdateringen vid källan bör ögonblicks bilderna lyckas.

För SQL-källor kan en ögonblicks bild inte utföras av följande orsaker:

* Det SQL-skript eller SQL-mål för SQL-skript som beviljar data resurs behörigheten har inte körts. Eller för Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) körs skriptet med hjälp av SQL-autentisering i stället för Azure Active Directory autentisering.  
* Käll data lagret eller mål-SQL data lagret har pausats.
* Ögonblicks bilds processen eller mål data lagret stöder inte SQL-datatyper. Mer information finns i [Dela från SQL-källor](how-to-share-from-sql.md#supported-data-types).
* Käll data lagret eller mål-SQL data lagret är låst av andra processer. Azure Data Share låser inte dessa data lager. Men befintliga lås i dessa data lager kan göra att en ögonblicks bild inte fungerar.
* Mål-SQL-tabellen refereras till av en sekundär nyckel begränsning. Om en mål tabell har samma namn som en tabell i källdata under en ögonblicks bild, släpps tabellen i Azure Data Share och en ny tabell skapas. Om en sekundär nyckel begränsning refereras till mål-SQL-tabellen går det inte att släppa tabellen.
* En CSV-målfil skapas, men det går inte att läsa data i Excel. Du kanske ser det här problemet när SQL-tabellen Source innehåller data som innehåller tecken som inte är engelska. I Excel väljer du fliken **Hämta data** och väljer CSV-filen. Välj filens ursprung **65001: Unicode (UTF-8)** och Läs sedan in data.

## <a name="updated-snapshot-schedules"></a>Uppdaterade ögonblicks bild scheman
När dataprovidern uppdaterar ögonblicks bildens schema för den skickade resursen måste data konsumenten inaktivera schemat för tidigare ögonblicks bilder. Aktivera sedan det uppdaterade ögonblicks bilds schemat för den mottagna resursen. 

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till självstudien [dela data](share-your-data.md) . 

Om du vill lära dig hur du tar emot data fortsätter du till kursen för att [godkänna och ta emot data](subscribe-to-data-share.md) .