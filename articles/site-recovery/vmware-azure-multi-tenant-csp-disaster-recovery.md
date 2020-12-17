---
title: Konfigurera VMware haveri beredskap till Azure i en miljö med flera organisationer som använder Site Recovery och CSP-programmet (Cloud Solution Provider) | Microsoft Docs
description: Beskriver hur du konfigurerar VMware haveri beredskap i en miljö med flera klienter med Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: fb2a8a7bb14758ab21eb2183a119f456b53c8562
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654957"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Konfigurera VMware haveri beredskap i en miljö med flera organisationer med Cloud Solution Provider (CSP)-programmet

[CSP-programmet](https://partner.microsoft.com/cloud-solution-provider) utvecklar bättre berättelser för Microsofts moln tjänster, inklusive Microsoft 365, Enterprise Mobility Suite och Microsoft Azure. Med CSP äger partner relationen från slut punkt till slut punkt med kunder och blir den primära Relations kontakt punkten. Partner kan distribuera Azure-prenumerationer för kunder och kombinera prenumerationerna med sina egna mervärdes anpassade erbjudanden.

Med [Azure Site Recovery](site-recovery-overview.md)kan du, som partner, hantera haveri beredskap för kunder direkt via CSP. Alternativt kan du använda CSP för att konfigurera Site Recovery-miljöer och låta kunderna hantera sina egna haveri beredskaps behov på ett självbetjänings sätt. I båda scenarierna är partners sambandet mellan Site Recovery och deras kunder. Partner tjänsterar kund relationen och fakturerar kunder för Site Recovery användning.

Den här artikeln beskriver hur du som partner kan skapa och hantera klient prenumerationer via CSP, för ett scenario med VMware-replikering för flera innehavare.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera VMware-replikering måste du göra följande:

- [Förbered](tutorial-prepare-azure.md) Azure-resurser, inklusive en Azure-prenumeration, ett virtuellt Azure-nätverk och ett lagrings konto.
- [Förbered](vmware-azure-tutorial-prepare-on-premises.md) lokala VMware-servrar och virtuella datorer.
- Skapa en separat hanterings server som kan kommunicera med de virtuella klient datorerna och vCenter-servrarna för varje klient. Endast du som partner bör ha åtkomst behörighet till den här hanterings servern. Lär dig mer om [miljöer med flera klienter](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Skapa ett klient konto

1. Logga in på ditt CSP-konto via [Microsoft Partner Center](https://partnercenter.microsoft.com/).
2. På **instrument panelens** meny väljer du **kunder**.
3. Klicka på knappen **Lägg till kund** på sidan som öppnas.
4. På sidan **ny kund** fyller du i konto information om klient organisationen.

    ![Sidan konto information](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Klicka sedan på **Nästa: prenumerationer**.
6. På sidan Val av prenumerationer markerar du kryss rutan **Microsoft Azure** . Du kan lägga till andra prenumerationer nu eller vid en annan tidpunkt.
7. På sidan **Granska** bekräftar du klient informationen och klickar sedan på **Skicka**.
8. När du har skapat klient kontot visas en bekräftelse sida som visar information om standard kontot och lösen ordet för den prenumerationen. Spara informationen och ändra lösen ordet senare vid behov genom att gå till Azure Portal inloggnings sida.

Du kan dela den här informationen med klienten som den är, eller så kan du skapa och dela ett separat konto om det behövs.

## <a name="access-the-tenant-account"></a>Åtkomst till klient kontot

Du kan komma åt klient organisationens prenumeration via instrument panelen för Microsoft Partner Center.

1. På sidan **kunder** klickar du på namnet på klient kontot.
2. På sidan **prenumerationer** för klient kontot kan du övervaka befintliga konto prenumerationer och lägga till fler prenumerationer, efter behov.
3. Om du vill hantera klient organisationens haveri återställnings åtgärder väljer du **alla resurser (Azure Portal)**. Detta ger dig åtkomst till klient organisationens Azure-prenumerationer.

    ![Länken alla resurser](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Du kan kontrol lera åtkomst genom att klicka på länken Azure Active Directory längst upp till höger i Azure Portal.

    ![Azure Active Directory länk](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Nu kan du utföra och hantera alla Site Recovery åtgärder för klienten i Azure Portal. Följ den tidigare beskrivna processen för att få åtkomst till klient prenumerationen via CSP för hanterad katastrof återställning.

## <a name="assign-tenant-access-to-the-subscription"></a>Tilldela klient åtkomst till prenumerationen

1. Kontrol lera att infrastrukturen för haveri beredskap har kon figurer ATS. Partner har åtkomst till klient prenumerationer via CSP-portalen, oavsett om haveri beredskap är hanterad eller självbetjäning. Konfigurera valvet och registrera infrastrukturen för klient prenumerationerna.
1. Ange innehavaren med [kontot som du skapade](#create-a-tenant-account).
1. Du kan lägga till en ny användare i klient prenumerationen via CSP-portalen på följande sätt:

    1. Gå till sidan för klientens CSP-prenumeration och välj alternativet **användare och licenser** .

       ![Innehavarens prenumerations sida för KRYPTOGRAFIPROVIDER](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    1. Nu ska du skapa en ny användare genom att ange relevant information och välja behörigheter, eller genom att ladda upp listan över användare i en CSV-fil.

    1. När du har skapat en ny användare går du tillbaka till Azure Portal. På sidan **prenumeration** väljer du den relevanta prenumerationen.

    1. Välj **åtkomst kontroll (IAM)** och klicka sedan på **roll tilldelningar**.

    1. Klicka på **Lägg till roll tilldelning** för att lägga till en användare med relevant åtkomst nivå. Användare som har skapats via CSP-portalen visas på fliken roll tilldelningar.

        ![Lägga till en användare](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- *Deltagar* rollen räcker för de flesta hanterings åtgärderna. Användare med den här åtkomst nivån kan utföra allt i en prenumeration förutom ändra åtkomst nivåer (för vilka åtkomst till *ägares* nivå krävs).
- Site Recovery har också tre [fördefinierade användar roller](site-recovery-role-based-linked-access-control.md)som kan användas för att ytterligare begränsa åtkomst nivåer efter behov.

## <a name="multi-tenant-environments"></a>Miljöer med flera klienter

Det finns tre större modeller med flera klienter:

* **HSP (Shared Hosting Services Provider)**: partnern äger den fysiska infrastrukturen och använder delade resurser (vCenter, data Center, fysisk lagring osv.) som värd för flera virtuella klient datorer i samma infrastruktur. Partnern kan tillhandahålla hantering av haveri beredskap som en hanterad tjänst, eller så kan klienten utföra en egen haveri beredskap som en självbetjänings lösning.

* **Dedikerad värd tjänst leverantör**: partnern äger den fysiska infrastrukturen, men använder dedikerade resurser (flera vCenter, fysiska data lager och så vidare) som värd för varje klients virtuella datorer på en separat infrastruktur. Partnern kan tillhandahålla hantering av katastrof återställning som en hanterad tjänst, eller så kan klienten äga den som en självbetjänings lösning.

* **(Managed Services Provider)**: kunden äger den fysiska infrastruktur som är värd för de virtuella datorerna och partnern tillhandahåller haveri beredskap och hantering.

Genom att konfigurera klient prenumerationer enligt beskrivningen i den här artikeln kan du snabbt börja aktivera kunder i alla relevanta modeller för flera klienter. Du kan lära dig mer om olika modeller för flera klienter och aktivera lokala åtkomst kontroller [här](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](site-recovery-role-based-linked-access-control.md) för att hantera Azure Site Recovery-distributioner.
- Lär dig mer om VMware till Azure- [replikerings arkitektur](vmware-azure-architecture.md).
- [Gå igenom självstudien](vmware-azure-tutorial.md) för att replikera virtuella VMware-datorer till Azure.
Lär dig mer om [miljöer med flera klienter](vmware-azure-multi-tenant-overview.md) för att replikera virtuella VMware-datorer till Azure.
