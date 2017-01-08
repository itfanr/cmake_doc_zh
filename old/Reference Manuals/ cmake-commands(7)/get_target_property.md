get_target_property
===

get_target_property 从一个目标中获取一个属性值。

  get_target_property(VAR target property)
　　从一个目标中获取属性值。属性的值会被存储在变量VAR中。如果该属性没有被发现，VAR会被设置为NOTFOUND。使用set_target_properties命令来设置属性值。属性值一般用于控制如何去构建一个目标，但是有些属性用来查询目标的信息。该命令可以获取当前已经被构建好的任意目标的属性。该目标不一定存在于当前的CMakeLists.txt文件中。

