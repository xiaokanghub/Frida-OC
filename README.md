# Frida
## methodofclass
```javascript
console.log("[*] Started: Find All Methods of a Specific Class");
if (ObjC.available)
{
    try
    {
        var className = "xxx";
        var methods = eval('ObjC.classes.' + className + '.$methods');
        for (var i = 0; i < methods.length; i++)
        {
            try
            {
                console.log("[-] "+methods[i]);
            }
            catch(err)
            {
                console.log("[!] Exception1: " + err.message);
            }
        }
    }
    catch(err)
    {
        console.log("[!] Exception2: " + err.message);
    }
}
else
{
    console.log("Objective-C Runtime is not available!");
}
console.log("[*] Completed: Find All Methods of a Specific Class");
```

## record
```javascript
if (ObjC.available)
{
    try
    {
        var className = "xxx";
        var funcName = "- xxx";
        var hook = eval('ObjC.classes.' + className + '["' + funcName + '"]');
        console.log("[*] Class Name: " + className);
        console.log("[*] Method Name: " + funcName);
        Interceptor.attach(hook.implementation, {
          onEnter: function(args) {
            //注意:有时args参数值在这里会是一个对象,如果函数返回值是字符串类型,为了更好理解则要这样写
            //这里假设args[2]是要记录的参数
            //ObjC.classes.NSString.stringWithString_(args[2])或者args[2].toString()或者ObjC.classes.NSString.stringWithString_(args[2]).toString()
            //具体情况需要测试下是上面这3种的哪种写法
            console.log("param:"+args[2]+" type:"+typeof args[2]);
          },
          onLeave: function(retval) {
            //注意:retval一般会返回一个对象,如果函数返回值是字符串类型,为了更好理解则要这样写
            //ObjC.classes.NSString.stringWithString_(retval)或者retval.toString()或者ObjC.classes.NSString.stringWithString_(retval).toString()
            //具体情况需要测试下是上面这3种的哪种写法
            console.log("Return value-> (type:"+typeof retval+",value:"+retval+")");
          }
        });
    }
    catch(err)
    {
        console.log("[!] Exception2: " + err.message);
    }
}
else
{
    console.log("Objective-C Runtime is not available!");
}
```

## overwrite
```javascript
if (ObjC.available)
{
    try
    {
        var className = "PARSPedometerInfo";
        var funcName = "- integratedSteps";
        var hook = eval('ObjC.classes.' + className + '["' + funcName + '"]');
        Interceptor.attach(hook.implementation, {
          onEnter: function(args) {
            console.log("Original args0-> (type:"+typeof args[0]+",value:"+args[0]+")");
            newargs0=ptr('xxx')
            args[0]=newargs0
            console.log("New args0-> (type:"+typeof args[0]+",value:"+args[0]+")");
          },
          onLeave: function(retval) {
            //注意:retval永远是一个对象,如果函数返回值是字符串类型,为了更好理解则要这样写
            //string_value=ObjC.classes.NSString.stringWithString_(retval)
            //console.log("Original return value-> (type:"+typeof string_value+",value:"+string_value+")");
            //newretval=ObjC.classes.NSString.stringWithString_("xxxx")
            //retval.replace(newretval)
            //console.log("New return value-> (type:"+typeof newretval+",value:"+newretval+")");

            console.log("Origin return value-> (type:"+typeof retval",value:"+retval+")");
            newretval=ptr("xxxx")
            retval.replace(newretval)
            console.log("New return value-> (type:"+typeof newretval",value:"+newretval+")");
          }
        });
    }
    catch(err)
    {
        console.log("[!] Exception2: " + err.message);
    }
}
else
{
    console.log("Objective-C Runtime is not available!");
}
```

