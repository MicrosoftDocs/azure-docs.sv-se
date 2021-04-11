---
title: Översikt över Azure Kubernetes service (AKS)-diagnostik
description: Lär dig mer om att själv diagnostisera kluster i Azure Kubernetes-tjänsten.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yunjchoi
ms.openlocfilehash: ee11221e5484a796b8dbbcb10a323288d3e74756
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011566"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Översikt över Azure Kubernetes service Diagnostics (för hands version)

Fel sökning av problem med Azure Kubernetes service (AKS) är en viktig roll i att underhålla klustret, särskilt om klustret kör verksamhets kritiska arbets belastningar. AKS-diagnostik är en intelligent och självdiagnostik som:
* Hjälper dig att identifiera och lösa problem i klustret. 
* Är Cloud-Native.
* Kräver ingen extra konfigurations-eller fakturerings kostnad.

Den här funktionen är nu i offentlig för hands version. 

## <a name="open-aks-diagnostics"></a>Öppna AKS-diagnostik

Åtkomst till AKS-diagnostik:

1. Navigera till ditt Kubernetes-kluster i [Azure Portal](https://portal.azure.com).
1. Klicka på **diagnostisera och lös problem** i det vänstra navigerings fönstret, som öppnar AKS-diagnostik.
1. Välj en kategori som bäst beskriver problemet med klustret, t. ex. _problem med klusternoden_, genom att:
    * Använd nyckelorden på Start sidans sida.
    * Ange ett nyckelord som bäst beskriver problemet i Sök fältet.

![Startsida](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Visa en diagnostisk rapport

När du har klickat på en kategori kan du Visa en diagnostisk rapport som är speciell för klustret. Diagnostiska rapporter anropar intelligenta eventuella problem i klustret med status ikoner. Du kan öka detalj nivån för varje ämne genom att klicka på **mer info** för att se en detaljerad beskrivning av:
* Ärenden
* Rekommenderade åtgärder
* Länkar till användbara dokument
* Relaterade mått
* Logga data 

Diagnostiska rapporter genereras baserat på klustrets aktuella tillstånd när du har kört olika kontroller. De kan vara användbara för att hitta problem med klustret och förstå nästa steg för att lösa problemet.

![Diagnostisk rapport](./media/concepts-diagnostics/diagnostic-report.png)

![Utökad diagnostisk rapport](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Kluster insikter

Följande diagnostiska kontroller är tillgängliga i **kluster insikter**.

### <a name="cluster-node-issues"></a>Problem med klusternod

Problem med klusternoderna kontrollerar om det finns problem med noder som orsakar att klustret beter sig.

- Problem med nods beredskap
- Nodfel
- Otillräckliga resurser
- Nod saknar IP-konfiguration
- Node CNI-problem
- Noden hittades inte
- Avstängning av nod
- Autentiseringsfel för nod
- Node Kube-proxy inaktuell

### <a name="create-read-update--delete-crud-operations"></a>Skapa, läsa, uppdatera & ta bort (CRUD) åtgärder

CRUD-åtgärder söker efter CRUD-åtgärder som orsakar problem i klustret.

- Fel vid borttagning av Använd undernät
- Åtgärds fel vid borttagning av nätverks säkerhets grupp
- Åtgärds fel vid borttagning av Använd väg tabell
- Refererat resurs etablerings fel
- Fel vid borttagning av offentlig IP-adress
- Distributions problem på grund av distributions kvot
- Åtgärds fel på grund av organisations princip
- Prenumerations registrering saknas
- Etablerings fel för VM-tillägg
- Under näts kapacitet
- Kvoten överskreds

### <a name="identity-and-security-management"></a>Identitets- och säkerhetshantering

Identitets-och säkerhets hantering identifierar autentiserings-och auktoriseringsfel som förhindrar kommunikation till klustret.

- Auktoriseringsfel för nod
- 401 fel
- 403-fel

## <a name="next-steps"></a>Nästa steg

* Samla in loggar för att hjälpa dig att felsöka kluster problem genom att använda [AKS Periscope](https://aka.ms/aksperiscope).

* Läs [avsnittet prioritering Practices](/azure/architecture/operator-guides/aks/aks-triage-practices) i AKS Day-2 Operations Guide.

* Publicera dina frågor eller feedback på [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) genom att lägga till [diag] i rubriken.