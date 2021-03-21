---
title: Ta bort ett Kör som-konto för Azure Automation
description: Den här artikeln beskriver hur du tar bort ett Kör som-konto med PowerShell eller från Azure Portal.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99056151"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Ta bort ett Kör som-konto för Azure Automation

Kör som-konton i Azure Automation tillhandahåller autentisering för att hantera resurser i den klassiska distributions modellen Azure Resource Manager eller Azure med hjälp av Automation-runbooks och andra automatiserings funktioner. Den här artikeln beskriver hur du tar bort ett Kör som-konto eller ett klassiskt kör som-konto. När du utför den här åtgärden behålls Automation-kontot. När du har tagit bort kör som-kontot kan du återskapa det i Azure Portal eller med det angivna PowerShell-skriptet.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto

1. Öppna ditt Automation-konto på Azure Portal.

2. I det vänstra fönstret väljer du **Kör som-konton** i avsnittet konto inställningar.

3. På egenskapssidan för Kör som-konton väljer du antingen Kör som-kontot eller det klassiska Kör som-kontot som du vill ta bort.

4. I rutan Egenskaper för det valda kontot klickar du på **ta bort**.

   ![Ta bort Kör som-konto](media/delete-run-as-account/automation-account-delete-run-as.png)

5. Medan kontot tas bort kan du följa förloppet under **Meddelanden** på menyn.

## <a name="next-steps"></a>Nästa steg

Om du vill återskapa kör som-kontot eller det klassiska kör som-kontot, se [skapa kör som-konton](create-run-as-account.md).