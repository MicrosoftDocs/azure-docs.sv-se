---
title: ISO 27001-skisser för delade tjänster
description: Kontroll mappning för ISO 27001-skiss exemplet för delade tjänster. Varje kontroll mappas till en eller flera Azure Policy definitioner som hjälper till med utvärderingen.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 64266e72bf2aa24ac7fbf70f87f27739709fa0be
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626349"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Kontroll mappning för ISO 27001-skiss exemplet för delade tjänster

Följande artikel beskriver hur Azure-ritningar ISO 27001 Shared Services skiss-exemplet mappar till ISO 27001-kontrollerna. Mer information om kontrollerna finns i [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Följande mappningar är till **ISO 27001:2013** -kontrollerna. Använd navigeringen till höger om du vill gå direkt till en bestämd kontroll mappning. Många av de mappade kontrollerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på och välj gransknings **\[ visa \] ISO 27001:2013-kontroller och distribuera särskilda VM-tillägg för att ge stöd för det inbyggda princip initiativet för gransknings krav** .

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen en-till-en-eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-shared/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>En. 6.1.2-ansvars fördelning

Att ha bara en Azure-prenumerations ägare tillåter inte administrativ redundans. Att ha för många Azure-prenumerationer kan däremot öka risken för intrång via ett komprometterat ägar konto. Den här skissen hjälper dig att underhålla ett lämpligt antal prenumerations ägare i Azure genom att tilldela två [Azure policy](../../../policy/overview.md) definitioner som granskar antalet ägare för Azure-prenumerationer. Hantering av prenumerations ägarens behörigheter kan hjälpa dig att implementera lämplig uppdelning av uppgifter.

- Högst 3 ägare bör anges för din prenumeration
- Det bör finnas fler än en ägare som tilldelats din prenumeration

## <a name="a821-classification-of-information"></a>En. 8.2.1-klassificering av information

Azures [tjänst för SQL sårbarhets bedömning](../../../../azure-sql/database/sql-vulnerability-assessment.md) kan hjälpa dig att identifiera känsliga data som lagras i dina databaser och innehåller rekommendationer för att klassificera dessa data. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition för att granska att sårbarheter som identifieras under genomsökningen av SQL sårbarhets bedömning åtgärdas.

- Säkerhets risker i SQL-databaser bör åtgärdas

## <a name="a912-access-to-networks-and-network-services"></a>En. 9.1.2-åtkomst till nätverk och nätverks tjänster

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) hjälper till att hantera vem som har åtkomst till Azure-resurser. Den här skissen hjälper dig att styra åtkomsten till Azure-resurser genom att tilldela sju [Azure policy](../../../policy/overview.md) definitioner. Dessa principer granskar användningen av resurs typer och konfigurationer som kan tillåta mer åtkomst till resurser.
Att förstå resurser som strider mot dessa principer kan hjälpa dig att vidta lämpliga åtgärder för att säkerställa åtkomst till Azure-resurser som är begränsade till behöriga användare.

- Visa gransknings resultat från virtuella Linux-datorer som har konton utan lösen ord
- Visa gransknings resultat från virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord
- Lagrings konton ska migreras till nya Azure Resource Manager resurser
- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser
- Granska virtuella datorer som inte använder hanterade diskar

## <a name="a923-management-of-privileged-access-rights"></a>En. 9.2.3-hantering av privilegierade behörigheter

