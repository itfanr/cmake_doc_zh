qt_wrap_ui
===

qt_wrap_ui 创建Qt的UI包裹器。

  qt_wrap_ui(resultingLibraryName HeadersDestName SourcesDestName SourceLists ...)
　　为所有在SourceLists中列出的.ui文件生成.h和.cxx文件。这些.h文件会被添加到使用HeadersDestNamesource列表的库中。这些.cxx文件会被添加到使用SourcesDestNamesource列表的库中。

