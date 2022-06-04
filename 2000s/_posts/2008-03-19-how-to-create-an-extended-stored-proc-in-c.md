---
layout: post
title: how to create an extended stored proc in c#
---

quoted from http://www.dotnet247.com/247reference/msgs/7/38630.aspx

Ok, to get you started, herewith a simple sample, the C# file contains a single class and one method.
After compiling the file, you need to register the class using regasm.
Regasm requires the assembly to have a strong name, therefore you ned to run sn -k to create a assembly key file (here called
MyKey.snk).

Once the class registered, load the stored procedure in SQL queryanalyzer and run the Proc.

More info on COM interop is found in the MSDN docs.


C# file:

``` csharp
using System;
using System.Runtime.InteropServices;
using System.Reflection;
using System.Runtime.CompilerServices;

[assembly: AssemblyTitle("CSServer")]
[assembly: AssemblyDescription("Test SQL .NET interop")]
[assembly: AssemblyVersion("1.0.0.1")]
[assembly: AssemblyDelaySign(false)]
[assembly: AssemblyKeyFile("MyKey.snk")]
namespace SQLInterop {
    public interface ITest{
        string SayHello();
        }

        [ClassInterface(ClassInterfaceType.AutoDual)]
        public class CsharpHelper : ITest {
            public string SayHello() {
                return "Hello from CSharp";
        }
    }
}
```

``` shell
#Create a key pair file, compile and register
sn -k MyKey.snk
csc /t:library Csserver.cs
regasm /tlb:Csserver.tlb csserver.dll /codebase
```

T-SQL stored proc.
``` sql
DECLARE @object int
DECLARE @hr int
DECLARE @property varchar(255)
DECLARE @return varchar(255)
DECLARE @src varchar(255), @desc varchar(255)

-- Create an object.
EXEC @hr = sp_OACreate 'SQLInterop.CsharpHelper', @object OUT
IF @hr <> 0
BEGIN
EXEC sp_OAGetErrorInfo @object, @src OUT, @desc OUT
SELECT hr=convert(varbinary(4),@hr), Source=@src, Description=@desc
RETURN
END

-- Call a method that returns a value.
EXEC @hr = sp_OAMethod @object, 'SayHello', @return OUT
IF @hr <> 0
BEGIN
EXEC sp_OAGetErrorInfo @object, @src OUT, @desc OUT
SELECT hr=convert(varbinary(4),@hr), Source=@src, Description=@desc
RETURN
END
PRINT @return

-- Destroy the object.
EXEC @hr = sp_OADestroy @object
IF @hr <> 0
BEGIN
EXEC sp_OAGetErrorInfo @object, @src OUT, @desc OUT
SELECT hr=convert(varbinary(4),@hr), Source=@src, Description=@desc
RETURN
END
```