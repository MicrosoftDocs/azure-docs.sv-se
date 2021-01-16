---
title: Planera och hantera kostnader
titleSuffix: Azure Machine Learning
description: Planera och hantera kostnader för Azure Machine Learning med kostnads analys i Azure Portal. Lär dig mer kostnads besparingar för att sänka kostnaderna när du skapar ML-modeller.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 81d9a6bcbd910a60e1f659e430bbe527eb3077c6
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251751"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Planera och hantera kostnader för Azure Machine Learning

I den här artikeln beskrivs hur du planerar och hanterar kostnader för Azure Machine Learning. Först använder du pris Kalkylatorn för Azure för att planera för kostnader innan du lägger till några resurser. När du lägger till Azure-resurserna granskar du de uppskattade kostnaderna. Använd slutligen kostnads besparingar när du tränar din modell med hanterade Azure Machine Learning beräknings kluster.

När du har börjat använda Azure Machine Learning-resurser kan du använda funktionerna för kostnads hantering för att ställa in budgetar och övervaka kostnader. Granska även de prognostiserade kostnaderna och identifiera utgifts trender för att identifiera områden där du kanske vill handla.

Ta reda på att kostnaderna för Azure Machine Learning bara är en del av månads kostnaderna på din Azure-faktura. Om du använder andra Azure-tjänster debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part. Den här artikeln beskriver hur du planerar för och hanterar kostnader för Azure Machine Learning. När du är bekant med att hantera kostnader för Azure Machine Learning bör du använda liknande metoder för att hantera kostnader för alla Azure-tjänster som används i din prenumeration.

När du tränar dina Machine Learning-modeller använder du hanterade Azure Machine Learning beräknings kluster för att dra nytta av mer kostnads besparingar:

* Konfigurera utbildnings kluster för automatisk skalning
* Ange kvoter för din prenumeration och dina arbets ytor
* Ange avslutnings principer för din utbildnings körning
* Använd virtuella datorer med låg prioritet (VM)
* Använd en Azure-reserverad VM-instans

## <a name="prerequisites"></a>Krav

Kostnadsanalys stöder en mängd olika typer av Azure-konton. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto. 

Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Beräkna kostnader innan du använder Azure Machine Learning

