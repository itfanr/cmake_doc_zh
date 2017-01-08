list
===

list 列表操作命令。

  　　list(LENGTH <list> <output variable>)
  　　list(GET <list> <element index> [<element index> ...] <output variable>)
  　　list(APPEND <list> <element> [<element> ...])
  　　list(FIND <list> <value> <output variable>)
  　　list(INSERT <list> <element_index> <element> [<element> ...])
  　　list(REMOVE_ITEM <list> <value> [<value> ...])
  　　list(REMOVE_AT <list> <index> [<index> ...])
  　　list(REMOVE_DUPLICATES <list>)
  　　list(REVERSE <list>)
  　　list(SORT <list>)
　　使用LENGTH选项时，该命令会返回给定list的长度。

　　使用GET选项时，该命令返回list中所有被index索引的元素构成的list。

　　使用APPEND选项时，该命令将会在该list之后追加若干元素。

　　使用FIND选项时，该命令将返回list中指定的元素的索引；若果未找到，返回-1。

　　使用INSERT选项时，该命令将在list中指定的位置插入若干元素。

　　使用REMOVE_AT和REMOVE_ITEM选项将会从list中删除一些元素。它们之间的区别是：REMOVE_ITEM删除的是指定的项，而REMOVE_AT删除的是在指定索引处的项。

　　使用REMOVE_DUPLICATES选项时，该命令将删除list中的重复项。

　　使用REVERSE选项时，该命令将把list的内容就地前后倒换。

　　使用SORT选项时，该命令将按字母序对list总的内容就地排序。

　　注意：在CMake中，一个list是一个由封号;分割的一组字符串。使用set命令可以创建一个list。例如，set(var a b c d e)命令将会创建一个list：a;b;c;d;e；而set(var "a b c d e")命令创建的只是一个字符串，或者说是只有一个项的list。

　　当使用指定索引的命令格式时，如果<element index>是大于等于0的数，<element index>是从list第一个项开始的序号，list的第一项的索引是0。如果<element index>小于等于-1，这个索引是从结尾开始的逆向索引，其中-1表示的是list的最后一项。当使用负数索引时，注意它们不是从0开始！-0与0等价，它指向list的第一个成员。


