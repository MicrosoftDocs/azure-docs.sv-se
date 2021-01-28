---
title: LDAP-synkronisering i Ranger-och Apache-Ambari i Azure HDInsight
description: Adressera LDAP-synkroniseringen i Ranger-och Ambari och ange allmänna rikt linjer.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fb9035b4d816c1af84b15e6865335aa1bdf86f5b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933414"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>LDAP-synkronisering i Ranger-och Apache-Ambari i Azure HDInsight

HDInsight Enterprise Security Package-kluster (ESP) använder Ranger för auktorisering. Apache Ambari och Ranger både synkroniserar användare och grupper oberoende av varandra och fungerar lite annorlunda. Den här artikeln är avsedd att användas för att adressera LDAP-synkroniseringen i Ranger-och Ambari.

## <a name="general-guidelines"></a>Allmänna riktlinjer

* Distribuera alltid kluster med en eller flera grupper.
* Om du vill använda fler grupper i klustret, kontrollerar du om det är klokt att uppdatera grupp medlemskapen i Azure Active Directory (Azure AD).
* Om du vill ändra kluster grupperna kan du ändra sync-filter genom att använda Ambari.
* Alla ändringar av grupp medlemskap i Azure AD återspeglas i klustret i efterföljande synkroniseringar. Ändringarna måste synkroniseras till Azure AD Domain Services (Azure AD DS) först och sedan till klustret.
* HDInsight-kluster använder Samba/winbind för att projicera grupp medlemskapen på klusternoderna.
* Grupp medlemmar synkroniseras transitivt (alla under grupper och medlemmar) till både Ambari och Ranger. 

## <a name="users-are-synced-separately"></a>Användare synkroniseras separat

 * Ambari och Ranger delar inte användar databasen eftersom de fungerar i två olika syfte. 
   * Om en användare behöver använda Ambari-ANVÄNDARGRÄNSSNITTET måste användaren synkroniseras med Ambari. 
   * Om användaren inte har synkroniserats till Ambari, avvisar Ambari UI/API, men andra delar av systemet fungerar (dessa skyddas av Ranger eller Resource Manager och inte av Ambari).
   * Om du vill inkludera användare eller grupper i Ranger-principer måste huvud kontona synkroniseras explicit i Ranger.

## <a name="ambari-user-sync-and-configuration"></a>Ambari och konfiguration av användar synkronisering

Från huvudnoderna, körs ett cron-jobb `/opt/startup_scripts/start_ambari_ldap_sync.py` varje timme för att schemalägga användar synkroniseringen. Cron-jobbet anropar Ambari REST-API: er för att utföra synkroniseringen. Skriptet skickar en lista med användare och grupper som ska synkroniseras (eftersom användarna inte tillhör de angivna grupperna, båda anges individuellt). Ambari synkroniserar sAMAccountName som användar namn och alla grupp medlemmar, transitivt.

Loggarna bör vara i `/var/log/ambari-server/ambari-server.log` . Mer information finns i [Konfigurera loggnings nivå för Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

I Data Lake kluster används hooken för att skapa användare för att skapa arbetsmapparna för de synkroniserade användarna och de har angetts som ägare till arbetsmapparna. Om användaren inte har synkroniserats till Ambari korrekt, kan det hända att användaren står inför fel i jobb som arbetsmappen inte är korrekt konfigurerad.

## <a name="ranger-user-sync-and-configuration"></a>Ranger användar synkronisering och konfiguration

Ranger har en inbyggd Sync-motor som körs varje timme för att synkronisera användare. Den delar inte användar databasen med Ambari. HDInsight konfigurerar Sök filtret för att synkronisera administratörs användaren, övervaknings enheten och medlemmarna i gruppen som angavs när klustret skapades. Grupp medlemmarna kommer att synkroniseras transitivt:

1. Inaktivera stegvis synkronisering.
1. Aktivera Sync-mappningen för användar gruppen.
1. Ange Sök filtret för att inkludera de transitiva grupp medlemmarna.
1. Synkronisera attributet sAMAccountName för användare och namnattributet för grupper.

### <a name="group-or-incremental-sync"></a>Grupp eller stegvis synkronisering

Ranger har stöd för ett alternativ för gruppsynkronisering, men fungerar som en korsning med användar filter, inte som en union mellan grupp medlemskap och användar filter. Ett vanligt användnings fall för gruppsynkroniserings filter i Ranger är-Group filter: (DN = clusteradmingroup), användar filter: (City = Seattle).

Stegvis synkronisering fungerar bara för användare som redan har synkroniserats (första gången). Stegvis synkronisering kommer inte att synkronisera nya användare som har lagts till i grupperna efter den inledande synkroniseringen.

### <a name="update-ranger-sync-filter"></a>Uppdatera Ranger-synkroniseringsfilter

LDAP-filtret finns i Ambari-ANVÄNDARGRÄNSSNITTET under konfigurations avsnittet Ranger användare-Sync. Det befintliga filtret kommer att ha formatet `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . Se till att du lägger till predikat i slutet och testa filtret med hjälp av `net ads` Sök kommandot eller ldp.exe eller något liknande.

## <a name="ranger-user-sync-logs"></a>Ranger loggar för användar synkronisering

Ranger-användar synkronisering kan inträffa från någon av huvudnoderna. Loggarna finns i `/var/log/ranger/usersync/usersync.log` . Gör så här för att öka utförligheten för loggarna:

1. Logga in på Ambari.
1. Gå till avsnittet Ranger-konfiguration.
1. Gå till avsnittet Advanced **usersync-log4j** .
1. Ändra `log4j.rootLogger` till `DEBUG` nivå. (När du har ändrat det bör det se ut `log4j.rootLogger = DEBUG,logFile,FilterLog` .)
1. Spara konfigurationen och starta om Ranger.

## <a name="known-issues-with-ranger-user-sync"></a>Kända problem med Ranger-synkronisering av användare
* Om grupp namnet innehåller Unicode-tecken, kan inte Ranger-synkronisering synkronisera objektet. Om en användare tillhör en grupp som har internationella tecken, kommer Ranger synkroniserar delvis grupp medlemskap
* Användar namn (sAMAccountName) och grupp namn (namn) måste vara högst 20 tecken långa. Om grupp namnet är längre, behandlas användaren som om de inte tillhör gruppen, när de beräknar behörigheterna.

## <a name="next-steps"></a>Nästa steg

* [Autentiseringsfel i Azure HDInsight](./domain-joined-authentication-issues.md)
* [Synkronisera Azure AD-användare till ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md)
