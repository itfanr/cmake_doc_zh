set_tests_properties
===

 set_tests_properties   设置若干个测试的属性值。

  set_tests_properties(test1 [test2...] PROPERTIES prop1 value1 prop2 value2)
　　为若干个测试设置一组属性。若属性未被发现，CMake将会报告一个错误。这组属性包括：WILL_FAIL， 如果设置它为true，那将会把这个测试的“通过测试/测试失败”标志反转。PASS_REGULAR_EXPRESSION，如果它被设置，这个测试的输出将会被检测是否违背指定的正则表达式，并且至少要有一个正则表达式要匹配；否则测试将会失败。

  例子: PASS_REGULAR_EXPRESSION "TestPassed;All ok"
　　FAIL_REGULAR_EXPRESSION: 如果该属性被设置，那么只要输出匹配给定的正则表达式中的一个，那么测试失败。

  例子: PASS_REGULAR_EXPRESSION "[^a-z]Error;ERROR;Failed"
　　PASS_REGULAR_EXPRESSION和FAIL_REGULAR_EXPRESSION属性都期望一个正则表达式列表（list）作为其参数。

　　TIMEOUT: 设置该属性将会限制测试的运行时长不超过指定的秒数。

