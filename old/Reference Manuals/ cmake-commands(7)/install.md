install
===

install 指定在安装时要运行的规则。
　　该命令为一个工程生成安装规则。在某一源文件路径中，调用这条命令所指定的规则会在安装时按顺序执行。在不同路径之间的顺序未定义。

　　该命令有诸多版本。其中的一些版本定义了文件以及目标的安装属性。这多个版本的公共属性都有所涉及，但是只有在指定它们的版本中，这些属性才是合法的（下面的DESTIONATION到OPTIONAL的选项列表是公共属性。——译注）。

　　DESTINATION选项指定了一个文件会安装到磁盘的哪个路径下。若果给出的是全路径（以反斜杠或者驱动器名开头），它会被直接使用。如果给出的是相对路径，它会被解释为相对于CMAKE_INSTALL_PREFIX的值的相对路径。

　　PERMISSIONS选项制定了安装文件需要的权限。合法的权限有：OWNER_READ，OWNER_WRITE，OWNER_EXECUTE，GROUP_READ，GROUP_WRITE，GROUP_EXECUTE，WORLD_READ，WORLD_WRITE，WORLD_EXECUTE，SETUID和SETGID。对于在某些特定的平台上没有意义的权限，在这些平台上会忽略这些选项。

　　CONFIGURATIONS选项指定了该安装规则将会加诸之上的一系列的构建配置（Debug，Release，等等）。

　　COMPONENT选项指定了该安装规则相关的一个安装部件的名字，比如“runtime”或“development”。对于那些指定安装部件的安装过程来说，在安装时只有与给定的部件名相关的安装规则会被执行。对于完整安装，所有部件都会被安装。

　　RENAME选项为一个可能不同于原始文件的已经安装的文件指定另一个名字。重命名只有在该命令正在安装一个单一文件时才被允许（猜测是为了防止文件名冲突时覆盖掉旧文件。——译注）。

　　OPTIONAL选项表示要安装的文件不存在不会导致错误。

　　TARGETS版本的install命令

  install(TARGETS targets... [EXPORT <export-name>]
          [[ARCHIVE|LIBRARY|RUNTIME|FRAMEWORK|BUNDLE|
            PRIVATE_HEADER|PUBLIC_HEADER|RESOURCE]
           [DESTINATION <dir>]
           [PERMISSIONS permissions...]
           [CONFIGURATIONS [Debug|Release|...]]
           [COMPONENT <component>]
           [OPTIONAL] [NAMELINK_ONLY|NAMELINK_SKIP]
          ] [...])
　　TARGETS格式的install命令规定了安装工程中的目标（targets）的规则。有5中可以被安装的目标文件：ARCHIVE，LIBRARY，RUNTIME，FRAMEWORK，和BUNDLE。除了被标记为MACOSX_BUNDLE属性的可执行文件被当做OS X上的BUNDLE目标外，其他的可执行文件都被当做RUNTIME目标。静态链接的库文件总是被当做ARCHIVE目标。模块库总是被当做LIBRARY目标。对于动态库不是DLL格式的平台来说，动态库会被当做LIBRARY目标来对待，被标记为FRAMEWORK的动态库是例外，它们被当做OS X上的FRAMEWORK目标。对于DLL平台而言，动态库的DLL部分被当做一个RUNTIME目标而对应的导出库被当做是一个ARCHIVE目标。所有基于Windows的系统，包括Cygwin，都是DLL平台。ARCHIVE，LIBRARY，RUNTIME和FRAMEWORK参数改变了后续属性会加诸之上的目标的类型。如果只给出了一种类型，那么只有那种类型的目标会被安装（这样通常只会安装一个DLL或者一个导出库。）

　　PRIVATE_HEADER，PUBLIC_HEADER，和RESOURCE选项的功能是，在非苹果平台上，将后续的属性应用在待安装的一个FRAMEWORK共享库目标的相关文件上。这些选项定义的规则在苹果系统上会被忽略掉，因为相关的文件将会被安装到framework文件夹内的合适位置。参见PRIVATE_HEADER，PUBLIC_HEADER和RESOURCE目标属性中更为详细的解释。

