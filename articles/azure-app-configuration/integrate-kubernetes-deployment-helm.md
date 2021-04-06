---
title: Integrera Azure App-konfiguration med Kubernetes-distribution med Helm
description: Lär dig hur du använder dynamiska konfigurationer i Kubernetes-distribution med Helm.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 4e38366ddcee07f38ca390acf9d580b8764c1c00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979835"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integrera med Kubernetes-distribution med Helm

Helm är ett sätt att definiera, installera och uppgradera program som körs i Kubernetes. Ett Helm-diagram innehåller den information som krävs för att skapa en instans av ett Kubernetes-program. Konfigurationen lagras utanför själva diagrammet i en fil med namnet *Values. yaml*. 

Under publicerings processen sammanfogar Helm diagrammet med rätt konfiguration för att köra programmet. Till exempel kan variabler som definierats i *Values. yaml* refereras som miljövariabler i de behållare som körs. Helm har också stöd för att skapa Kubernetes hemligheter, som kan monteras som data volymer eller exponeras som miljövariabler.

Du kan åsidosätta värdena som lagras i *Values. yaml* genom att tillhandahålla ytterligare yaml-baserade konfigurationsfiler på kommando raden när du kör Helm. Azure App-konfigurationen stöder export av konfigurations värden till YAML-filer. Genom att integrera den här export funktionen i din distribution kan dina Kubernetes-program utnyttja konfigurations värden som lagras i appens konfiguration.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Använd värden från appens konfiguration när du distribuerar ett program till Kubernetes med hjälp av Helm.
> * Skapa en Kubernetes-hemlighet baserat på en Key Vault referens i appens konfiguration.

Den här självstudien förutsätter grundläggande förståelse för att hantera Kubernetes med Helm. Lär dig mer om att installera program med Helm i [Azure Kubernetes-tjänsten](../aks/kubernetes-helm.md).

## <a name="prerequisites"></a>Förutsättningar

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- Installera [Azure CLI](/cli/azure/install-azure-cli) (version 2.4.0 eller senare)
- Installera [Helm](https://helm.sh/docs/intro/install/) (version 2.14.0 eller senare)
- Ett Kubernetes-kluster.

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Välj **konfigurations Utforskaren**  >  **skapa** för att lägga till följande nyckel/värde-par:

    | Tangent | Värde |
    |---|---|
    | inställningar. färg | Vit |
    | Settings. Message | Data från Azure App Configuration |

    Lämna **etiketten** och **innehålls typen** tom för tillfället.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Lägg till en Key Vault referens till app-konfigurationen
1. Logga in på [Azure Portal](https://portal.azure.com) och Lägg till en hemlighet till [Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) med namn **lösen ordet** och värdens **lösen ord**. 
2. Välj den instans av konfigurations Arkiv för app som du skapade i föregående avsnitt.

3. Välj **konfigurations Utforskaren**.

4. Välj **+ skapa**  >  **Key Vault-referens** och ange sedan följande värden:
    - **Nyckel**: Välj **hemligheter. password**.
    - **Etikett**: lämna värdet tomt.
    - **Prenumeration**, **resurs grupp** och **nyckel valv**: Ange de värden som motsvarar dem i nyckel valvet som du skapade i föregående steg.
    - **Hemlighet**: Välj det hemliga namnet **lösen ord** som du skapade i föregående avsnitt.

## <a name="create-helm-chart"></a>Skapa Helm-diagram ##
Skapa först ett exempel på Helm-diagram med följande kommando
```console
helm create mychart
```

Helm skapar en ny katalog med namnet mitt diagram med den struktur som visas nedan. 

> [!TIP]
> Följ den här [diagram guiden](https://helm.sh/docs/chart_template_guide/getting_started/) för mer information.

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

Uppdatera sedan avsnittet **spec: Template: spec: containers** i filen *Deployment. yaml* . Följande fragment lägger till två miljövariabler i behållaren. Du ställer in värdena dynamiskt vid distributions tiden.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

Den fullständiga filen *Deployment. yaml* efter uppdateringen bör se ut som nedan.

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

Om du vill lagra känsliga data som Kubernetes hemligheter lägger du till filen *hemligheter. yaml* under mappen mallar.

> [!TIP]
> Lär dig mer om hur du använder [Kubernetes hemligheter](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

Uppdatera slutligen filen *Values. yaml* med följande innehåll om du vill ange standardvärden för de konfigurations inställningar och hemligheter som refereras till i filen *Deployment. yaml* och *hemligheter. yaml* . De faktiska värdena skrivs över av konfigurationen som hämtas från App-konfigurationen.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Skicka konfiguration från App Configuration i Helm-installation ##
Hämta först konfigurationen från App-konfigurationen till en *yaml* -fil. Använd ett nyckel filter för att bara hämta de nycklar som börjar med **Inställningar.**. Om nyckel filtret inte räcker för att utesluta nycklar med Key Vault referenser kan du använda argumentet **--Skip-Key Vault** för att utesluta dem. 

> [!TIP]
> Läs mer om [export kommandot](/cli/azure/appconfig/kv#az-appconfig-kv-export). 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Hämta sedan hemligheter till en fil med namnet *hemligheter. yaml*. Kommando rads argumentet **– matcha-valv** matchar Key Vault referenser genom att hämta de faktiska värdena i Key Vault. Du måste köra det här kommandot med autentiseringsuppgifter som har åtkomst behörighet till motsvarande Key Vault.

> [!WARNING]
> Eftersom den här filen innehåller känslig information, bör du hålla filen i omsorg och rensa när den inte behövs längre.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Använd Helm Upgrade s **-f-** argumentet för att skicka de två konfigurationsfiler som du har skapat. De åsidosätter de konfigurations värden som definierats i *Values. yaml* med de värden som exporteras från App-konfigurationen.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

Du kan också använda argumentet **--set** för att Helm-uppgraderingen för att skicka literala nyckel värden. Att använda argumentet **--set** är ett bra sätt att undvika att bevara känsliga data till disk. 

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

Kontrol lera att konfigurationer och hemligheter har angetts genom att komma åt [Kubernetes-instrumentpanelen](../aks/kubernetes-dashboard.md). Du ser att **färg** -och **meddelande** värden från App-konfigurationen har fyllts i i behållarens miljövariabler.

![Snabbstart av lokal app](./media/kubernetes-dashboard-env-variables.png)

En hemlighet, **lösen ord**, butiker som Key Vault referens i app Configuration har också lagts till i Kubernetes hemligheter. 

![Skärm bild som markerar lösen ordet i avsnittet data.](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du exporterat Azure App konfigurations data som ska användas i en Kubernetes-distribution med Helm. Om du vill veta mer om hur du använder app-konfiguration kan du fortsätta till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [Azure CLI](/cli/azure/appconfig)
