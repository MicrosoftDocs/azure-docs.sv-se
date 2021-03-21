---
title: Hantera AKS Node-uppgraderingar med GitHub-åtgärder
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du uppdaterar AKS-noder med GitHub-åtgärder
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 6876cf1e5044246492e249d8a61060cbeac46f96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217965"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>Tillämpa säkerhets uppdateringar på Azure Kubernetes service (AKS)-noder automatiskt med hjälp av GitHub-åtgärder

Säkerhets uppdateringar är en viktig del av att underhålla ditt AKS-klusters säkerhet och efterlevnad med de senaste korrigeringarna för det underliggande operativ systemet. Dessa uppdateringar omfattar säkerhets korrigeringar för operativ system eller kernel-uppdateringar. Vissa uppdateringar kräver att en nod startas om för att slutföra processen.

Med `az aks upgrade` den här metoden får du ett avbrott i noll för att tillämpa uppdateringar. Kommandot hanterar de senaste uppdateringarna på alla noder i klustret, cordoning och tömmer trafik till noderna och startar om noderna, och sedan tillåts trafik till de uppdaterade noderna. Om du uppdaterar noderna med en annan metod kommer AKS inte att starta om noderna automatiskt.

> [!NOTE]
> Den största skillnaden mellan `az aks upgrade` när den används med `--node-image-only` -flaggan är att när den används, uppgraderas bara nodens avbildningar. Om det utelämnas kommer både nodens avbildningar och Kubernetes kontroll Plans version att uppgraderas. Du kan kontrol lera [dokumenten för hanterade uppgraderingar på noder][managed-node-upgrades-article] och [dokumenten för kluster uppgraderingar][cluster-upgrades-article] för mer detaljerad information.

Alla Kubernetes-noder körs på en virtuell Azure-dator (VM). Dessa virtuella datorer kan vara Windows-eller Linux-baserade. Linux-baserade virtuella datorer använder en Ubuntu avbildning, där operativ systemet har kon figurer ATS för automatisk sökning efter uppdateringar varje natt.

När du använder `az aks upgrade` kommandot skapar Azure CLI en överspänning av nya noder med de senaste säkerhets-och kernel-uppdateringarna, och de här noderna inlednings vis avspärrade för att förhindra att appar schemaläggs till dem förrän uppdateringen är färdig. Efter slut för ande är Azure cordons (gör noden otillgänglig för schemaläggning av nya arbets belastningar) och tömningar (flyttar befintliga arbets belastningar till en annan nod) de äldre noderna och uncordon nya, och överför alla schemalagda program till de nya noderna på ett effektivt sätt.

Den här processen är bättre än att uppdatera Linux-baserade kärnor manuellt eftersom Linux kräver en omstart när en ny kernel-uppdatering installeras. Om du uppdaterar operativ systemet manuellt måste du också starta om den virtuella datorn, manuellt cordoning och tömma alla appar.

Den här artikeln visar hur du kan automatisera uppdaterings processen för AKS-noder. Du använder GitHub-åtgärder och Azure CLI för att skapa en uppdaterings uppgift `cron` som baseras på som körs automatiskt.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du måste också ha Azure CLI-versionen 2.0.59 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

Den här artikeln förutsätter också att du har ett [GitHub][github] -konto för att skapa dina åtgärder i.

## <a name="create-a-timed-github-action"></a>Skapa en GitHub åtgärd

`cron` är ett verktyg som gör att du kan köra en uppsättning kommandon eller jobb enligt ett automatiserat schema. Om du vill skapa ett jobb för att uppdatera dina AKS-noder enligt ett automatiserat schema behöver du en lagrings plats som värd för dina åtgärder. Vanligt vis konfigureras GitHub-åtgärder på samma lagrings plats som ditt program, men du kan använda valfri lagrings plats. I den här artikeln använder vi ditt [profil lager][profile-repository]. Om du inte har någon skapar du en ny lagrings plats med samma namn som ditt GitHub-användarnamn.

1. Navigera till din lagrings plats på GitHub
1. Klicka på fliken **åtgärder** överst på sidan.
1. Om du redan har konfigurerat ett arbets flöde i den här lagrings platsen dirigeras du till listan över slutförda körningar, i det här fallet klickar du på knappen **nytt arbets flöde** . Om det här är ditt första arbets flöde i lagrings platsen kommer GitHub att presentera dig för vissa projektmallar, klicka på länken **Konfigurera ett arbets flöde själv** under beskrivnings texten.
1. Ändra arbets flödet `name` och `on` taggarna som liknar nedanstående. GitHub-åtgärder använder samma [POSIX cron-syntax][cron-syntax] som alla Linux-baserade system. I det här schemat meddelar vi arbets flödet att köras var 15: e dag på 3am.

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. Skapa ett nytt jobb med hjälp av nedan. Det här jobbet heter `upgrade-node` , körs på en Ubuntu-agent och ansluter till ditt Azure CLI-konto för att utföra de steg som krävs för att uppgradera noderna.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>Konfigurera Azure CLI i arbets flödet

