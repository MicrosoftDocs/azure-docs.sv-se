---
title: Distribuera och konfigurera certifikat för företags certifikat utfärdare för för hands versionen av Azure Firewall Premium
description: Lär dig hur du distribuerar och konfigurerar certifikat för företags certifikat utfärdare för för hands versionen av Azure Firewall Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: fba95214a6bbb0482166eab8f77f30911986fbb7
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102525502"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall-preview"></a>Distribuera och konfigurera Enterprise CA-certifikat för för hands versionen av Azure Firewall

> [!IMPORTANT]
> Azure Firewall Premium är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


I för hands versionen av Azure Firewall Premium ingår en TLS-kontroll, som kräver en kedja av certifikatautentisering. För produktions distributioner bör du använda en företags-PKI för att generera de certifikat som du använder med Azure Firewall Premium. Använd den här artikeln för att skapa och hantera ett mellanliggande CA-certifikat för för hands versionen av Azure Firewall Premium.

Mer information om certifikat som används av Azure Firewall Premium Preview finns i avsnittet om för hands versioner av [Azure Firewall Premium](premium-certificates.md).

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du vill använda en företags certifikat utfärdare för att skapa ett certifikat som ska användas med Azure Firewall Premium Preview måste du ha följande resurser: 

- en Active Directory skog 
- en rot certifikat utfärdare för Active Directory certifikat tjänster med webb registrering aktive rad 
- en Azure Firewall Premium med brand Väggs princip på Premium-nivå 
- en Azure Key Vault 
- en hanterad identitet med Läs behörighet till **certifikat och hemligheter** som definierats i Key Vault åtkomst princip 

## <a name="request-and-export-a-certificate"></a>Begär och exportera ett certifikat

1. Få åtkomst till webbplatsen för webb registrering på rot certifikat utfärdaren, vanligt vis `https://<servername>/certsrv` och välj **begär ett certifikat**.
1. Välj **Avancerad certifikat förfrågan**.
1. Välj **skapa och skicka en begäran till denna certifikat utfärdare**.
1. Fyll i formuläret med den underordnade certifikat utfärdarens mall som visas:
1. Skicka begäran och installera certifikatet.
1. Om du antar att denna begäran görs från en Windows-Server med Internet Explorer öppnar du **Internet alternativ**.
1. Gå till fliken **innehåll** och välj **certifikat**.
1. Välj det certifikat som nyss utfärdats och välj sedan **Exportera**.
1. Välj **Nästa** för att starta guiden. Välj **Ja, exportera den privata nyckeln** och välj sedan **Nästa**.
1. . pfx-filformat är valt som standard. Avmarkera **ta med alla certifikat i certifierings Sök vägen om möjligt**. Om du exporterar hela certifikat kedjan kommer importen till Azure-brandväggen att Miss förloppet.
1. Tilldela och bekräfta ett lösen ord för att skydda nyckeln och välj sedan **Nästa**.
1. Välj ett fil namn och en export plats och välj sedan **Nästa**.
1. Välj **Slutför** och flytta det exporterade certifikatet till en säker plats.

## <a name="add-the-certificate-to-a-firewall-policy"></a>Lägg till certifikatet i en brand Väggs princip

1. I Azure Portal går du till sidan certifikat i Key Vault och väljer **generera/importera**.
1. Välj **Importera** som skapande metod, namnge certifikatet, Välj den exporterade. pfx-filen, ange lösen ordet och välj sedan **skapa**.
1. Gå till sidan **TLS-kontroll (för hands version)** i brand Väggs principen och välj din hanterade identitet, Key Vault och certifikat. 
1. Välj **Spara**.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="TLS-kontroll":::

## <a name="validate-tls-inspection"></a>Verifiera TLS-kontroll

1. Skapa en program regel med TLS-kontroll till mål-URL: en eller FQDN som du väljer.  Exempel: `*bing.com`.
1. Från en domänansluten dator i regelns käll intervall navigerar du till ditt mål och väljer Lås symbolen bredvid adress fältet i webbläsaren. Certifikatet bör visa att det har utfärdats av din företags certifikat utfärdare i stället för en offentlig CA.
1. Visa certifikatet för att visa mer information, inklusive certifikat Sök vägen.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="certifikat information":::
1. Kör följande KQL-fråga i Log Analytics för att returnera alla begär Anden som har genomgått TLS-kontroll:
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   Resultatet visar den fullständiga URL: en för den inspekterade trafiken: :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="KQL-fråga":::

## <a name="next-steps"></a>Nästa steg

[För hands versionen av Azure Firewall Premium i Azure Portal](premium-portal.md)
