---
title: Ytterligare länkar om AVERT vFXT för Azure
description: Använd de här resurserna om du vill ha mer information om AVERT vFXT för Azure, inklusive AVERT kluster dokumentation och vFXT Management-dokumentation.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 6f9a9f8e6d2a279871ded3dff614be99dd2901df
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271098"
---
# <a name="additional-documentation"></a>Ytterligare dokumentation

Den här artikeln innehåller länkar till ytterligare dokumentation om hanterings gränssnittet AVERT på kontroll panelen och relaterade ämnen.

## <a name="avere-cluster-documentation"></a>AVERT kluster dokumentation

Ytterligare kluster dokumentation för AVERT finns på webbplatsen <https://azure.github.io/Avere/> . Dessa dokument kan hjälpa dig att förstå klustrets funktioner och hur du konfigurerar dess inställningar.

* [Guiden skapa kluster för FXT](<https://azure.github.io/Avere/#fxt_cluster>) är utformad för kluster som består av fysiska maskinvarukonfigurationer, men viss information i dokumentet är också relevant för vFXT-kluster. I synnerhet kan nya vFXT-kluster administratörer ha nytta av att läsa följande avsnitt:
  * Genom att [Anpassa inställningarna för support och övervakning](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) förklaras hur du anpassar inställningarna för överförings överföring och aktiverar fjärr styrning.
  * [Konfiguration av VServers och globalt namn område](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) innehåller information om hur du skapar ett namn område för klienter.
  * När du [konfigurerar DNS för ett AVERT-kluster](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) förklaras hur du konfigurerar resursallokering (Round-Robin).
  * [Lägga till lagrings dokument på Server sidan](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) Lägg till core-filer.

* [Kluster konfigurations guiden](<https://azure.github.io/Avere/#operations>) är en fullständig referens till Inställningar och alternativ för ett AVERT-kluster. Ett vFXT-kluster använder en delmängd av dessa alternativ, men de flesta av samma konfigurations sidor gäller.

* I [instrument panels guiden](<https://azure.github.io/Avere/#operations>) förklaras hur du använder funktionerna för kluster övervakning på fliken aver i kontroll panelen.

## <a name="vfxt-creation-and-management-documentation"></a>dokumentation om skapande och hantering av vFXT

En fullständig guide för att använda vfxt.py, ett skriptbaserade verktyg för att skapa och hantera moln kluster, finns på GitHub: [Cloud Cluster Management med vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).
