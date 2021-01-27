---
title: Konfigurera en instans och autentisering (portalen)
titleSuffix: Azure Digital Twins
description: Se så här konfigurerar du en instans av tjänsten Azure Digitals dubbla tjänster med hjälp av Azure Portal
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: d38e631a5a12381b407dca4dd9e2f2ae63365453
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882460"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Konfigurera en digital Azure-instans och autentisering (portal)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Den här artikeln beskriver steg för steg hur du **konfigurerar en ny Azure Digital-instansen**, inklusive hur du skapar instansen och konfigurerar autentisering. När du har slutfört den här artikeln har du en Azure Digital-instansen som är redo att starta programmering mot.

Den här versionen av den här artikeln går igenom de här stegen manuellt, en i taget, med hjälp av Azure Portal. Azure-portalen är en webbaserad, enhetlig konsol som ger ett alternativ till kommandoradsverktyg.
* Om du vill gå igenom dessa steg manuellt med hjälp av CLI, se CLI-versionen av den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (CLI)*](how-to-set-up-instance-cli.md).
* Om du vill köra en automatiserad installation med hjälp av ett skript exempel för distribution, se den skript version som beskrivs i den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (skript)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Skapa Azure Digitals-instansen

I det här avsnittet ska du **skapa en ny instans av Azure Digitals dubbla** med [Azure Portal](https://ms.portal.azure.com/). Navigera till portalen och logga in med dina autentiseringsuppgifter.

Börja med att välja _skapa en resurs_ på Start sidan för Azure-tjänster en gång i portalen.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Välj Skapa en resurs från start sidan för Azure Portal":::

Sök efter *Azure Digital-dubbla* i sökrutan och välj tjänsten **Azure Digitals dubblare** i resultatet. Välj knappen _skapa_ för att skapa en ny instans av tjänsten.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Välj &quot;skapa&quot; från sidan Azure Digitals dubblare tjänst":::

Fyll i värdena som visas nedan på sidan *skapa resurs* :
* **Prenumeration**: den Azure-prenumeration du använder
  - **Resurs grupp**: en resurs grupp där instansen ska distribueras. Om du inte redan har en befintlig resurs grupp i åtanke kan du skapa en här genom att välja länken *Skapa ny* och ange ett namn för en ny resurs grupp
* **Plats**: en Azure Digital-aktiverad region för distributionen. Mer information om regional support finns i [*Azure-produkter som är tillgängliga per region (Azure Digitals)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Resurs namn**: ett namn för din Azure Digital-instansen. Om din prenumeration har en annan Azure Digital-instans i den region som redan använder det angivna namnet uppmanas du att välja ett annat namn.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Fylla i de beskrivna värdena för att skapa en Azure Digital-resurs med dubbla resurser":::

När du är färdig väljer du _Granska + skapa_. Då kommer du till en sammanfattnings sida där du kan granska instans informationen som du har angett och trycka på _skapa_. 

### <a name="verify-success-and-collect-important-values"></a>Verifiera lyckade och samla in viktiga värden

När du har överfört *skapa* kan du visa statusen för din instanss distribution i dina Azure-meddelanden i portalens ikon fält. Meddelandet anger när distributionen har slutförts och du kommer att kunna välja knappen _gå till resurs_ för att visa din skapade instans.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Vy över Azure-meddelanden som visar en lyckad distribution och markerar knappen gå till resurs":::

Alternativt, om distributionen Miss lyckas, indikerar meddelandet varför. Observera råd från fel meddelandet och försök att skapa instansen igen.

>[!TIP]
>När du har skapat din instans kan du när som helst gå tillbaka till sidan genom att söka efter namnet på din instans i Azure Portal Sök fältet.

På *översikts* sidan för instansen noterar du *namnet*, *resurs gruppen* och *värd namnet*. Detta är alla viktiga värden som du kan behöva när du fortsätter att arbeta med din Azure Digital-instansen. Om andra användare kommer att program mera mot instansen bör du dela dessa värden med dem.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Markera viktiga värden från instansens översikts sida":::

Nu har du en Azure Digital-instansen som är redo att sätta igång. Sedan ger du rätt Azure-användare behörighet att hantera den.

## <a name="set-up-user-access-permissions"></a>Konfigurera användar åtkomst behörigheter

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Öppna först sidan för din Azure Digital-instansen i Azure Portal. Från instansens meny väljer du *åtkomst kontroll (IAM)*. Lägg till en ny roll tilldelning genom att klicka på knappen  **+ Lägg till** .

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Välja att lägga till en roll tilldelning från sidan åtkomst kontroll (IAM)":::

På följande sida för *Lägg till roll tilldelning* fyller du i värdena (måste slutföras av en användare med [tillräckliga behörigheter](#prerequisites-permission-requirements) i Azure-prenumerationen):
* **Roll**: Välj *Azure Digitals sammanflätade data ägare* från List menyn
* **Tilldela åtkomst till**: Använd *användare, grupp eller tjänstens huvud namn*
* **Välj**: Sök efter namnet eller e-postadressen för den användare som ska tilldelas. När du väljer resultatet visas användaren i avsnittet *markerade medlemmar* .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Fylla i listade fält i dialog rutan Lägg till roll tilldelning":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Tryck på knappen *Spara* när du är klar med att ange informationen.

### <a name="verify-success"></a>Verifieringen lyckades

Du kan visa roll tilldelningen som du har angett under *åtkomst kontroll (IAM) > roll tilldelningar*. Användaren ska visas i listan med en roll av *Azure Digitals sammanflätade data ägare*. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Vy över roll tilldelningarna för en digital Azure-instans i Azure Portal":::

Nu har du en Azure Digital-instansen som är redo att gå och har tilldelats behörighet att hantera den.

## <a name="next-steps"></a>Nästa steg

Testa enskilda REST API-anrop på din instans med hjälp av Azure Digitals flätat CLI-kommandon: 
* [AZ DT-referens](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Anvisningar: använda Azure Digitals flätat CLI*](how-to-use-cli.md)

Du kan också se hur du ansluter ett klient program till din instans med autentiserings kod:
* [*Instruktion: skriva kod för app-autentisering*](how-to-authenticate-client.md)