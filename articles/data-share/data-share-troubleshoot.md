---
title: Felsöka Azure Data Share
description: Lär dig hur du felsöker problem med inbjudningar och fel när du skapar eller tar emot data resurser med Azure Data Share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 08/14/2020
ms.openlocfilehash: c68c9dc961475d6916b1f00e7d4f596bfd8c77dd
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257803"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Felsök vanliga problem i Azure Data Share 

Den här artikeln visar hur du felsöker vanliga problem med Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Azure Data Share-inbjudningar 

I vissa fall, när en ny användare klickar på **acceptera inbjudan** från e-postinbjudan som skickades, kan de visas med en tom lista med inbjudningar. 

![Inga inbjudningar](media/no-invites.png)

Detta kan bero på följande orsaker:

* **Azure Data Share-tjänsten har inte registrerats som en resurs leverantör för någon Azure-prenumeration i Azure-klienten.** Du får det här problemet om det inte finns någon data resurs resurs i din Azure-klient. När du skapar en Azure Data resurs resurs registrerar den automatiskt resurs leverantören i din Azure-prenumeration. Du kan också registrera data resurs tjänsten manuellt genom att följa dessa steg. Du måste ha Azure Contributor-rollen för att slutföra de här stegen.

    1. I Azure Portal navigerar du till **prenumerationer**
    1. Välj den prenumeration som du vill använda för att skapa en Azure Data resurs resurs
    1. Klicka på **resurs leverantörer**
    1. Sök efter **Microsoft. DataShare**
    1. Klicka på **Registrera** 

    Du måste ha [Azure Contributor-rollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) till Azure-prenumerationen för att slutföra de här stegen. 

* **Inbjudan skickas till ditt e-postalias i stället för din e-postadress för Azure-inloggning.** Om du har registrerat Azure Data Share-tjänsten eller redan har skapat en data resurs resurs i Azure-klienten, men ändå inte kan se inbjudan, kan det bero på att providern har angett ditt e-postalias som mottagare i stället för din e-postadress för Azure-inloggning. Kontakta din dataprovider och kontrol lera att de har skickat inbjudan till din Azure-inloggnings-e-postadress och inte ditt e-postalias.

* **Inbjudan har redan accepterats.** Länken i e-postmeddelandet tar dig till sidan med inbjudan till data resursen i Azure Portal, som endast visar väntande inbjudningar. Om du redan har accepterat inbjudan visas den inte längre i Inbjudnings sidan för data resurser. Fortsätt till din data resurs resurs som du använde för att ta emot inbjudan till för att Visa mottagna resurser och konfigurera ditt mål för Azure Datautforskaren kluster inställningen.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Fel vid skapande eller mottagning av en ny resurs

"Det gick inte att lägga till data uppsättningar"

"Det gick inte att mappa data uppsättningar"

"Det gick inte att bevilja data resursen Resource x åtkomst till y"

"Du har inte rätt behörighet till x"

"Det gick inte att lägga till Skriv behörigheter för Azure Data Share-konto till en eller flera av de valda resurserna"

Om du får ovanstående fel när du skapar en ny resurs eller mappar data uppsättningar, kan det bero på otillräcklig behörighet för Azure Data Store. Se [roller och krav](concepts-roles-permissions.md) för nödvändiga behörigheter. 

Du måste ha Skriv behörighet för att dela eller ta emot data från ett Azure-datalager, som vanligt vis finns i deltagar rollen. 

Om det här är första gången du delar eller tar emot data från Azure Data Store, behöver du även *Microsoft. auktorisering/roll tilldelningar/Skriv* behörighet, som vanligt vis finns i ägar rollen. Även om du har skapat Azure Data Store-resursen så blir det inte automatiskt ägaren till resursen. Med rätt behörighet ger Azure Data Share service automatiskt den hanterade identitets åtkomsten för data resursen till data lagret. Den här processen kan ta några minuter att börja gälla. Om det uppstår ett problem på grund av den här fördröjningen, försök igen om några minuter.

SQL-baserad delning kräver ytterligare behörigheter. Mer information finns i fel sökning av SQL-baserad delning.

## <a name="troubleshooting-sql-based-sharing"></a>Felsöka SQL-baserad delning

"Användaren x finns inte i SQL Database"

Om du får det här felet när du lägger till en data uppsättning från en SQL-baserad källa kan det bero på att du inte har skapat någon användare för den hanterade Azure Data Share-identiteten i SQL Database.  Lös problemet genom att köra följande skript:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Om du får det här felet när du mappar data uppsättningen till ett SQL-baserat mål kan det bero på att du inte har skapat någon användare för den hanterade Azure Data Share-identiteten på din SQL Server.  Lös problemet genom att köra följande skript:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Observera att *<share_acc_name>* är namnet på din data resurs resurs.      

Kontrol lera att du har följt alla krav som anges i avsnittet [dela data](share-your-data.md) och [acceptera och ta emot data](subscribe-to-data-share.md) .

## <a name="snapshot-failed"></a>Ögonblicks bild misslyckades
Det gick inte att ta ögonblicks bilder på grund av olika orsaker. Du hittar ett detaljerat fel meddelande genom att klicka på Start tiden för ögonblicks bilden och sedan på status för varje data uppsättning. 

Om fel meddelandet är relaterat till behörighet kontrollerar du att data delnings tjänsten har den behörighet som krävs. Se [roller och krav](concepts-roles-permissions.md) för mer information. Om det här är första gången du tar en ögonblicks bild kan det ta några minuter för data resurs resursen att beviljas åtkomst till Azure Data Store. Vänta några minuter och försök igen.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) . 

Om du vill lära dig hur du tar emot data fortsätter du till kursen för att [godkänna och ta emot data](subscribe-to-data-share.md) .

