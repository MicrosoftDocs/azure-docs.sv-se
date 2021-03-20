---
title: Konfigurera säker databas för Azure Data Box
description: Lär dig hur du använder Customer Lockbox med Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 178ad169c1b576458f38b440ca79f4bb9eb012f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92124919"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>Använd Customer Lockbox för Azure Data Box (förhands granskning)

Azure Data Box används för att överföra kund information till och från Azure. Det finns instanser där Microsoft Support kan behöva komma åt kund information under en Supportbegäran. Du kan använda Customer Lockbox som ett gränssnitt för att granska och godkänna eller avvisa dessa förfrågningar om data åtkomst. 

Den här artikeln beskriver hur Customer Lockbox begär Anden initieras och spåras för Data Box-enhet import samt export order. Artikeln gäller för både Azure Data Box enheter och Azure Data Box Heavy enheter. 

## <a name="devops-workflow-for-data-access"></a>DevOps-arbetsflöde för data åtkomst

Support-och Data Box-enhet drifts teamet på Microsoft har normalt inte åtkomst till kund information. De försöker lösa problem med hjälp av standard verktyg och telemetri. <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

Om problemen inte kan lösas och kräver Microsoft Support att undersöka eller reparera data, begär de utökad åtkomst via just-in-Time (JIT)-portalen. JIP-portalen verifierar behörighets nivån, ger Multi-Factor Authentication och inkluderar även ett godkännande från Microsofts interna god kännare. God kännaren kan till exempel vara DevOps Manager. 

När din begäran om utökad åtkomst har godkänts via JIT-portalen, kommer Microsoft också att kräva ditt uttryckliga medgivande för att komma åt data, om du har aktiverat en säker databas. Åtkomsten begärs och spåras via Customer Lockboxs tjänsten i portalen. 

Om du inte har aktiverat säker databas behövs inte ditt medgivande för att komma åt data.


## <a name="prerequisites-for-access-request"></a>Krav för åtkomstbegäran

Innan du börjar ska du kontrollera att:

1. Du har skapat en Azure Data Box order enligt instruktionerna i:
    1. [Självstudie: beställ Azure Data Box](data-box-deploy-ordered.md) för import order.
    1. [Självstudie: beställ Azure Data Box](data-box-deploy-export-ordered.md) för export order.

2. Du har konfigurerat Customer Lockbox för Data Box-enhet. Det här är en opt-in-tjänst. 

    1. Customer Lockbox är för närvarande en för hands version av Data Box-enhets tjänsten. Om du vill aktivera Customer Lockbox för Data Box-enhet för din organisation kan du registrera dig för [Customer lockbox för offentlig för hands version av Azure](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu).
    2. Customer Lockbox är automatiskt tillgängligt för alla kunder som har en Support plan för Azure med en minimal utvecklings nivå. <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. En tjänstbegäran eller ett support ärende har redan öppnats för det här problemet. För information om support ärenden, se [fil a tjänstbegäran för data Box-enhet](data-box-disk-contact-microsoft-support.md).


## <a name="track-approve-request-via-lockbox"></a>Spåra, godkänn begäran via säker databas

Följ dessa steg om du vill spåra och godkänna en begäran om åtkomst till kund information:

1. Microsoft upptäcker att det finns ett problem med att ladda upp eller hämta data i Azure Data Center. Data Box-enhet ordningen stoppas till exempel under **data kopierings** fasen. 

    Support teknikern ansluter till Data Box-enhet via support-sessionen och försöker felsöka problemet med hjälp av standard verktyg och telemetri. Om Data Box-enhet diskarna är låsta och resurserna inte är tillgängliga, skapar support teknikern en begäran om låsning. 
 
2. När begäran skapas, skickas vanligt vis aviseringen till prenumerations administratören, men du kan också konfigurera en grupp för aviseringar. 

3. Du kan se begäran om säker begäran i Azure Portal för ditt godkännande. 

    ![Begäran i Azure Portal](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    Om du vill godkänna begäran om låsning från portalen kan du välja **Godkänn**.

    ![Godkänn begäran](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    När begäran har godkänts är enhets diskarna olåsta och resurserna är tillgängliga i support sessionen.

4. Support teknikern löser uppladdnings problemet och inaktiverar support sessionen.

När problemet har åtgärd ATS kommer data kopierings jobbet att försättas i slutet.


## <a name="next-steps"></a>Nästa steg

- [Customer Lockbox för Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->