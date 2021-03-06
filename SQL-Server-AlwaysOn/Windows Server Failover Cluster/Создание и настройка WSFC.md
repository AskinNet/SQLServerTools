# Создание и настройка отказоустойчивого кластера Windows

После установки компонентов WSFC и проверки конфигурации узлов можно переходить к созданию и настройке самого кластера. Начнем с создания самого кластера и начальных настроек, а после сделаем простую настройку кворума.

## Создание кластера

### Через "Диспетчер отказоустойчивости кластеров"

1. Запускаем мастер создания кластера.

![Начало создания кластера](media/3.%20Настройки%20WSFC/1.%20Начало%20создания%20кластера.PNG)

2. Открывается раздел "Перед началом работы". Идем далее.

![Перед началом создания кластера](media/3.%20Настройки%20WSFC/2.%20Мастер%20создания%20кластера.PNG)

3. Добавляем узлы, из которых будет состоять наш кластер.

![Настройка состава узлов кластера](media/3.%20Настройки%20WSFC/3.%20Выбор%20серверов%20кластера.PNG)

4. Настраиваем имя и адрес кластера.

![Настройки точек доступа](media/3.%20Настройки%20WSFC/4.%20Настройка%20точек%20доступа.PNG)

5. Проверяем настройки и начинаем установку.

![Подтверждение настроек кластера перед созданием](media/3.%20Настройки%20WSFC/5.%20Подтверждение%20создания%20кластера.PNG)

6. Ждем окончание процесса создания кластера.

![Процесс создания кластера](media/3.%20Настройки%20WSFC/6.%20Процесс%20создания%20кластера.PNG)

7. И ... кластер создан!

![Кластер успешно создан](media/3.%20Настройки%20WSFC/7.%20Результат%20создания%20кластера.PNG)

8. Вот так это будет выглядеть в диспетчере отказоустойчивости кластеров.

![Только что созданный кластер](media/3.%20Настройки%20WSFC/8.%20Новый%20кластер.PNG)

### PowerShel

Для создания кластера используется следующая команда:

```PowerShell
# Name - имя кластера
# Node - список имен серверов, которые будут выступать узлами кластера
# StaticAddress - статический адрес кластера в сети
New-Cluster -Name "AG-WSFC" -Node "SQL-AG-1", "SQL-AG-2" -StaticAddress 192.168.1.143
```

Просто, не правда ли! Выглядеть это будет так:

1. Выполняем команду.

![Команда создания кластера](media/3.%20Настройки%20WSFC/PowerShell/1.%20PS.%20Команда%20создания%20кластера.PNG)

2. Ждем окончания создания кластера.

![Процесс создания кластера и настройки](media/3.%20Настройки%20WSFC/PowerShell/2.%20PS.%20Процесс%20создания%20кластера.PNG)

3. Готово!

![Кластер создан!](media/3.%20Настройки%20WSFC/PowerShell/3.%20PS.%20Создан%20новый%20кластер.PNG)

Все готово!

## Настройка кворума

Кворум (Quorum) - это некоторый дополнительный ресурс, который используется в голосовании (опросе) узлов кластера для проверки работоспособности. Обычно используется в тех случаях, когда количество узлов в кластере четное. В качестве кворума может использоваться общий диск, сервер-свидетель с общей файловой папкой и другие способы настроек.

Для нашего просто примера воспользуемся общей папкой, которая доступна всем узлам в кластере. Подробнее про кворум можно [прочитать здесь](https://docs.microsoft.com/ru-ru/sql/sql-server/failover-clusters/windows/wsfc-quorum-modes-and-voting-configuration-sql-server?view=sql-server-2017).

### Через "Диспетчер отказоустойчивости кластеров"

1. Запустим мастер настройки кворума.

![Запуск настройки кворума](media/3.%20Настройки%20WSFC/9.%20Начало%20настройки%20кворума.PNG)

2. Откроется раздел "Перед началом работы". Нажмем "Далее".

![Перед созданием кворума](media/3.%20Настройки%20WSFC/10.%20Мастер%20настройки%20кворума.PNG)

3. На этапе выбора параметров конфигурации кворума остановим свой выбор на дополнительных настройках.

![Варианты кворума](media/3.%20Настройки%20WSFC/11.%20Выбор%20варианта%20настройки%20кворума.PNG)

4. В выборе конфигурации голосования оставляем выбранными все узлы без изменений.

![Варианты голосования](media/3.%20Настройки%20WSFC/12.%20Выбор%20конфигурации%20голосования.PNG)

5. На этапе выбора свидетеля выбираем файловый ресурс, как было сказано Выше. Для нашего примера этого достаточно.

![Выбор свидетеля кворума](media/3.%20Настройки%20WSFC/13.%20Настроить%20файловый%20ресурс%20свидетель.PNG)

6. В настройках файлового ресурса укажем путь к общей папке, доступной каждому узлу кластера.

![Настройка файлового ресурса](media/3.%20Настройки%20WSFC/14.%20Настройка%20файлового%20ресурса%20свидетеля.PNG)

7. Подтверждаем настройки перед применением изменений.

![Подтверждение настроек кворума](media/3.%20Настройки%20WSFC/15.%20Подтверждение%20настройки%20кворума.PNG)

8. И кворум готов!

![Кворум успешно настроен](media/3.%20Настройки%20WSFC/16.%20Быстрая%20настройка%20и%20результат.PNG)

Но как сделать эти же настройки с помощью PowerShell?

### PowerShell

С помощью PowerShell все гораздо проще, чем использование мастера настройки кворума.

```PowerShell
Set-ClusterQuorum -NodeAndFileShareMajority \\DC\Quorum
```

Вот и все готово!