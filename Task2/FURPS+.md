
| Код| Требования| Комментарий|
------------------------------------------ |--------------------------------------------------------------------------------------------------- |-------------------------------------------------------------------------------------------------------------------------- |
| **F — Функциональные  (Functional)**|||
| F1 | Новый клиент может подать заявку на депозит через сайт или интернет-банк                            | Для сайта: требуется только ФИО и телефон. Для интернет-банка: выбор суммы, счета, подтверждение СМС. |
| F2 | Клиент может подать заявку на кредит через сайт или интернет-банк | На сайте возможна предварительная оценка при наличии паспортных данных. В интернет-банке — персонализированные предложения. |
| F3 | Система отображает актуальные ставки по депозитам и кредитам| Ставки берутся из АБС (в будущем), сейчас — из XLS.|
| F4 | Дебетовые Заявки обрабатываются сотрудниками бэк-офиса в АБС | Перед обработкой они должны получить информацию о текущих ставках - (сейчас по почте от коллег из кредитного отдела)   |
| F5 | При одобрении депозита или кредита клиент получает уведомление по СМС |  Вызывает АБС через SMS-шлюз по REST API.|
| F6| Предодобренные клиенты могут получить ускоренное рассмотрение заявки на кредит | Предварительная информация берётся из системы скоринга через REST API. Для них не нужно повторно проводить кредитный скоринг |
| F7 | Новые клиенты должены пройти идентификацию в отделении после подачи заявки | Только после этого можно открыть депозит/кредит.|
| F8 | Операция на подачу заявки на кредит подтверждается СМС-кодом.| Можно использовать готовый СМС-шлюз, но систему подтверждения нужно написать с нуля|
| F9| Менеджер кол-центра может предложить особые условия по депозиту| Менеджер должен иметь доступ к справочной информации и текущим ставкам (на первое время предлагается хранить xsl файл со ставками в АБС)|
| **U — Использование (Usability)**|||
| U1| Интерфейс должен соответствовать корпоративной системе дизайна| Единые цвета, элементы, логотипы, шрифты и т.п.|
| U2| Отклики системы должны быть быстрыми (миллисекунды)| Сейчас наблюдаются задержки при загрузке справочных данных. Решается кэшированием в Redis|
| **R — Надежность  (Reliability)**|||
| R1 | Все сервисы должны работать 24/7 и быть доступны в 99.9% случаев | Это касается интернет-банка, АБС, SMS-сервисов и других систем. |
| R2 | Должна быть возможность горизонтального масштабирования интернет-банка | АБС может масштабироваться только вертикально из-за своей базы данных |
| R3 | В случае сбоя в основном ЦОД система должна автоматически переключиться на резервный | Банк уже имеет резервный ЦОД, но нужно реализовать автоматическое переключение.|
| **P — Производительность  (Performance)**  |||
| P1| Скорость выполнения операций должна быть менее секунды| Время отклика API — не более 500 мс в 95% случаев|Особенно важно для взаимодействия с внешними системами и получения данных.|
| P2| Обмен данными между системами (например, АБС и Кредитным конвейером) должен происходить раз в сутки | Ускорить невозможно из-за ограничений системы. Выполняется через ETL-процесс|
| **S — Поддерживаемость  (Supportability)** |||
| S1| Желательно использовать технологии, в которых есть экспертиза внутри банка| Например базу данных MS SQL или Oracle|
| S2| Должна быть создана документация для дальнейшего расширения системы| Это MVP, поэтому важно предусмотреть последующее развитие.|
| **+R — + Ограничения (Restrictions)**|||
| +R1 | Передача чувствительной информации должна осуществляться с использованием шифрования трафика|Использовать TLS 1.2+.|
| +R2 | Должна быть реализована защита от XSS, CSRF и других типов атак | Для обеспечения безопасности клиентских интерфейсов.|
| +R3 | Сайт и интернет-банк должны быть доступны с мобильных устройств| Реализация через адаптивный дизайн и PWA|
| +R4 | Стоит предусмотреть возможность перевода интернет-банка на микросервисную архитектуру| Это повысит отказоустойчивость и позволит гибко развивать отдельные компоненты.|
| +R5| Должна быть реализована централизованная система логирования и мониторинга| ELK-стек. Для диагностики проблем и контроля состояния сервисов.|
| +R6 | Стоит реализовать API-шлюз (API Gateway) между интернет-банком и внутренними системами              | Это поможет избежать прямого вызова API АБС и улучшит управляемость.|
| +R7 | Систему кредитного скоринга не нужно нагружать предрасчетами скорингов в рабочее время| Выполнять операции с 21.00 до 6.00|
| +R8 | Кредитный конвеер и АБС не взаимодействуют в реал-тайме| Изменения накапливаются, а потом происходит ETL-процесс раз в сутки