　　可以指定NAMELINK_ONLY或者NAMELINK_SKIP选项作为LIBRARY选项。在一些平台上，版本化的共享库有一个符号链接，比如lib<name>.so -> lib<name>.so.1，其中“lib<name>.so.1”是so库文件名（soname）而“lib<name>.so”是一个符号链接，当指定“-l<name>”选项时，链接器将会查找这个符号链接。如果一个库目标已经被安装，NAMELINK_ONLY选项表示仅仅安装符号链接；而NAME_SKIP选项则表示仅仅安装库文件而不是符号链接。当两种选项都没有给出时，动态库的两个部分都会被安装。在那些版本化的共享库没有符号链接或者库没有被版本化的平台，选项NAMELINK_SKIP安装这个库，而NAMELINK_ONLY选项什么都不会安装。参见VERSION和SOVERSION目标属性，获取关于创建版本化共享库的更多细节。

　　在该命令的TARGETS版本的一次调用中，可以一次性指定一个或多个属性组。一个目标也可以被多次安装到不同的位置。假设有三个目标myExe，mySharedLib和myStaticLib，下面的代码

    install(TARGETS myExe mySharedLib myStaticLib
            RUNTIME DESTINATION bin
            LIBRARY DESTINATION lib
            ARCHIVE DESTINATION lib/static)
    install(TARGETS mySharedLib DESTINATION /some/full/path)
将会把myExe安装到<prefix>/bin目录下，把myStaticLib安装到<prefix>/lib/static目录下。在非-DLL平台上，mySharedLib将会被安装到<prefix>/lib和/some/full/path下。在DLL平台上，mySharedLib DLL将会被安装到<prefix>/bin和/some/full/path路径下，它的导出库会被安装到<prefix>/lib/static和/some/full/path路径下。

　　EXPORT选项将已经安装的目标文件和一个名为<export-name>的导出文件关联起来。它必须出现在所有RUNTIME，LIBRARY或者ARCHIVE选项之前。为了实际安装导出文件本身（export file），调用install(EXPORT)。参见下述install命令EXPORT版本的文档获取更多的细节。

　　将EXCLUDE_FROM_ALL设置为true时，安装一个目标会造成未定义的行为。

　　FILES版本的install命令

  install(FILES files... DESTINATION <dir>
          [PERMISSIONS permissions...]
          [CONFIGURATIONS [Debug|Release|...]]
          [COMPONENT <component>]
          [RENAME <name>] [OPTIONAL])
　　FILES版本的install命令指定了为一个工程安装文件的规则。在命令中，以相对路径方式给出的文件名是相对于当前源代码路径而言的。以这个版本安装的文件，如果没有指定PERMISSIONS选项，默认会具有OWNER_WRITE，OWNER_READ，GROUP_READ，和WORLD_READ的权限。

　　PROGRAMS版本的install命令

  install(PROGRAMS files... DESTINATION <dir>
          [PERMISSIONS permissions...]
          [CONFIGURATIONS [Debug|Release|...]]
          [COMPONENT <component>]
          [RENAME <name>] [OPTIONAL])
　　PROGRAMS版本与FILES版本一样，只在默认权限上有所不同：它还包括了OWNER_EXECUTE，GROUP_EXECUTE和WORLD_EXECUTE选项。INSTALL的这个版本用来安装不是目标的程序，比如shell脚本。使用TARGETS格式安装该工程内部构建的目标。

　　DIRECTORY版本的install命令

  install(DIRECTORY dirs... DESTINATION <dir>
          [FILE_PERMISSIONS permissions...]
          [DIRECTORY_PERMISSIONS permissions...]
          [USE_SOURCE_PERMISSIONS] [OPTIONAL]
          [CONFIGURATIONS [Debug|Release|...]]
          [COMPONENT <component>] [FILES_MATCHING]
          [[PATTERN <pattern> | REGEX <regex>]
           [EXCLUDE] [PERMISSIONS permissions...]] [...])
　　INSTALL的DIRECTORY版本将一个或者多个路径下的内容安装到指定的目标地址下。目录结构会原封不动地（verbatim）拷贝到目标地址。每个路径名的最后一部分会追加到目标路径下，但是结尾反斜杠（trailing slash）可以用来避免这一点，因为这样最后一部分就是空的。给定的相对路径名被解释成相对于当前源路径的路径。如果没有指定输入目录名字，目标目录会被创建，但是不会安装任何东西。FILE_PERMISSIONS和DIRECTORY_PERMISSIONS选项指定了赋予目标路径和目标文件的权限。如果指定了USE_SOURCE_PERMISSIONS选项，但没有指定FILE_PERMISSIONS选项，文件权限将沿袭源目录结构的权限，而且这个路径会被赋予PAROGRAMS版本中指定的默认权限。

