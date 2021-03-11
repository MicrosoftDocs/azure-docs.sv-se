---
title: PowerShell-exempel för Azure AD-programproxy
description: Använd de här PowerShell-exemplen för Azure AD-programproxy för att få information om programproxy-appar och-kopplingar i din katalog, tilldela användare och grupper till appar och hämta certifikat information.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeff14e1676389eb2204cad45ea7b00d5aa9796b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566659"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD PowerShell-exempel för Azure AD-programproxy

Följande tabell innehåller länkar till exempel på PowerShell-skript för Azure AD-programproxy. De här exemplen kräver antingen [AzureAD v2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2) eller [AzureAD v2 PowerShell för för hands versionen av Graph module](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), om inget annat anges.


Mer information om de cmdlets som används i de här exemplen finns i [Application Management](/powershell/module/azuread/#application_proxy_application_management) och Application [proxy Connector Management](/powershell/module/azuread/#application_proxy_connector_management).

| Länk | Beskrivning |
|---|---|
|**Application Proxy-appar**||
| [Visa en lista med grundläggande information om alla Application Proxy-appar](scripts/powershell-get-all-app-proxy-apps-basic.md) | Visar grundläggande information (AppId, DisplayName, ObjId) om alla Application Proxy-appar i din katalog. |
| [Visa utökad information för alla Application Proxy-appar](scripts/powershell-get-all-app-proxy-apps-extended.md) | Visar utökad information (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) om alla Application Proxy-appar i din katalog.  |
| [Visa en lista över alla Application Proxy-appar per anslutnings grupp](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Visar information om alla Application Proxy-appar i din katalog och vilka anslutnings grupper som apparna är tilldelade till. |
| [Hämta alla Application Proxy-appar med en livs längd princip för token](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Visar en lista över alla Application Proxy-appar i din katalog med en livs längd princip för token och dess information. Det här exemplet kräver [för hands versionen av AzureAD v2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview). |
|**Anslutningsgrupper**||
| [Hämta alla kopplings grupper och anslutningar i katalogen](scripts/powershell-get-all-connectors.md) | Visar en lista över alla kopplings grupper och anslutningar i din katalog. |
| [Flytta alla appar som är kopplade till en anslutnings grupp till en annan anslutnings grupp](scripts/powershell-move-all-apps-to-connector-group.md) | Flyttar alla program som är kopplade till en kopplings grupp till en annan kopplings grupp. |
|**Tilldelade användare och grupper**||
| [Visa användare och grupper som har tilldelats till ett program för programproxy](scripts/powershell-display-users-group-of-app.md) | Visar en lista över användare och grupper som har tilldelats till ett särskilt program för programproxy. |
| [Tilldela en användare till ett program](scripts/powershell-assign-user-to-app.md) | Tilldelar en specifik användare till ett program. |
| [Tilldela en grupp till ett program](scripts/powershell-assign-group-to-app.md) | Tilldelar en specifik grupp till ett program. |
|**Extern URL-konfiguration**||
| [Hämta alla Application Proxy-appar med hjälp av standard domäner (. msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Visar alla Application Proxy-program som använder standard domäner (. msappproxy.net). |
| [Hämta alla Application Proxy-appar med hjälp av jokertecken](scripts/powershell-get-all-wildcard-apps.md) | Visar alla Application Proxy-appar som använder jokertecken. |
|**Anpassad domän konfiguration**||
| [Hämta alla Application Proxy-appar med hjälp av anpassade domäner och certifikat information](scripts/powershell-get-all-custom-domains-and-certs.md) | Visar en lista över alla programproxy-appar som använder anpassade domäner och den certifikat information som är associerad med de anpassade domänerna. |
| [Hämta alla Azure AD proxy Application-appar som publicerats utan att något certifikat laddas upp](scripts/powershell-get-all-custom-domain-no-cert.md) | Visar en lista över alla programproxy-appar som använder anpassade domäner, men som inte har ett giltigt TLS/SSL-certifikat laddat upp. |
| [Hämta alla Azure AD proxy Application-appar som publicerats med samma certifikat](scripts/powershell-get-custom-domain-identical-cert.md) | Visar en lista över alla Azure AD proxy Application-appar som publicerats med samma certifikat. |
| [Hämta alla Azure AD proxy Application-appar som publicerats med samma certifikat och Ersätt det](scripts/powershell-get-custom-domain-replace-cert.md) | För Azure AD proxy-programappar som publiceras med ett identiskt certifikat kan du ersätta certifikatet i grupp. |
