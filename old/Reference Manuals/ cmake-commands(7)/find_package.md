find_package
===

find_package 为外部工程加载设置。

  find_package(<package> [version] [EXACT] [QUIET]
               [[REQUIRED|COMPONENTS] [components...]]
               [NO_POLICY_SCOPE])
　　查找并加载外来工程的设置。该命令会设置<package>_FOUND变量，用来指示要找的包是否被找到了。如果这个包被找到了，与它相关的信息可以通过包自身记载的变量中得到。QUIET选项将会禁掉包没有被发现时的警告信息。REQUIRED选项表示如果报没有找到的话，cmake的过程会终止，并输出警告信息。在REQUIRED选项之后，或者如果没有指定REQUIRED选项但是指定了COMPONENTS选项，在它们的后面可以列出一些与包相关的部件清单（components list）。[version]参数需要一个版本号，它是正在查找的包应该兼容的版本号（格式是major[.minor[.patch[.tweak]]]）。EXACT选项要求该版本号必须精确匹配。如果在find-module内部对该命令的递归调用没有给定[version]参数，那么[version]和EXACT选项会自动地从外部调用前向继承。对版本的支持目前只存在于包和包之间（详见下文）。

　　用户代码总体上应该使用上述的简单调用格式查询需要的包。本命令文档的剩余部分则详述了find_package的完整命令格式以及具体的查询过程。期望通过该命令查找并提供包的项目维护人员，我们鼓励你能继续读下去。

　　该命令在搜索包时有两种模式：“模块”模式和“配置”模式。当该命令是通过上述的精简格式调用的时候，合用的就是模块模式。在该模式下，CMake搜索所有名为Find<package>.cmake的文件，这些文件的路径由变量由安装CMake时指定的CMAKE_MODULE_PATH变量指定。如果查找到了该文件，它会被CMake读取并被处理。该模式对查找包，检查版本以及生成任何别的必须信息负责。许多查找模块（find-module）仅仅提供了有限的，甚至根本就没有对版本化的支持；具体信息查看该模块的文档。如果没有找到任何模块，该命令会进入配置模式继续执行。

 　　完整的配置模式下的命令格式是：

  find_package(<package> [version] [EXACT] [QUIET]
               [[REQUIRED|COMPONENTS] [components...]] [NO_MODULE]
               [NO_POLICY_SCOPE]
               [NAMES name1 [name2 ...]]
               [CONFIGS config1 [config2 ...]]
               [HINTS path1 [path2 ... ]]
               [PATHS path1 [path2 ... ]]
               [PATH_SUFFIXES suffix1 [suffix2 ...]]
               [NO_DEFAULT_PATH]
               [NO_CMAKE_ENVIRONMENT_PATH]
               [NO_CMAKE_PATH]
               [NO_SYSTEM_ENVIRONMENT_PATH]
               [NO_CMAKE_PACKAGE_REGISTRY]
               [NO_CMAKE_BUILDS_PATH]
               [NO_CMAKE_SYSTEM_PATH]
               [CMAKE_FIND_ROOT_PATH_BOTH |
                ONLY_CMAKE_FIND_ROOT_PATH |
                NO_CMAKE_FIND_ROOT_PATH])
　　NO_MODULE可以用来明确地跳过模块模式。它也隐含指定了不使用在精简格式中使用的那些选项。

　　配置模式试图查找一个由待查找的包提供的配置文件的位置。包含该文件的路径会被存储在一个名为<package>_DIR的cache条目里。默认情况下，该命令搜索名为<package>的包。如果指定了NAMES选项，那么其后的names参数会取代<package>的角色。该命令会为每个在names中的name搜索名为<name>Config.cmake或者<name全小写>-config.cmake的文件。通过使用CONFIGS选项可以改变可能的配置文件的名字。以下描述搜索的过程。如果找到了配置文件，它将会被CMake读取并处理。由于该文件是由包自身提供的，它已经知道包中内容的位置。配置文件的完整地址存储在cmake的变量<package>_CONFIG中。

　　所有CMake要处理的配置文件将会搜索该包的安装信息，并且将该安装匹配的适当版本号（appropriate version）存储在cmake变量<package>_CONSIDERED_CONFIGS中，与之相关的版本号（associated version）将被存储在<package>_CONSIDERED_VERSIONS中。

　　如果没有找到包配置文件，CMake将会生成一个错误描述文件，用来描述该问题——除非指定了QUIET选项。如果指定了REQUIRED选项，并且没有找到该包，将会报致命错误，然后配置步骤终止执行。如果设置了<package>_DIR变量被设置了，但是它没有包含配置文件信息，那么CMake将会直接无视它，然后重新开始查找。

