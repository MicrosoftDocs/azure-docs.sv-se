---
title: Integrera Azure App Configuration kubernetes-distribution med hjälp av Helm
description: Lär dig hur du använder dynamiska konfigurationer i Kubernetes-distribution med Helm.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 6276fc2027e92d5b7baaf9237a928e7828a3b021
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775776"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integrera med Kubernetes-distribution med Hjälp av Helm

Helm är ett sätt att definiera, installera och uppgradera program som körs i Kubernetes. Ett Helm-diagram innehåller den information som krävs för att skapa en instans av ett Kubernetes-program. Konfigurationen lagras utanför själva diagrammet i en fil med namnet *values.yaml*. 

Under lanseringsprocessen sammanfogar Helm diagrammet med rätt konfiguration för att köra programmet. Variabler som definieras i *values.yaml kan till* exempel refereras till som miljövariabler i containrarna som körs. Helm stöder också skapandet av Kubernetes-hemligheter, som kan monteras som datavolymer eller exponeras som miljövariabler.

Du kan åsidosätta de värden som *lagras i values.yaml* genom att ange ytterligare YAML-baserade konfigurationsfiler på kommandoraden när du kör Helm. Azure App Configuration stöder export av konfigurationsvärden till YAML-filer. Genom att integrera den här exportkapaciteten i distributionen kan Kubernetes-program utnyttja konfigurationsvärden som lagras i App Configuration.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Använd värden från App Configuration när du distribuerar ett program till Kubernetes med hjälp av Helm.
> * Skapa en Kubernetes-hemlighet baserat på en Key Vault-referens i App Configuration.

Den här självstudien förutsätter grundläggande kunskaper om att hantera Kubernetes med Helm. Läs mer om att installera program med Helm i [Azure Kubernetes Service](../aks/kubernetes-helm.md).

## <a name="prerequisites"></a>Förutsättningar

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- Installera [Azure CLI](/cli/azure/install-azure-cli) (version 2.4.0 eller senare)
- Installera [Helm](https://helm.sh/docs/intro/install/) (version 2.14.0 eller senare)
- Ett Kubernetes-kluster.

## <a name="create-an-app-configuration-store"></a>Skapa en App Configuration butik

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Välj **Configuration Explorer**  >  **Skapa** för att lägga till följande nyckel/värde-par:

    | Tangent | Värde |
    |---|---|
    | settings.color | Vit |
    | settings.message | Data från Azure App Configuration |

    Lämna **Etikett** och **Innehållstyp** tomma för tillfället.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Lägga till en Key Vault-referens för App Configuration
1. Logga in på Azure Portal [och](https://portal.azure.com) lägg till en hemlighet [i Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) med namnet **Lösenord** och värdet **myPassword**. 
2. Välj den App Configuration Store-instans som du skapade i föregående avsnitt.

3. Välj **Configuration Explorer.**

4. Välj **+ Skapa**  >  **nyckelvalvsreferens** och ange sedan följande värden:
    - **Nyckel:** Välj **secrets.password**.
    - **Etikett:** Lämna det här värdet tomt.
    - **Prenumeration,** **Resursgrupp** och **Nyckelvalv:** Ange de värden som motsvarar de i nyckelvalvet som du skapade i föregående steg.
    - **Hemlighet:** Välj hemligheten med **namnet Lösenord** som du skapade i föregående avsnitt.

## <a name="create-helm-chart"></a>Skapa Helm-diagram ##
Skapa först ett Helm-exempeldiagram med följande kommando
```console
helm create mychart
```

Helm skapar en ny katalog med namnet mychart med strukturen som visas nedan. 

> [!TIP]
> Följ den här [diagramguiden om](https://helm.sh/docs/chart_template_guide/getting_started/) du vill veta mer.

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

Uppdatera sedan avsnittet **spec:template:spec:containers** i *filen deployment.yaml.* Följande kodfragment lägger till två miljövariabler i containern. Du anger deras värden dynamiskt vid tidpunkten för distributionen.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

Den fullständiga *filen deployment.yaml* efter uppdateringen bör se ut som nedan.

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

Om du vill lagra känsliga data som Kubernetes Secrets lägger du till en *secrets.yaml-fil* under mappen templates.

> [!TIP]
> Läs mer om hur du använder [Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

Slutligen uppdaterar du *filen values.yaml* med följande innehåll för att eventuellt ange standardvärden för de konfigurationsinställningar och hemligheter som refereras i *filerna deployment.yaml* *och secrets.yaml.* Deras faktiska värden skrivs över av konfigurationen som hämtas från App Configuration.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Skicka konfiguration från App Configuration Helm-installation ##
Först laddar du ned konfigurationen från App Configuration till en *myConfig.yaml-fil.* Använd ett nyckelfilter för att bara ladda ned de nycklar som börjar med **inställningarna.**. Om nyckelfiltret i ditt fall inte är tillräckligt för att undanta nycklar Key Vault referenser kan du använda argumentet **--skip-keyvault** för att undanta dem. 

> [!TIP]
> Läs mer om [exportkommandot](/cli/azure/appconfig/kv#az_appconfig_kv_export). 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Ladda sedan ned hemligheter till en fil med namnet *mySecrets.yaml*. Kommandoradsargumentet **--resolve-keyvault** matchar Key Vault genom att hämta de faktiska värdena i Key Vault. Du måste köra det här kommandot med autentiseringsuppgifter som har åtkomstbehörighet till motsvarande Key Vault.

> [!WARNING]
> Eftersom den här filen innehåller känslig information bör du hålla filen med försiktighet och rensa när den inte längre behövs.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Använd helm upgrade-argumentet **-f** för att skicka in de två konfigurationsfilerna som du har skapat. De åsidosätter konfigurationsvärdena som definieras *i values.yaml* med de värden som exporteras från App Configuration.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

Du kan också använda argumentet **--set för** Helm Upgrade för att skicka literalnyckelvärden. Att använda **argumentet --set** är ett bra sätt att undvika att bevara känsliga data till disk. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

Kontrollera att konfigurationer och hemligheter har angetts genom att öppna [Kubernetes-instrumentpanelen](../aks/kubernetes-dashboard.md). Du ser att färg- **och** **meddelandevärdena** från App Configuration har fyllts i containerns miljövariabler.

![Snabbstart av lokal app](./media/kubernetes-dashboard-env-variables.png)

En hemlighet, **password**, lagrar som Key Vault referens i App Configuration också lades till i Kubernetes Secrets. 

![Skärmbild som visar lösenordet i avsnittet Data.](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien exporterade du Azure App Configuration data som ska användas i en Kubernetes-distribution med Helm. Om du vill veta mer om hur App Configuration kan du fortsätta till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [Azure CLI](/cli/azure/appconfig)
