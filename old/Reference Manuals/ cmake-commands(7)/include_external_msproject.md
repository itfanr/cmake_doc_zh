include_external_msproject
===

include_external_msproject 在一个workspace中包含一个外部的Microsoft工程。　　　　　　

　　include_external_msproject(projectname location dep1 dep2 ...)

　　在生成的workspace文件中包含一个外部的Microsoft工程。它会创建一个名为[projectname]的目标。这个目标可以用在add_dependencies命令中让其他工程依赖于这个外部工程。当前版本下，该命令在UNIX平台上不会做任何事情。


