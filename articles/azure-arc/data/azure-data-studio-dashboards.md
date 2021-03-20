---
title: Azure Data Studio instrument paneler
description: Azure Data Studio instrument paneler
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a96be6d4da3d292b2e9881652aad28f318ccee8a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92107580"
---
# <a name="azure-data-studio-dashboards"></a>Azure Data Studio instrument paneler

[Azure Data Studio](/sql/azure-data-studio/what-is) ger en upplevelse som liknar Azure Portal för att visa information om dina Azure Arc-resurser.  Dessa vyer kallas **instrument paneler** och har en layout och alternativ som liknar vad du kan se om en viss resurs i Azure Portal, men ger dig flexibiliteten att se den informationen lokalt i din miljö, om du inte har någon tillgänglig anslutning till Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connecting-to-a-data-controller"></a>Ansluta till en datakontrollant

### <a name="prerequisites"></a>Förutsättningar

- Ladda ned [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
- Azure Arc-tillägget har installerats

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>Ta reda på URL: en för data Controller serverns API-slutpunkt

Först måste du ansluta Azure Data Studio till URL: en för data Controller service API-slutpunkten.

Du kan köra följande kommando för att hämta slut punkten:

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

Du ser utdata som ser ut så här:

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

Om du använder en LoadBalancer-typ vill du kopiera den externa IP-adressen och port numret. Om du använder NodePort ska du använda IP-adressen för din Kubernetes API-Server och port numret som anges under kolumnen PORT (er).

Nu ska du skapa en URL till din slut punkt genom att kombinera den här informationen så här:

```console
https://<ip address>:<port>

Example:
https://52.154.152.24:30080
```

Anteckna din IP-adress eftersom du kommer att använda den i nästa steg.

### <a name="connect"></a>Ansluta

1. Öppna Azure Data Studio

1. Välj fliken **anslutningar** till vänster

I det nedre expanderar du panelen med namnet **Azure Arc-kontrollanter**.

Klicka på +-ikonen för att lägga till en ny data kontroll enhets anslutning.

Längst upp på skärmen i paletten kommando anger du den URL som du skapade i steg 1. Klicka på RETUR.
Ange användar namnet för data styrenheten.  Detta var det användar namns värde som du skickade under distributionen av data styrenheten.  Klicka på RETUR.
Ange lösen ordet för data styrenheten.  Detta var det lösen ords värde som du skickade vid distributionen av data styrenheten. Klicka på RETUR.

Nu när du är ansluten till en datakontrollant kan du Visa instrument panelerna för datakontrollanten och eventuella SQL-hanterade instanser eller PostgreSQL-resurser för storskalig Server grupp som du har.

## <a name="view-the-data-controller-dashboard"></a>Visa instrument panelen för data kontroller

Högerklicka på datakontrollanten på panelen anslutningar på den expanderade panelen för överdrive **controllers** och välj **Hantera**.

Här kan du se information om data styrenhets resursen, till exempel namn, region, anslutnings läge, resurs grupp, prenumeration, styrenhets slut punkt och namn område.  Du kan se en lista över alla hanterade databas resurser som hanteras av data styrenheten också.

Du ser att layouten liknar det du kan se i Azure Portal.

Du kan enkelt starta skapandet av en SQL-hanterad instans eller en PostgreSQL-Server grupp genom att klicka på knappen + ny instans.

Du kan också öppna Azure Portal i sammanhang till denna datakontrollant genom att klicka på knappen Öppna i Azure Portal.

## <a name="view-the-sql-managed-instance-dashboards"></a>Visa instrument paneler för SQL-hanterade instanser

Om du har skapat några SQL-hanterade instanser kan du se dem på panelen anslutningar i den expanderade Azure Data controllers-panelen under den datakontrollant som hanterar dem.

Om du vill visa instrument panelen för SQL-hanterad instans för en specifik instans högerklickar du på instansen och väljer hantera.

Anslutnings panelen visas till höger och du uppmanas att logga in/lösen ordet för att ansluta till SQL-instansen. Om du känner till anslutnings informationen kan du ange den och klicka på Anslut.  Om du inte vet kan du klicka på Avbryt.  Oavsett hur du gör det kommer du till instrument panelen när anslutnings panelen stängs.

På fliken Översikt kan du Visa information om SQL-hanterad instans, till exempel resurs grupp, datakontrollant, prenumerations-ID, status, region med mera.  Du kan också se länk som du kan klicka på för att gå till Grafana-eller Kibana-instrumentpanelerna i sammanhang till den SQL-hanterade instansen.

Om du kan ansluta till SQL Manage-instansen kan du se ytterligare information här.

Du kan ta bort den SQL-hanterade instansen från här eller öppna Azure Portal för att visa den SQL-hanterade instansen i Azure Portal.

Om du klickar på fliken anslutnings strängar till vänster kan du se en lista över förbyggda anslutnings strängar för den SQL-hanterade instansen som gör det enkelt för dig att kopiera/klistra in i olika program eller kod.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>Visa instrument paneler för PostgreSQL-ProScale Server grupp

Om du har skapat vissa PostgreSQL-servergrupper kan du se dem på panelen anslutningar i den expanderade Azure Data controllers-panelen under den datakontrollant som hanterar dem.

Om du vill visa instrument panelen för Server gruppen PostgreSQL för en specifik Server grupp högerklickar du på Server gruppen och väljer hantera.

På fliken Översikt kan du Visa information om Server gruppen, till exempel resurs grupp, datakontrollant, prenumerations-ID, status, region med mera.  Du kan också se länk som du kan klicka på för att gå till Grafana-eller Kibana-instrumentpanelerna i sammanhang till den server gruppen.

Du kan ta bort Server gruppen härifrån eller öppna Azure Portal för att Visa Server gruppen i Azure Portal.

Om du klickar på fliken anslutnings strängar till vänster kan du se en lista över fördefinierade anslutnings strängar för Server gruppen, vilket gör det enkelt för dig att kopiera/klistra in i olika program eller kod.

Om du klickar på fliken egenskaper till vänster kan du se ytterligare information.

Om du klickar på fliken resurs hälsa till vänster kan du se den aktuella höga hälso statusen för Server gruppen.

Om du klickar på fliken diagnostisera och lösa problem till vänster kan du starta fel söknings antecknings boken PostgreSQL.

Om du klickar på fliken ny support förfrågan till vänster kan du starta Azure Portal i sammanhang till Server gruppen och skapa en support förfrågan för Azure därifrån.