　　如果给定了[version]参数，那么配置模式仅仅会查找那些在命令中请求的版本（格式是major[.minor[.patch[.tweak]]]）与包请求的版本互相兼容的那些版本的包。如果指定了EXACT选项，一个包只有在它请求的版本与[version]提供的版本精确匹配时才能被找到。CMake不会对版本数的含义做任何的转换。包版本号由包自带的版本文件来检查。对于一个备选的包配置文件<config-file>.cmake，对应的版本文件的位置紧挨着它，并且名字或者是<config-file>-version.cmake或者是<config-file>Version.cmake。如果没有这个版本文件，那么配置文件就会认为不兼容任何请求的版本。当找到一个版本文件之后，它会被加载然后用来检查（find_package）请求的版本号。版本文件在一个下述变量被定义的嵌套域中被加载：

   PACKAGE_FIND_NAME          = <package>名字。
   PACKAGE_FIND_VERSION       = 请求的完整版本字符串
   PACKAGE_FIND_VERSION_MAJOR = 如果被请求了，那么它是major版本号，否则是0。
   PACKAGE_FIND_VERSION_MINOR = 如果被请求了，那么它是minor版本号，否则是0。
   PACKAGE_FIND_VERSION_PATCH = 如果被请求了，那么它是patch版本号，否则是0。
   PACKAGE_FIND_VERSION_TWEAK = 如果被请求了，那么它是tweak版本号，否则是0。
   PACKAGE_FIND_VERSION_COUNT = 版本号包含几部分，0到4。
　　版本文件会检查自身是否满足请求的版本号，然后设置了下面这些变量：

  PACKAGE_VERSION            = 提供的完整的版本字符串。
  PACKAGE_VERSION_EXACT      = 如果版本号精确匹配，返回true。
  PACKAGE_VERSION_COMPATIBLE = 如果版本号相兼容，返回true。
  PACKAGE_VERSION_UNSUITABLE = 如果不适合任何版本，返回true。
　　下面这些变量将会被find_package命令检查，用以确定配置文件是否提供了可接受的版本。在find_package命令返回后，这些变量就不可用了。如果版本可接受，下述的变量会被设置：

  <package>_VERSION       = 提供的完整的版本字符串。
  <package>_VERSION_MAJOR = 如果被请求了，那么它是major版本号，否则是0。
  <package>_VERSION_MINOR = 如果被请求了，那么它是minor版本号，否则是0。
  <package>_VERSION_PATCH = 如果被请求了，那么它是patch版本号，否则是0。
  <package>_VERSION_TWEAK = 如果被请求了，那么它是tweak版本号，否则是0。
  <package>_VERSION_COUNT = 版本号包含几部分，0到4。
然后，对应的包配置文件才会被加载。当多个包配置文件都可用时，并且这些包的版本文件都与请求的版本兼容，选择哪个包将会是不确定的。不应该假设cmake会选择最高版本或者是最低版本。（以上的若干段是对find_package中版本匹配步骤的描述，并不需要用户干预——译注。）

　　配置模式提供了一种高级接口和搜索步骤的接口。这些被提供的接口的大部分是为了完整性的要求，以及在模块模式下，包被find-module加载时供内部使用。大多数用户仅仅应该调用：

  find_package(<package> [major[.minor]] [EXACT] [REQUIRED|QUIET])
来查找包。鼓励那些需要提供CMake包配置文件的包维护人员应该命名这些文件并安装它们，这样下述的整个过程将会找到它们而不需要使用附加的选项。

　　CMake为包构造了一组可能的安装前缀。在每个前缀下，若干个目录会被搜索，用来查找配置文件。下述的表格展示了待搜索的路径。每个条目都是专门为Windows(W)，UNIX(U)或者Apple(A)约定的安装树指定的。

    <prefix>/                                               (W)
   <prefix>/(cmake|CMake)/                                 (W)
   <prefix>/<name>*/                                       (W)
   <prefix>/<name>*/(cmake|CMake)/                         (W)
   <prefix>/(share|lib)/cmake/<name>*/                     (U)
   <prefix>/(share|lib)/<name>*/                           (U)
   <prefix>/(share|lib)/<name>*/(cmake|CMake)/             (U)
　　在支持OS X平台和Application Bundles的系统上，包含配置文件的框架或者bundles会在下述的路径中被搜索：

    <prefix>/<name>.framework/Resources/                    (A)
   <prefix>/<name>.framework/Resources/CMake/              (A)
   <prefix>/<name>.framework/Versions/*/Resources/         (A)
   <prefix>/<name>.framework/Versions/*/Resources/CMake/   (A)
   <prefix>/<name>.app/Contents/Resources/                 (A)
   <prefix>/<name>.app/Contents/Resources/CMake/           (A)
　　在所有上述情况下，<name>是区分大小写的，并且对应于在<package>或者由NAMES给定的任何一个名字。

　　这些路径集用来与那些在各自的安装树上提供了配置文件的工程协作。上述路径中被标记为(W)的是专门为Windows上的安装设置的，其中的<prefix>部分可能是一个应用程序的顶层安装路径。那些被标记为(U)的是专门为UNIX平台上的安装设置的，其中的<prefix>被多个包共用。这仅仅是个约定，因此，所有(W)和(U)路径在所有平台上都仍然会被搜索。那些被标记为(A)的路径是专门为Apple平台上的安装设置的。CMake变量CMAKE_FIND_FRAMEWORK和CMAKE_FIND_APPBUNDLE确定了偏好的顺序，如下所示：