Den här skissen hjälper dig att begränsa och kontrol lera privilegierade åtkomst rättigheter genom att tilldela fyra [Azure policy](../../../policy/overview.md) -definitioner för att granska externa konton med ägare och/eller Skriv behörigheter och konton med ägare och/eller Skriv behörigheter som inte har Multi-Factor Authentication aktiverat. Rollbaserad åtkomst kontroll i Azure (Azure RBAC) hjälper till att hantera vem som har åtkomst till Azure-resurser. Den här skissen tilldelar också tre Azure Policy definitioner för att granska användningen av Azure Active Directory autentisering för SQL-servrar och Service Fabric. Med hjälp av Azure Active Directory-autentisering möjliggörs förenklad behörighets hantering och centraliserad identitets hantering för databas användare och andra Microsoft-tjänster. Den här skissen tilldelar också en Azure Policy-definition för att granska användningen av anpassade Azure RBAC-regler. Att förstå var anpassade Azure RBAC-regler implementeras kan hjälpa dig att kontrol lera behovet och korrekt implementering, eftersom anpassade Azure RBAC-regler är fel känsliga.

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen
- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med Skriv behörighet bör tas bort från din prenumeration
- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar
- Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering
- Granska användningen av anpassade RBAC-regler

## <a name="a924-management-of-secret-authentication-information-of-users"></a>En. 9.2.4-hantering av information om hemlig autentisering av användare

Den här skissen tilldelar tre [Azure policy](../../../policy/overview.md) definitioner för granskning av konton som inte har Multi-Factor Authentication aktiverat. Multi-Factor Authentication hjälper till att skydda konton även om en viss autentiseringsinformation komprometteras. Genom att övervaka konton utan att Multi-Factor Authentication har Aktiver ATS kan du identifiera konton som kan vara sannolikare. Den här skissen tilldelar också två Azure Policy definitioner som granskar virtuella Linux-lösenords lösen ords fil behörigheter för att varna om de är felaktigt inställda. Med den här inställningen kan du vidta lämpliga åtgärder för att säkerställa att autentiserare inte komprometteras.

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration
- Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen
- Visa gransknings resultat från virtuella Linux-datorer som inte har passwd-filbehörigheterna inställt på 0644

## <a name="a925-review-of-user-access-rights"></a>En. 9.2.5-granskning av användar behörighet

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure Portal kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar fyra [Azure policy](../../../policy/overview.md) definitioner till gransknings konton som ska prioriteras för granskning, inklusive avskrivna konton och externa konton med utökade behörigheter.

- Föråldrade konton bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med Skriv behörighet bör tas bort från din prenumeration

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A. 9.2.6 borttagning eller justering av åtkomst rättigheter

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) och Azure RBAC kan du uppdatera användar roller för att återspegla organisations ändringar. Vid behov kan konton blockeras från att logga in (eller tas bort), vilket omedelbart tar bort åtkomst behörighet till Azure-resurser. Den här skissen tilldelar två [Azure policy](../../../policy/overview.md) definitioner för att granska avskrivet konto som bör tas bort.

- Föråldrade konton bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration

## <a name="a942-secure-log-on-procedures"></a>9.4.2 säkra inloggnings procedurer

Den här skissen tilldelar tre Azure Policy definitioner för granskning av konton som inte har Multi-Factor Authentication aktiverat. Azure AD Multi-Factor Authentication ger ytterligare säkerhet genom att kräva en andra form av autentisering och ger stark autentisering. Genom att övervaka konton utan att Multi-Factor Authentication har Aktiver ATS kan du identifiera konton som kan vara sannolikare.

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration
- Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen

## <a name="a943-password-management-system"></a>Ett. 9.4.3 lösen ords hanterings system

Den här skissen hjälper dig att använda starka lösen ord genom att tilldela 10 [Azure policy](../../../policy/overview.md) -definitioner som granskar virtuella Windows-datorer som inte uppfyller minimi kraven för styrka och andra lösen ord. Om de virtuella datorerna strider mot principen för lösen ords säkerhet kan du vidta åtgärder för att säkerställa att lösen ord för alla VM-användarkonton är kompatibla med principen.

