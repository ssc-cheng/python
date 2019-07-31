# 异常处理

## 异常

> 有时候代码写错了，执行程序的时候，执行到错误代码的时候，程序直接终止报错，这是因为Python检测到一个错误时，解释器就无法继续执行了，出现了错误的提示，这就是"异常"。

```python
l = [0,1,2]
l[3]            #下标超限

IndexError: list index out of range

int(input(""请输入数字))       #整型转换错误

ValueError: invalid literal for int() with base 10: 'ds'

```



## 捕获异常 try...except...

> 将可能出错的代码放到try里面，except可以指定类型捕获异常。except里面的代码是捕获到异常时执行,将错误捕获，这样程序就不会因为一段代码包异常而导致整个程序崩溃。

```python
#捕获一个异常
try:
    num = int(input("请输入一个数字："))
    num = [][1]                         #捕获valuseerror 就不能捕获indexerror
except ValueError as e:   #e为存储异常的别名
    print("错误信息为：",e)

#捕获多个个异常
try:
    # num = int(input("请输入一个数字："))
    num = [][1]
except (ValueError,IndexError) as e:        #捕获多个错误
    print("错误信息为：",e)
    
#捕获所有异常
try:
    # num = int(input("请输入一个数字："))
    num = [][1]
except Exception as e:        # Exception 可以捕获任何类型的异常
    print("错误信息为：",e)
```

- 把可能出现问题的代码，放在try中
- 把处理异常的代码，放在except中

## 异常常用语句

```
try:
    正常的操作
except:
    发生异常，执行这块代码
```

```
try:
    正常的操作
except:
    pass  #可以不处理
else:
    print('没有捕获到异常')  #如果没有异常执行这块代码,否则不执行
```

```python
try: 
    print('--------test----------') # try里面有异常 
    1/0 
except Exception as e: # Exception 可以捕获任何类型的异常 
    print(e) 
finally: 
	print('不管有没有捕获到异常，finally都是执行的') 
```

```python
try: 
    print('--------test----------') # try里面有异常 
    1/0 
except Exception as e: # Exception 可以捕获任何类型的异常 
    print(e) 
else: 
	print('没有捕获到异常') # 捕获到异常，执行else 的代码 

finally: 
	print('不管有没有捕获到异常，finally都是执行的') 

```



## 异常嵌套

```python
#异常嵌套

try:
    d = {1:2}
    # d.pop(2)         #try  语句出现异常，后面的代码都不执行
    try:
        print([][1])      #这里是indexError  并没有捕获这个错误  仍会报错
        int("a")
    except ValueError as e:
        print("valueError",e)
except (KeyError,IndexError) as e:    #里面的try没有捕获到这个异常，那么外面的try(有指定的话)会接收到这个异常
    print("Error",e)
   
```

```python
def a(): 
    print('执行a函数’) 
    1/0 				# 制造一个异常 
    print('a函数执行完成’) 
def b(): 
    print('执行b函数’) 
    a() 			# 调用a函数 
    print('b函数执行完成’) 
def c(): 
    print('执行c函数’) 
    try: 
    	b() 		# 调用b函数 
    except Exception as e: 
    	print(e) 
    print('c函数执行完成’) 

c() 

```



总结：

- 如果try嵌套，那么如果里面的try没有捕获到这个异常，那么外面的try会接收到这个异常，然后进行处理，如果外边的try依然没有捕获到，那么再进行传递。。。

- 如果多个函数嵌套调用，内层函数异常，异常会往外部传递，直到异常被抛出，或被处理。





## 自定义异常

> 自定义异常，都要直接或间接继承Error或Exception类。
> 由开发者主动抛出主动抛出自定义异常，在python中使用raise关键字，

```python
# 自定义一个异常类 
class LeNumExcept(Exception): # 自定义异常类需要继承Exception 
	def __str__(self): 
		return '[error:]你输入的数字小于0，请出入大于0的数字’
 

try: 
	num = int(input('请出入一个数字：’)) 
    if num < 0: 
    # raise 关键字抛出异常 
    raise LeNumExcept() 自定义异常

except LeNumExcept as e: 
    print(e) # 捕获异常 

else: 
    print('没有异常') 

```



## 异常处理中抛出异常

> 在开发中有，有时候会有这样一种功能，由调用者决定是否需要做异常处理，如不处理，可继续抛出。

```python
class Test(object):
    def __init__(self, switch):
        self.switch = switch #开关
    def calc(self, a, b):
        try:
            return a/b
        except Exception as result:
            if self.switch:
                print("捕获开启，已经捕获到了异常，信息如下:")
                print(result)
            else:
                #重新抛出这个异常，此时就不会被这个异常处理给捕获到，从而触发默认的异常处理
                raise


a = Test(True)
a.calc(11,0)

print("-"*100)

a.switch = False
a.calc(11,0)
```

