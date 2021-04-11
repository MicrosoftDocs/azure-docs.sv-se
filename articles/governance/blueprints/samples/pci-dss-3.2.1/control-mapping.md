---
title: PCI-DSS v 3.2.1 skiss exempel kontroller
description: Kontroll mappning av betalnings kortet bransch Data Security Standard v 3.2.1 skiss exempel till Azure Policy och Azure RBAC.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 028981e7c6fc6e3886781d33079dcdfa0aebcc39
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386101"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Kontroll mappning för PCI-DSS v 3.2.1-skiss exemplet

I följande artikel beskrivs hur du mappar PCI-DSS v 3.2.1-skiss exemplet till PCI-DSS v 3.2.1-kontrollerna. Mer information om kontrollerna finns i [PCI-DSS v 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Följande mappningar är till **PCI-DSS v 3.2.1:2018-** kontrollerna. Använd navigeringen till höger om du vill gå direkt till en bestämd kontroll mappning. Många av de mappade kontrollerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på och välj **PCI v 3.2.1:2018** inbyggda princip initiativ.

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen en-till-en-eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>Skydd för 1.3.2 och 1.3.4-gränser

Den här skissen hjälper dig att hantera och styra nätverk genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakar nätverks säkerhets grupper med tillåtande regler. Regler som är för tillåtelse kan tillåta oönskad nätverks åtkomst och bör granskas. Den här skissen tilldelar en Azure Policy-definition som övervakar oskyddade slut punkter, program och lagrings konton. Slut punkter och program som inte skyddas av en brand vägg och lagrings konton med obegränsad åtkomst kan ge oavsiktlig åtkomst till information som finns i informations systemet.

- Granska obegränsad nätverks åtkomst till lagrings konton
- Åtkomst via slut punkt mot Internet bör vara begränsad

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4. a, 4,1, 4.1. g, 4.1. h och 6.5.3 kryptografiskt skydd

Den här skissen hjälper dig att genomdriva principen med hjälp av kryptografiska kontroller genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som tillämpar vissa kryptografiska kontroller och gransknings användningen av svaga kryptografiska inställningar. Att förstå var dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan hjälpa dig att vidta korrigerande åtgärder för att säkerställa att resurserna konfigureras i enlighet med din informations säkerhets princip. Mer specifikt kräver principerna som tilldelats av den här skissen transparent data kryptering på SQL-databaser. granska saknad kryptering på lagrings konton och variabler för Automation-konto. Det finns också principer för att hantera oskyddade anslutningar till lagrings konton, Function-appar, WebApp, API Apps och Redis Cache och granska okrypterade Service Fabric-kommunikation.

- Funktionsapp bör endast vara tillgängligt via HTTPS
- Webb program bör endast vara tillgängliga via HTTPS
- API-appen bör bara vara tillgänglig via HTTPS
- transparent datakryptering på SQL-databaser ska aktive ras
- Disk kryptering bör tillämpas på virtuella datorer
- Variabler för Automation-konton ska vara krypterade
- Endast säkra anslutningar till din Redis Cache ska vara aktiverade
- Säker överföring till lagringskonton ska vara aktiverat
- Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign
- transparent datakryptering på SQL-databaser ska aktive ras
- Distribuera transparent data kryptering för SQL DB

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5,1, 6,2, 6,6 och 11.2.1 sårbarhets-genomsökning och system uppdateringar

Den här skissen hjälper dig att hantera problem med informations systemet genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakar saknade system uppdateringar, sårbarheter för operativ system, sårbarheter i SQL och virtuella dator sårbarheter i Azure Security Center. Azure Security Center tillhandahåller rapporterings funktioner som gör att du kan få inblick i real tid i säkerhets läget för distribuerade Azure-resurser.

- Övervaka saknade Endpoint Protection i Azure Security Center
- Distribuera standard Microsoft IaaSAntimalware-tillägget för Windows Server
- Distribuera hot identifiering på SQL-servrar
- Systemuppdateringar ska ha installerats på dina datorer
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i SQL-databaser bör åtgärdas
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 och 7.1.3 separering av uppgifter

Att ha bara en Azure-prenumerations ägare tillåter inte administrativ redundans. Att ha för många Azure-prenumerationer kan däremot öka risken för intrång via ett komprometterat ägar konto. Den här skissen hjälper dig att underhålla ett lämpligt antal prenumerations ägare i Azure genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar antalet ägare för Azure-prenumerationer. Hantering av prenumerations ägarens behörigheter kan hjälpa dig att implementera lämplig uppdelning av uppgifter.

- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Högst 3 ägare bör anges för din prenumeration 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3,2, 7.2.1, 8.3.1. a och 8.3.1. b hantering av privilegierade behörigheter

Den här skissen hjälper dig att begränsa och kontrol lera privilegierade åtkomst rättigheter genom att tilldela [Azure policy](../../../policy/overview.md) definitioner för att granska externa konton med behörigheterna ägare, skriv och/eller Läs och anställda konton med ägar-och/eller Skriv behörighet som inte har Multi-Factor Authentication aktiverat. Rollbaserad åtkomst kontroll i Azure (Azure RBAC) hjälper till att hantera vem som har åtkomst till Azure-resurser. Att förstå var anpassade Azure RBAC-regler implementeras kan hjälpa dig att kontrol lera behovet och korrekt implementering, eftersom anpassade Azure RBAC-regler är fel känsliga. Den här skissen tilldelar också [Azure policy](../../../policy/overview.md) definitioner för att granska användningen av Azure Active Directory autentisering för SQL-servrar. Genom att använda Azure Active Directory-autentisering fören klar hantering av behörigheter och centraliserar identitets hanteringen för databas användare och andra Microsoft  
tjänster.
 
- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med Skriv behörighet bör tas bort från din prenumeration
- Externa konton med Läs behörighet bör tas bort från din prenumeration
- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen
- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration
- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar
- Granska användningen av anpassade RBAC-regler

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.5, lägsta behörighet och granskning av användar behörighet

Rollbaserad åtkomst kontroll i Azure (Azure RBAC) hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure Portal kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner till gransknings konton som ska prioriteras för granskning, inklusive avskrivna konton och externa konton med utökade behörigheter.

- Föråldrade konton bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med Skriv behörighet bör tas bort från din prenumeration
- Externa konton med Läs behörighet bör tas bort från din prenumeration

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 borttagning eller justering av åtkomst rättigheter

Rollbaserad åtkomst kontroll i Azure (Azure RBAC) hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure Active Directory och Azure RBAC kan du uppdatera användar roller för att återspegla organisations ändringar. Vid behov kan konton blockeras från att logga in (eller tas bort), vilket omedelbart tar bort åtkomst behörighet till Azure-resurser. Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner till granska avskrivet konto som bör tas bort.

- Föråldrade konton bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3. a, b, 8.2.4. a, b och 8.2.5-baserad autentisering

Den här skissen hjälper dig att använda starka lösen ord genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar virtuella Windows-datorer som inte uppfyller minimi kraven och andra lösen ords krav. Om de virtuella datorerna strider mot principen för lösen ords säkerhet kan du vidta åtgärder för att säkerställa att lösen ord för alla VM-användarkonton är kompatibla med principen.

- \[För hands version \] : granska virtuella Windows-datorer som inte har en högsta ålder för lösen ord på 70 dagar
- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som inte har en högsta ålder för lösen ord på 70 dagar
- \[För hands version \] : granska virtuella Windows-datorer som inte begränsar minsta längd på lösen ord till 14 tecken
- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som inte begränsar minsta längd på lösen ord till 14 tecken
- \[För hands version \] : granska virtuella Windows-datorer som tillåter åter användning av de tidigare 24 lösen orden
- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som tillåter åter användning av de tidigare 24 lösen orden

## <a name="103-and-1054-audit-generation"></a>10,3 och 10.5.4 audit generation

Den här skissen hjälper dig att se till att system händelser loggas genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar logg inställningar på Azure-resurser.
Diagnostikloggar ger inblick i åtgärder som utfördes i Azure-resurser. Azure-loggar förlitar sig på synkroniserade interna klockor för att skapa en tidskorrelerad post av händelser mellan resurser.

- Granskning ska aktive ras för avancerade data säkerhets inställningar på SQL Server
- Granska diagnostikinställning
- Granska gransknings inställningar för SQL Server-nivå
- Distribuera granskning på SQL-servrar
- Lagrings konton ska migreras till nya Azure Resource Manager resurser
- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser

## <a name="1236-and-1237-information-security"></a>Informations säkerhet för 12.3.6 och 12.3.7

Den här skissen hjälper dig att hantera och kontrol lera nätverket genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar de acceptabla nätverks platserna och de godkända företags produkter som tillåts för miljön. Dessa kan anpassas efter varje företag genom princip parametrarna i var och en av dessa principer.

- Tillåtna platser
- Tillåtna platser för resurs grupper

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av PCI-DSS v 3.2.1-skissen kan du gå till följande artiklar för att lära dig mer om översikten och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [PCI – DSS v 3.2.1 skiss – översikt](./index.md) 
>  [PCI-DSS v 3.2.1-skiss – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).