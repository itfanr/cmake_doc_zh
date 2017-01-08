load_cache
===

load_cache 从另一个工程的CMake cache中加载值。

  　　load_cache(pathToCacheFile READ_WITH_PREFIX
             prefix entry1...)
　　该命令读取指定的cache文件，并将以请求的前缀为其前缀的那些cache文件中的entry(ies)保存到变量中。这个格式仅仅读取值，但是不在本地工程的cache中创建entry(ies)。

  　　load_cache(pathToCacheFile [EXCLUDE entry1...]
             [INCLUDE_INTERNALS entry1...])
　　从另一个cache文件中加载值并像内部entry(ies)那样，将它们存储到本地工程的cache中。这条命令对于一个依赖于另一个不同构建树上的另一个工程的工程比较有用。EXCLUDE选项给出了那些需要排除在外的entry(ies)的一个list。INCLUDE_INTERNALS选项给出了需要包含的entry(ies)的内部entry(ies)的一个list。通常情况下，不需要引入内部entry(ies)。强烈不推荐使用该命令的这种格式，但是它可以被用来维持向后兼容性。

