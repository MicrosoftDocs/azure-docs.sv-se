---
title: Skydda en immateriell MSSPs-egenskap (Managed Security Service Provider) i Azure Sentinel | Microsoft Docs
description: Lär dig mer om hur hanterade säkerhets tjänst leverantörer (MSSPs) kan skydda den intellektuella egendom som de har skapat i Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: bagol
ms.openlocfilehash: 1c15c341d85fae667ee23883043350340ad866b8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315645"
---
# <a name="protecting-mssp-intellectual-property-in-azure-sentinel"></a>Skydda MSSP intellektuell egendom i Azure Sentinel

Den här artikeln beskriver de metoder som hanterade säkerhets tjänst leverantörer (MSSPs) kan använda för att skydda immateriell egendom som de har utvecklat i Azure Sentinel, till exempel Azure Sentinel Analytics-regler, jakt frågor, spel böcker och arbets böcker.

Vilken metod du väljer beror på hur var och en av dina kunder köper Azure; Oavsett om du agerar som en [leverantör av moln lösningar (CSP)](#cloud-solutions-providers-csp)eller om kunden har ett [Enterprise-avtal-(EA)/pay-as-you-go](#enterprise-agreements-ea--pay-as-you-go-payg) -konto (PAYG). I avsnitten nedan beskrivs var och en av dessa metoder separat.

## <a name="cloud-solutions-providers-csp"></a>Cloud Solutions-leverantörer (CSP)

Om du åter använder Azure som en moln lösnings leverantör (CSP) hanterar du kundens Azure-prenumeration. Tack vare [admin-on-of-of (administrate)](/partner-center/azure-plan-manage)beviljas användare i gruppen admin agents från din MSsP-klient med ägar åtkomst till kundens Azure-prenumeration och kunden har ingen åtkomst som standard.

Om andra användare från MSSP-klienten, utanför gruppen admin agents, behöver åtkomst till kund miljön, rekommenderar vi att du använder [Azure Lighthouse](multiple-tenants-service-providers.md). Med Azure Lighthouse kan du ge användare eller grupper åtkomst till ett särskilt omfång, till exempel en resurs grupp eller prenumeration, med hjälp av någon av de inbyggda rollerna.

Om du behöver ge kund användare åtkomst till Azure-miljön rekommenderar vi att du ger dem åtkomst på *resurs grupps* nivå i stället för hela prenumerationen, så att du kan visa/dölja delar av miljön efter behov.

Exempel:

- Du kan ge kunden åtkomst till flera resurs grupper där deras program finns, men fortfarande behålla Azure Sentinel-arbetsytan i en separat resurs grupp, där kunden inte har åtkomst.

- Använd den här metoden för att göra det möjligt för kunder att Visa valda arbets böcker och spel böcker, som är separata resurser som kan finnas i sina egna resurs grupper.

Även om du beviljar åtkomst på resurs grupps nivå kommer kunderna fortfarande att ha åtkomst till loggdata för de resurser som de har åtkomst till, till exempel loggar från en virtuell dator, även utan åtkomst till Azure Sentinel. Mer information finns i [Hantera åtkomst till Azure Sentinel-data per resurs](resource-context-rbac.md).

> [!TIP]
> Om du behöver ge dina kunder åtkomst till hela prenumerationen kanske du vill se vägledningen i [Enterprise Agreement (EA)/betala per användning (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg).
>

### <a name="sample-azure-sentinel-csp-architecture"></a>Exempel på Azure Sentinel CSP-arkitektur

Följande bild beskriver hur de behörigheter som beskrivs i [föregående avsnitt](#cloud-solutions-providers-csp) kan fungera när du ger till gång till CSP-kunder:

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="Skydda din Azure Sentinel-egendom med CSP-kunder.":::

Titta på den här bilden:

- Användare som beviljats **ägar** åtkomst till CSP-prenumerationen är användare i gruppen admin-agenter i MSsP Azure AD-klienten.
- Andra grupper från MSSP får åtkomst till kund miljön via Azure Lighthouse.
- Kund åtkomst till Azure-resurser hanteras av Azure RBAC på resurs grupps nivå.

    Detta gör det möjligt för MSSPs att dölja Azure Sentinel-komponenter vid behov, till exempel analys regler och jakt frågor.

Mer information finns även i dokumentationen för [Azure Lighthouse](/azure/lighthouse/concepts/cloud-solution-provider).

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>Enterprise Agreement (EA)/betala per användning (PAYG)

Om kunden köper direkt från Microsoft har kunden redan fullständig åtkomst till Azure-miljön och du kan inte dölja vad som är i kundens Azure-prenumeration.

Skydda i stället din immateriell egendom som du har utvecklat i Azure Sentinel enligt följande, beroende på vilken typ av resurs du behöver skydda:

### <a name="analytics-rules-and-hunting-queries"></a>Analys regler och jakt frågor

Analys regler och jakt frågor finns både i Azure Sentinel och kan därför inte skiljas från Azure Sentinel-arbetsytan.

Även om en användare bara har behörighet för Azure Sentinel-läsare, kommer de fortfarande att kunna visa frågan. I det här fallet rekommenderar vi att du är värd för dina analys regler och jakt frågor i din egen MSSP-klient, i stället för till kund innehavaren.

För att göra detta behöver du en arbets yta i din egen klient med Azure Sentinel aktiverat, och du måste också se kund arbets ytan via [Azure Lighthouse](multiple-tenants-service-providers.md).

Om du vill skapa en analys regel eller jakt fråga i MSSP-klienten som refererar till data i kund klienten, måste du använda `workspace` instruktionen enligt följande:

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

När du lägger till en `workspace` instruktion i analys reglerna bör du tänka på följande:

- **Inga aviseringar på kund arbets ytan**. Regler som skapats på det här sättet, skapar inte varningar eller incidenter på kund arbets ytan. Både aviseringar och incidenter finns bara i din MSSP-arbetsyta.

- **Skapa separata aviseringar för varje kund**. När du använder den här metoden rekommenderar vi också att du använder separata varnings regler för varje kund och identifiering, eftersom arbets ytans instruktion är annorlunda i varje enskilt fall.

    Du kan lägga till kund namnet i varnings regel namnet för att enkelt identifiera den kund där aviseringen utlöses. Separata aviseringar kan resultera i ett stort antal regler, som du kanske vill hantera med hjälp av skript eller [Azure Sentinel som kod](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928).

    Exempel:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="Skapa separata regler i din MSSP-arbetsyta för varje kund.":::

- **Skapa separata MSsP-arbetsytor för varje kund**. Att skapa separata regler för varje kund och identifiering kan leda till att du når det maximala antalet analys regler för din arbets yta (512). Om du har många kunder och förväntar dig att uppnå den här gränsen kanske du vill skapa en separat MSSP-arbetsyta för varje kund.

    Exempel:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="Skapa en arbets yta och regler i din MSSP-klient för varje kund.":::

> [!IMPORTANT]
> Nyckeln till att använda den här metoden använder Automation för att hantera en stor uppsättning regler i dina arbets ytor.
>
> Mer information finns i [analys regler för flera arbets ytor](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211)
>

### <a name="workbooks"></a>Arbetsböcker

Om du har utvecklat en Azure Sentinel-arbetsbok som du inte vill att din kund ska kopiera, ska du vara värd för arbets boken i MSSP-klienten. Se till att du har åtkomst till dina kund arbets ytor via Azure Lighthouse och se till att ändra arbets boken så att du kan använda dessa kund arbets ytor.

Exempel:

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="Arbets böcker över arbets ytor":::

Mer information finns i [arbets böcker för flera arbets ytor](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks).

Om du vill att kunden ska kunna visa arbets bokens visualiseringar samtidigt som du behåller kod hemligheten, rekommenderar vi att du exporterar arbets boken till Power BI.

Exportera din arbets bok till Power BI:

- **Gör det enklare att dela arbets bokens visualiseringar**. Du kan skicka en länk till kunden till Power BI instrument panelen där de kan visa rapporterade data, utan att det krävs åtkomst behörighet för Azure.
- **Aktiverar schemaläggning**. Konfigurera Power BI för att skicka e-postmeddelanden med jämna mellanrum som innehåller en ögonblicks bild av instrument panelen för den tiden.

Mer information finns i [importera Azure Monitor loggdata till Power BI](/azure/azure-monitor/visualize/powerbi).

### <a name="playbooks"></a>Spelböcker

Du kan skydda din spel böcker enligt följande, beroende på var de analys regler som utlöser Spelbok har skapats:

- **Analys regler som skapats i arbets ytan MSsP**.  Se till att skapa din spel böcker i MSSP-klienten och att du får alla incident-och aviserings data från MSSP-arbetsytan. Du kan koppla spel böcker varje gång du skapar en ny regel i din arbets yta.

    Exempel:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="Regler som skapats i arbets ytan MSSP.":::

- **Analys regler som skapats på kund arbets ytan**. Använd Azure-Lighthouse för att koppla analys regler från kundens arbets yta till en Spelbok som finns på MSSP-arbetsytan. I det här fallet hämtar Spelbok aviserings-och incident data och annan kund information från kund arbets ytan.

    Exempel:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="Regler som skapats på kund arbets ytan.":::

I båda fallen, om Spelbok behöver åtkomst till kundens Azure-miljö, använder du en användare eller tjänstens huvud namn som har åtkomst via Lighthouse.

Men om Spelbok behöver åtkomst till icke-Azure-resurser i kundens klient organisation, till exempel Azure AD, Office 365 eller Microsoft 365 Defender, måste du skapa ett huvud namn för tjänsten med lämpliga behörigheter i kund klienten och sedan lägga till identiteten i Spelbok.

> [!NOTE]
> Om du använder Automation-regler tillsammans med din spel böcker måste du ange behörigheterna för Automation-regeln för resurs gruppen där spel böcker Live.
> Mer information finns i [behörigheter för Automation-regler för att köra spel böcker](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks).
>

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

- [Azure Sentinel Technical-Spelbok för MSSPs](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [Hantera flera klienter i Azure Sentinel som en MSSP](multiple-tenants-service-providers.md)
- [Utöka Azure Sentinel för arbetsytor och klientorganisationer](extend-sentinel-across-workspaces-tenants.md)
- [Självstudie: Visualisera och övervaka dina data](tutorial-monitor-your-data.md)
- [Självstudie: Konfigurera automatiserade hot svar i Azure Sentinel](tutorial-respond-threats-playbook.md)