　　通过使用PATTERN或REGEX选项可以对路径安装做出细粒度的控制。这些用于匹配的选项指定了一个查询模式或正则表达式来匹配输入路径内的路径或文件。它们可以用来将特定的选项（见下文）加诸于遇到的文件和路径的一个子集上。每个输入文件或路径的完整路径（反斜杠/开头的路径）将用来匹配该表达式。PATTERN仅仅用来匹配完全文件名：匹配该模式的全路径的那部分必须出现在文件名的结尾，并且必须以一个反斜杠开始。

　　正则表达式会用来匹配一个完全路径的任何部分，但是它也可以使用'/'和'$'模仿PATTERN的行为。默认情况下，所有文件和路径不管是否匹配都会被安装。可以在第一个匹配选项之前指定FILE_MATCHING选项，这样就能禁止安装那些不与任何表达式匹配的文件。比如，代码

  install(DIRECTORY src/ DESTINATION include/myproj
          FILES_MATCHING PATTERN "*.h")
将会精确匹配并安装从源码树上得到的头文件。

　　有些选项后面可以跟在PATTERN或者REGEX表达式的后面，这样这些选项只能加诸于匹配PATTERN/REGEX的文件或路径上。EXCLUDE选项将会指示安装过程跳过那些匹配的文件或者路径。PERMISSIONS选项可以覆盖那些匹配PATTERN/REGEX的文件的权限设定。例如，代码

  install(DIRECTORY icons scripts/ DESTINATION share/myproj
          PATTERN "CVS" EXCLUDE
          PATTERN "scripts/*"
          PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ
                      GROUP_EXECUTE GROUP_READ)
会将icons路径安装到share/myproject/icons下，同时把scripts目录安装到share/myproj路径下。icons将具备默认的文件权限，scripts将会被给与指定的权限，但是所有CVS路径排除在外。

　　SCRIPT和CODE版本的install命令

  install([[SCRIPT <file>] [CODE <code>]] [...])
　　SCRIPT格式将会在安装期调用给定的脚本文件。如果脚本文件名是一个相对路径，它会被解释为相对于当前的源路径。CODE格式将会在安装期调用给定的CMake代码。code被指定为一个双引号括起来的单独的参数。例如，代码

  install(CODE "MESSAGE(\"Sample install message.\")")
会在安装时打印一条消息。

 

　　EXPORT版本的install命令

  install(EXPORT <export-name> DESTINATION <dir>
          [NAMESPACE <namespace>] [FILE <name>.cmake]
          [PERMISSIONS permissions...]
          [CONFIGURATIONS [Debug|Release|...]]
          [COMPONENT <component>])
　　EXPORT格式的install命令生成并安装一个包含将安装过程的安装树导入到另一个工程中的CMake文件。Target格式的安装过程与上文提及的使用EXPORT选项的install(TARGET ...)格式的命令中的EXPORT <export-name>选项是相关的。NAMESPACE选项会在它们被写入到导入文件时加到目标名字之前。缺省时，生成的文件就是<export-name>.cmake；但是FILE选项可以用来指定不同于次的文件名。FILE选项后面的参数必须是一“.cmake”为扩展名的文件。如果指定了CONFIGURATIONS选项，那么只有那些具名的配置中的一个被安装时，这个文件才会被安装。而且，生成的导入文件只能涉及到匹配的目标配置版本。如果指定了一个COMPONENT选项，并且<component>与那个<export-name>相关的目标指定的部件不匹配，那么行为是未定义的。如果一个库目标被包含在export之中，但是与之关联的库却没有背包含，那么结果是未指定的。

　　EXPORT格式可以协助外部工程使用当前工程构建出来并安装的目标。例如，代码

  install(TARGETS myexe EXPORT myproj DESTINATION bin)
  install(EXPORT myproj NAMESPACE mp_ DESTINATION lib/myproj)
将会把可执行文件myexe安装到<prefix>/bin下，并且将导入它的代码写到文件"<prefix>/lib/myproj/myproj.cmake"中。一个外部工程可以用include命令加载这个文件，并且可以在安装树上使用导入的目标名mp_myexe（前缀_目标名——译注）引用myexe可执行文件，如同这个目标是它自身的构建树的内置目标一样。

　　注意：这个命令会取代INSTALL_TARGETS命令以及PRE_INSTALL_SCRIPT和POST_INSTALL_SCRIPT两个目标属性。它也可以取代FILES格式的INSTALL_FILES命令和INSTALL_PROGRAMS命令。由INSTALL命令生成的安装规则相对于那些由INSTALL_TARGETS，INSTALL_FILES和INSTALL_PROGRAMS命令生成的安装规则处理顺序是未定义的。


