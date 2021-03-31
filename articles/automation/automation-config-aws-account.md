---
title: Autentisera Azure Automation-runbooks med Amazon Web Services
description: Den här artikeln beskriver hur du autentiserar Runbooks med Amazon Web Services.
keywords: aws-autentisering, konfigurera aws
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: acb056fb959fda320a14059e2b36a0f5e3fb0b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83837201"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autentisera runbooks med Amazon Web Services (AWS)

Du kan automatisera vanliga uppgifter med resurser i Amazon Web Services (AWS) med hjälp av Automation-runbooks i Azure. Du kan automatisera många aktiviteter i AWS med hjälp av Automation-runbooks precis som du kan göra med resurser i Azure. För autentisering måste du ha en Azure-prenumeration.

## <a name="obtain-aws-subscription-and-credentials"></a>Hämta AWS-prenumeration och autentiseringsuppgifter

För att autentisera med AWS måste du skaffa en AWS-prenumeration och ange en uppsättning AWS-autentiseringsuppgifter för att autentisera dina runbooks som körs från Azure Automation. Vissa autentiseringsuppgifter krävs för AWS-åtkomst nyckeln och den hemliga nyckeln. Se [använda AWS-autentiseringsuppgifter](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).

## <a name="configure-automation-account"></a>Konfigurera ett Automation-konto

Du kan använda ett befintligt Automation-konto för att autentisera med AWS. Du kan också ange ett konto för Runbooks som riktar sig till AWS-resurser. I det här fallet skapar du ett nytt [Automation-konto](automation-create-standalone-account.md).  

## <a name="store-aws-credentials"></a>Lagra AWS-autentiseringsuppgifter

Du måste lagra AWS-autentiseringsuppgifterna som till gångar i Azure Automation. Instruktioner om hur du skapar åtkomst nyckeln och den hemliga nyckeln finns i [Hantera åtkomst nycklar för ditt AWS-konto](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) . När nycklarna är tillgängliga kopierar du åtkomst nyckel-ID: t och det hemliga nyckel-ID: t på en säker plats. Du kan ladda ned nyckel filen för att spara den på en säker plats.

## <a name="create-credential-asset"></a>Skapa referens till gång

När du har skapat och kopierat AWS-säkerhetsnycklarna måste du skapa en inloggnings till gång med Automation-kontot. Med till gången kan du lagra AWS-nycklarna på ett säkert sätt och referera till dem i dina runbooks. Se [skapa en ny inloggnings till gång med Azure Portal](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal). Ange följande AWS-information i de angivna fälten:
    
* **Namn**  -  **AWScred** eller ett lämpligt värde efter dina namngivnings standarder
* **Användar namn** – ditt åtkomst-ID
* **Lösen ord** – namnet på den hemliga nyckeln 

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar runbooks för att automatisera uppgifter i AWS finns i [distribuera en virtuell Amazon Web Services virtuell dator med en Runbook](automation-scenario-aws-deployment.md).