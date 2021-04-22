---
title: Planera och hantera kostnader
titleSuffix: Azure Machine Learning
description: Planera och hantera kostnader för Azure Machine Learning med kostnadsanalys i Azure Portal. Lär dig fler kostnadsbesparande tips för att sänka dina kostnader när du skapar ML-modeller.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: d8f74e438175758b1f09e1809b5eba15c1b26c3c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873639"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Planera och hantera kostnader för Azure Machine Learning

Den här artikeln beskriver hur du planerar och hanterar kostnader för Azure Machine Learning. Först använder du priskalkylatorn för Azure för att planera kostnader innan du lägger till några resurser. När du sedan lägger till Azure-resurserna granskar du de uppskattade kostnaderna. Använd slutligen kostnadsbesparande tips när du tränar din modell med hanterade Azure Machine Learning beräkningskluster.

När du har börjat använda Azure Machine Learning resurser kan du använda kostnadshanteringsfunktionerna för att ange budgetar och övervaka kostnader. Granska även de prognostiserade kostnaderna och identifiera utgiftstrender för att identifiera områden där du kanske vill agera.

Förstå att kostnaderna för Azure Machine Learning bara är en del av månadskostnaden på din Azure-faktura. Om du använder andra Azure-tjänster debiteras du för alla Azure-tjänster och -resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part. Den här artikeln förklarar hur du planerar för och hanterar kostnader för Azure Machine Learning. När du är bekant med att hantera kostnader för Azure Machine Learning kan du använda liknande metoder för att hantera kostnader för alla Azure-tjänster som används i din prenumeration.

När du tränar dina maskininlärningsmodeller kan du använda hanterade Azure Machine Learning för att dra nytta av fler kostnadsbesparande tips:

* Konfigurera dina träningskluster för automatisk skalning
* Ange kvoter för din prenumeration och dina arbetsytor
* Ange avslutningsprinciper för din träningskörning
* Använda lågprioriterade virtuella datorer (VM)
* Använda en azure-reserverad VM-instans

## <a name="prerequisites"></a>Krav

Kostnadsanalys stöder en mängd olika typer av Azure-konton. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto. 

Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Beräkna kostnader innan du använder Azure Machine Learning

