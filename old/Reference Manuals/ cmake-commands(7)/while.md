while
===

while  当条件为真时，评估（执行）一组命令。

  while(condition)
    COMMAND1(ARGS ...)
    COMMAND2(ARGS ...)
    ...
  endwhile(condition)
　　所有在while和与之配对的endwhile之间的命令将会被记录，但并不会执行。只有当endwhile被评估，并且条件为真时，这个命令列表的记录才会被调用。条件值的评估与if命令使用相同的逻辑。

