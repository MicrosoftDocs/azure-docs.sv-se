---
title: Felsöka autoskalning med Virtual Machine Scale Sets
description: Felsök autoskalning med Virtual Machine Scale Sets. Förstå vanliga problem och hur du löser dem.
author: avirishuv
ms.author: avverma
ms.topic: troubleshooting
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviwer: jushiman
ms.custom: avverma
ms.openlocfilehash: 11302c301bee466f678d544d0c4838c39cec9c8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91818536"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Felsöka automatisk skalning med VM-skalningsuppsättningar
**Problem** – du har skapat en infrastruktur för automatisk skalning i Azure Resource Manager att använda skalnings uppsättningar för virtuella datorer, t. ex. genom att distribuera en mall som den här: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale  – du har definierat dina skalnings regler och den fungerar utmärkt, förutom oavsett hur mycket belastning du lägger till på de virtuella datorerna, skalas den inte.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Några saker att tänka på är:

* Hur många virtuella processorer har varje virtuell dator och du läser in varje vCPU?
  Föregående exempel på Azure snabb starts mal len har ett do_work. php-skript som läser in en enda vCPU. Om du använder en virtuell dator som är större än en vCPU VM-storlek som Standard_A1 eller D1 måste du köra den här inläsningen flera gånger. Kontrol lera hur många virtuella processorer som finns för dina virtuella datorer genom att granska [storlekar för virtuella Windows-datorer i Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Hur många virtuella datorer i den virtuella datorns skalnings uppsättning arbetar du med var och en?
  
    En skalnings händelse inträffar bara när genomsnitts processorn över **alla** virtuella datorer i en skalnings uppsättning överskrider tröskelvärdet under den tid som anges internt i reglerna för autoskalning.
* Saknar du några skalnings händelser?
  
    Kontrol lera gransknings loggarna i Azure Portal för skalnings händelser. Det kanske gick att skala upp och skala ned som missades. Du kan filtrera efter "Scale".
  
    ![Granskningsloggar][audit]
* Är ditt skalnings-och skalnings tröskelvärden tillräckligt annorlunda?
  
    Anta att du anger en regel för att skala ut när den genomsnittliga CPU: n är större än 50% över fem minuter och skalas i när genomsnitts processor är mindre än 50%. Den här inställningen skulle orsaka ett "växlar"-problem när CPU-användningen ligger nära tröskelvärdet, med skalnings åtgärder som ständigt ökar och minskar storleken på uppsättningen. På grund av den här inställningen försöker AutoScale-tjänsten förhindra "växlar", vilket kan ge manifest som icke-skalning. Därför bör du se till att tröskelvärdena för skalning och skalnings tröskel är tillräckligt olika för att ge utrymme mellan skalning.
* Skrev du din egen JSON-mall?
  
    Det är enkelt att göra misstag, så börja med en mall som den ovan som är beprövad att fungera och gör små stegvisa ändringar. 
* Kan du skala in eller ut manuellt?
  
    Försök att distribuera om den virtuella datorns skalnings uppsättnings resurs med en annan "kapacitet"-inställning för att ändra antalet virtuella datorer manuellt. En exempel mall är här: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – du kan behöva redigera mallen för att se till att den har samma maskin storlek som skalnings uppsättningen använder. Om du kan ändra antalet virtuella datorer manuellt, vet du att problemet är isolerat till autoskalning.
* Kontrol lera dina Microsoft. Compute/virtualMachineScaleSet-och Microsoft. Insights-resurser i [Azure Resource Explorer](https://resources.azure.com/)
  
    Azure Resource Explorer är ett oumbärligt fel söknings verktyg som visar statusen för dina Azure Resource Manager-resurser. Klicka på din prenumeration och titta på den resurs grupp som du felsöker. Under Compute Resource providern tittar du på den skalnings uppsättning för virtuella datorer som du skapade och kontrollerar vyn instans, som visar status för en distribution. Kontrol lera också instans visningen av virtuella datorer i skalnings uppsättningen för den virtuella datorn. Gå sedan till Microsoft. Insights-resurs leverantören och kontrol lera att reglerna för autoskalning ser rätt ut.
* Fungerar diagnostiskt tillägg att arbeta med prestanda data?
  
    **Uppdatera:** Den automatiska skalningen i Azure har förbättrats för att använda en värdbaserad Mät pipeline, som inte längre kräver att ett diagnostiskt tillägg installeras. Efterföljande stycken gäller inte längre om du skapar ett program för automatisk skalning med hjälp av den nya pipelinen. Ett exempel på Azure-mallar som har konverterats för att använda värd pipelinen finns här: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale . 
  
    Att använda värdbaserade mått för autoskalning är bättre av följande orsaker:
  
  * Färre rörliga delar eftersom inga diagnostiska tillägg behöver installeras.
  * Enklare mallar. Lägg bara till insikter för automatiska skalnings regler i en befintlig mall för skalnings uppsättningar.
  * Mer tillförlitlig rapportering och snabbare start av nya virtuella datorer.
    
    Det enda skälet till att du kanske vill fortsätta använda ett diagnostiskt tillägg är om du behöver rapportering/skalning för Minnesdiagnostik. Värdbaserade mått rapporterar inte minne.
    
    Med detta i åtanke kan du bara följa resten av den här artikeln om du använder diagnostiska tillägg för automatisk skalning.
    
    Autoskalning i Azure Resource Manager kan fungera (men inte längre) med hjälp av ett VM-tillägg som kallas Diagnostics-tillägget. Den genererar prestanda data till ett lagrings konto som du definierar i mallen. Dessa data sammanställs sedan av Azure Monitors tjänsten.
    
    Om tjänsten Insights inte kan läsa data från de virtuella datorerna ska du skicka ett e-postmeddelande till dig. Du får till exempel ett e-postmeddelande om de virtuella datorerna inte är igång. Se till att kontrol lera din e-postadress på den e-postadress som du angav när du skapade ditt Azure-konto.
    
    Du kan också titta på data själv. Titta på Azure Storage-kontot med hjälp av en moln Utforskare. Du kan till exempel använda [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2)för att logga in och välja den Azure-prenumeration du använder. Titta sedan på det namn på lagrings kontot för diagnostik som refereras i definitionen för diagnostik-tillägg i distributions mal len.
    
    ![Cloud Explorer][explorer]
    
    Du ser en massa tabeller där data från varje virtuell dator lagras. Ta Linux och CPU-måttet som exempel, titta på de senaste raderna. I Visual Studio Cloud Explorer har du stöd för ett frågespråk så att du kan köra en fråga. Du kan till exempel köra en fråga för "timestamp gt datetime" 2016-02-02T21:20:00Z "" för att se till att du får de senaste händelserna. Tids zonen motsvarar UTC. Motsvarar de data du ser i de skalnings regler som du ställer in? I följande exempel har processorn för maskin 20 börjat öka till 100% under de senaste fem minuterna.
    
    ![Lagrings tabeller][tables]
    
    Om data inte finns där innebär det att problemet är löst med diagnostiskt tillägg som körs i de virtuella datorerna. Om data finns där betyder det att det är problem med dina skalnings regler eller med tjänsten Insights. Kontrol lera [Azure-status](https://azure.microsoft.com/status/).
    
    När du har gått igenom de här stegen kan du prova följande resurser om du fortfarande har problem med autoskalning: 
    * Läs forumen på [Microsoft Q&en fråge sida](/answers/topics/azure-virtual-machines.html)eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure) 
    * Logga ett support samtal. Var beredd på att dela mallen och Visa prestanda data.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
