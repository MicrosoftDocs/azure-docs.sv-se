---
title: Hantera IoT Central från Azure Portal | Microsoft Docs
description: Den här artikeln beskriver hur du skapar och hanterar IoT Central program från Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: 3e5e126815d0171a6c1627a08419b05b9a3c0c23
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719213"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Hantera IoT Central från Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Du kan använda [Azure Portal](https://portal.azure.com) för att skapa och hantera IoT Central program.

## <a name="create-iot-central-applications"></a>Skapa IoT Central program

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Om du vill skapa ett program går [du IoT Central sidan](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) Program i Azure Portal:

![Skapa IoT Central formulär](media/howto-manage-iot-central-from-portal/image6a.png)

* **Resursnamnet** är ett unikt namn som du kan välja för ditt IoT Central i din Azure-resursgrupp.

* **Program-URL** är den URL som du kan använda för att komma åt ditt program.

* **Plats** är [det geografiska](https://azure.microsoft.com/global-infrastructure/geographies/) område där du vill skapa ditt program. Normalt bör du välja den plats som är fysiskt närmast dina enheter för att få optimala prestanda. Azure IoT Central är för närvarande tillgänglig på följande platser:

  * Asien och stillahavsområdet
  * Australien
  * Europa
  * Japan
  * Storbritannien
  * USA

  När du har valt en plats kan du inte senare flytta programmet till en annan plats.

När du har fyllt i alla fält väljer du **Skapa**. Mer information finns i [snabbstarten Skapa IoT Central ett](quick-deploy-iot-central.md) program.

## <a name="manage-existing-iot-central-applications"></a>Hantera befintliga IoT Central program

Om du redan har Azure IoT Central ett program kan du ta bort det eller flytta det till en annan prenumeration eller resursgrupp i Azure Portal.

> [!NOTE]
> Program som skapas med *den kostnadsfria* planen kräver inte någon Azure-prenumeration och därför visas de inte i din Azure-prenumeration på Azure Portal. Du kan bara se och hantera kostnadsfria appar från IoT Central portalen.

Kom igång genom att söka efter ditt program i sökfältet överst i Azure Portal. Du kan också visa alla dina program genom att _söka efter IoT Central program_ och välja tjänsten:

![Skärmbild som visar sökresultatet för "IoT Central Program" med den första tjänsten vald.](media/howto-manage-iot-central-from-portal/search-iot-central.png)

När du väljer ett program i sökresultatet visas Azure Portal dess översikt. Du kan navigera till programmet genom att välja IoT Central **url:**

![Skärmbild som visar sidan "Översikt" med "url IoT Central program" markerad.](media/howto-manage-iot-central-from-portal/image3.png)

Om du vill flytta programmet till en annan resursgrupp väljer **du** Ändra bredvid resursgruppen. På sidan **Flytta** resurser väljer du den resursgrupp som du vill flytta programmet till:

![Skärmbild som visar sidan "Översikt" med "Resursgrupp (ändra)" markerad.](media/howto-manage-iot-central-from-portal/image4a.png)

Om du vill flytta programmet till en annan prenumeration väljer  **du Ändra** bredvid prenumerationen. På sidan **Flytta** resurser väljer du den prenumeration som du vill flytta programmet till:

![Hanteringsportal: resurshantering](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central program från Azure Portal föreslår vi nästa steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)