---
title: Så här distribuerar du verktyget formulär igenkänning exempel etiketting
titleSuffix: Azure Cognitive Services
description: Lär dig de olika sätt som du kan använda för att distribuera formulär tolkens exempel etikett verktyg som hjälper dig med övervakad inlärning.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 02/11/2021
ms.author: lajanuar
ms.openlocfilehash: 0f5f0714235ee23624b3a199eac744155d2bbdd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "101093383"
---
# <a name="deploy-the-sample-labeling-tool"></a>Distribuera exempeletikettverktyget

Formulär tolkens exempel etikett verktyg är ett program som ger ett enkelt användar gränssnitt (UI), som du kan använda för att manuellt etikettera formulär (dokument) i syfte att övervaka inlärningen. I den här artikeln innehåller vi länkar och instruktioner som lär dig att:

* [Kör verktyget för att märka med verktyget lokalt](#run-the-sample-labeling-tool-locally)
* [Distribuera exempel etikett verktyget till en Azure Container Instance (ACI)](#deploy-with-azure-container-instances-aci)
* [Använd och bidra till OCR-formuläret med öppen källkod som etikett verktyg](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Kör verktyget för att märka med verktyget lokalt

Det snabbaste sättet att starta märkning av data är att köra verktyget för att köra etiketter lokalt. Följande snabb start använder formulär tolken REST API och verktyget för att träna en anpassad modell med manuellt märkta data. 

* [Snabb start: etikett formulär, träna en modell och analysera ett formulär med hjälp av verktyget för att använda exempel etiketter](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Distribuera med Azure Container Instances (ACI)

Innan vi börjar är det viktigt att Observera att det finns två sätt att distribuera exempel etikett verktyget till en Azure Container Instance (ACI). Båda alternativen används för att köra verktyget för etikettering med ACI:

* [Använda Azure Portal](#azure-portal)
* [Använda Azure CLI](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

Följ de här stegen för att skapa en ny resurs med hjälp av Azure Portal: 

1. Logga in på [Azure-portalen](https://portal.azure.com/signin/index/).
2. Välj **Skapa en resurs**.
3. Välj sedan **webbapp**.

   > [!div class="mx-imgBorder"]
   > ![Välja webbapp](./media/quickstarts/create-web-app.png)

4. Se först till att fliken **grundläggande** är markerad. Nu kommer du att behöva ange viss information:

   > [!div class="mx-imgBorder"]
   > ![Välj grunderna](./media/quickstarts/select-basics.png)
   * Prenumeration – Välj en befintlig Azure-prenumeration
   * Resurs grupp – du kan återanvända en befintlig resurs grupp eller skapa en ny för projektet. Vi rekommenderar att du skapar en ny resurs grupp.
   * Namn – ge din webbapp ett namn. 
   * Publicera – Välj **Docker-behållare**
   * Operativ system – Välj **Linux**
   * Region – Välj en region som passar dig.
   * Linux-plan – Välj en pris nivå/plan för din app service. 

   > [!div class="mx-imgBorder"]
   > ![Konfigurera din webbapp](./media/quickstarts/select-docker.png)

5. Välj sedan fliken **Docker** .

   > [!div class="mx-imgBorder"]
   > ![Välj Docker](./media/quickstarts/select-docker.png)

6. Nu ska vi konfigurera din Docker-behållare. Alla fält är obligatoriska om inget annat anges:
<!-- markdownlint-disable MD025 -->
# <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

* Alternativ-Välj **en behållare**
* Avbildnings källa – Välj **privat register** 
* Server-URL – ange detta till `https://mcr.microsoft.com`
* Användar namn (valfritt) – skapa ett användar namn. 
* Lösen ord (valfritt) – skapa ett säkert lösen ord som du kommer ihåg.
* Bild och tagg – ange detta till `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview`
* Kontinuerlig distribution – ange detta till **på** om du vill få automatiska uppdateringar när utvecklings teamet gör ändringar i exempel etikett verktyget.
* Start kommando – ange detta till `./run.sh eula=accept`

# <a name="v20"></a>[v2.0](#tab/v2-0)  

* Alternativ-Välj **en behållare**
* Avbildnings källa – Välj **privat register** 
* Server-URL – ange detta till `https://mcr.microsoft.com`
* Användar namn (valfritt) – skapa ett användar namn. 
* Lösen ord (valfritt) – skapa ett säkert lösen ord som du kommer ihåg.
* Bild och tagg – ange detta till `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
* Kontinuerlig distribution – ange detta till **på** om du vill få automatiska uppdateringar när utvecklings teamet gör ändringar i exempel etikett verktyget.
* Start kommando – ange detta till `./run.sh eula=accept`

 ---

   > [!div class="mx-imgBorder"]
   > ![Konfigurera Docker](./media/quickstarts/configure-docker.png)

7. Klart! Välj sedan **Granska + skapa** och sedan **skapa** för att distribuera din webbapp. När du är klar kan du komma åt din webbapp på den URL som anges i **översikten** för din resurs.

> [!NOTE]
> När du skapar din webbapp kan du också konfigurera auktorisering/autentisering. Detta är inte nödvändigt för att komma igång.

> [!IMPORTANT]
> Du kan behöva aktivera TLS för din webbapp för att kunna visa den på `https` adressen. Följ anvisningarna i [Aktivera en TLS-slutpunkt](../../container-instances/container-instances-container-group-ssl.md) för att konfigurera en sidvagn-behållare än aktiverar TLS/SSL för din webbapp.
<!-- markdownlint-disable MD001 -->
### <a name="azure-cli"></a>Azure CLI

Som ett alternativ till att använda Azure Portal kan du skapa en resurs med hjälp av Azure CLI. Innan du fortsätter måste du installera [Azure CLI](/cli/azure/install-azure-cli). Du kan hoppa över det här steget om du redan arbetar med Azure CLI. 

Det finns några saker du behöver veta om det här kommandot:

* `DNS_NAME_LABEL=aci-demo-$RANDOM` genererar ett slumpmässigt DNS-namn. 
* Det här exemplet förutsätter att du har en resurs grupp som du kan använda för att skapa en resurs. Ersätt `<resource_group_name>` med en giltig resurs grupp som är kopplad till din prenumeration. 
* Du måste ange var du vill skapa resursen. Ersätt `<region name>` med din önskade region för webb programmet.
* Det här kommandot accepterar EULA automatiskt.

Kör det här kommandot från Azure CLI för att skapa en Web App-resurs för verktyget för etikettering:

<!-- markdownlint-disable MD024 -->
# <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"

```

# <a name="v20"></a>[v2.0](#tab/v2-0)


```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
``` 


---

### <a name="connect-to-azure-ad-for-authorization"></a>Ansluta till Azure AD för auktorisering

Vi rekommenderar att du ansluter din webbapp till Azure Active Directory. Detta säkerställer att endast användare med giltiga autentiseringsuppgifter kan logga in och använda din webbapp. Följ anvisningarna i [Konfigurera din app service-app](../../app-service/configure-authentication-provider-aad.md) för att ansluta till Azure Active Directory.

## <a name="open-source-on-github"></a>Öppen källkod på GitHub

Etikett verktyget för OCR-form är också tillgängligt som ett projekt med öppen källkod på GitHub. Verktyget är ett webb program som skapats med hjälp av reagerar + Redux och är skrivet i TypeScript. Om du vill veta mer eller Contribute kan du se [OCR-verktyget för form etikettering](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg

Använd snabb starten för [träna med etiketter](./quickstarts/label-tool.md) för att lära dig hur du använder verktyget för att manuellt etikettera tränings data och utföra övervakad inlärning.