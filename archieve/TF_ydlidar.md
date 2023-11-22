<!-- omit from toc --> 
# Картографирование и локализация роботов

- [TF](#tf)


## TF


TF, сокращение от "Transformations", является важным модулем в системе ROS (Robot Operating System).

TF обеспечивает обработку преобразований координат между различными кадрами отслеживания. Он позволяет интегрировать данные из различных датчиков, причем каждый датчик может находиться в своем собственном кадре координат.

Трансформация между двумя кадрами представляет собой преобразование между их системами координат. Это может быть преобразование положения, ориентации или обоих.

Например, пусть есть датчик на роботе, который предоставляет данные о его позиции в кадре координат "robot". Если необходимо преобразовать эти данные в кадр координат "world", TF будет использовать преобразование между "robot" и "world" для выполнения преобразования.

Для преобразования координат между кадрами TF использует информацию о времени и о геометрических преобразованиях между кадрами. Он хранит эту информацию в виде графа, в котором узлы представляют кадры координат, а дуги представляют преобразования между ними.

TF также предоставляет возможность опубликовать информацию о трансформациях, используя стандартный протокол публикации сообщений ROS. Это позволяет другим узлам подписываться на информацию о трансформациях и использовать ее для определения местоположения робота и преобразования данных из датчиков.

Чтобы сообщить о том, насколько высоко располагается лидар относительно пола, добавим запуск узла, который создаёт **статический TF** между фреймами. Создадим новый launch c названием `kitty_tf.launch`:

```xml
<node pkg="tf" type="static_transform_publisher" name="base_footprint_2_base_link" args="0 0 0.3 0 0 0 base_footprint laser_frame 100" />
```

> Не забудьте, что `<launch>` тэг должен обрамлять весь файл, то есть добавлять надо внутрь него!

Тут мы немного цепляем запуск узлов в launch-файла. Чтобы опубликовать статический TF (сообщить расположение `laser_frame` относительно `base_footprint`), нам нужно запустить узел [static_transform_publisher](http://wiki.ros.org/tf#static_transform_publisher) из пакета [tf](https://wiki.ros.org/tf).

Аргументы у static_transform_publisher узла следующие `x y z yaw pitch roll frame_id child_frame_id period_in_ms`. Разберём:

- `x, y, z` - линейное расположение `child_frame_id` относительно `frame_id`,
- `yaw pitch roll` - угловое расположение (поворот) 
  
<p align="center">
    <img src=../assets/lesson_03/Yaw.gif width=250/>
    <img src=../assets/lesson_03/Pitch.gif width=250/>
    <img src=../assets/lesson_03/Roll.gif width=250/>
</p>

- `child_frame_id` относительно `frame_id`,
- `frame_id child_frame_id` - имена фрейма, между которыми создаём TF,
- `period_in_ms` - частота публикации, обычно ставится 100 или 1000, и норм.

Запускаем наш `launch` с запуском лидара и новый (в другом терминале) с `tf`, который мы создали 

```bash
roslaunch kitty_package kitty_tf.launch
```

Посмотрим Rviz и увидим, что данные отобразились! Отлично!

> :muscle: Попробуй поменять значения `x, y, z, yaw pitch roll` в аргументах TF узла и перезапускать после изменений launch с этим узлом. Посмотри, как это влияет на отображение. Сделайте корректное отображение данных с лидара. 