## HOOK Function
```javascript
if (ObjC.available)
{
    try
    {
        var className = "PARSPedometerInfo";
        var funcName = "- integratedSteps";
        var hook = eval('ObjC.classes.' + className + '["' + funcName + '"]');
        Interceptor.attach(hook.implementation, {
          onEnter: function(args) {
            console.log("Original args0-> type:"+typeof args[0]+" value:"+args[0])

            newargs0=ptr('xxx')
            args[0]=newargs0
            console.log("New args0-> type:"+typeof args[0]+" value:"+args[0]")
            send(args[0]);
          },
          onLeave: function(retval) {
            console.log("Original retval-> type:"+typeof args[0]+" value:"+args[0])

            newretval=ptr("xxxx")
            retval.replace(newretval)
            console.log("New retval-> type:"+typeof args[0]+" value:"+newretval)
            send(newretval)
          }
        });
    }
    catch(err)
    {
        console.log("[!] Exception2: " + err.message);
    }
}
else
{
    console.log("Objective-C Runtime is not available!");
}
```

## 记录函数执行日志
```javascript
if (ObjC.available)
{
    try
    {
        var className = "xxx";
        var funcName = "- xxx";
        var hook = eval('ObjC.classes.' + className + '["' + funcName + '"]');
        console.log("[*] Class Name: " + className);
        console.log("[*] Method Name: " + funcName);
        Interceptor.attach(hook.implementation, {
          onEnter: function(args) {
            console.log("param:"+args[0]+" type:"+typeof args[0]);
          },
          onLeave: function(retval) {
            console.log("retval:"+retval+" type:"+typeof retval);
          }
        });
    }
    catch(err)
    {
        console.log("[!] Exception2: " + err.message);
    }
}
else
{
    console.log("Objective-C Runtime is not available!");
}
```

## 调用函数
```javascript
id __cdecl +[NSString stringWithStrings:](NSString_meta *self, SEL a2, id a3)
{
  __int64 v3; // x20
  void *v4; // x0
  void *v5; // x19
    ...
}
```
```javascript
[NSString stringWithString:@"Hello World"] 
becomes 
var NSString = ObjC.classes.NSString; NSString.stringWithString_("Hello World");

if (ObjC.available)
{
    try
    {
        //var my_obj=ObjC.chooseSync(ObjC.classes.PARSHealthPedometer10thHomeViewController)[0]
        var my_obj=ObjC.classes.PARSHealthPedometer10thHomeViewController.alloc()
        my_obj["- requestUploadWithSure:"](1)
    }
    catch(err)
    {
        console.log("[!] Exception2: " + err.message);
    }
}
else
{
    console.log("Objective-C Runtime is not available!");
}
```
## Print NSString  
```javascript
Interceptor.attach(ObjC.classes.NSString['+ stringWithUTF8String:'].implementation, {
    onEnter: function (args) {
      console.log('[+] Hooked +[NSString stringWithUTF8String:] ');
    },
    onLeave: function (retval) {
      var str = new ObjC.Object(ptr(retval)).toString()
      console.log('[+] Returning [NSString stringWithUTF8String:] -> ', str);
      return retval;
    }
});

Interceptor.attach(ObjC.classes.__NSCFString['- isEqualToString:'].implementation, {
    onEnter: function (args) {
      var str = new ObjC.Object(ptr(args[2])).toString()
      console.log('[+] Hooked __NSCFString[- isEqualToString:] ->' , str);
    }
});

Interceptor.attach(ObjC.classes.NSTaggedPointerString['- isEqualToString:'].implementation, {
    onEnter: function (args) {
      var str = new ObjC.Object(ptr(args[2])).toString()
      console.log('[+] Hooked NSTaggedPointerString[- isEqualToString:] ->' , str);
    }
});
```
## 内存断点
```javascript
Process.setExceptionHandler(function(exp) {
  console.warn(JSON.stringify(Object.assign(exp, { _lr: DebugSymbol.fromAddress(exp.context.lr), _pc: DebugSymbol.fromAddress(exp.context.pc) }), null, 2));
  Memory.protect(exp.memory.address, Process.pointerSize, 'rw-');
  // can also use `new NativeFunction(Module.findExportByName(null, 'mprotect'), 'int', ['pointer', 'uint', 'int'])(parseInt(this.context.x2), 2, 0)`
  return true; // goto PC 
});

Interceptor.attach(funcPtr, {
  onEnter: function (args) {
    console.log('onEnter', JSON.stringify({
      x2: this.context.x2,
      mprotect_ret: Memory.protect(this.context.x2, 2, '---'),
      errno: this.errno
    }, null, 2));
  },
  onLeave: function (retval) {
    console.log('onLeave');
  }
});
```

