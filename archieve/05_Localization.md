<!-- omit from toc --> 
# Локализация

- [AMCL](#amcl)

## AMCL

Алгоритм, который применяется для определения местоположения робота на карте, называется AMCL. AMCL использует многочастичный фильтр для отслеживания положения робота на карте. В нашем роботе для реализации AMCL мы используем [пакет ROS](http://wiki.ros.org/amcl).

```xml
<?xml version="1.0"?>
<launch>

    <arg name="robot_pose_x"/>
    <arg name="robot_pose_y"/>
    <arg name="scan_input_topic" default="scan" />

    <node pkg="amcl" type="amcl" name="amcl" output="screen">
        <rosparam file="$(find kitty_package)/config/amcl/amcl.yaml" command="load"/>
        <param name="initial_pose_x" value="$(arg robot_pose_x)"/>
        <param name="initial_pose_y" value="$(arg robot_pose_y)"/>
        <remap from="scan" to="$(arg scan_input_topic)"/>
    </node>

</launch>
```
Сохраните его в `kitty_software->drivers`, как `amcl.launch`

Конфиг `amcl.yaml` уже лежит в папке `config` этого репозитория, сохраните его к себе в проект. Кроме самого алгоритма `amcl` нам еще пригодится `map_server` - ROS-пакет, с помощью которого созданная карта при помощи пакета `gmapping` может быть сохранена.

```xml
<?xml version="1.0"?>
<launch>
    <arg name="map_file"/>
    <!-- map server -->
    <node name="map_server" pkg="map_server" type="map_server" args="$(arg map_file)">
        <param name="frame_id" value="map"/>
    </node>
</launch>
```
Сохраните его в `kitty_software->drivers`, как `map_server.launch`

> Теперь нам нужно создать файл полного запуска управления с джойстика, обзовем его, как: `full_start_joy_control.launch`. Он должен будет включать в себя:
- `start_joy_teleop.launch`. В нем нужно будет задать аргументы `frwd_limit`, `bkwrd_limit`, `steer_limit`, у которого будут значения `2`, `-1`, `30` соответственно.
- `uc_serial_server.launch`. Не забудьте в него добавить аргумент `port`, принимающий значение `/dev/ttyACM0`
- `uc_convertion_layer.launch`

