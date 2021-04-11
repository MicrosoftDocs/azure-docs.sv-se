---
title: Ta bort start/stoppa VM v2 (för hands version) – Översikt
description: Den här artikeln beskriver hur du tar bort funktionen starta/stoppa virtuella datorer v2 (för hands version).
services: azure-functions
ms.subservice: ''
ms.date: 03/30/2021
ms.topic: conceptual
ms.openlocfilehash: b4308be8f7494c1cb6f6b4839fc5a2e9717668e3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112032"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>Ta bort start/stoppa VM v2 (för hands version)

När du har aktiverat funktionen starta/stoppa virtuella datorer v2 (för hands version) för att hantera körnings statusen för dina virtuella Azure-datorer kan du sluta använda den. Du kan ta bort den här funktionen genom att ta bort resurs gruppen som är dedikerad för att lagra följande resurser som stöd för att starta/stoppa VM v2 (för hands version):

- Azure Functions program
- Scheman i Azure Logic Apps
- Application Insights-instansen
- Azure-lagringskonto

## <a name="delete-the-dedicated-resource-group"></a>Ta bort den dedikerade resurs gruppen

Om du vill ta bort resurs gruppen följer du stegen som beskrivs i artikeln [Azure Resource Manager resurs grupp och resurs borttagning](../../azure-resource-manager/management/delete-resource-group.md) .

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar om den här funktionen finns i [distribuera Start/Stop v2](deploy.md) (för hands version).