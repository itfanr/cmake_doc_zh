unset
===

unset  撤销对一个变量，cache变量或者环境变量的设置。

  unset(<variable> [CACHE])
　　删除一个指定的变量，让它变成未定义的。如果指定了CACHE选项，那么这个变量将会从cache中删除而不是当前作用域。<variable>可以是一个环境变量，比如：

  unset(ENV{LD_LIBRARY_PATH})
在这个例子中，这个变量将会从当前的环境中被删除。

