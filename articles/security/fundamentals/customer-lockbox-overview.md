---
title: Customer Lockbox för Microsoft Azure
description: Teknisk översikt över Customer Lockbox för Microsoft Azure, som ger kontroll över moln leverantörs åtkomst när Microsoft kan behöva åtkomst till kund information.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 04/05/2021
ms.openlocfilehash: 80d1e4f39d69f761b801ccec834c0228057e4847
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448533"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Customer Lockbox för Microsoft Azure

> [!NOTE]
> Din organisation måste ha ett support avtal för [Azure](https://azure.microsoft.com/support/plans/) med en minimal **utvecklings** nivå för att använda den här funktionen.

De flesta åtgärder, support och fel sökning som utförs av Microsofts personal och under processorer behöver inte åtkomst till kund information. I sällsynta fall där sådan åtkomst krävs är Customer Lockbox för Microsoft Azure ett gränssnitt för kunder som ska granska och godkänna eller avvisa förfrågningar om kund data åtkomst. Den används i fall där en Microsoft-tekniker behöver åtkomst till kund information, oavsett om det är ett svar till ett support ärende som initieras av kunden eller ett problem som har identifierats av Microsoft.

Den här artikeln beskriver hur du aktiverar Customer Lockbox och hur begär Anden om låsning initieras, spåras och lagras för senare granskningar och granskningar.

<a name='supported-services-and-scenarios-in-general-availability'></a><a name='supported-services-and-scenarios-in-preview'></a>
## <a name="supported-services-and-scenarios"></a>Tjänster och scenarier som stöds

### <a name="general-availability"></a>Allmän tillgänglighet
Följande tjänster är allmänt tillgängliga för Customer Lockbox:

- Azure API Management
- Azure App Service
- Azure Cognitive Services
- Azure Container Registry
- Azure Database for MySQL
- Azure Databricks
- Azure Data Box
- Azure-datautforskaren
- Azure Data Factory
- Azure Database for PostgreSQL
- Azure Functions
- Azure HDInsight
- Azure Kubernetes Service
- Azure Monitor
- Azure Storage
- Azure SQL Database
- Azure-prenumerationsöverföringar
- Azure Synapse Analytics
- Virtuella datorer i Azure (som täcker fjärr skrivbords åtkomst, åtkomst till minnes dum par och Managed Disks)

### <a name="public-preview"></a>Offentlig för hands version
Följande tjänster är för närvarande i för hands version för Customer Lockbox:

- Azure Machine Learning
- Azure Batch

## <a name="enable-customer-lockbox"></a>Aktivera Customer Lockbox

Nu kan du aktivera Customer Lockbox från [modulen Administration](https://aka.ms/customerlockbox/administration) på bladet Customer lockbox.  

> [!NOTE]
> Om du vill aktivera Customer Lockbox måste användar kontot ha [rollen global administratör tilldelad](../../active-directory/roles/manage-roles-portal.md).

## <a name="workflow"></a>Arbetsflöde

Följande steg beskriver ett typiskt arbets flöde för en Customer Lockbox begäran.

1. Någon i en organisation har ett problem med sina Azure-arbetsbelastningar.

2. När den här personen har felsöker problemet, men inte kan åtgärda det, öppnar de ett support ärende från [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Biljetten tilldelas en support tekniker för Azure-kunder.

3. En support tekniker för Azure granskar tjänstbegäran och bestämmer nästa steg för att lösa problemet.

4. Om support teknikern inte kan felsöka problemet med hjälp av standard verktyg och tjänst genererade data, är nästa steg att begära utökade behörigheter genom att använda en just-in-Time (JIT)-åtkomst tjänst. Den här begäran kan ske från den ursprungliga support teknikern eller från en annan tekniker eftersom problemet eskaleras till Azure DevOps-teamet.

5. När åtkomstbegäran har skickats av Azure-teknikern utvärderar just-in-Time-tjänsten begäran med hänsyn till faktorer som:
    - Resursens omfattning
    - Om beställaren är en isolerad identitet eller med Multi-Factor Authentication
    - Behörighets nivåer

    Den här begäran kan även innehålla ett godkännande från interna Microsoft-god kännare, baserat på en JIT-regel. God kännaren kan till exempel vara kund support eller DevOps Manager.

6. När begäran kräver direkt åtkomst till kund information initieras en Customer Lockbox begäran. Till exempel fjärr skrivbords åtkomst till en kunds virtuella dator.

    Begäran **besvaras nu i ett Kundmeddelat** tillstånd och väntar på kundens godkännande innan åtkomst beviljas.

7. Vid kund organisationen får användaren som har [ägar rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) för Azure-prenumerationen ett e-postmeddelande från Microsoft för att meddela dem om den väntande åtkomst förfrågan. Den här personen är utsedd god kännare för Customer Lockbox begär Anden.

    Exempel på e-post:

    ![Azure Customer Lockbox – e-postavisering](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. E-postmeddelandet innehåller en länk till bladet **Customer lockbox** i modulen Administration. Med den här länken loggar den utsedda god kännaren in på Azure Portal för att visa väntande begär Anden som deras organisation har för Customer Lockbox:

    ![Sidan Azure Customer Lockbox-landning](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   Begäran finns kvar i kund kön i fyra dagar. Efter den här tiden upphör åtkomst förfrågan automatiskt att gälla och ingen åtkomst beviljas till Microsoft-tekniker.

9. För att få information om den väntande begäran kan den utsedda god kännaren välja begäran om säker begäran från **väntande begär Anden**:

    ![Azure Customer Lockbox – Visa väntande begäran](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. Den angivna god kännaren kan också välja **ID för tjänstbegäran** för att se begäran om support ärende som skapades av den ursprungliga användaren. Den här informationen innehåller en kontext för varför Microsoft Support aktive ras och historiken för det rapporterade problemet. Exempel:

    ![Azure Customer Lockbox – Visa begäran om support ärende](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Efter att ha granskat begäran väljer den utsedda god kännaren **Godkänn** eller **neka**:

    ![Azure-Customer Lockbox – Välj Godkänn eller neka](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Som ett resultat av valet:
    - **Godkänn**: åtkomst beviljas till Microsoft-teknikern. Åtkomst beviljas för en standard period på åtta timmar.
    - **Neka**: den utökade åtkomst förfrågan från Microsoft-teknikern avvisas och ingen ytterligare åtgärd vidtas.

I gransknings syfte loggas de åtgärder som vidtas i det här arbets flödet i [Customer lockbox begär Anden](#auditing-logs).

## <a name="auditing-logs"></a>Granskning av loggar

Customer Lockbox-loggar lagras i aktivitetsloggar. I Azure Portal väljer du **aktivitets loggar** för att Visa gransknings information som är relaterad till Customer lockbox begär Anden. Du kan filtrera på vissa åtgärder, till exempel:
- **Neka Lockbox-begäran**
- **Skapa Lockbox-begäran**
- **Godkänn Lockbox-begäran**
- **Förfallo datum för säker begäran**

Som exempel:

![Azure Customer Lockbox-aktivitets loggar](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Customer Lockbox-integrering med Azure Security Benchmark

Vi har lanserat en ny bas linje kontroll ([3,13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) i Azures säkerhets benchmark som täcker Customer lockbox tillämplighet. Kunderna kan nu dra nytta av benchmarkn för att granska Customer Lockbox tillämpbarhet för en tjänst.

## <a name="exclusions"></a>Undantag

Customer Lockbox begär Anden utlöses inte i följande tekniska support scenarier:

- Nöd situationer som ligger utanför standard drifts procedurer. Ett större tjänst avbrott kräver till exempel omedelbara åtgärder för att återställa eller återställa tjänster i ett oväntat eller oförutsägbart scenario. De här "Break glas"-händelserna är ovanliga och, i de flesta fall, behöver inte åtkomst till kunddata för att lösa problemet.
- En Microsoft-tekniker får åtkomst till Azure-plattformen som en del av fel sökningen och exponeras oavsiktligt för kund information. Till exempel kan Azures nätverksteam utföra felsökning som resulterar i en paketavbildning på en nätverksenhet. Det är ovanligt att sådana scenarier ger till gång till meningsfulla kvantiteter av kunddata. Kunder kan skydda sina data ytterligare genom att använda under överföring och i rest-kryptering.

Customer Lockbox begär Anden utlöses inte heller av externa juridiska krav för data. Mer information finns i avsnittet om [myndighets begär Anden om data](https://www.microsoft.com/trust-center/) i Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Customer Lockbox är tillgängligt för alla kunder som har ett [support](https://azure.microsoft.com/support/plans/) avtal för Azure med en minimal **utvecklings** nivå. Du kan aktivera Customer Lockbox från [modulen Administration](https://aka.ms/customerlockbox/administration) på bladet Customer lockbox.

Customer Lockbox förfrågningar initieras av en Microsoft-tekniker om den här åtgärden krävs för att följa ett support ärende.
