---
title: Анализ особенностей использования мобильного приложения и поведения пользователей с помощью Центра приложений Visual Studio и служб Azure
description: Сведения о Центре приложений и других службах помогут вам принимать взвешенные бизнес-решения, анализируя использование мобильного приложения пользователями.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 744e41200a5f7e5ff898e7a0786a4284de176b7a
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632596"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Анализ и понимание использования мобильных приложений

Насколько хорошо вы понимаете, как пользователи используют ваши приложения? Сколько человек активно работают с приложением и как меняется режим использования с течением времени? Какие функции они используют и какие из них применяются чаще всего? Где находятся эти пользователи? Сколько пользователей использует последнюю версию приложения? Все эти вопросы важны для того, чтобы развить успешный бизнес на основе приложения. Чтобы получить ответы на такие аналитические вопросы, следует собирать из приложений данные об использовании.

Чем масштабнее вы анализируете данные, тем больше найдется возможностей, позволяющих улучшить приложение и обеспечить удовлетворенность пользователей. Очень важно извлекать из данных практические полезные сведения для сохранения удовлетворенности пользователей.

## <a name="importance-of-analytics"></a>Важность аналитики

- Изучите своих пользователей. Узнайте, как они взаимодействуют с приложением и что мотивирует их возвращаться к нему, чтобы точнее настроить это приложение и обеспечить хорошее взаимодействие для развития бизнеса.
- Следите за метриками использования, чтобы принимать взвешенные решения о методах продажи приложения и улучшении обслуживания клиентов.
- Измеряйте производительность приложения.
- Узнайте, какие элементы приложения больше всего влияют на его полезность и производительность.
- Получите на основе данных полезные сведения о проблемах, связанных с обработкой и хранением данных.

Указанные ниже службы помогут в аналитике мобильных приложений.

## <a name="visual-studio-app-center"></a>Центр приложений Visual Studio

[Служба аналитики Центра приложений](/appcenter/analytics/) позволяет расширить аудиторию, уделяя внимание наиболее важным аспектам. Решение обеспечивает детализированные отчеты и аналитические сведения о сеансах работы пользователей, самых популярных устройствах, версиях ОС и поведении пользователей. Вы можете легко создавать пользовательские события для отслеживания любых действий с широкими возможностями аналитики приложений.

### <a name="visual-studio-app-center-features"></a>Возможности Центра приложений Visual Studio

- Бесплатно отслеживайте шаблоны использования, привлекательность для пользователей и другие показатели вовлеченности.
- Выявляйте тенденции, а также отслеживайте поведение и вовлеченность пользователей с помощью пользовательских событий.
- На единой панели мониторинга вы найдете готовые метрики и подробные аналитические сведения об использовании приложений (ежедневно, еженедельно, ежемесячно), данные о сеансах и свойствах устройств, а также результаты демографического анализа пользователей.
- Непрерывно экспортируйте все данные службы аналитики Центра приложений в Azure, где они будут храниться неограниченно долго. Служба аналитики Центра приложений поддерживает экспорт в Хранилище BLOB-объектов Azure и Azure Application Insights.
- Настройте интеграцию с Azure Application Insights, чтобы получить еще более глубокие аналитические сведения, такие как период удержания, а также данные воронок и информацию по когортам.
- Для интеграции пакета SDK достаточно одной строки кода. Это позволяет начать работу за считаные минуты.
- Получите поддержку платформ iOS, Android, macOS, tvOS, Xamarin, React Native, Unity и Cordova.

### <a name="visual-studio-app-center-references"></a>Справочная информация о Центре приложений Visual Studio

- [Регистрация в Центре приложений](https://appcenter.ms/signup)
- [Начало работы со службой аналитики Центра приложений](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor

Azure Monitor включает службу [Application Insights](/azure/azure-monitor/app/app-insights-overview), которая предоставляет средства для сбора и анализа данных телеметрии, позволяющие повысить производительность и выполнять мониторинг мобильного приложения. Вы можете воспользоваться преимуществами Application Insights, настроив экспорт в эту службу с помощью службы аналитики Центра приложений. Application Insights позволяет выполнять запросы, сегментирование, фильтрацию и анализ данных телеметрии пользовательских событий из ваших приложений, в дополнение к другим средствам службы аналитики Центра приложений.

### <a name="azure-monitor-features"></a>Возможности Azure Monitor

- Запросы по данным телеметрии о пользовательских событиях.
- Фильтрация данных телеметрии событий с мощными возможностями сегментирования.
- Анализ шаблонов конверсии, периодов удержания и навигации в приложении. Вы можете использовать:
  - воронки для анализа и отслеживания параметров конверсии;
  - сведения о периоде удержания для анализа того, насколько хорошо ваше приложение сохраняет внимание пользователей с течением времени;
  - книги для объединения визуализаций и текста в отчет, к которому можно предоставить общий доступ;
  - когорты для присвоения имен и сохранения конкретных групп пользователей или событий, чтобы на них можно было легко ссылаться из других средств аналитики.

### <a name="azure-monitor-references"></a>Справочники по Azure Monitor

- [Портал Azure](https://portal.azure.com/)
- [Анализ мобильного приложения с помощью Центра приложений и Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Использование службы аналитики Центра приложений с Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab

[Azure PlayFab](https://playfab.com/) предлагает комплексную серверную платформу с игровыми службами, аналитикой в реальном времени и LiveOps, которые необходимы для создания игр мирового класса, подключенных к облаку. Эти службы уменьшают преграды, стоящие перед разработчиками игр. Платформа предоставляет студиям любого размера экономичные решения для разработки, которые можно масштабировать вместе с играми. Эти службы помогают студиям привлекать и удерживать игроков, а также получать прибыль. Благодаря PlayFab разработчики могут применить интеллектуальные возможности облака для создания и эксплуатации игр, анализа игровых данных и повышения качества взаимодействия в играх.

### <a name="azure-playfab-features"></a>Возможности Azure PlayFab

- Мониторинг панелей мониторинга в режиме реального времени.
- Оценка производительности игр по основным метрикам.
- Просмотр сводных данных о ежедневной и ежемесячной производительности игр в автоматически создаваемых отчетах. Вы можете просматривать отчеты в Game Manager, скачивать их или автоматически доставлять в почтовый ящик.
- Тестирование А/Б, которое позволяет экспериментировать и находить оптимальные значения для конкретных переменных.
- Возможность сегментации игроков для их автоматического группирования.

### <a name="azure-playfab-references"></a>Справочники по Azure PlayFab

- [Портал PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analytics](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Краткие руководства](/gaming/playfab/#pivot=documentation&panel=quickstarts)