　　安装前缀是通过以下步骤被构建出来的。如果指定了NO_DEFAULT_PATH选项，所有NO_*选项都会被激活。

　　1、搜索在cmake特有的cache变量中指定的搜索路径。这些变量是为了在命令行中用-DVAR=value选项指定而设计的。通过指定NO_CMAKE_PATH选项可以跳过该搜索路径。搜索路径还包括：

    CMAKE_PREFIX_PATH
   CMAKE_FRAMEWORK_PATH
   CMAKE_APPBUNDLE_PATH
　　2、搜索cmake特有的环境变量。这些变量是为了在用户的shell配置中进行配置而设计的。通过指定NO_CMAKE_ENVIRONMENT_PATH选项可以跳过该路径。搜索的路径包括：

   <package>_DIR
   CMAKE_PREFIX_PATH
   CMAKE_FRAMEWORK_PATH
   CMAKE_APPBUNDLE_PATH
　　3、搜索HINTS选项指定的路径。这些路径应该是由操作系统内省时计算产生的，比如由其它已经找到的项的位置而提供的线索。硬编码的参考路径应该在PATHS选项中指定。

　　4、搜索标准的系统环境变量。如果指定了NO_SYSTEM_ENVIRONMENT_PATH选项，这些路径会被跳过。以"/bin"或"/sbin"结尾的路径条目会被自动转换为它们的父路径。搜索的路径包括：

   PATH
　　5、搜索在CMake GUI中最新配置过的工程的构建树。可以通过设置NO_CMAKE_BUILDS_PATH选项来跳过这些路径。这是为了在用户正在依次构建多个相互依赖的工程时而准备的。

　　6、搜索存储在CMake用户包注册表中的路径。通过设置NO_CMAKE_PACKAGE_REGISTRY选项可以跳过这些路径。当CMake嗲用export(PACKAGE<name>)配置一个工程时，这些路径会被存储在注册表中。参见export(PACKAGE)命令的文档阅读更多细节。

　　7、搜索在当前系统的平台文件中定义的cmake变量。可以用NO_CMAKE_SYSTEM_PATH选项跳过这些路径。

   CMAKE_SYSTEM_PREFIX_PATH

   CMAKE_SYSTEM_FRAMEWORK_PATH

   CMAKE_SYSTEM_APPBUNDLE_PATH
　　8、搜索由PATHS选项指定的路径。这些路径一般是硬编码的参考路径。

　　在Darwin或者支持OS X 框架的系统上，cmake变量CMAKE_FIND_FRAMEWORK可以用来设置为空，或者下述值之一：
   　　"FIRST"  - 在标准库或头文件之前查找框架。在Darwin系统上这是默认选项。
   　　"LAST"   - 在标准库或头文件之后查找框架。
   　　"ONLY"   - 仅仅查找框架。
   　　"NEVER" - 从不查找框架。
　　在Darwin或者支持OS X Application Bundles的系统，cmake变量CMAKE_FIND_APPBUNDLE可以被设置为空或者下面这些值中的一个：

    　 "FIRST"  - 在标准库或头文件之前查找application bundles。在Darwin系统上这是默认选项。
   　　"LAST"   - 在标准库或头文件之后查找application bundles。
   　　"ONLY"   - 仅仅查找application bundles。
   　　"NEVER" - 从不查找application bundles。
　　CMake变量CMAKE_FIND_ROOT_PATH指定了一个或者多个优先于其他搜索路径的搜索路径。该变量能够有效地重新定位在给定位置下进行搜索的根路径。该变量默认为空。当使用交叉编译时，该变量十分有用：用该变量指向目标环境的根目录，然后CMake将会在那里查找。默认情况下，在CMAKE_FIND_ROOT_PATH中列出的路径会首先被搜索，然后是“非根”路径。该默认规则可以通过设置CMAKE_FIND_ROOT_PATH_MODE_LIBRARY做出调整。在每次调用该命令之前，都可以通过设置这个变量来手动覆盖默认行为。如果使用了NO_CMAKE_FIND_ROOT_PATH变量，那么只有重定位的路径会被搜索。

　　默认的搜索顺序的设计逻辑是按照使用时从最具体到最不具体。通过多次调用find_library命令以及NO_*选项，可以覆盖工程的这个默认顺序：

     find_library(<VAR> NAMES name PATHS paths... NO_DEFAULT_PATH)
    find_library(<VAR> NAMES name)
　　只要这些调用中的一个成功返回，结果变量就会被设置并且被存储到cache中；这样随后的调用都不会再行搜索。如果那找到的库是一个框架，VAR将会被设置为指向框架“<完整路径>/A.framework” 的完整路径。当一个指向框架的完整路径被用作一个库文件，CMake将使用-framework A，以及-F<完整路径>这两个选项将框架连接到目标上。

　　参见cmake_policy()命令的文档中关于NO_POLICY_SCOPE选项讨论。