Använd [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att beräkna kostnaderna innan du skapar resurserna i ett Azure Machine Learning konto. Välj **AI + Machine Learning** till vänster och välj sedan **Azure Machine Learning** för att börja.  

Följande skärm bild visar kostnads uppskattningen med hjälp av Kalkylatorn:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Kostnads uppskattning i Azure kalkylatorn":::

När du lägger till nya resurser i din arbets yta går du tillbaka till den här kalkylatorn och lägger till samma resurs här för att uppdatera dina kostnads beräkningar.

Mer information finns i [Azure Machine Learning prissättning](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Förstå den fullständiga fakturerings modellen för Azure Machine Learning

Azure Machine Learning körs på Azure-infrastruktur som periodiserar kostnader tillsammans med Azure Machine Learning när du distribuerar den nya resursen. Det är viktigt att förstå att ytterligare infrastruktur kan periodisera kostnader. Du måste hantera den kostnaden när du gör ändringar i distribuerade resurser. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Kostnader som vanligt vis påförs med Azure Machine Learning

När du skapar resurser för en Azure Machine Learning arbets yta skapas även resurser för andra Azure-tjänster. De är:

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Basic-konto
* [Azure Block Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (generell användning v1)
* [Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Kostnader kan uppstå efter borttagning av resurs

När du tar bort en Azure Machine Learning-arbetsyta i Azure Portal eller med Azure CLI fortsätter följande resurser att existera. De fortsätter att Periodisera kostnader tills du tar bort dem.

* Azure Container Registry
* Azure-Block Blob Storage
* Key Vault
* Application Insights

Om du vill ta bort arbets ytan tillsammans med dessa beroende resurser använder du SDK:

```python
ws.delete(delete_dependent_resources=True)
```

Om du skapar Azure Kubernetes service (AKS) på din arbets yta, eller om du kopplar några beräknings resurser till din arbets yta, måste du ta bort dem separat i [Azure Portal](https://portal.azure.com).

### <a name="using-monetary-credit-with-azure-machine-learning"></a>Använda monetär kredit med Azure Machine Learning

Du kan betala för Azure Machine Learning avgifter med din kredit för ditt EA-belopp. Du kan dock inte använda EA-krediter för att betala för avgifter för produkter och tjänster från tredje part, inklusive de som finns på Azure Marketplace.


## <a name="create-budgets"></a>Skapa budgetar

Du kan skapa [budgetar](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att hantera kostnader och skapa [aviseringar](../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. 

Budgetar kan skapas med filter för vissa resurser eller tjänster i Azure om du vill ha mer detaljerad information i din övervakning. Filter hjälper till att se till att du inte av misstag skapar nya resurser som kostar dig ytterligare pengar. Mer information om filter alternativen när du skapar en budget finns i alternativ för [grupper och filter](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportera kostnadsdata

Du kan också [Exportera dina kostnads data](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) till ett lagrings konto. Detta är användbart när du behöver eller andra för att utföra ytterligare data analyser för kostnader. Ett ekonomi team kan till exempel analysera data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett dags-, vecko-eller månads schema och ange ett anpassat datum intervall. Att exportera kostnads data är det rekommenderade sättet att hämta kostnads data uppsättningar.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Andra sätt att hantera och minska kostnaderna för Azure Machine Learning

Använd de här tipsen för att få kostnader för beräknings resurserna för Machine Learning.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Använda Azure Machine Learning Compute Cluster (AmlCompute)

Med ständigt föränderliga data behöver du snabb och strömlinjeformad modell utbildning och omträning för att upprätthålla korrekta modeller. Kontinuerlig utbildning kommer dock till en kostnad, särskilt för djup inlärnings modeller på GPU: er. 

Azure Machine Learning användare kan använda det hanterade Azure Machine Learning Compute Cluster, även kallat AmlCompute. AmlCompute stöder olika GPU-och CPU-alternativ. AmlCompute är internt värd för prenumerations uppdrag genom Azure Machine Learning. Det ger samma säkerhet, efterlevnad och styrning i företags klass med Azure IaaS Cloud Scale.

Eftersom dessa Compute-pooler finns inuti Azures IaaS-infrastruktur kan du distribuera, skala och hantera din utbildning med samma krav på säkerhet och efterlevnad som resten av infrastrukturen.  Dessa distributioner sker i din prenumeration och följer dina styrnings regler. Läs mer om [Azure Machine Learning Compute](how-to-create-attach-compute-cluster.md).

### <a name="configure-training-clusters-for-autoscaling"></a>Konfigurera utbildnings kluster för automatisk skalning

Automatisk skalning av kluster baserat på kraven i din arbets belastning hjälper dig att minska kostnaderna så att du bara använder det du behöver.

AmlCompute-kluster har utformats för att skalas dynamiskt baserat på din arbets belastning. Klustret kan skalas upp till det maximala antalet noder som du konfigurerar. När varje körning slutförts släpper klustret noderna och skalar till det konfigurerade antalet noder.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Du kan också konfigurera hur lång tid noden är inaktiv innan den skalas ned. Som standard är inaktiv tid före nedskalning inställd på 120 sekunder.

+ Om du utför mindre iterativ experimentering bör du minska den här tiden för att spara kostnaderna.
+ Om du utför en mycket iterativ utveckling/testning experimentering kan du behöva öka tiden så att du inte betalar för kontinuerlig skalning upp och ned efter varje ändring i ditt utbildnings skript eller miljö.

AmlCompute-kluster kan konfigureras för att ändra arbets belastnings kraven i Azure Portal med hjälp av [AMLCOMPUTE SDK-klassen](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py), [AmlCompute CLI](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)med [REST-API: er](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Ange kvoter för resurser

AmlCompute levereras med en [kvot konfiguration (eller gräns)](how-to-manage-quotas.md#azure-machine-learning-compute). Den här kvoten är av VM-serien (till exempel Dv2-serien, NCv3-serien) och varierar beroende på region för varje prenumeration. Prenumerationer börjar med små standardvärden och hjälper dig att komma igång, men Använd den här inställningen för att kontrol lera hur mycket Amlcompute resurser som är tillgängliga för att kunna användas i din prenumeration. 

Konfigurera även [kvoten för arbets ytan med VM-serien](how-to-manage-quotas.md#workspace-level-quotas)för varje arbets yta i en prenumeration. På så sätt kan du ha mer detaljerad kontroll över kostnaderna som varje arbets yta kan ådra sig och begränsa vissa VM-familjer. 

Om du vill ange kvoter på arbets ytans nivå börjar du med [Azure Portal](https://portal.azure.com).  Välj en arbets yta i din prenumeration och välj **användnings områden + kvoter** i det vänstra fönstret. Välj sedan fliken **Konfigurera kvoter** för att Visa kvoterna. Du måste ha behörighet för prenumerations omfånget för att kunna ange kvoten, eftersom det är en inställning som påverkar flera arbets ytor.

### <a name="set-run-autotermination-policies"></a>Ange kör principer för autoavslutning 

I vissa fall bör du konfigurera dina utbildnings körningar för att begränsa deras varaktighet eller avsluta dem tidigt. Till exempel när du använder Azure Machine Learning den inbyggda inställningen för min parameter eller Automatisk maskin inlärning.

Här följer några alternativ som du har:
* Definiera en parameter som anropas `max_run_duration_seconds` i din RunConfiguration för att styra den längsta tid som en körning kan utökas till den beräkning du väljer (antingen lokal eller fjärrstyrd moln beräkning).
* För [justering](how-to-tune-hyperparameters.md#early-termination)av den här inställningen definierar du en princip för tidig avslutning från en bandit-princip, en princip för att stoppa en median eller en princip för avtrunkering. Om du vill kontrol lera om det finns ytterligare kontroller av de flesta parametrar använder du parametrar som `max_total_runs` eller `max_duration_minutes` .
* För [Automatisk maskin inlärning](how-to-configure-auto-train.md#exit)ställer du in liknande avslutnings principer med hjälp av  `enable_early_stopping` flaggan. Använd också egenskaper som `iteration_timeout_minutes` och `experiment_timeout_minutes` för att kontrol lera maximal varaktighet för en körning eller för hela experimentet.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Använd virtuella datorer med låg prioritet

Med Azure kan du använda överflödigt outnyttjad kapacitet som Low-Priority virtuella datorer i skalnings uppsättningar för virtuella datorer, batch och tjänsten Machine Learning. Dessa allokeringar är emptible men kommer till ett reducerat pris jämfört med dedikerade virtuella datorer. I allmänhet rekommenderar vi att du använder Low-Priority virtuella datorer för batch-arbetsbelastningar. Du bör också använda dem där avbrott kan återskapas antingen via omsändningar (för batch-Inferencing) eller genom omstarter (för djup inlärnings utbildning med kontroll punkt).

Low-Priority virtuella datorer har en enda kvot som är separat från det dedikerade kvot svärdet, som ingår i VM-serien. Läs [mer om AmlCompute-kvoter](how-to-manage-quotas.md).

 Low-Priority virtuella datorer fungerar inte för beräknings instanser eftersom de behöver stöd för interaktiva Notebook-upplevelser.

### <a name="use-reserved-instances"></a>Använda reserverade instanser

Ett annat sätt att spara pengar på beräknings resurser är Azure reserverad VM-instans. Med detta erbjudande genomför du ett år eller tre års villkor. Dessa rabatter är upp till 72% av priserna för betala per användning och tillämpas direkt på din månatliga Azure-faktura.

Azure Machine Learning Compute har stöd för reserverade instanser. Om du köper en räkenskapsårs-eller tre-års reserverad instans kommer vi automatiskt att tillämpa rabatt mot din Azure Machine Learning hanterade beräkning.


## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du optimerar din moln investering med Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att hantera kostnader med [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att [förhindra oväntade kostnader](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Ta den [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guidade utbildningen.