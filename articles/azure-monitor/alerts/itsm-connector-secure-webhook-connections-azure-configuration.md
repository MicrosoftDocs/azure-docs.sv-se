---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) säker export i Azure Monitor – Azure-konfigurationer
description: Den här artikeln visar hur du konfigurerar Azure för att ansluta dina ITSM-produkter/-tjänster med säker export i Azure Monitor för att centralt övervaka och hantera ITSM-arbetsobjekt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 62167b8fd72c660e50378a9492528aa5b70bbee7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625757"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>Konfigurera Azure för att ansluta ITSM-verktyg med säker export

Den här artikeln innehåller information om hur du konfigurerar Azure för att använda "säker export".
Följ de här stegen för att använda "säker export":

1. [Registrera appen med Azure AD.](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [Definiera tjänstens huvud namn.](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [Skapa en säker webhook-åtgärds grupp.](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. Konfigurera din partner miljö.
    Säker export stöder anslutningar med följande ITSM-verktyg:
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [BMC-Helix](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>Registrera dig för Azure Active Directory

Följ de här stegen för att registrera programmet med Azure AD:

1. Följ stegen i [Registrera ett program med Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md).
2. I Azure AD väljer du **exponera program**.
3. Välj **set** för **program-ID-URI**.

   [![Skärm bild av alternativet för att ställa in U R i för programmet I D.](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad.png)](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad-expand.png#lightbox)
4. Välj **Spara**.

## <a name="define-service-principal"></a>Definiera tjänstens huvud namn

Åtgärds grupp tjänsten är en första parts applikation, och har därför behörighet att hämta autentiseringstoken från AAD-programmet för att kunna autentisera med tjänsten nu.
Som ett valfritt steg kan du definiera program rollen i den skapade appens manifest, vilket kan göra det möjligt att ytterligare begränsa åtkomsten så att endast vissa program med den specifika rollen kan skicka meddelanden. Den här rollen måste tilldelas till åtgärds gruppens tjänst huvud namn (kräver administratörs behörighet för innehavare).

Det här steget kan göras via samma [PowerShell-kommandon](../alerts/action-groups.md#secure-webhook-powershell-script).

## <a name="create-a-secure-webhook-action-group"></a>Skapa en säker webhook-åtgärds grupp

När programmet har registrerats med Azure AD kan du skapa arbets objekt i ITSM-verktyget baserat på Azure-aviseringar med hjälp av åtgärden säker webhook i åtgärds grupper.

Åtgärds grupper ger ett modulärt och återanvändbart sätt att utlösa åtgärder för Azure-aviseringar. Du kan använda åtgärds grupper med mått aviseringar, aktivitets logg aviseringar och Azure Log Analytics-aviseringar i Azure Portal.
Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper i Azure Portal](../alerts/action-groups.md).

Om du vill lägga till en webhook till en åtgärd, följer du dessa anvisningar för säker webhook:

1. Sök efter och välj **övervaka** i [Azure Portal](https://portal.azure.com/). I **övervaknings** fönstret samlas alla övervaknings inställningar och data i en vy.
2. Välj **aviseringar**  >  **Hantera åtgärder**.
3. Välj [Lägg till åtgärds grupp](../alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal)och fyll i fälten.
4. Ange ett namn i rutan **Åtgärds grupp namn** och ange ett namn i rutan **kort namn** . Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.
5. Välj **säker webhook**.
6. Välj följande information:
   1. Välj objekt-ID för den Azure Active Directory instans som du har registrerat.
   2. För URI: n klistrar du in den webhook-URL som du kopierade från [ITSM-verktygets miljö](#configure-the-itsm-tool-environment).
   3. Ange **aktivera det gemensamma aviserings schemat** till **Ja**. 

   Följande bild visar konfigurationen av en exempel säker webhook-åtgärd:

   ![Skärm bild som visar en säker webhook-åtgärd.](media/itsm-connector-secure-webhook-connections-azure-configuration/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>Konfigurera ITSM-verktygets miljö

Konfigurationen består av två steg:

1. Hämta URI: n för säker export definition.
2. Definitioner enligt flödet av ITSM-verktyget.

## <a name="next-steps"></a>Nästa steg

* [ServiceNow säker export konfiguration](./itsmc-secure-webhook-connections-servicenow.md)
* [Konfiguration av BMC säker export](./itsmc-secure-webhook-connections-bmc.md)
