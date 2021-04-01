---
title: Skapa ett labb i Azure DevTest Labs | Microsoft Docs
description: Den här artikeln vägleder dig genom processen att skapa ett labb med hjälp av Azure Portal och Azure DevTest Labs.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 9fa33a59dd35bfe3469f30f2349f8a08c45bd5e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92058351"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Skapa ett labb i Azure DevTest Labs

Ett labb i Azure DevTest Labs är den infrastruktur som omfattar en grupp med resurser, till exempel virtuella datorer (VM), som låter dig hantera resurserna genom att ange gränser och kvoter. Den här artikeln beskriver steg för steg hur du skapar ett labb med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande om du vill skapa ett labb:

* En Azure-prenumeration. Mer information om köpalternativ för Azure finns i [Så här köper du Azure](https://azure.microsoft.com/pricing/purchase-options/) eller [Kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/). För att kunna skapa labbet måste du vara prenumerationens ägare.

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>Kom igång med Azure DevTest Labs på några få minuter

Genom att klicka på följande länk överförs du till sidan Azure Portal som gör att du kan börja skapa ett nytt labb i Azure DevTest Labs.

[Kom igång med Azure DevTest Labs på några få minuter](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>Fyll i inställningar för ditt nya konto

På sidan **skapa en DevTest labb** fyller du i följande inställningar.

> [!TIP]
> Längst ned på varje sida hittar du en länk som gör att du kan **Ladda ned en mall för automatisering**.

### <a name="basic-settings"></a>Grundläggande inställningar

Som standard visas fliken **grundläggande inställningar** . 

![grundläggande inställningar](./media/devtest-lab-create-lab/basic-settings.png)

Fyll i följande värden:

|Name|Beskrivning|
|---|---|
|**Prenumeration** | Krävs. Välj den **prenumeration** som du vill koppla till labbet.|
|**Resursgrupp**| Krävs. Ange ett **namn på resurs gruppen** för labbet. Skapa en ny om det inte finns någon.|
|**Labb namn**| Krävs. Ange ett **namn** på labbet.|
|**Plats**|Krävs. Välj en plats där du vill lagra labbet.|
|**Offentliga miljöer**| Se [Konfigurera och använda offentliga miljöer](devtest-lab-configure-use-public-environments.md).

### <a name="auto-shutdown-settings"></a>Inställningar för automatisk avstängning

Växla till sidan för **Automatisk avstängning** för att se dess inställningar. Med automatisk avstängning kan du automatiskt stänga av alla datorer i ett labb vid en schemalagd tidpunkt varje dag.

![Fliken Automatisk avstängning](./media/devtest-lab-create-lab/auto-shutdown.png)

På sidan kan du aktivera **Automatisk avstängning** och definiera parametrarna för automatisk avstängning av alla Labbets virtuella datorer. Funktionen automatisk avstängning är främst en kostnadfunktion där du kan ange om du vill att den virtuella datorn ska stängas automatiskt. Du kan ändra inställningarna för automatisk avstängning när du har skapat labbet genom att följa stegen som beskrivs i artikeln [hantera alla principer för ett labb i Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).

### <a name="networking"></a>Nätverk

När du skapar ett labb skapas ett standard nätverk åt dig. Växla till fliken **nätverk** om du vill ändra/konfigurera inställningen som du vill. Välj till exempel ett befintligt virtuellt nätverk.

![Fliken nätverk ](./media/devtest-lab-create-lab/networking.png)

### <a name="tags"></a>Taggar

Ange **NAME** (namn) och **VALUE** (värde) för **Tags** (taggar) om du vill skapa anpassade taggar som läggs till i alla resurser som du skapar i labbet. Taggar är användbara för att hantera och ordna labbresurser efter kategori. Mer information om taggar, inklusive hur du lägger till taggar när du har skapat labbet, finns i [Lägga till taggar i ett labb](devtest-lab-add-tag.md).

![Fliken Taggar ](./media/devtest-lab-create-lab/tags.png)

### <a name="review-and-create"></a>Granska och skapa

När du är färdig väljer du **skapa**. Du kan övervaka statusen för processen för att skapa labb genom att titta i **meddelande** fältet längst upp till höger på Portal sidan. 

![Fliken Skapa](./media/devtest-lab-create-lab/create-1.png)

## <a name="completed-the-creation"></a>Skapandet har slutförts

När du är klar visas knappen **gå till resurs** längst ned på sidan och i meddelande fönstret. Du kan också uppdatera **DevTest Labs** -sidan om du vill se det nyligen skapade labbet i listan över labb.  

![Skapar tjänsten](./media/devtest-lab-create-lab/create-2.png)

Tryck på knappen **gå till resurs** så kommer du till start sidan för ditt nya DevTest Labs-konto.

![Resurs](./media/devtest-lab-create-lab/go-to-resource.png)

Du kan också söka efter **DevTest Labs** i Azure Portal. Välj det nya kontot i listan och gå till start sidan. 

![Tjänsten har skapats](./media/devtest-lab-create-lab/created.png)

## <a name="next-steps"></a>Nästa steg

När du har skapat labbet kan du fundera på följande steg:

* [Säker åtkomst till ett labb](devtest-lab-add-devtest-user.md)
* [Ange labb principer](devtest-lab-set-lab-policy.md)
* [Skapa en labb-mall](devtest-lab-create-template.md)
* [Skapa anpassade artefakter för dina virtuella datorer](devtest-lab-artifact-author.md)
* [Lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md)

