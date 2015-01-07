# cLua_wrapper
Simple Lua Wrapper v1 (SLW1). SLW1 is an "simple-to-use" library to wrap C/C++ and use it from lua scripts. 

cLua_wrapper 的工作结构
----------------------
在使用 cLua_wrapper 的时候要明白它的大致结构，cLua_wrapper 的结构非常简单，大致分为 3 个部分：

1. state 部分实现了 Lua 状态机基本的功能，即创建lua state、载入 Lua 脚本、判断 Lua 元素是否为空、堆栈元素数量、关闭 Lua 状态机、重启 Lua 状态机、获取当前使用状态机使用内存大小（Byte）与强制 GC 功能；
2. selector 部分负责任实现 C++ 与 Lua 的操作，这部分任务主要是通过操作 Lua 堆栈（stack）完成，调用与获取 Lua 中函数、元素与队列；
3. 使用了的lua绑定，绑定的方式是tolua++。

##环境要求

- gcc 4.7+
- Lua 5.1+
- C++11 compliant compiler

cLua_wrapper 的使用
-------------------


###创建 cLua_wrapper 对象

使用方式为：

    # 创建实例
	new clua::CState(true, tolua_interface_open);
	

    # 载入脚本
    STATE.load("example.lua");

    访问 example.lua 的内容即可以使用
    
### 访问 Lua 元素

	lua :
	-- test.lua
	foo = 4
	bar = {}
	bar[3] = "hi"
		bar["key"] = "there"

	c++ :
	STATE.Load("/path/to/test.lua");
	assert(STATE["foo"] == 4);
	assert(STATE["bar"][3] == "hi");
	assert(STATE["bar"]["key"] == "there");
    
### C++ 调用 Lua 函数    

	lua ：
	-- test.lua
	
	function foo()
	end

	function add(a, b)
	  return a + b
	end
	
	function sum_and_difference(a, b)
	  return (a+b), (a-b);
	end
	
	function bar()
	  return 4, true, "hi"
	end

	mytable = {}
	function mytable.foo()
	    return 4
	end
	
	c++ ：
	STATE state;
	state.Load("test.lua");

	// Call function with no arguments or returns
	STATE["foo"]();

	// Call function with two arguments that returns an int
	// The type parameter can be one of int, lua_Number, std::string,
	// bool, or unsigned int
	int result = STATE["add"](5, 2);
	assert(result == 7);


	// Call function that returns multiple values
	int sum, difference;
	sel::tie(sum, difference) = STATE["sum_and_difference"](3, 1);
	assert(sum == 4 && difference == 2);

	// Call function in table
	result = STATE["mytable"]["foo"]();
	assert(result == 4);







    
    