- Visa gransknings resultat från virtuella Windows-datorer där inställningen för lösen ords komplexitet är aktive rad
- Visa gransknings resultat från virtuella Windows-datorer som inte har en högsta ålder för lösen ord på 70 dagar
- Visa gransknings resultat från virtuella Windows-datorer som inte har minsta ålder på lösen ord på 1 dag
- Visa gransknings resultat från virtuella Windows-datorer som inte begränsar minsta längd på lösen ord till 14 tecken
- Visa gransknings resultat från virtuella Windows-datorer som tillåter åter användning av de tidigare 24 lösen orden

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>En. 10.1.1-princip för användning av kryptografiska kontroller

Den här skissen hjälper dig att genomdriva principen om användningen av kryptografiska kontroller genom att tilldela 13 [Azure policy](../../../policy/overview.md) definitioner som tillämpar vissa kryptografiska kontroller och gransknings användningen av svaga krypterings inställningar. Att förstå var dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan hjälpa dig att vidta korrigerande åtgärder för att säkerställa att resurserna konfigureras i enlighet med din informations säkerhets princip. Mer specifikt kräver principerna som tilldelats av den här skissen kryptering för Blob Storage-konton och data Lake Storage-konton. Kräv transparent data kryptering på SQL-databaser; granska saknad kryptering på lagrings konton, SQL-databaser, virtuella dator diskar och variabler för Automation-konton. granska oskyddade anslutningar till lagrings konton, Function-appar, webbappar, API Apps och Redis Cache; granska svag kryptering av lösen ord för virtuella datorer; och granska okrypterad Service Fabric kommunikation.

- Funktionsapp bör endast vara tillgängligt via HTTPS
- Webb program bör endast vara tillgängliga via HTTPS
- API-appen bör bara vara tillgänglig via HTTPS
- Visa gransknings resultat från virtuella Windows-datorer som inte lagrar lösen ord med omvänd kryptering
- Disk kryptering bör tillämpas på virtuella datorer
- Variabler för Automation-konton ska vara krypterade
- Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras
- Säker överföring till lagringskonton ska vara aktiverat
- Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign
- transparent datakryptering på SQL-databaser ska aktive ras

## <a name="a1241-event-logging"></a>Händelse loggning för 12.4.1

Den här skissen hjälper dig att se till att system händelser loggas genom att tilldela sju [Azure policy](../../../policy/overview.md) definitioner som granskar logg inställningar på Azure-resurser.
Diagnostikloggar ger inblick i åtgärder som utfördes i Azure-resurser.

- Granska beroende agent distribution – VM-avbildning (OS) har inte listats
- Granska beroende agent distribution i skalnings uppsättningar för virtuella datorer – VM-avbildning (OS) har inte listats
- [För hands version]: granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- Granska Log Analytics agent distribution i skalnings uppsättningar för virtuella datorer – VM-avbildningen (OS) har inte listats
- Granska diagnostikinställning
- Granskning på SQL Server måste vara aktiverat

## <a name="a1243-administrator-and-operator-logs"></a>En. 12.4.3-administratör och operatörs loggar

Den här skissen hjälper dig att se till att system händelser loggas genom att tilldela sju Azure Policy definitioner som granskar logg inställningar på Azure-resurser. Diagnostikloggar ger inblick i åtgärder som utfördes i Azure-resurser.

- Granska beroende agent distribution – VM-avbildning (OS) har inte listats
- Granska beroende agent distribution i skalnings uppsättningar för virtuella datorer – VM-avbildning (OS) har inte listats
- [För hands version]: granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- Granska Log Analytics agent distribution i skalnings uppsättningar för virtuella datorer – VM-avbildningen (OS) har inte listats
- Granska diagnostikinställning
- Granskning på SQL Server måste vara aktiverat

## <a name="a1244-clock-synchronization"></a>Synkronisering av 12.4.4-klockor

Den här skissen hjälper dig att se till att system händelser loggas genom att tilldela sju Azure Policy definitioner som granskar logg inställningar på Azure-resurser. Azure-loggar förlitar sig på synkroniserade interna klockor för att skapa en tidskorrelerad post av händelser mellan resurser.

