# Lua Shared Library Patch

This repo is a collection of lua patches to build shared library `liblua.so` with `make`.



## Usage

Download the patch file with corresponding version with lua.

Use version 5.3.5 as an example.

```bash
curl -R -O http://www.lua.org/ftp/lua-5.3.5.tar.gz
tar zxf lua-5.3.5.tar.gz
cd lua-5.3.5
curl https://raw.githubusercontent.com/WqyJh/lua_so_patch/master/lua_so-5.3.5.patch | patch -p1
make linux test
make install
```

Then you got `liblua.a` and `liblua.so` installed.




## How to make an patch

### 1. Download a source code

```bash
curl -R -O http://www.lua.org/ftp/lua-5.3.5.tar.gz
```



### 2. untar and copy

```bash
tar zvxf lua-5.3.5.tar.gz
cp -r lua-5.3.5 lua-5.3.5-new
```



### 3. Change the Makefiles in new

Under `lua-5.3.5-new/`, there are two Makefiles to be modified.

```bash
.
├── Makefile
└── src
    └── Makefile
```

1. Makefile

   ```bash
   # Modify
   TO_LIB= liblua.a
   TO_LIB= liblua.a liblua.so
   ```

2. src/Makefile

   ```bash
   # Append CFLAG: -fPIC
   CFLAGS= ... $(MYCFLAGS)
   CFLAGS= ... $(MYCFLAGS) -fPIC

   # Add
   LUA_SO= liblua.so
   
   # Modify
   ALL_T= $(LUA_A) $(LUA_T) $(LUAC_T)
   ALL_T= $(LUA_A) $(LUA_T) $(LUAC_T) $(LUA_SO)
   
   # Add
   $(LUA_SO): $(CORE_O) $(LIB_O)
   	$(CC) -o $@ -shared -fPIC $? -ldl -lm
   ```



### 4. Make a patch

```bash
diff -rc lua-5.3.5 lua-5.3.5-new > lua_so-5.3.5.patch
```


