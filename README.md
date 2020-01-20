# 中台Unity-Protobuf 文档

## 1. C#部分
**C#部分使用[Google官方版本](https://developers.google.com/protocol-buffers/docs/csharptutorial)**  
proto生成C#文件可以使用Google提供的protoc命令。  
MacOS下通过`brew install protobuf`安装，或者在[这里](https://github.com/protocolbuffers/protobuf/releases/latest)下载对应平台的版本。  
批量生成C#文件可以使用`protoc PROTO_DIR/*.proto --proto_path=PROTO_DIR --csharp_out=OUT_DIR`  
代码中如下使用：
```
Req pb = new Req
{
	Login = new Login
	{
		Dev = new LoginDev
		{
			Name = "DonLiu"
		}
	}
};
```  
>*注：使用protoc --chsarp_out生成的C#文件中，Proto字段名会自动改变大小写，Repeated的字段可能会自动在末尾加s*  
  
上发服务器之前使用`pb.ToByteArray()`转换成bytes类型。  
服务器下发的数据拿到bytes后使用`Rsp rsp = Rsp.Parser.ParseFrom(bytes);`解析。  

*目前客户端使用UnityWebRequest向服务器发送请求，如果项目需要其他网络模块可以自行添加。*  



## 2. Lua部分
**Lua部分目前使用[starwing/lua-protobuf](https://github.com/starwing/lua-protobuf)，[中文文档](https://zhuanlan.zhihu.com/p/26014103)**  
>*关于在xLua中集成lua-protobuf第三方库，参考[这里](https://blog.csdn.net/iningwei/article/details/86631992).*  

### **加载proto两种方法**
#### 1.使用Google Protobuf自带的protoc命令生成.pb文件(推荐)：
生成单个.proto对应的.pb`protoc -o Req.pb Req.proto`  
从多个.proto中整合生成一个.pb`protoc -o lua.pb *.proto`  
>*注：如果多个.proto中有相互import的关系，则第二个命令只能在.proto所在文件夹中执行*  

代码中读取proto配置：
```  
local pb = require "pb"
assert(pb.loadfile("lua.pb"))
```  

#### 2.使用[starwing/lua-protobuf](https://github.com/starwing/lua-protobuf)项目中的[protoc.lua](https://github.com/starwing/lua-protobuf/blob/master/protoc.lua)在运行时加载.proto：  
```
local protoc = require "protoc"
local protoFileString = ...
protoc:load(protoFileString)
```

### 序列化数据上发服务器
在代码中首先`local pb = require "pb"`  

使用pb序列化上发数据：
```
local t = {
    login = {
        dev = {
            name = "DonLiu"
        }
    }
}
local data = pb.encode("pb.Req", t)
```  
使用pb反序列化返回数据：  
```
local table = pb.decode("pb.Rsp", data)
```


