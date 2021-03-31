---
title: Distribuera ett Service Fabric hanterat kluster (för hands version) med Azure Resource Manager
description: Lär dig hur du skapar ett Service Fabric hanterat kluster med en Azure Resource Manager-mall
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 079e8e3e39bd306332abb43fa2ae1f8cc04e305b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91410508"
---
# <a name="quickstart-deploy-a-service-fabric-managed-cluster-preview-with-an-azure-resource-manager-template"></a>Snabb start: Distribuera ett Service Fabric hanterat kluster (för hands version) med en Azure Resource Manager mall

Service Fabric hanterade kluster är en utveckling av resurs modellen för Azure Service Fabric-kluster som effektiviserar distributionen och kluster hanterings upplevelsen. Service Fabric hanterade kluster är en fullständigt inkapslad resurs som gör att du kan distribuera en enda Service Fabric kluster resurs i stället för att behöva distribuera alla underliggande resurser som utgör ett Service Fabric kluster. I den här artikeln beskrivs hur du distribuerar ett Service Fabric hanterat kluster för testning i Azure med hjälp av en Azure Resource Manager-mall (ARM-mall).

Basic SKU-klustret med tre noder som distribueras i den här självstudien är endast avsett att användas i instruktions syfte (i stället för produktions arbets belastningar). Mer information finns i  [Service Fabric hanterade kluster SKU: er](overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här snabb starten:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure-exempel – Service Fabric Cluster-mallar](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Basic-SKU-1-NT).

## <a name="create-a-client-certificate"></a>Skapa ett klient certifikat

Service Fabric hanterade kluster använder ett klient certifikat som nyckel för åtkomst kontroll. Om du redan har ett klient certifikat som du vill använda för åtkomst kontroll till klustret kan du hoppa över det här steget.

Om du behöver skapa ett nytt klient certifikat följer du stegen i [Ange och hämta ett certifikat från Azure Key Vault](../key-vault/certificates/quick-create-portal.md).

Anteckna tumavtrycket för certifikatet eftersom det krävs för att distribuera mallen i nästa steg.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall.

      [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fservice-fabric-cluster-templates%2Fmaster%2FSF-Managed-Basic-SKU-1-NT%2Fazuredeploy.json)

2. Välj eller ange följande värden

    Ange dina egna värden för följande mallparametrar för den här snabb starten:

    * **Prenumeration**: Välj en Azure-prenumeration.
    * **Resurs grupp**: Välj **Skapa ny**. Ange ett unikt namn för resurs gruppen, till exempel *myResourceGroup*, och välj sedan **OK**.
    * **Plats**: Välj en plats, till exempel **eastus2**. Regioner som stöds för förhands granskning av Service Fabric hanterade kluster inkluderar `centraluseuap` , `eastus2euap` , `eastasia` ,, `northeurope` `westcentralus` och `eastus2` .
    * **Kluster namn**: Ange ett unikt namn för klustret, till exempel *mysfcluster*.
    * **Admin-användar namn**: Ange ett namn för den administratör som ska användas för RDP på de underliggande virtuella datorerna i klustret.
    * **Administratörs lösen ord**: Ange ett lösen ord för den administratör som ska användas för RDP på de underliggande virtuella datorerna i klustret.
    * **Tumavtryck för klient certifikat**: Ange tumavtrycket för det klient certifikat som du vill använda för att få åtkomst till klustret. Om du inte har ett certifikat, följer du [Ange och hämta ett certifikat](../key-vault/certificates/quick-create-portal.md) för att skapa ett självsignerat certifikat.
    * **Namn på nodtyp**: Ange ett unikt namn för nodtypen, till exempel *NT1*.
    * **Jag samtycker till villkoren ovan**: Markera den här kryss rutan om du vill samtycka. 

3. Välj **Köp**.

4. Det tar några minuter för ditt hanterade Service Fabric-kluster att distribueras. Vänta tills distributionen har slutförts innan du går vidare till nästa steg.

## <a name="validate-the-deployment"></a>Verifiera distributionen

### <a name="review-deployed-resources"></a>Granska distribuerade resurser

När distributionen är klar hittar du Service Fabric Explorer värdet i utdata och öppnar adressen i en webbläsare för att Visa klustret i Service Fabric Explorer. När du uppmanas att ange ett certifikat använder du det certifikat som klienten tumavtryck angavs i mallen.

> [!NOTE]
> Du hittar utdata för distributionen i Azure Portal under fliken distribution av resurs grupper.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen för Service Fabric hanterade klustret. Så här tar du bort resursgruppen via portalen:

1. Ange namnet på din resurs grupp i rutan *Sök* högst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat ett hanterat Service Fabric-kluster. Mer information om hur du skalar ett kluster finns i:

> [!div class="nextstepaction"]
> [Skala ut ett Service Fabric hanterat kluster](tutorial-managed-cluster-scale.md)
