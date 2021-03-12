---
title: Ytterligare inställningar för Azure API för FHIR
description: Översikt över ytterligare inställningar som du kan ställa in för Azure API för FHIR
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: matjazl
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: 23c4fb6be4e30e78faa1ce411037f828d6518f50
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020253"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Ytterligare inställningar för Azure API för FHIR

I den här instruktions guiden kommer vi att granska de ytterligare inställningar som du kanske vill ställa in i ditt Azure API för FHIR. Det finns ytterligare sidor som kan granskas i ännu mer information.

## <a name="configure-database-settings"></a>Konfigurera databas inställningar

Azure API för FHIR använder databasen för att lagra data. Prestanda för den underliggande databasen beror på antalet enheter för programbegäran (RU) som valts under tjänst etableringen eller i databas inställningarna när tjänsten har etablerats.

Data flödet måste tillhandahållas för att säkerställa att tillräckligt med system resurser är tillgängliga för din databas hela tiden. Hur många ru: er du behöver för ditt program beror på vilka åtgärder du utför. Åtgärder kan vara från enkel läsning och skrivning till mer komplexa frågor.

Mer information om hur du ändrar standardinställningarna finns i [Konfigurera databas inställningar](configure-database.md).

## <a name="access-control"></a>Åtkomstkontroll

Azure API för FHIR tillåter endast behöriga användare att komma åt FHIR-API: et. Du kan konfigurera behöriga användare genom två olika mekanismer. Det primära och rekommenderade sättet att konfigurera åtkomst kontroll är att använda [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/index.yml), som kan nås via det **åtkomst kontroll bladet (IAM)** . Azure RBAC fungerar bara om du vill skydda data Plans åtkomst med hjälp av den Azure Active Directory klient som är associerad med din prenumeration. Om du vill använda en annan klient, erbjuder Azure-API: et för FHIR en lokal mekanism för åtkomst kontroll för data planet. Konfigurations alternativen är inte lika avancerade när du använder den lokala RBAC-mekanismen. Om du vill ha mer information väljer du något av följande alternativ:

* [Azure RBAC för FHIR data plan](configure-azure-rbac.md). Det här är det bästa alternativet när du använder den Azure Active Directory klient som är associerad med din prenumeration.
* [Lokal FHIR data Plans åtkomst kontroll](configure-local-rbac.md). Använd bara det här alternativet om du behöver använda en extern Azure Active Directory-klient för åtkomst kontroll för data plan. 

## <a name="enable-diagnostic-logging"></a>Aktivera diagnostisk loggning
Du kanske vill aktivera diagnostikloggning som en del av installationen för att kunna övervaka din tjänst och ha rätt rapportering för efterlevnad. Mer information om hur du konfigurerar diagnostikloggning finns i vår [instruktion](enable-diagnostic-logging.md) om hur du konfigurerar diagnostikloggning, tillsammans med några exempel frågor. 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>Använd anpassade rubriker för att lägga till data i gransknings loggar
I Azure API för FHIR kanske du vill inkludera ytterligare information i loggarna, som kommer från det anropande systemet. Om du vill använda den här informationen kan du använda anpassade sidhuvuden.

Du kan använda anpassade rubriker för att avbilda flera typer av information. Exempel:

* Information om identitet eller auktorisering
* Anroparens ursprung
* Ursprunglig organisation
* Klient system information (elektronisk hälso post, patient Portal)

Information om hur du lägger till dessa data i gransknings loggarna finns i avsnittet [använda anpassade HTTP-huvuden för att lägga till data i gransknings loggarna](use-custom-headers.md) .

## <a name="next-steps"></a>Nästa steg

I den här instruktions guiden ställer du in ytterligare inställningar för Azure API för FHIR.

Ta en titt på serien med självstudier för att skapa ett webb program som läser FHIR-data.

>[!div class="nextstepaction"]
>[Distribuera JavaScript-program](tutorial-web-app-fhir-server.md)