---
title: Felsök vanliga fel – Azure Database for MySQL
description: Lär dig hur du felsöker vanliga fel vid migrering av användare som är nya i den Azure Database for MySQL tjänsten
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ca75416a66bcf2c90028c7f1dc11fbe23a9a9bd9
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631375"
---
# <a name="common-errors"></a>Vanliga fel

Azure Database for MySQL är en fullständigt hanterad tjänst som drivs av community-versionen av MySQL. MySQL-upplevelsen i en hanterad tjänst miljö kan skilja sig från att köra MySQL i din egen miljö. I den här artikeln visas några vanliga fel som användarna kan stöta på när de migrerar till eller utvecklar Azure Database for MySQL tjänsten för första gången.

## <a name="common-connection-errors"></a>Vanliga anslutnings fel

#### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>FEL 1184 (08S01): avbruten anslutning 22 till DB: "DB-Name" User: ' User ' Host: ' hostIP ' (init_connect kommando misslyckades)
Ovanstående fel inträffar efter lyckad inloggning men innan ett kommando körs när sessionen har upprättats. Ovanstående meddelande anger att du har angett ett felaktigt värde för init_connect Server parametern, vilket gör att sessionen inte kan initieras.

Det finns vissa Server parametrar som require_secure_transport som inte stöds på sessionstillståndet och som därför försöker ändra värdena för dessa parametrar med hjälp av init_connect kan resultera i fel 1184 vid anslutning till MySQL-servern enligt nedan

MySQL> Visa databaser; FEL 2006 (HY000): MySQL-servern har borta ingen anslutning. Försöker återansluta... Anslutnings-ID: 64897 aktuell databas: * * * ingen * * _ fel 1184 (08S01): avbruten anslutning 22 till DB: ' DB-Name ' User: ' User ' värd: ' hostIP ' (init_connect kommando misslyckades)

_ *Resolution**: du bör återställa init_connect värde på fliken Server parametrar i Azure Portal och bara ange de Server parametrar som stöds med hjälp av init_connect-parameter. 


## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Fel på grund av brist på SUPER-privilegium och DBA-roll

Rollen SUPER Privilege och DBA stöds inte för tjänsten. Därför kan det uppstå några vanliga fel som anges nedan:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>FEL 1419: du har inte behörigheten SUPER och binär loggning har Aktiver ATS (du *kanske* vill använda den mindre säkra log_bin_trust_function_creators-variabeln)

Ovanstående fel kan inträffa när du skapar en funktion, utlöses enligt nedan eller importerar ett schema. DDL-instruktionerna som skapa-funktion eller skapa utlösare skrivs till den binära loggen så att den sekundära repliken kan köra dem. SQL-tråden för replikering har fullständig behörighet, vilket kan utnyttjas för att höja privilegierna. För att skydda mot den här risken för att servrar som har binär loggning aktive rad kräver MySQL-motorn lagrad funktion skapare måste ha behörigheten SUPER, förutom den vanliga behörigheten Skapa rutin som krävs. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Lösning**: Lös problemet genom att ange log_bin_trust_function_creators till 1 från [Server parametrar](howto-server-parameters.md) -bladet i portalen, köra DDL-instruktionerna eller importera schemat för att skapa önskade objekt och återställa log_bin_trust_function_creators-parametern till det tidigare värdet efter att det har skapats.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>FEL 1227 (42000) på rad 101: åtkomst nekad; du behöver (minst en av) superprivilegierna för den här åtgärden. Åtgärden misslyckades med ExitCode 1

Ovanstående fel kan uppstå när du importerar en dumpfil eller skapar en procedur som innehåller [avfinare](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html). 

**Lösning**: för att lösa det här felet kan administratörs användaren bevilja behörighet att skapa eller köra procedurer genom att köra kommandot Grant som i följande exempel:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
Alternativt kan du ersätta avfinarna med namnet på den administratörs användare som kör import processen som visas nedan.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```
#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>FEL 1227 (42000) på rad 295: åtkomst nekad; du behöver (minst en av) Super-eller SET_USER_ID behörigheterna för den här åtgärden

Ovanstående fel kan uppstå när du kör skapa vy med definar-instruktioner som en del av att importera en dumpfil eller köra ett skript. Azure Database for MySQL tillåter inte SUPER-privilegier eller SET_USER_ID behörighet till någon användare. 

**Lösning**: 
* Använd avrundare användare för att köra skapa vy om möjligt. Det är troligt att det finns många vyer med olika definare som har olika behörigheter, så det kan vara omöjligt att göra det.  ELLER
* Redigera dumpfilen eller skapa Visa skript och ta bort definar =-instruktionen från dumpfilen eller 
* Redigera dumpfilen eller skapa Visa skript och ersätt avgränsarna med användare med administratörs behörighet som utför importen eller kör skript filen.

> [!Tip] 
> Använd sed eller perl för att ändra en dumpfil eller SQL-skript för att ersätta definar =-instruktionen

## <a name="common-connection-errors-for-server-admin-login"></a>Vanliga anslutnings fel för inloggning för Server administratör

När en Azure Database for MySQL-server skapas tillhandahålls en inloggning av Server administratören av slutanvändaren när servern skapas. Med inloggningen Server administratör kan du skapa nya databaser, lägga till nya användare och bevilja behörigheter. Om Server administratörs inloggningen tas bort, eller om dess lösen ord ändras, kan du börja se anslutnings fel i programmet samtidigt som anslutningarna. Nedan följer några vanliga fel

#### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>FEL 1045 (28000): åtkomst nekad för användare ' username ' @ ' IP-adress ' (Använd lösen ord: Ja)

Ovanstående fel inträffar om:

* Användar namnet finns inte
* Användar namnet för användaren har tagits bort
* lösen ordet har ändrats eller återställts.

**Lösning**: 
* Verifiera om "username" finns som en giltig användare på servern eller har tagits bort av misstag. Du kan köra följande fråga genom att logga in på den Azure Database for MySQL användaren:
  ```sql
  select user from mysql.user;
  ```
* Om du inte kan logga in på MySQL för att köra ovanstående fråga, rekommenderar vi att du [återställer administratörs lösen ordet med hjälp av Azure Portal](howto-create-manage-server-portal.md). Med alternativet Återställ lösen ord från Azure Portal kan du återskapa användaren, återställa lösen ordet och återställa administratörs behörigheterna, vilket gör att du kan logga in med Server administratören och utföra ytterligare åtgärder.

## <a name="next-steps"></a>Nästa steg
Om du inte hittar det svar du söker efter kan du tänka på följande:

- Publicera din fråga på [Microsoft Q&en fråge sida](/answers/topics/azure-database-mysql.html) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Skicka ett e-postmeddelande till Azure Database for MySQLs gruppen [ @Ask Azure dB för MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Den här e-postadressen är inte ett alias för teknisk support.
- Kontakta Azure-supporten, [File a Ticket from the Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
