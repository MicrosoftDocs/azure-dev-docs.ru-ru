---
author: edburns
ms.author: edburns
ms.date: 08/09/2020
ms.openlocfilehash: e006f2ec9d6f0d3b2d83a6653d65da19489dc221
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88052268"
---
### <a name="account-for-authentication-and-authorization"></a>Учет требований аутентификации и авторизации

Большинство приложений используют какие-либо механизмы аутентификации и авторизации.  Если вы используете LDAP для аутентификации, WebLogic Server в Azure поддерживает автоматическую интеграцию. Предложение в Marketplace использует доменные службы Azure Active Directory (Azure AD DS) с защищенным протоколом LDAP.  Предложение создает область по умолчанию для WebLogic Server из данных в Azure AD DS.  Дополнительные сведения см. в статье [Авторизация и аутентификация конечных пользователей для переноса приложений Java в WebLogic Server в Azure](../migrate-weblogic-with-aad-ldap.md).
