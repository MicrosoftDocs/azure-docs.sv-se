---
title: Felsöka Azure Red Hat OpenShift
description: Felsöka och lösa vanliga problem med Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: af66abff3507279dd1954fd83627900578229866
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100632959"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Fel sökning för Azure Red Hat OpenShift

Den här artikeln beskriver några vanliga problem som uppstår när du skapar eller hanterar Microsoft Azure Red Hat OpenShift-kluster.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Försöker skapa ett misslyckat kluster igen

Om du skapar ett Azure Red Hat OpenShift-kluster med hjälp av `az` CLI-kommandot Miss lyckas återförsöket att försöka skapa fortsätter.
Använd `az openshift delete` för att ta bort det misslyckade klustret och skapa sedan ett helt nytt kluster.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Dolt Azure Red Hat OpenShift-kluster resurs grupp

För närvarande `Microsoft.ContainerService/openShiftManagedClusters` är resursen som skapas automatiskt av Azure CLI ( `az openshift create` kommandot) dold i Azure Portal. I vyn **resurs grupp** markerar du **Visa dolda typer** för att Visa resurs gruppen.

![Skärm bild av kryss rutan dold typ i portalen](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Att skapa ett kluster resulterar i ett fel som inte gick att hitta någon registrerad Resource Provider

Om du skapar ett kluster resulterar det i ett fel som `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` , sedan var du en del av förhands granskningen och måste nu [köpa reserverade instanser för virtuella Azure-datorer](https://aka.ms/openshift/buy) för att kunna använda den allmänt tillgängliga produkten. En reservation minskar dina utgifter genom förskotts betalning för fullständigt hanterade Azure-tjänster. Se [*vad som är Azure reservations*](../cost-management-billing/reservations/save-compute-costs-reservations.md) för att lära dig mer om reservationer och hur de sparar pengar.

## <a name="next-steps"></a>Nästa steg

- Testa [Hjälp Center med Red Hat OpenShift](https://help.openshift.com/) för mer information om OpenShift-felsökning.

- Få svar på [vanliga frågor om Azure Red Hat OpenShift](openshift-faq.md).