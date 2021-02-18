---
title: Skapa en Azure AD-klient för Azure Red Hat OpenShift
description: Så här skapar du en Azure Active Directory-klient (Azure AD) som är värd för ditt Microsoft Azure Red Hat OpenShift-kluster.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: b98f02adeb850f16127658c7d02d44754512e216
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635017"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Skapa en Azure AD-klient för Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 kommer att dras tillbaka 30 juni 2022. Stöd för att skapa nya Azure Red Hat OpenShift 3,11-kluster fortsätter till och med 30 november 2020. Efter pensionering kommer de återstående Azure Red Hat OpenShift 3,11-klustren att stängas av för att förhindra säkerhets problem.
> 
> Följ den här guiden för att [skapa ett Azure Red Hat OpenShift 4-kluster](tutorial-create-cluster.md).
> Om du har frågor kan du [kontakta oss](mailto:arofeedback@microsoft.com).

Microsoft Azure Red Hat OpenShift kräver en [Azure Active Directory-klient (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) som du kan använda för att skapa klustret. En *klient* organisation är en dedikerad instans av Azure AD som en organisation eller app-utvecklare får när de skapar en relation med Microsoft genom att registrera sig för Azure, Microsoft Intune eller Microsoft 365. Varje Azure AD-klient är distinkt och åtskild från andra Azure AD-klienter och har sina egna arbets-och skol identiteter och app-registreringar.

Om du inte redan har en Azure AD-klient kan du följa de här anvisningarna för att skapa en.

## <a name="create-a-new-azure-ad-tenant"></a>Skapa en ny Azure AD-klient

Så här skapar du en klient:

1. Logga in på [Azure Portal](https://portal.azure.com/) med det konto som du vill koppla till ditt Azure Red Hat OpenShift-kluster.
2. Öppna [bladet Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) för att skapa en ny klient organisation (även kallat en ny *Azure Active Directory*).
3. Ange ett **organisations namn**.
4. Ange ett **första domän namn**. Detta kommer att ha *onmicrosoft.com* tillagd till. Du kan återanvända värdet för *organisations namn* här.
5. Välj ett land eller en region där klienten ska skapas.
6. Klicka på **Skapa**.
7. När din Azure AD-klient har skapats väljer du länken **Klicka här för att hantera din nya katalog** . Ditt nya klient namn ska visas längst upp till höger i Azure Portal:  

    ![Skärm bild av portalen som visar klient namnet längst upp till höger][tenantcallout]  

8. Anteckna *klient-ID: t* så att du senare kan ange var du vill skapa ditt Azure Red Hat OpenShift-kluster. I portalen bör du nu se bladet Azure Active Directory översikt för din nya klient. Välj **Egenskaper** och kopiera värdet för ditt **katalog-ID**. Vi kommer att referera till det här värdet som `TENANT` i själv studie kursen [skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md) .

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Resurser

Se [Azure Active Directory-dokumentationen](../active-directory/index.yml) för mer information om [Azure AD-klienter](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar ett huvud namn för tjänsten, genererar en URL för klient hemlighet och autentiserings-motanrop och skapar en ny Active Directory användare för att testa appar i ditt Azure Red Hat OpenShift-kluster.

[Skapa ett Azure AD-appobjekt och en användare](howto-aad-app-configuration.md)