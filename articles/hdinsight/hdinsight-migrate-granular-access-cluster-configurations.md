---
title: Detaljerade rollbaserade åtkomstkonfigurationer Azure HDInsight kluster
description: Lär dig mer om de ändringar som krävs som en del av migreringen till detaljerad rollbaserad åtkomst för HDInsight-klusterkonfigurationer.
author: tylerfox
ms.author: tyfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: afb30f4648f1649bf6cc6cc6a3bf02f433f49d45
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774939"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrera till detaljerad rollbaserad åtkomst för klusterkonfigurationer

Vi introducerar några viktiga ändringar för att stödja mer detaljerad rollbaserad åtkomst för att hämta känslig information. Som en del av dessa ändringar kan vissa åtgärder krävas senast **den 3 september 2019** om du använder en av de berörda entiteterna/scenarierna . [](#am-i-affected-by-these-changes)

## <a name="what-is-changing"></a>Vad ändras?

Tidigare kunde hemligheter hämtas via HDInsight-API:et av klusteranvändare som har Azure-rollerna Ägare, Deltagare eller [Läsare,](../role-based-access-control/rbac-and-directory-admin-roles.md)eftersom de var tillgängliga för alla med `*/read` behörigheten . Hemligheter definieras som värden som kan användas för att få mer förhöjd åtkomst än vad en användares roll ska tillåta. Dessa inkluderar värden som HTTP-autentiseringsuppgifter för klustergateway, lagringskontonycklar och databasautentiseringsuppgifter.

Från och med den 3 september 2019 kräver åtkomst till dessa hemligheter behörigheten, vilket innebär att de inte längre kan nås av användare med `Microsoft.HDInsight/clusters/configurations/action` rollen Läsare. Rollerna som har den här behörigheten är Deltagare, Ägare och den nya rollen SOM HDInsight-klusteroperatör (mer information finns nedan).

Vi introducerar också en ny [hdInsight-klusteroperatörsroll](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) som kommer att kunna hämta hemligheter utan att beviljas administratörsbehörighet för deltagare eller ägare. Sammanfattningsvis:

| Roll                                  | Tidigare                                                                                       | Framöver       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Läsare                                | – Läsbehörighet, inklusive hemligheter.                                                                   | – Läsbehörighet, **exklusive** hemligheter | 
| HDInsight-klusteroperator<br>(Ny roll) | Ej tillämpligt                                                                                              | – Läs-/skrivåtkomst, inklusive hemligheter         | 
| Deltagare                           | – Läs-/skrivåtkomst, inklusive hemligheter.<br>– Skapa och hantera alla typer av Azure-resurser.<br>– Köra skriptåtgärder.     | Ingen ändring |
| Ägare                                 | – Läs-/skrivåtkomst inklusive hemligheter.<br>– Fullständig åtkomst till alla resurser<br>– Delegera åtkomst till andra.<br>– Köra skriptåtgärder. | Ingen ändring |

Information om hur du lägger till rolltilldelningen HDInsight-klusteroperatör till en användare för att ge dem läs-/skrivåtkomst till klusterhemligheter finns i avsnittet Lägga till rolltilldelningen [HDInsight-klusteroperatör](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)till en användare .

## <a name="am-i-affected-by-these-changes"></a>Påverkas jag av dessa ändringar?

Följande entiteter och scenarier påverkas:

- [API:](#api)Användare som använder `/configurations` `/configurations/{configurationName}` slutpunkterna eller .
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) version 1.1.1 eller senare.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) version 3.20.0 eller lägre.
- [Azure Data Lake och Stream Analytics Tools för Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) version 2.3.9000.1.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) version 3.15.0 eller senare.
- [SDK för .NET](#sdk-for-net)
    - [versionerna 1.x eller 2.x:](#versions-1x-and-2x)Användare som använder metoderna , , eller från `GetClusterConfigurations` `GetConnectivitySettings` `ConfigureHttpSettings` `EnableHttp` `DisableHttp` configurationsOperationsExtensions-klassen.
    - [version 3.x och senare:](#versions-3x-and-up)Användare som använder `Get` metoderna , , eller från klassen `Update` `EnableHttp` `DisableHttp` `ConfigurationsOperationsExtensions` .
- [SDK för Python:](#sdk-for-python)Användare som använder `get` metoderna eller från klassen `update` `ConfigurationsOperations` .
- [SDK för Java:](#sdk-for-java)Användare som använder `update` metoderna eller från klassen `get` `ConfigurationsInner` .
- [SDK för Go:](#sdk-for-go)Användare som använder `Get` metoderna eller från `Update` `ConfigurationsClient` struct-structen.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) under version 2.0.0.
Se avsnitten nedan (eller använd länkarna ovan) för att se migreringsstegen för ditt scenario.

### <a name="api"></a>API

Följande API:er kommer att ändras eller bli inaktuella:

- [**GET /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (känslig information har tagits bort)
    - Användes tidigare för att hämta enskilda konfigurationstyper (inklusive hemligheter).
    - Från och med den 3 september 2019 returnerar det här API-anropet nu enskilda konfigurationstyper med utelämnade hemligheter. Om du vill hämta alla konfigurationer, inklusive hemligheter, använder du det nya POST/configurations-anropet. Om du bara vill hämta gatewayinställningar använder du det nya POST/getGatewaySettings-anropet.
- [**GET /configurations**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (inaktuell)
    - Används tidigare för att hämta alla konfigurationer (inklusive hemligheter)
    - Från och med den 3 september 2019 kommer det här API-anropet att bli inaktuellt och stöds inte längre. Om du vill hämta alla konfigurationer framöver använder du det nya POST/configurations-anropet. Om du vill hämta konfigurationer med utelämnade känsliga parametrar använder du anropet GET /configurations/{configurationName}.
- [**POST /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (inaktuell)
    - Användes tidigare för att uppdatera autentiseringsuppgifter för gateway.
    - Från och med den 3 september 2019 kommer det här API-anropet att bli inaktuellt och stöds inte längre. Använd nya POST /updateGatewaySettings i stället.

Följande ersättnings-API:er har lagts till:</span>

- [**POST /configurations**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Använd det här API:et för att hämta alla konfigurationer, inklusive hemligheter.
- [**POST /getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Använd det här API:et för att hämta gatewayinställningar.
- [**POST /updateGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Använd det här API:et för att uppdatera gatewayinställningar (användarnamn och/eller lösenord).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

Om du använder version 1.1.1 eller senare uppdaterar du till den senaste versionen [av Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) för att undvika avbrott.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Om du använder version 3.20.0 eller senare uppdaterar du till den senaste versionen [av Azure Toolkit for IntelliJ för](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) att undvika avbrott.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake och Stream Analytics Tools för Visual Studio

Uppdatera till version 2.3.9000.1 eller senare av [Azure Data Lake och Stream Analytics Tools för Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) för att undvika avbrott.  Om du behöver hjälp med att uppdatera kan du läsa vår [dokumentation Update Data Lake Tools for Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Om du använder version 3.15.0 eller senare uppdaterar du till den senaste [versionen av Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) för att undvika avbrott.

### <a name="sdk-for-net"></a>SDK för .NET

#### <a name="versions-1x-and-2x"></a>Versionerna 1.x och 2.x

Uppdatera till [version 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) av HDInsight SDK för .NET. Minimala kodändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- `ClusterOperationsExtensions.GetClusterConfigurations` returnerar **inte längre känsliga parametrar som lagringsnycklar** (core-site) eller HTTP-autentiseringsuppgifter (gateway).
    - Om du vill hämta alla konfigurationer, inklusive känsliga parametrar, `ClusterOperationsExtensions.ListConfigurations` använder du i framtiden.  Observera att användare med rollen Läsare inte kan använda den här metoden. Detta möjliggör detaljerad kontroll över vilka användare som kan komma åt känslig information för ett kluster.
    - Om du bara vill hämta autentiseringsuppgifter för HTTP-gateway använder du `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.GetConnectivitySettings` är nu inaktuell och har ersatts av `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.ConfigureHttpSettings` är nu inaktuell och har ersatts av `ClusterOperationsExtensions.UpdateGatewaySettings` .

- `ConfigurationsOperationsExtensions.EnableHttp` och `DisableHttp` är nu inaktuella. HTTP är nu alltid aktiverat, så dessa metoder behövs inte längre.

#### <a name="versions-3x-and-up"></a>Version 3.x och senare

Uppdatera till [version 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) eller senare av HDInsight SDK för .NET. Minimala kodändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get) returnerar **inte längre känsliga parametrar som lagringsnycklar** (core-site) eller HTTP-autentiseringsuppgifter (gateway).
    - Om du vill hämta alla konfigurationer, inklusive känsliga parametrar, [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list) använder du i framtiden.Observera att användare med rollen Läsare inte kan använda den här metoden. Detta möjliggör detaljerad kontroll över vilka användare som kan komma åt känslig information för ett kluster. 
    - Om du bara vill hämta autentiseringsuppgifter för HTTP-gateway använder du [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings) . 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update) är nu inaktuell och har ersatts av [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings) . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp) och [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp) är nu inaktuella. HTTP är nu alltid aktiverat, så dessa metoder behövs inte längre.

### <a name="sdk-for-python"></a>SDK för Python

Uppdatera till [version 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) eller senare av HDInsight SDK för Python. Minimala kodändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) returnerar **inte längre känsliga parametrar som lagringsnycklar** (core-site) eller HTTP-autentiseringsuppgifter (gateway).
    - Om du vill hämta alla konfigurationer, inklusive känsliga parametrar, [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) använder du i framtiden.Observera att användare med rollen Läsare inte kan använda den här metoden. Detta möjliggör detaljerad kontroll över vilka användare som kan komma åt känslig information för ett kluster. 
    - Om du bara vill hämta autentiseringsuppgifter för HTTP-gateway använder du [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) .
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) är nu inaktuell och har ersatts av [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) .

### <a name="sdk-for-java"></a>SDK för Java

Uppdatera till [version 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) eller senare av HDInsight SDK för Java. Minimala kodändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- `ConfigurationsInner.get` returnerar **inte längre känsliga parametrar som lagringsnycklar** (kärnplats) eller HTTP-autentiseringsuppgifter (gateway).
- `ConfigurationsInner.update` är nu inaktuell.

### <a name="sdk-for-go"></a>SDK för Go

Uppdatera till [version 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) eller senare av HDInsight SDK för Go. Minimala kodändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get) returnerar **inte längre känsliga parametrar som lagringsnycklar** (kärnplats) eller HTTP-autentiseringsuppgifter (gateway).
    - Om du vill hämta alla konfigurationer, inklusive känsliga parametrar, [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List) använder du i framtiden.Observera att användare med rollen Läsare inte kan använda den här metoden. Detta möjliggör detaljerad kontroll över vilka användare som kan komma åt känslig information för ett kluster. 
    - Om du bara vill hämta autentiseringsuppgifter för HTTP-gateway använder du [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings) .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) är nu inaktuell och har ersatts av [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Uppdatera till [Az PowerShell version 2.0.0](https://www.powershellgallery.com/packages/Az) eller senare för att undvika avbrott.  Minimala kodändringar kan krävas om du använder en metod som påverkas av dessa ändringar.
- `Grant-AzHDInsightHttpServicesAccess` är nu inaktuell och har ersatts av den nya `Set-AzHDInsightGatewayCredential` cmdleten.
- `Get-AzHDInsightJobOutput` har uppdaterats för att ge stöd för detaljerad rollbaserad åtkomst till lagringsnyckeln.
    - Användare med rollerna	HDInsight-klusteroperator, Deltagare och Ägare påverkas inte.
    - Användare med endast rollen Läsare måste uttryckligen ange `DefaultStorageAccountKey` parametern.
- `Revoke-AzHDInsightHttpServicesAccess` är nu inaktuell. HTTP är nu alltid aktiverat, så den här cmdleten behövs inte längre.
 Se [az. Mer information finns i migreringsguiden för HDInsight.](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Lägga till rolltilldelningen HDInsight-klusteroperatör till en användare

En användare [](../role-based-access-control/built-in-roles.md#owner) med rollen Ägare kan tilldela rollen klusteroperatör för [HDInsight](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) till användare som du vill ska ha läs-/skrivåtkomst till känsliga konfigurationsvärden för HDInsight-kluster (till exempel autentiseringsuppgifter för klustergateway och lagringskontonycklar).

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Det enklaste sättet att lägga till den här rolltilldelningen är att `az role assignment create` använda kommandot i Azure CLI.

> [!NOTE]
> Det här kommandot måste köras av en användare med rollen Ägare, eftersom endast de kan bevilja dessa behörigheter. är namnet på tjänstens huvudnamn eller e-postadress för den användare som du vill tilldela `--assignee` rollen HDInsight-klusteroperatör. Om du får ett felmeddelande om otillräcklig behörighet kan du läsa vanliga frågor och svar nedan.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Bevilja roll på resursnivå (kluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Bevilja roll på resursgruppsnivå

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Bevilja roll på prenumerationsnivå

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Du kan också använda Azure Portal för att lägga till rolltilldelningen HDInsight-klusteroperatör till en användare. Se dokumentationen Tilldela [Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="faq"></a>Vanliga frågor

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Varför ser jag felet 403 när jag har uppdaterat mina API-förfrågningar och/eller API-verktyg?

Klusterkonfigurationer ligger nu bakom detaljerad rollbaserad åtkomstkontroll och kräver behörighet `Microsoft.HDInsight/clusters/configurations/*` att komma åt dem. För att få den här behörigheten tilldelar du rollen HDInsight-klusteroperatör, deltagare eller ägare till användaren eller tjänstens huvudnamn som försöker få åtkomst till konfigurationer.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Varför visas "Otillräcklig behörighet för att slutföra åtgärden" när jag kör Azure CLI-kommandot för att tilldela rollen HDInsight-klusteroperatör till en annan användare eller tjänstens huvudnamn?

Förutom att ha rollen Ägare måste användaren eller tjänstens huvudnamn som kör kommandot ha tillräcklig Azure AD-behörighet för att leta upp objekt-ID:erna för mottagaren. Det här meddelandet anger otillräcklig Azure AD-behörighet. Försök att ersätta argumentet med och ange objekt-ID:t för mottagaren som parameter i stället för namnet (eller huvudnamns-ID:t för `-–assignee` `–assignee-object-id` en hanterad identitet). Mer information finns i avsnittet om [valfria](/cli/azure/role/assignment#az_role_assignment_create) parametrar i dokumentationen az role assignment create.

Om det fortfarande inte fungerar kontaktar du Azure AD-administratören för att få rätt behörigheter.

### <a name="what-will-happen-if-i-take-no-action"></a>Vad händer om jag inte gör något?

Från och med den 3 september 2019 returnerar anropen inte längre någon information och anropet returnerar inte längre känsliga parametrar, till exempel lagringskontonycklar eller `GET /configurations` `POST /configurations/gateway` `GET /configurations/{configurationName}` klusterlösenordet. Samma sak gäller för motsvarande SDK-metoder och PowerShell-cmdlets.

Om du använder en äldre version av något av verktygen för Visual Studio, VSCode, IntelliJ eller Eclipse ovan fungerar de inte längre förrän du uppdaterar.

Mer detaljerad information finns i motsvarande avsnitt i det här dokumentet för ditt scenario.