Använd [priskalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att beräkna kostnader innan du skapar resurserna i ett Azure Machine Learning konto. Till vänster väljer du **AI + Machine Learning** och väljer sedan Azure Machine Learning **för** att börja.  

Följande skärmbild visar kostnadsuppskattningen med hjälp av kalkylatorn:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Kostnadsuppskattning i Azure-kalkylatorn":::

När du lägger till nya resurser på arbetsytan återgår du till den här kalkylatorn och lägger till samma resurs här för att uppdatera dina kostnadsuppskattningar.

Mer information finns i [Azure Machine Learning priser.](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Förstå den fullständiga faktureringsmodellen för Azure Machine Learning

Azure Machine Learning körs på Azure-infrastruktur som ackumulerar kostnader tillsammans med Azure Machine Learning när du distribuerar den nya resursen. Det är viktigt att förstå att ytterligare infrastruktur kan ackumulera kostnader. Du måste hantera den kostnaden när du gör ändringar i distribuerade resurser. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Kostnader som vanligtvis påförs med Azure Machine Learning

När du skapar resurser för en Azure Machine Learning skapas även resurser för andra Azure-tjänster. De är:

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Grundläggande konto
* [Azure Block Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (generell användning v1)
* [Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Kostnader kan tillkomma efter resursborttagning

När du tar Azure Machine Learning en arbetsyta i Azure Portal eller med Azure CLI finns följande resurser kvar. De fortsätter att ackumulera kostnader tills du tar bort dem.

* Azure Container Registry
* Azure Block Blob Storage
* Key Vault
* Application Insights

Om du vill ta bort arbetsytan tillsammans med dessa beroende resurser använder du SDK:

```python
ws.delete(delete_dependent_resources=True)
```

Om du skapar Azure Kubernetes Service (AKS) på arbetsytan, eller om du kopplar några beräkningsresurser till din arbetsyta, måste du ta bort dem separat [i Azure Portal](https://portal.azure.com).

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Använda Azure-förskottsbetalningskredit med Azure Machine Learning

Du kan betala för Azure Machine Learning med din Azure-förskottsbetalningskredit (kallades tidigare för ekonomiskt åtagande). Du kan dock inte använda Azure-förskottsbetalningar för att betala för avgifter för produkter och tjänster från tredje part, inklusive de från Azure Marketplace.


## <a name="create-budgets"></a>Skapa budgetar

Du kan skapa [budgetar](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att hantera kostnader och skapa [aviseringar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resursgrupper, så de är användbara som en del av en övergripande strategi för kostnadsövervakning. 

Budgetar kan skapas med filter för specifika resurser eller tjänster i Azure om du vill ha mer kornighet i övervakningen. Filter hjälper till att se till att du inte oavsiktligt skapar nya resurser som kostar ytterligare pengar. Mer information om filteralternativen när du skapar en budget finns i [Alternativ för gruppering och filter.](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="export-cost-data"></a>Exportera kostnadsdata

Du kan också [exportera kostnadsdata till](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ett lagringskonto. Detta är användbart när du behöver eller andra för att göra ytterligare dataanalys för kostnader. Ett ekonomiteam kan till exempel analysera data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett schema per dag, vecka eller månad och ange ett anpassat datumintervall. Att exportera kostnadsdata är det rekommenderade sättet att hämta kostnadsdatauppsättningar.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Andra sätt att hantera och minska kostnader för Azure Machine Learning

Använd de här tipsen för att innehålla kostnader för dina beräkningsresurser för maskininlärning.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Använda Azure Machine Learning beräkningskluster (AmlCompute)

Med ständigt föränderliga data behöver du snabb och smidig modellträning och omträning för att upprätthålla korrekta modeller. Kontinuerlig träning kostar dock, särskilt för djupinlärningsmodeller på GPU:er. 

Azure Machine Learning kan använda det hanterade Azure Machine Learning beräkningsklustret, även kallat AmlCompute. AmlCompute stöder en mängd olika GPU- och CPU-alternativ. AmlCompute finns internt för din prenumerations räkning av Azure Machine Learning. Det ger samma säkerhet, efterlevnad och styrning i företagsklass i Azure IaaS-molnskala.

Eftersom dessa beräkningspooler finns i Azures IaaS-infrastruktur kan du distribuera, skala och hantera din träning med samma säkerhets- och efterlevnadskrav som resten av infrastrukturen.  Dessa distributioner sker i din prenumeration och följer styrningsreglerna. Läs mer om [Azure Machine Learning beräkning](how-to-create-attach-compute-cluster.md).

### <a name="configure-training-clusters-for-autoscaling"></a>Konfigurera träningskluster för autoskalning

Autoskalning av kluster baserat på kraven för din arbetsbelastning hjälper till att minska dina kostnader så att du bara använder det du behöver.

AmlCompute-kluster är utformade för att skala dynamiskt baserat på din arbetsbelastning. Klustret kan skalas upp till det maximala antalet noder som du konfigurerar. När varje körning slutförs släpper klustret noderna och skalas till det konfigurerade minsta antalet noder.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Du kan också konfigurera hur lång tid noden är inaktiv innan nedskalning. Som standard är inaktivitetstiden innan nedskalning inställd på 120 sekunder.

+ Om du utför mindre iterativa experiment kan du minska den här tiden för att spara kostnader.
+ Om du utför mycket iterativa utvecklings-/testexperiment kan du behöva öka tiden så att du inte betalar för konstant upp- och nedskalning efter varje ändring i ditt träningsskript eller din miljö.

AmlCompute-kluster kan konfigureras för dina föränderliga arbetsbelastningskrav i Azure Portal med hjälp av [AmlCompute SDK-klassen](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) [, AmlCompute CLI](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute), med [REST-API:erna](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Ange kvoter för resurser

AmlCompute levereras med en [kvotkonfiguration (eller gräns).](how-to-manage-quotas.md#azure-machine-learning-compute) Den här kvoten är per VM-familj (till exempel Dv2-serien, NCv3-serien) och varierar efter region för varje prenumeration. Prenumerationer börjar med små standardvärden för att komma igång, men använd den här inställningen för att styra hur många Amlcompute-resurser som är tillgängliga att användas i din prenumeration. 

Konfigurera även kvot [på arbetsytenivå efter VM-familj](how-to-manage-quotas.md#workspace-level-quotas), för varje arbetsyta i en prenumeration. På så sätt kan du få mer detaljerad kontroll över de kostnader som varje arbetsyta potentiellt kan medföra och begränsa vissa VM-familjer. 

Om du vill ange kvoter på arbetsytenivå börjar du [i Azure Portal](https://portal.azure.com).  Välj en arbetsyta i din prenumeration och **välj Användning + kvoter** i den vänstra rutan. Välj sedan **fliken Konfigurera kvoter** för att visa kvoterna. Du behöver behörighet i prenumerationsomfånget för att ange kvoten, eftersom det är en inställning som påverkar flera arbetsytor.

### <a name="set-run-autotermination-policies"></a>Ange principer för att köra autoterminering 

I vissa fall bör du konfigurera dina träningskörningar för att begränsa deras varaktighet eller avsluta dem tidigt. När du till exempel använder Azure Machine Learning inbyggda hyperparameterjustering eller automatiserad maskininlärning.

Här är några alternativ som du har:
* Definiera en parameter med namnet i Din RunConfiguration för att styra den maximala varaktighet som en körning kan utökas till på den beräkning du väljer (antingen lokal beräkning eller `max_run_duration_seconds` fjärrmolnbearbetning).
* För [justering av hyperparametern](how-to-tune-hyperparameters.md#early-termination)definierar du en princip för tidig avslutning från en Grupprincip, en princip för medianstopp eller en princip för val av trunkering. Om du vill styra hyperparameterns genomsökning ytterligare använder du parametrar som `max_total_runs` eller `max_duration_minutes` .
* För [automatiserad maskininlärning](how-to-configure-auto-train.md#exit)anger du liknande avslutningsprinciper med hjälp av  `enable_early_stopping` flaggan . Använd även egenskaper som `iteration_timeout_minutes` och för att styra den maximala `experiment_timeout_minutes` varaktigheten för en körning eller för hela experimentet.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Använda lågprioriterade virtuella datorer

Med Azure kan du använda överflödig outnyttjad kapacitet som Low-Priority virtuella datorer i VM-skalningsuppsättningar, Batch och Machine Learning tjänsten. Dessa allokeringar är förebyggande men har ett lägre pris jämfört med dedikerade virtuella datorer. I allmänhet rekommenderar vi att du använder Low-Priority virtuella datorer för Batch-arbetsbelastningar. Du bör också använda dem där avbrott kan återställas antingen genom att skicka om (för Batch-inferens) eller genom omstarter (för djupinlärningsträning med kontrollpunkter).

Low-Priority virtuella datorer har en enda kvot som är separat från det dedikerade kvotvärdet, som är av VM-familj. Läs [mer om AmlCompute-kvoter](how-to-manage-quotas.md).

 Low-Priority virtuella datorer fungerar inte för beräkningsinstanser, eftersom de behöver stöd för interaktiva notebook-upplevelser.

### <a name="use-reserved-instances"></a>Använda reserverade instanser

Ett annat sätt att spara pengar på beräkningsresurser är Azure Reserved VM Instance. Med det här erbjudandet förbinder du dig till ett eller tre års villkor. Dessa rabatter är upp till 72 % av betala per användning-priserna och tillämpas direkt på din månatliga Azure-faktura.

Azure Machine Learning Compute har inbyggda funktioner för reserverade instanser. Om du köper en reserverad instans på ett år eller tre år tillämpar vi automatiskt rabatt på din Azure Machine Learning hanterad beräkning.


## <a name="next-steps"></a>Nästa steg

- Lär [dig hur du optimerar din molninvestering med Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Läs mer om att hantera kostnader med [kostnadsanalys.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Lär dig mer om hur [du förhindrar oväntade kostnader.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Gå den [guidade Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kursen.