- Granska beroende agent distribution – VM-avbildning (OS) har inte listats
- Granska beroende agent distribution i skalnings uppsättningar för virtuella datorer – VM-avbildning (OS) har inte listats
- [För hands version]: granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- Granska Log Analytics agent distribution i skalnings uppsättningar för virtuella datorer – VM-avbildningen (OS) har inte listats
- Granska diagnostikinställning
- Granskning på SQL Server måste vara aktiverat

## <a name="a1251-installation-of-software-on-operational-systems"></a>En. 12.5.1-installation av program vara på operativa system

Anpassningsbar program kontroll är en lösning från Azure Security Center som hjälper dig att styra vilka program som kan köras på dina virtuella datorer i Azure. Den här skissen tilldelar en Azure Policy-definition som övervakar ändringar i uppsättningen tillåtna program. Den här funktionen hjälper dig att styra installationen av program vara och program på virtuella Azure-datorer.

- Anpassningsbara program kontroller för att definiera säkra program ska aktive ras på dina datorer

## <a name="a1261-management-of-technical-vulnerabilities"></a>En. 12.6.1-hantering av tekniska sårbarheter

Den här skissen hjälper dig att hantera problem med informations systemet genom att tilldela fem [Azure policy](../../../policy/overview.md) definitioner som övervakar saknade system uppdateringar, sårbarheter för operativ system, sårbarheter i SQL och virtuella dator sårbarheter i Azure Security Center. Azure Security Center tillhandahåller rapporterings funktioner som gör att du kan få inblick i real tid i säkerhets läget för distribuerade Azure-resurser.

- Övervaka saknade Endpoint Protection i Azure Security Center
- Systemuppdateringar ska ha installerats på dina datorer
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i SQL-databaser bör åtgärdas
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning

## <a name="a1262-restrictions-on-software-installation"></a>12.6.2-begränsningar för program varu installation

Anpassningsbar program kontroll är en lösning från Azure Security Center som hjälper dig att styra vilka program som kan köras på dina virtuella datorer i Azure. Den här skissen tilldelar en Azure Policy-definition som övervakar ändringar i uppsättningen tillåtna program. Begränsningar för program varu installation kan hjälpa dig att minska sannolikheten för att program varu sårbarheter införs.

- Anpassningsbara program kontroller för att definiera säkra program ska aktive ras på dina datorer

## <a name="a1311-network-controls"></a>13.1.1 nätverks kontroller

Den här skissen hjälper dig att hantera och styra nätverk genom att tilldela en [Azure policy](../../../policy/overview.md) definition som övervakar nätverks säkerhets grupper med tillåtna regler. Regler som är för tillåtelse kan tillåta oönskad nätverks åtkomst och bör granskas. Den här skissen tilldelar också tre Azure Policy definitioner som övervakar oskyddade slut punkter, program och lagrings konton. Slut punkter och program som inte skyddas av en brand vägg och lagrings konton med obegränsad åtkomst kan ge oavsiktlig åtkomst till information som finns i informations systemet.

- Åtkomst via slut punkt mot Internet bör vara begränsad
- Lagrings konton bör begränsa nätverks åtkomsten

## <a name="a1321-information-transfer-policies-and-procedures"></a>13.2.1 information överförings principer och procedurer

Skissen hjälper dig att se till att informations överföring med Azure-tjänster är säker genom att tilldela två [Azure policy](../../../policy/overview.md) definitioner för att granska oskyddade anslutningar till lagrings konton och Redis cache.

- Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras
- Säker överföring till lagringskonton ska vara aktiverat

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av ritningen ISO 27001 Shared Services kan du gå till följande artiklar om du vill lära dig mer om arkitekturen och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [ISO 27001-skiss för delade tjänster – översikt](./index.md) 
>  [ISO 27001 skiss för delade tjänster – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
