# 添加lua_module
1. 先安装luajit    

1.1 通过yum安装
```
# 安装
sudo yum install -y luajit-devel
# 添加环境变量
export LUAJIT_LIB=/usr/lib64
export LUAJIT_INC=/usr/include/luajit-2.0
```

1.2 通过编译安装
```
# 下载
wget http://luajit.org/download/LuaJIT-2.0.5.tar.gz
tar zxf LuaJIT-2.0.5.tar.gz
cd LuaJIT-2.0.5
# 编译
make PREFIX=/usr/local/luajit
make install PREFIX=/usr/local/luajit
# 添加环境变量
export LUAJIT_LIB=/usr/local/luajit/lib
export LUAJIT_INC=/usr/local/luajit/include/luajit-2.0
```

2. 使用dso_tool编译模块
```
./sbin/dso_tool --add-module=/path/tengine/modules/ngx_http_lua_module
```
3. 配置文件加载模块
```
dso {
    load ngx_http_lua_module.so;
}
```
4. 测试
```
# 先添加一个lua脚本
vim lua/hello.lua
ngx.say("Hello World")

# 再配置
    server {
        listen       80;
        server_name  localhost;

        location /lua {
            access_by_lua_file '/usr/local/nginx/lua/hello.lua';
        }
    }
    
# 用curl请求
curl 'http://localhost/lua'
Hello World
```

# 其它
在 http 块中添加以下内容：
```
lua_package_path "/path/to/lua/scripts/?.lua;;";
lua_shared_dict my_cache 10m;
lua_code_cache on;
```
* 告诉 ngx_http_lua_module 模块在 /path/to/lua/scripts 目录中查找 Lua 脚本。
* 启用 ngx_http_lua_module 模块的 Lua 脚本缓存功能，并使用名为 my_cache 的共享内存字典来存储缓存数据。
* 如果 lua_code_cache 设置为 off，每次请求都会重新编译 Lua 脚本。如果设置为 on，则会将编译后的代码缓存到内存中，以提高性能。
