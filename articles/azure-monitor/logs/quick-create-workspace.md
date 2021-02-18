---
title: Skapa en Log Analytics arbets yta i Azure Portal | Microsoft Docs
description: Lär dig hur du skapar en Log Analytics arbets yta för att aktivera hanterings lösningar och data insamling från molnet och lokala miljöer i Azure Portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 4129605d043f93a79b4e3a7d5f70ffaa80edb659
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624970"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Skapa en Log Analytics-arbetsyta i Azure-portalen
Använd menyn **Log Analytics arbets ytor** för att skapa en arbets yta för Log Analytics med hjälp av Azure Portal. En Log Analytics-arbetsyta är en unik miljö för Azure Monitor loggdata. Varje arbets yta har sin egen data lagrings plats och konfiguration, och data källor och lösningar har kon figurer ATS för att lagra data i en viss arbets yta. Du behöver en Log Analytics arbets yta om du vill samla in data från följande källor:

* Azure-resurser i din prenumeration
* Lokala datorer som övervakas av System Center Operations Manager
* Enhets samlingar från Configuration Manager 
* Diagnostik eller loggdata från Azure Storage

För andra källor, till exempel virtuella Azure-datorer och virtuella Windows-eller Linux-datorer i din miljö, se följande avsnitt:

*  [Samla in data från virtuella Azure-datorer](../vm/quick-collect-azurevm.md) 
*  [Samla in data från hybrid Linux-datorer](../vm/quick-collect-linux-computer.md)
*  [Samla in data från hybrid Windows-dator](../vm/quick-collect-windows-computer.md)

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Skapa en arbetsyta
1. Klicka på **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics arbets ytor**.

    ![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
2. Klicka på **Lägg till** och välj sedan alternativ för följande objekt:

   * Ange ett namn för den nya **Log Analytics-arbetsytan**, som *DefaultLAWorkspace*. Det här namnet måste vara globalt unikt för alla Azure Monitor prenumerationer.
   * Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
   * För **resurs grupp** väljer du att använda en befintlig resurs grupp som redan har kon figurer ATS eller skapa en ny.  
   * Välj en tillgänglig **plats**.  Mer information finns i vilka [regioner Log Analytics är tillgängliga i](https://azure.microsoft.com/regions/services/) och söka efter Azure Monitor från fältet **Sök efter ett produkt** .  
   * Om du skapar en arbetsyta i en ny prenumeration som skapats efter 2 april 2018 används prisplanen *Per GB* automatiskt och alternativet för att välja en prisnivå är inte tillgängligt.  Om du skapar en arbets yta för en befintlig prenumeration som skapats före 2 april eller prenumeration som var kopplad till en befintlig Enterprise-avtal-registrering (EA), väljer du önskad pris nivå.  Mer information om de specifika nivåerna finns [Log Analytics pris information](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Skapa Log Analytics resurs bladet](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. När du har angett den nödvändiga informationen i fönsterrutan **Log Analytics-arbetsyta** klickar du på **OK**.  

När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn. 

## <a name="troubleshooting"></a>Felsökning
När du skapar en arbets yta som har tagits bort under de senaste 14 dagarna och i [läget för mjuk borttagning](../logs/delete-workspace.md#soft-delete-behavior)kan åtgärden ha olika resultat beroende på konfigurationen för arbets ytan:
1. Om du anger samma namn på arbets ytan, resurs gruppen, prenumerationen och regionen som i den borttagna arbets ytan återställs din arbets yta, inklusive dess data, konfiguration och anslutna agenter.
2. Om du använder samma arbets ytans namn, men en annan resurs grupp, prenumeration eller region, får du ett fel meddelande *om att arbets ytans namn redan används. Försök* med ett annat. Om du vill åsidosätta den mjuka borttagningen och permanent ta bort arbets ytan och skapa en ny arbets yta med samma namn, följer du dessa steg för att återställa arbets ytan och utföra permanent borttagning:
   - [Återställa](../logs/delete-workspace.md#recover-workspace) din arbetsyta
   - [Ta bort arbetsytan permanent](../logs/delete-workspace.md#permanent-workspace-delete)
   - Skapa en ny arbets yta med samma arbets ytans namn

## <a name="next-steps"></a>Nästa steg
Nu när du har en arbets yta tillgänglig kan du konfigurera insamling av övervakning, köra loggs ökningar för att analysera dessa data och lägga till en hanterings lösning för att tillhandahålla ytterligare data och analytiska insikter. 

* Se [övervaka hälsan för Log Analytics arbets ytan i Azure Monitor](../logs/monitor-workspace.md) skapa aviserings regler för att övervaka arbets ytans hälsa. 
* Se [samla in Azure Service-loggar och-mått för användning i Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace) för att aktivera data insamling från Azure-resurser med Azure-diagnostik eller Azure Storage.
