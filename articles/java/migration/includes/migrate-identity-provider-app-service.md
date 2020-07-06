---
author: yevster
ms.author: yebronsh
ms.date: 5/19/2020
ms.openlocfilehash: 51ed106fde71e37467571baab2b327b092dddc15
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507642"
---
### <a name="migrate-and-enable-the-identity-provider"></a>Миграция и включение поставщика удостоверений

Если какому-то из приложений требуется аутентификация или авторизация, обязательно настройте для них доступ к поставщику удостоверений с помощью следующих рекомендаций:

* Если роль поставщика удостоверений выполняет Azure Active Directory, дополнительные изменения не требуются.
* Если роль поставщика удостоверений выполняет локальный лес Active Directory, примените решение гибридной идентификации на основе Azure Active Directory. Дополнительные сведения см. в [документации по гибридной идентификации](/azure/active-directory/hybrid/).
* Если роль поставщика удостоверений выполняет другое локальное решение, например PingFederate, обратитесь к статье [Выборочная установка Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom), чтобы настроить федерацию с Azure Active Directory. Кроме того, вы можете применить Spring Security для работы с поставщиком удостоверений через [OAuth2 и OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) или [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2).
