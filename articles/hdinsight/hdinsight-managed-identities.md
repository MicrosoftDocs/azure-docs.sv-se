---
title: Hanterade identiteter i Azure HDInsight
description: Innehåller en översikt över implementeringen av hanterade identiteter i Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: f2b7f6e8421a735db131bc05605936e8cb2d87eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944123"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Hanterade identiteter i Azure HDInsight

En hanterad identitet är en identitet som registrerats i Azure Active Directory (Azure AD) vars autentiseringsuppgifter hanteras av Azure. Med hanterade identiteter behöver du inte registrera tjänstens huvud namn i Azure AD. Eller Upprätthåll autentiseringsuppgifter, till exempel certifikat.

Hanterade identiteter används i Azure HDInsight för att komma åt Azure AD Domain Services eller komma åt filer i Azure Data Lake Storage Gen2 när det behövs.

Det finns två typer av hanterade identiteter: användare tilldelade och tilldelade system. Azure HDInsight har endast stöd för användar tilldelade hanterade identiteter. HDInsight har inte stöd för systemtilldelade hanterade identiteter. En användare som tilldelats en hanterad identitet skapas som en fristående Azure-resurs, som du sedan kan tilldela till en eller flera Azure Service-instanser. Däremot skapas en systemtilldelad hanterad identitet i Azure AD och aktive ras sedan direkt på en viss Azure-tjänstinstans automatiskt. Livs längden för den systemtilldelade hanterade identiteten är sedan knuten till livs längden för den tjänst instans som den är aktive rad för.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight-hanterad identitets implementering

I Azure HDInsight kan hanterade identiteter endast användas av HDInsight-tjänsten för interna komponenter. Det finns för närvarande ingen metod som stöds för att generera åtkomsttoken med hjälp av hanterade identiteter som är installerade på HDInsight-klusternoder för åtkomst till externa tjänster. För vissa Azure-tjänster som Compute VM: ar implementeras hanterade identiteter med en slut punkt som du kan använda för att hämta åtkomsttoken. Den här slut punkten är för närvarande inte tillgänglig i HDInsight-noder.

Om du behöver starta dina program för att undvika att placera hemligheter/lösen ord i analys jobben (t. ex. SCALA-jobb) kan du distribuera dina egna certifikat till klusternoderna med skript åtgärder och sedan använda certifikatet för att hämta en åtkomsttoken (till exempel för att få åtkomst till Azure-nyckel valvet).

## <a name="create-a-managed-identity"></a>Skapa en hanterad identitet

Hanterade identiteter kan skapas med någon av följande metoder:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

De återstående stegen för att konfigurera den hanterade identiteten beror på scenariot där den ska användas.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Hanterade identitets scenarier i Azure HDInsight

Hanterade identiteter används i Azure HDInsight i flera scenarier. Se relaterade dokument för detaljerade installations-och konfigurations anvisningar:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kundhanterad nyckeldiskkryptering](disk-encryption.md)

HDInsight förnyar automatiskt certifikaten för de hanterade identiteter som du använder för dessa scenarier. Det finns dock en begränsning när flera olika hanterade identiteter används för tids krävande kluster, men certifikat förnyelsen kanske inte fungerar som förväntat för alla hanterade identiteter. Om du planerar att använda tids krävande kluster (t. ex. mer än 60 dagar) rekommenderar vi att du använder samma hanterade identitet för alla scenarier ovan. 

Om du redan har skapat ett långvarigt kluster med flera olika hanterade identiteter och körs på något av följande problem:
 * I ESP-kluster börjar kluster tjänster inte att fungera eller skalas upp och andra åtgärder startar inte med autentiseringsfel.
 * I ESP-kluster, vid ändring av AAD-DS LDAPs-certifikat, uppdateras LDAPs-certifikatet inte automatiskt, och därför kan inte LDAP-synkronisering och automatisk skalning av UPS starta.
 * MSI-åtkomst till ADLS Gen2 start fungerar inte.
 * Det går inte att rotera krypterings nycklar i CMK-scenariot.

sedan bör du tilldela de roller och behörigheter som krävs för ovanstående scenarier till alla de hanterade identiteter som används i klustret. Om du till exempel har använt olika hanterade identiteter för ADLS Gen2-och ESP-kluster, ska båda av dem ha rollerna "lagring av BLOB-dataägare" och "HDInsight Domain Services-deltagare" kopplade till dem för att undvika att köra de här problemen.

## <a name="faq"></a>Vanliga frågor

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Vad händer om jag tar bort den hanterade identiteten när klustret har skapats?

Klustret kommer att köra problem när den hanterade identiteten behövs. Det finns för närvarande inget sätt att uppdatera eller ändra en hanterad identitet när klustret har skapats. Vår rekommendation är att se till att den hanterade identiteten inte tas bort under kluster körningen. Du kan också återskapa klustret och tilldela en ny hanterad identitet.

## <a name="next-steps"></a>Nästa steg

* [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)
