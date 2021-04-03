---
title: Översikt över FQDN-taggar för Azure-brandvägg
description: En FQDN-tagg representerar en grupp med fullständigt kvalificerade domän namn (FQDN) som är kopplade till välkända Microsoft-tjänster.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c0bd2f6a021baae80dafcc3d544e1062d8e022e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94653342"
---
# <a name="fqdn-tags-overview"></a>Översikt över FQDN-Taggar

En FQDN-tagg representerar en grupp med fullständigt kvalificerade domän namn (FQDN) som är kopplade till välkända Microsoft-tjänster. Du kan använda en FQDN-tagg i program regler för att tillåta nödvändig utgående nätverks trafik genom brand väggen.

Om du till exempel vill tillåta Windows Update nätverks trafik manuellt genom brand väggen måste du skapa flera program regler per Microsoft-dokumentationen. Med hjälp av FQDN-taggar kan du skapa en program regel, inkludera taggen **Windows updates** och nu kan nätverks trafik till Microsoft Windows Update-slutpunkter flöda genom brand väggen.

Du kan inte skapa egna FQDN-taggar, eller så kan du inte ange vilka FQDN: er som ingår i en tagg. Microsoft hanterar FQDN: er som omfattas av FQDN-taggen och uppdaterar taggen som FQDN-ändringar. 

<!--- screenshot of application rule with a FQDN tag.-->

I följande tabell visas de aktuella FQDN-taggar som du kan använda. Microsoft underhåller dessa taggar och du kan vänta på att ytterligare Taggar läggs till regelbundet.

## <a name="current-fqdn-tags"></a>Aktuella FQDN-Taggar

|FQDN-tagg  |Beskrivning  |
|---------|---------|
|Windows Update     |Tillåt utgående åtkomst till Microsoft Update enligt beskrivningen i [så här konfigurerar du en brand vägg för program uppdateringar](/mem/configmgr/sum/get-started/install-a-software-update-point).|
|Windows-diagnostik|Tillåt utgående åtkomst till alla [slut punkter för Windows-diagnostik](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|Tillåt utgående åtkomst till [Maps](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service-miljön (ASE)|Tillåt utgående åtkomst till ASE-plattformstrafik. Den här taggen avser inte kundspecifik lagring och SQL-slutpunkter som skapats av ASE. De bör aktive ras via [tjänst slut punkter](../virtual-network/tutorial-restrict-network-access-to-resources.md) eller läggas till manuellt.<br><br>Mer information om hur du integrerar Azure-brandväggen med ASE finns i [låsa en app service-miljön](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Tillåter utgående åtkomst till Azure Backup-tjänsterna.|
|Azure HDInsight|Tillåter utgående åtkomst för HDInsight-plattforms trafik. Den här taggen avser inte kundspecifik lagring eller SQL-trafik från HDInsight. Aktivera dessa med hjälp av [tjänst slut punkter](../virtual-network/tutorial-restrict-network-access-to-resources.md) eller Lägg till dem manuellt.|
|WindowsVirtualDesktop (WVD)|Tillåter utgående plattforms trafik för virtuella Windows-datorer. Den här taggen avser inte distributions-/regionsspecifika lagring och Service Bus slut punkter som skapats av WVD. Dessutom krävs DNS-och KMS-nätverks regler. Mer information om hur du integrerar Azure-brandväggen med WVD finns i [använda Azure Firewall för att skydda fönster distributioner av virtuella skriv bord](protect-windows-virtual-desktop.md).|
|Azure Kubernetes Service (AKS)|Tillåter utgående åtkomst till AKS. Mer information finns i [använda Azure-brandväggen för att skydda AKS-distributioner (Azure Kubernetes service)](protect-azure-kubernetes-service.md).|

> [!NOTE]
> När du väljer FQDN-tagg i en program regel måste fältet protokoll: port anges till **https**.

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar en Azure-brandvägg finns i [Självstudier: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure Portal](tutorial-firewall-deploy-portal.md).