# Урок 5. Docker Compose и Docker Swarm
## Classwork
Знакомились с работой Docker Compose и Docker Swarm
## Homework 
### Задание 1:
1) создать сервис, состоящий из 2 различных контейнеров: 1 - веб, 2 - БД
2) далее необходимо создать 3 сервиса в каждом окружении (dev, prod, lab)
3) по итогу на каждой ноде должно быть по 2 работающих контейнера
4) выводы зафиксировать

### Задание 2*:
1) нужно создать 2 ДК-файла, в которых будут описываться сервисы
2) повторить задание 1 для двух окружений: lab, dev
3) обязательно проверить и зафиксировать результаты, чтобы можно было выслать преподавателю для проверки

Задание со звездочкой - повышенной сложности, это нужно учесть при выполнении (но сделать его необходимо).

### или (если нет возможности создать вторую node)
1) Создать 2 ДК-файла, в которых будут описываться сервисы состоящие из двух контейнеров: 1 - веб, 2 - БД
2) Сделать реплики контейнеров воспользовавшись сервисом Docker Swarm;
3) Проверить работоспособность контейнеров.

**Термины**

Для того чтобы пользоваться swarm, надо запомнить несколько типов сущностей:

Node - это наши виртуальные машины, на которых установлен docker. Есть manager и workers ноды. Manager нода управляет 
workers нодами. Она отвечает за создание/обновление/удаление сервисов на workers, а также за их масштабирование и 
поддержку в требуемом состоянии. Workers ноды используются только для выполнения поставленных задач и не могут управлять 
кластером.

Stack - это набор сервисов, которые логически связаны между собой. По сути это набор сервисов, которые мы описываем в 
обычном compose файле. Части stack (services) могут располагаться как на одной ноде, так и на разных.

Service - это как раз то, из чего состоит stack. Service является описанием того, какие контейнеры будут создаваться. 
Если вы пользовались docker-compose.yaml, то уже знакомы с этой сущностью. Кроме стандартных полей docker в режиме swarm 
поддерживает ряд дополнительных, большинство из которых находятся внутри секции deploy.

Task - это непосредственно созданный контейнер, который docker создал на основе той информации, которую мы указали при 
описании service. Swarm будет следить за состоянием контейнера и при необходимости его перезапускать или перемещать на 
другую ноду.

**Первый вариант решение(без использования второй node):**

Создаем два ДК-файла:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/b2190ce6-e35a-4a58-a0d8-76c8382e8c0d)

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/c3b412e1-0218-4b2f-b155-10f8992abe77)

Компилируем node:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/a4a3c9d9-a645-4bbb-92f3-2631365504a9)

Проверяем:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/3d70bed8-597d-4034-a2c1-19aabd450cfe)

Пытаемся задеплоить в Stack:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/731f14fe-4a6d-44da-8891-ed5092d0fa9e)
![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/e938da50-c76d-4d5e-a20e-fe990a4b1b47)

Получаем ошибку: "версия данного файла не поддерживается", продолжаем делать, как делали на семинаре.
Запускаем первый ДК-файл:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/65158074-fc4f-4cff-9e87-50485248c7c2)

Проверяем:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/f60af2ac-4693-43cf-9320-fea60cf1ab57)

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/5a1273aa-e5ae-4280-9570-3e64767370a5)

Запускаем второй ДК-файл:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/63b3118a-8405-475f-a14f-6b5f20cb344b)

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/5c2c2584-0d16-4afa-a547-f4563afa5064)

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/8f43c4e2-2c12-477b-8601-c5568e4924ac)

Воспользуемся функционалом Docker Swarm и создадим дубликат одного и того же образа:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/708610e1-6bd7-4be1-8606-7745999176d3)

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/5fdf6541-a12f-47b6-9990-a99dc1c6c3a2)

Аналогично можем проделать с другими образами, далее повязать их сетью и получить полные дубликаты: БД + WEB.

**Второй вариант решение(с использованием второй node):**

Подключаемся по SSH ко второй VM и устанавливаем swarm соединение, предварительно инициализировав manager node:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/76989062-b216-484a-8bce-06bcd1658c4a)

Запускаем первый ДК-файл на manager node:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/72879a8d-7b0f-44a0-9922-b92a8ad2ecbb)

Проверяем:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/b7a44534-0579-4762-a18f-6e31789cb736)

Запускаем второй ДК-файл на worker node:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/8d2d5d33-8ad2-4084-b349-a23729885218)

Проверяем:
![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/8707e06f-0dbd-49f0-9fb5-65045a0494ae)

Попробуем запустить первый ДК-файл на node worker:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/8045e2f8-d169-4df2-a123-5a48e007fbae)

Проверяем, все работает, ошибки дублирования не произошло т. к имена image разные(ДК-файлы в разных директориях 
находятся на VM)

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/d69faae2-fd66-4033-ad58-674b0b71325d)

Запускаем на manager node второй ДК-файл и получаем четыре работающих контейнера на одной node и на другой
(не считая hello-word):

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/4a015eb4-cd44-4852-b1ad-b32516622711)

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/8cba36f0-02e7-4e9e-b9b9-b3ccf5f28d3d)

Если бы расположение папок было идентично на manager node и worker node, пришлось бы создавать реплики образов и 
обвязывать их сетью:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/8e08cb33-60a9-40cb-9ddc-dfd1f1c5df84)

Тем временем наблюдаем за статусом worker node, он "упал"(Отказался идти на повышение). Переподключаем swarm соединение:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/062e0cb8-2d84-4525-b3c3-bac6e1285d7d)

Создаем еще реплики образов и видим как система их перераспределяет:

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/7d1a93ee-02c1-4fa1-8c7d-b1e262fcb445)

![image](https://github.com/Jetrong/DZSeminar5/assets/136317824/b8c117ab-7c9d-46db-a006-fc80c34d28a7)

*Подготовил студент GeekBrains* [**`Анисимов Роман`**]
DZSeminar5