I `steps` nyckeln definierar du allt arbete som arbets flödet ska köra för att uppgradera noderna.

Hämta och logga in på Azure CLI.

1. På den högra sidan av skärmen GitHub-åtgärder letar du upp *Sök fältet för Marketplace* och skriver **"Azure-inloggning"**.
1. Du får resultatet av en åtgärd som kallas Azure- **inloggning** publicerad **av Azure**:

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="Sök resultatet visar två rader, den första åtgärden kallas för &quot;Azure inloggning&quot; &quot;och den andra&quot; Azure Container Registry inloggning &quot;":::

1. Klicka på **Azure-inloggning**. På nästa skärm klickar du på **kopierings ikonen** längst upp till höger i kod exemplet.

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="Resultat fönstret för Azure login-åtgärd med kod exempel nedanför, röd fyrkant runt en kopierings ikon visar Klicka på plats":::

1. Klistra in följande under `steps` nyckeln:

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. Kör följande kommando från Azure CLI för att skapa ett nytt användar namn och lösen ord.

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    Utdata bör likna följande JSON:

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **I ett nytt webbläsarfönster** navigerar du till GitHub-lagringsplatsen och öppnar fliken **Inställningar** i lagrings platsen. Klicka på **hemligheter** och klicka sedan på **ny lagrings plats hemlighet**.
1. Som *namn* använder du `AZURE_CREDENTIALS` .
1.  För *värde* lägger du till hela innehållet från utdata i föregående steg där du har skapat ett nytt användar namn och lösen ord.

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="Formulär som visar AZURE_CREDENTIALS som hemlig rubrik, och utdata från det körda kommandot som har klistrats in som JSON":::

1. Klicka på **Lägg till hemlighet**.

CLI som används av åtgärden loggas på ditt Azure-konto och är redo att köra kommandon.

Skapa stegen för att köra Azure CLI-kommandon.

1. Gå till **sidan Sök** på *GitHub Marketplace* på höger sida av skärmen och Sök i *Azure CLI-åtgärden*. Välj *Azure CLI-åtgärd av Azure*.

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="Sök Resultat för Azure CLI-åtgärd med det första resultatet som visas av Azure":::

1. Klicka på kopierings knappen på *GitHub Marketplace-resultatet* och klistra in innehållet i åtgärden i huvud redigeraren under steget *Azure-inloggning* , som liknar följande:

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only --yes
    ```

    > [!TIP]
    > Du kan koppla bort `-g` parametrarna och `-n` från kommandot genom att lägga till dem i hemligheter som liknar föregående steg. Ersätt `{resourceGroupName}` `{aksClusterName}` plats hållarna och efter deras hemliga motsvarigheter, till exempel `${{secrets.RESOURCE_GROUP_NAME}}` och `${{secrets.AKS_CLUSTER_NAME}}`

1. Byt namn på filen till `upgrade-node-images`.
1. Klicka på **Starta incheckning**, Lägg till en meddelande rubrik och spara arbets flödet.

När du har skapat åtgärden sparas arbets flödet och är klart att köras.

> [!NOTE]
> Om du vill uppgradera en enskild nod i stället för alla noder i klustret lägger du till `--name` parametern i `az aks nodepool upgrade` kommandot för att ange namnet på noden. Exempel:
> ```azurecli-interactive
> az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>Kör GitHub-åtgärden manuellt

Du kan köra arbets flödet manuellt, förutom den schemalagda körningen, genom att lägga till en ny `on` utlösare som kallas `workflow_dispatch` . Den färdiga filen bör se ut som YAML nedan:

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>Nästa steg

- Mer information om de senaste Node-avbildningarna finns i [AKS-versions anteckningarna](https://github.com/Azure/AKS/releases) .
- Lär dig hur du uppgraderar Kubernetes-versionen med [uppgradera ett AKS-kluster][cluster-upgrades-article].
- Lär dig mer om flera noder och hur du uppgraderar noder med [skapa och hantera flera noder][use-multiple-node-pools]i pooler.
- Läs mer om [system Node-pooler][system-pools]
- Information om hur du sparar kostnader med hjälp av plats instanser finns i [lägga till en AKS för en plats][spot-pools] .

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
