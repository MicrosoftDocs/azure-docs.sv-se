---
title: YAML-referens för containergrupp
description: Referens för YAML-filen som stöds av Azure Container Instances för att konfigurera en containergrupp
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: efbea7b64ccdf685d4c82bd406f2aa09227e53e1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771145"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-referens: Azure Container Instances

Den här artikeln beskriver syntaxen och egenskaperna för YAML-filen som stöds av Azure Container Instances konfigurera en [containergrupp](container-instances-container-groups.md). Använd en YAML-fil för att ange gruppkonfigurationen till [kommandot az container create][az-container-create] i Azure CLI. 

En YAML-fil är ett praktiskt sätt att konfigurera en containergrupp för reproducerbara distributioner. Det är ett koncist alternativ [till att Resource Manager](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) en mall eller Azure Container Instances-SDK:er för att skapa eller uppdatera en containergrupp.

> [!NOTE]
> Den här referensen gäller för YAML-filer Azure Container Instances REST API version `2019-12-01` .

## <a name="schema"></a>Schema 

Schemat för YAML-filen följer, inklusive kommentarer för att markera nyckelegenskaper. En beskrivning av egenskaperna i det här schemat finns i [avsnittet Egenskapsvärden.](#property-values)

```yml
name: string  # Name of the container group
apiVersion: '2019-12-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
  sku: string # SKU for the container group
  encryptionProperties:
    vaultBaseUrl: string
    keyName: string
    keyVersion: string
  initContainers: # Array of init containers in the group
  - name: string
    properties:
      image: string
      command:
      - string
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      volumeMounts:
      - name: string
        mountPath: string
        readOnly: boolean
```

## <a name="property-values"></a>Egenskapsvärden

I följande tabeller beskrivs de värden som du behöver ange i schemat.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.ContainerInstance/containerGroups-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  name | sträng | Yes | Namnet på containergruppen. |
|  apiVersion | Enum | Yes | 2018-10-01 |
|  location | sträng | No | Resursplatsen. |
|  tags | objekt | No | Resurstaggarna. |
|  identity | objekt | No | Containergruppens identitet, om den har konfigurerats. - [ContainerGroupIdentity-objekt](#containergroupidentity-object) |
|  properties | objekt | Yes | [ContainerGroupProperties-objekt](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>ContainerGroupIdentity-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  typ | Enum | No | Den typ av identitet som används för containergruppen. Typen "SystemAssigned, UserAssigned" innehåller både en implicit skapad identitet och en uppsättning användartilldelade identiteter. Typen "Ingen" tar bort alla identiteter från containergruppen. – SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | objekt | No | Listan över användaridentiteter som är associerade med containergruppen. Nyckelreferenserna för användaridentitetsordlistan kommer att vara Azure Resource Manager resurs-ID:n i formuläret: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |




### <a name="containergroupproperties-object"></a>ContainerGroupProperties-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  containrar | matris | Yes | Containrarna i containergruppen. - [Behållarobjekt](#container-object) |
|  imageRegistryCredentials | matris | No | Autentiseringsuppgifterna för avbildningsregistret som containergruppen skapas från. - [ImageRegistryCredential-objekt](#imageregistrycredential-object) |
|  restartPolicy | Enum | No | Starta om principen för alla containrar i containergruppen. - `Always` Starta alltid om – `OnFailure` Starta om vid fel – Starta aldrig `Never` om. - Always, OnFailure, Never |
|  Ip | objekt | No | Containergruppens IP-adresstyp. - [IpAddress-objekt](#ipaddress-object) |
|  osType | Enum | Yes | Den typ av operativsystem som krävs av containrarna i containergruppen. – Windows eller Linux |
|  volumes | matris | No | Listan över volymer som kan monteras av containrar i den här containergruppen. - [Volymobjekt](#volume-object) |
|  diagnostik | objekt | No | Diagnostikinformation för en containergrupp. - [ContainerGroupDiagnostics-objekt](#containergroupdiagnostics-object) |
|  networkProfile | objekt | No | Nätverksprofilinformationen för en containergrupp. - [ContainerGroupNetworkProfile-objekt](#containergroupnetworkprofile-object) |
|  dnsConfig | objekt | No | DNS-konfigurationsinformation för en containergrupp. - [DnsConfiguration-objekt](#dnsconfiguration-object) |
| sku | Enum | No | SKU:n för en containergrupp – Standard eller Dedikerad |
| encryptionProperties | objekt | No | Krypteringsegenskaperna för en containergrupp. - [EncryptionProperties-objekt](#encryptionproperties-object) | 
| initContainers | matris | No | Init-containrarna för en containergrupp. - [InitContainerDefinition-objekt](#initcontainerdefinition-object) |




### <a name="container-object"></a>Behållarobjekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  name | sträng | Yes | Namnet på containerinstansen som användaren angav. |
|  properties | objekt | Yes | Egenskaperna för containerinstansen. - [ContainerProperties-objekt](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>ImageRegistryCredential-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  server | sträng | Yes | Docker-avbildningsregisterservern utan protokoll som "http" och "https". |
|  användarnamn | sträng | Yes | Användarnamnet för det privata registret. |
|  password | sträng | No | Lösenordet för det privata registret. |




### <a name="ipaddress-object"></a>IpAddress-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  ports | matris | Yes | Listan över portar som exponeras i containergruppen. - [Portobjekt](#port-object) |
|  typ | Enum | Yes | Anger om IP-adressen är exponerad för det offentliga Internet eller privata virtuella nätverket. – Offentlig eller Privat |
|  Ip | sträng | No | DEN IP-adress som exponeras för det offentliga Internet. |
|  dnsNameLabel | sträng | No | DNS-namnetiketten för IP-adressen. |




### <a name="volume-object"></a>Volymobjekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  name | sträng | Yes | Namnet på volymen. |
|  azureFile | objekt | No | Azure-filvolymen. - [AzureFileVolume-objekt](#azurefilevolume-object) |
|  emptyDir | objekt | No | Den tomma katalogvolymen. |
|  hemlighet | objekt | No | Den hemliga volymen. |
|  gitRepo | objekt | No | Git-lagringsplatsens volym. - [GitRepoVolume-objekt](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objekt | No | Logganalysinformation för containergrupp. - [LogAnalytics-objekt](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  id | sträng | Yes | Identifieraren för en nätverksprofil. |




### <a name="dnsconfiguration-object"></a>DnsConfiguration-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  Namnservrar | matris | Yes | DNS-servrarna för containergruppen. - sträng |
|  searchDomains | sträng | No | DNS-sökdomänerna för värdnamnssökning i containergruppen. |
|  alternativ | sträng | No | DNS-alternativ för containergruppen. |


### <a name="encryptionproperties-object"></a>EncryptionProperties-objekt

| Namn  | Typ  | Obligatorisk  | Värde |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | sträng    | Yes   | Den grundläggande URL:en för nyckelvalvet. |
| Nyckelnamn   | sträng    | Yes   | Krypteringsnyckelns namn. |
| keyVersion    | sträng    | Yes   | Krypteringsnyckelversionen. |

### <a name="initcontainerdefinition-object"></a>InitContainerDefinition-objekt

| Namn  | Typ  | Obligatorisk  | Värde |
|  ---- | ---- | ---- | ---- |
| name  | sträng |  Yes | Namnet på init-containern. |
| properties    | objekt    | Yes   | Egenskaperna för init-containern. - [InitContainerPropertiesDefinition-objekt](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>ContainerProperties-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  image | sträng | Yes | Namnet på avbildningen som används för att skapa containerinstansen. |
|  command | matris | No | Kommandon som ska köras i containerinstansen i exec-format. – sträng |
|  ports | matris | No | De exponerade portarna på containerinstansen. - [ContainerPort-objekt](#containerport-object) |
|  environmentVariables | matris | No | Miljövariablerna som ska anges i containerinstansen. - [EnvironmentVariable-objekt](#environmentvariable-object) |
|  resources | objekt | Yes | Resurskraven för containerinstansen. - [ResourceRequirements-objekt](#resourcerequirements-object) |
|  volumeMounts | matris | No | Volymmonteringarna som är tillgängliga för containerinstansen. - [VolumeMount-objekt](#volumemount-object) |
|  livenessProbe | objekt | No | Liveness-avsökningen. - [ContainerProbe-objekt](#containerprobe-object) |
|  readinessProbe | objekt | No | Beredskapsavsökningen. - [ContainerProbe-objekt](#containerprobe-object) |




### <a name="port-object"></a>Portobjekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  Protokollet | Enum | No | Protokollet som är associerat med porten. – TCP eller UDP |
|  port | heltal | Yes | Portnumret. |




### <a name="azurefilevolume-object"></a>AzureFileVolume-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  Resursnamn | sträng | Yes | Namnet på Azure-filresursen som ska monteras som en volym. |
|  Readonly | boolean | No | Flaggan som anger om Azure File som delas monterad som en volym är skrivskyddade. |
|  storageAccountName | sträng | Yes | Namnet på lagringskontot som innehåller Azure-filresursen. |
|  storageAccountKey | sträng | No | Lagringskontots åtkomstnyckel som används för åtkomst till Azure-filresursen. |




### <a name="gitrepovolume-object"></a>GitRepoVolume-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  katalog | sträng | No | Målkatalognamn. Får inte innehålla eller börja med "..".  Om "." anges är volymkatalogen git-lagringsplatsen.  Om inget annat anges innehåller volymen git-lagringsplatsen i underkatalogen med det angivna namnet. |
|  repository | sträng | Yes | Lagringsplats-URL |
|  revision | sträng | No | Genomför hash för den angivna revisionen. |



### <a name="loganalytics-object"></a>LogAnalytics-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  workspaceId | sträng | Yes | Arbetsyte-ID för Log Analytics |
|  workspaceKey | sträng | Yes | Arbetsytenyckeln för Log Analytics |
|  logType | Enum | No | Den loggtyp som ska användas. – ContainerInsights eller ContainerInstanceLogs |
|  metadata | objekt | No | Metadata för log analytics. |


### <a name="initcontainerpropertiesdefinition-object"></a>InitContainerPropertiesDefinition-objekt

| Namn  | Typ  | Obligatorisk  | Värde |
|  ---- | ---- | ---- | ---- |
| image | sträng    | No    | Avbildningen av init-containern. |
| command   | matris | No    | Kommandot som ska köras i init-containern i exec-format. – sträng |
| environmentVariables | matris  | No |Miljövariablerna som ska anges i init-containern. - [EnvironmentVariable-objekt](#environmentvariable-object)
| volumeMounts |matris   | No    | Volymmonteringarna som är tillgängliga för init-containern. - [VolumeMount-objekt](#volumemount-object)

### <a name="containerport-object"></a>ContainerPort-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  Protokollet | Enum | No | Protokollet som är associerat med porten. – TCP eller UDP |
|  port | heltal | Yes | Portnumret som exponeras i containergruppen. |




### <a name="environmentvariable-object"></a>EnvironmentVariable-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  name | sträng | Yes | Namnet på miljövariabeln. |
|  värde | sträng | No | Värdet för miljövariabeln. |
|  secureValue | sträng | No | Värdet för den säkra miljövariabeln. |




### <a name="resourcerequirements-object"></a>ResourceRequirements-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  Begäranden | objekt | Yes | Resursbegäranden för den här containerinstansen. - [ResourceRequests-objekt](#resourcerequests-object) |
|  Gränser | objekt | No | Resursgränserna för den här containerinstansen. - [ResourceLimits-objekt](#resourcelimits-object) |




### <a name="volumemount-object"></a>VolumeMount-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  name | sträng | Yes | Namnet på volymmonteringen. |
|  mountPath | sträng | Yes | Sökvägen i containern där volymen ska monteras. Får inte innehålla kolon (:). |
|  Readonly | boolean | No | Flaggan som anger om volymmonteringen är skrivskyddade. |




### <a name="containerprobe-object"></a>ContainerProbe-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  Exec | objekt | No | Körningskommandot för avsökning – [ContainerExec-objekt](#containerexec-object) |
|  httpGet | objekt | No | Http Get-inställningarna för avsökning – [ContainerHttpGet-objekt](#containerhttpget-object) |
|  initialDelaySeconds | heltal | No | Den inledande fördröjningen sekunder. |
|  periodSeconds | heltal | No | Perioden sekunder. |
|  failureThreshold | heltal | No | Tröskelvärdet för fel. |
|  successThreshold | heltal | No | Tröskelvärdet för lyckad. |
|  timeoutSekunder | heltal | No | Tidsgränsen sekunder. |




### <a name="resourcerequests-object"></a>ResourceRequests-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | antal | Yes | Minnesbegäran i GB för den här containerinstansen. |
|  Cpu | antal | Yes | CPU-begäran för den här containerinstansen. |
|  Gpu | objekt | No | GPU-begäran för den här containerinstansen. - [GpuResource-objekt](#gpuresource-object) |




### <a name="resourcelimits-object"></a>ResourceLimits-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | antal | No | Minnesgränsen i GB för den här containerinstansen. |
|  Cpu | antal | No | Cpu-gränsen för den här containerinstansen. |
|  Gpu | objekt | No | GPU-gränsen för den här containerinstansen. - [GpuResource-objekt](#gpuresource-object) |




### <a name="containerexec-object"></a>ContainerExec-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  command | matris | No | Kommandon som ska köras i containern. - sträng |




### <a name="containerhttpget-object"></a>ContainerHttpGet-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  path | sträng | No | Sökvägen till avsökningen. |
|  port | heltal | Yes | Portnumret som ska avsavsas. |
|  System | Enum | No | Schemat. - http eller https |




### <a name="gpuresource-object"></a>GpuResource-objekt

|  Namn | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  count | heltal | Yes | Antalet GPU-resurser. |
|  sku | Enum | Yes | GPU-resursens SKU. – K80, P100, V100 |


## <a name="next-steps"></a>Nästa steg

Se självstudien [Distribuera en grupp med flera containrar med hjälp av en YAML-fil.](container-instances-multi-container-yaml.md)

Se exempel på hur du använder en YAML-fil för att distribuera containergrupper i ett [virtuellt](container-instances-vnet.md) nätverk eller [som monterar en extern volym](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
