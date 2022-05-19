# zsrMSDOS
Расчет переходного процесса замкнутой системы регулирования (пневматика) с ПИ-регулятором
----------------
Лабораторная работа по МОСУ 1994-го года
----
Ввод исходных данных в текстовом режиме, которые записывает в файл `zsr_asu.dat` для повторного использования. Затем переключается в графический режим и рисует график точками с драйвером графического режима `egavga.bgi`. На ноутбуке IBM ThinkPad с процессором Intel 486SX и с оперативной памятью 3 Мбайта график считает примерно 5 минут

#### Общее описание

Этот расчет выполняется после:
 - идентификации объекта регулирования
 - аппроксимации
 - расчета переходного процесса

Главная задача отрасли АСУТП -
Расчет характеристик объекта по кривой разгона и расчет оптимальных настроек ПИ(ПИД)-регулятора
(аппаратного, программного или супервизорного)
одноконтурного или внутреннего контура каскадного регулирования с помощью конечно-разностной формы,
использующей как правило градиентный метод поиска оптимальных настроек
(5 настроек вместе с задержкой исполнительного механизма + регулирующего органа и их постоянной времени)
путем анализа его переходного процесса и величины перерегулирования.

#### Процедура такова.

На объекте из установившегося режима даем ступеньку на вход и записываем его кривую разгона до выхода
в установившийся режим снова.

Так 3-4 раза.

Специфика объекта такова, что рабочую точку можно ловить несколько часов или суток.
Поэтому динамика пишется уже без ручного вмешательства и без внешних возмущающих воздействий -
это очень важно.

Заходим на сервер по RDP, открываем расчеты и считаем параметры объекта принимая,
 что около рабочей точки внешняя характеристика объекта линейна и однозначна,
 как апериодическое звено 3-го порядка и не имеет взаимосвязей на выходах - для начала простейший случай.

Считаем адекватность модели по критерию Фишера (дисперсия остаточная и дисперсия квадратов отклонений).
Глядя на посчитанные параметры объекта (задержка, передаточный коэффициент, три постоянные времени)
делаем начальное приближение для настроек регулятора.

Раньше для этого пользовались немецкими номограммами 1960-х, но по ним выходили такие настройки,
что первые проходы выдавали неприемлемо большие махи. Запускаем расчет оптимальных настроек.
Считает долго (несколько суток или месяцев) и время расчета сильно зависит от правильности
выбора начального приближения. На первой стадии уточняем квант по времени путем его деления на
2 на каждом следующем проходе до тех пор, пока интегральная разница между текущим и предыдущим
проходом не пропадет. На второй стадии выбираем метод поиска оптимальных настроек -
последовательного приближения (проще и дольше) или градиентный (сложнее и быстрее).
Расчет пишет промежуточные результаты в БД на SQL Server-е.

Параллельно запускаем на двух других клиентах еще по одному экземпляру расчета с последовательным отставанием,
которые пишут свои промежуточные результаты каждый в такую же, но свою тестовую БД,
которую раскатываем из бэкапа рабочей БД сразу после запуска рабочего расчета.
Когда проходы сходятся, расчет останавливается. Время до окончания оценивается по промежуточным результатам из БД.
Если все три расчета выдают один и тот же результат, считаем, что конечный результат достоверный

#### Ссылочная литература

![Воронов Основы теории автоматического управления 2](https://user-images.githubusercontent.com/104857185/169300447-36c1207a-5957-4198-96ac-d02bae81e7b2.png)

в 2-х частях см. на сервере в папке `..\Математическое обеспечение` проекта
