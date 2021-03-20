---
title: 'Exportera API: er från Azure API Management till Power Platform | Microsoft Docs'
description: 'Lär dig att exportera API: er från API Management till Power Platform.'
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 7c4d32dd63120a52fd7351977943574455e5cfad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93146662"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Exportera API: er från Azure API Management till Power Platform 

Medborgarna som använder Microsofts [plattforms plattform](https://powerplatform.microsoft.com) behöver ofta få till gång till de affärs funktioner som utvecklas av professionella utvecklare och distribueras i Azure. Med [Azure API Management](https://aka.ms/apimrocks) kan professionella utvecklare publicera sin backend-tjänst som API: er och enkelt exportera dessa API: er till Power Platform (Power Apps och energi automatisering) som anpassade anslutningar för konsumtion av medborgarna. 

Den här artikeln vägleder dig genom stegen för att exportera API: er från API Management till Power Platform. 

## <a name="prerequisites"></a>Förutsättningar

+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
+ Se till att det finns ett API i API Management-instansen som du vill exportera till Power Platform
+ Se till att du har en Power Apps eller en automatiserad [miljö](/powerapps/powerapps-overview#power-apps-for-admins) 

## <a name="export-an-api"></a>Exportera ett API

1. Gå till din API Management-tjänst i Azure Portal och välj **API: er** på menyn.
2. Klicka på de tre punkterna bredvid det API som du vill exportera. 
3. Välj **Exportera**.
4. Välj **Power Apps och automatisera energi spar läge**.
5. Välj en miljö att exportera API: t till. 
6. Ange ett visnings namn som ska användas som namn på det anpassade anslutnings programmet.  
7. Valfritt, om API: t skyddas av en OAuth 2,0-server, måste du också ange ytterligare information, inklusive `Client ID` ,,, `Client secret` `Authorization URL` `Token URL` och `Refresh URL` .  
8. Välj **Exportera**. 

När exporten är klar navigerar du till din Power app eller Energis par miljö. API: et visas som en anpassad anslutning.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Power Platform](https://powerplatform.microsoft.com/)
* [Lär dig vanliga uppgifter i API Management genom att följa självstudierna](./import-and-publish.md)
