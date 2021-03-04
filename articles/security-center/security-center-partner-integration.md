---
title: Integrera säkerhetslösningar i Azure Security Center | Microsoft Docs
description: Läs hur Azure Security Center integrerar med partners för att förbättra den övergripande säkerheten för dina Azure-resurser.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: memildin
ms.openlocfilehash: ff23a1fa4b631fc10163f22d94ccdbd8cbe657c2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099258"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrera säkerhetslösningar i Azure Security Center
Det här dokumentet hjälper dig att hantera säkerhetslösningar som redan är anslutna till Azure Security Center och lägga till nya.

## <a name="integrated-azure-security-solutions"></a>Integrerade Azure-säkerhetslösningar
Med Security Center är det enkelt att aktivera integrerade säkerhetslösningar i Azure. Fördelarna innefattar:

- **Förenklad distribution**: Security Center erbjuder effektiviserad etablering av integrerade partnerlösningar. För lösningar som program mot skadlig kod och sårbarhets bedömning kan Security Center etablera agenten på dina virtuella datorer. För brand Väggs apparater kan Security Center ta hand om en stor del av nätverks konfigurationen som krävs.
- **Integrerade identifieringar**: säkerhets händelser från partner lösningar samlas in, aggregeras och visas automatiskt som en del av Security Center aviseringar och incidenter. Dessa händelser kombineras också med identifieringar från andra källor för att tillhandahålla funktioner för avancerad hotidentifiering.
- **Enhetlig övervakning och hantering av hälsa**: Kunderna kan använda integrerade hälsohändelser för att snabbt övervaka alla partnerlösningar. Grundläggande hantering finns tillgängligt med enkel åtkomst till avancerad konfiguration med hjälp av partnerlösningen.

För närvarande omfattar integrerade säkerhetslösningar sårbarhets bedömning av [Qualys](https://www.qualys.com/public-cloud/#azure) och [Rapid7](https://www.rapid7.com/products/insightvm/) och [Microsoft Azure brand vägg för webbaserade program på Azure Application Gateway](../web-application-firewall/ag/ag-overview.md).

> [!NOTE]
> Security Center installerar inte Log Analytics-agenten på virtuella partner enheter eftersom de flesta säkerhets leverantörer förhindrar att externa agenter körs på sina enheter.

Mer information om hur du integrerar verktyg för sårbarhets sökning från Qualys, inklusive en inbyggd skanner som är tillgänglig för Azure Defender-kunder, finns i [sårbarhets bedömningar för Azure-Virtual Machines](deploy-vulnerability-assessment-vm.md).

Security Center erbjuder även sårbarhets analys för din:

* SQL-databaser – Se [utforska rapporter om sårbarhets bedömning på instrument panelen för sårbarhets bedömning](defender-for-sql-on-machines-vulnerability-assessment.md#explore-vulnerability-assessment-reports)
* Azure Container Registry avbildningar – se [använda Azure Defender för behållar register för att söka igenom dina avbildningar efter sårbarheter](defender-for-container-registries-usage.md)

## <a name="how-security-solutions-are-integrated"></a>Så här integreras säkerhetslösningar
Azure-säkerhetslösningar som distribueras från Security Center ansluts automatiskt. Du kan också ansluta andra säkerhets data källor, inklusive datorer som körs lokalt eller i andra moln.

[![Integrerings av partnerlösningar](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Hantera integrerade Azure-säkerhetslösningar och andra datakällor

1. Öppna **Security Center** från [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

1. Från Security Center menyn väljer du **säkerhetslösningar**.

På sidan **säkerhets lösningar** kan du se hälsan hos integrerade Azure-säkerhetslösningar och köra grundläggande hanterings uppgifter.

### <a name="connected-solutions"></a>Anslutna lösningar

Avsnittet **anslutna lösningar** innehåller säkerhetslösningar som för närvarande är anslutna till Security Center. Den visar också hälso status för varje lösning.  

![Anslutna lösningar](./media/security-center-partner-integration/connected-solutions.png)

Status för en partner lösning kan vara:

* **Felfritt** (grönt) – inga hälso problem.
* Ej **felfri** (röd) – det finns ett hälso problem som kräver omedelbar uppmärksamhet.
* **Stoppad rapportering** (orange) – lösningen har slutat rapportera dess hälsa.
* **Inte rapporterat** (grått) – lösningen har inte rapporterat något än och inga hälso data är tillgängliga. Statusen för en lösning kan vara avrapporterad om den var ansluten nyligen och fortfarande distribueras.

> [!NOTE]
> Om hälso status data inte är tillgängliga visar Security Center datum och tid för den senaste händelsen som togs emot för att indikera om lösningen är rapporterad eller inte. Om inga hälso data är tillgängliga och inga aviseringar togs emot under de senaste 14 dagarna, visar Security Center att lösningen inte är felfri eller rapporterar.
>
>

Välj **Visa** för ytterligare information och alternativ, till exempel:

   - **Lösnings konsol** – öppnar hanterings upplevelsen för den här lösningen.
   - **Länk VM** – öppnar sidan länka program. Här kan du ansluta resurser till partnerlösningen.
   - **Ta bort lösningen**
   - **I**

   ![Information om partnerlösning](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Identifierade lösningar

Security Center identifierar automatiskt säkerhetslösningar som körs i Azure men inte är anslutna till Security Center och visar lösningarna i avsnittet **identifierade lösningar** . Dessa lösningar omfattar Azure-lösningar som [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)och partner lösningar.

> [!NOTE]
> Aktivera **Azure Defender** på prenumerations nivå för funktionen identifierade lösningar. Läs mer i [snabb start: Aktivera Azure Defender](enable-azure-defender.md).

Välj **Anslut** under en lösning för att integrera med Security Center och bli informerad om säkerhets aviseringar.

### <a name="add-data-sources"></a>Lägg till datakällor

Avsnittet **Lägg till datakällor** innehåller andra tillgängliga datakällor som kan anslutas. Om du vill få anvisningar om att lägga till data från någon av dessa källor klickar du på **LÄGG TILL**.

![Datakällor](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Nästa steg

Den här artikeln berättade om hur man integrerar partnerlösningar i Security Center. Information om hur du konfigurerar en integrering med Azure Sentinel eller någon annan SIEM finns i [exportera Security Center data kontinuerligt](continuous-export.md).