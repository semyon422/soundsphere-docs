1) Download and install Visual Studio 2013 (all addons can be disabled): *link is hidden*
2) git clone --recurse-submodules https://github.com/jdeeny/lovemidi
3) Open lovemidi\rtmidi\msw\rtmidilib.vcproj, VS will update it. Close it.
4) Download LuaJIT https://github.com/love2d/megasource/tree/main/libs/LuaJIT
5) Find VS2012 x64 Cross Tools Command Prompt in your Start menu. Copy it (this is shortcut to cmd) to desktop. Edit it, replace 11.0 with 12.0 in object line and working directory line.
6) Open it, cd path/to/luajit/src, run msvcbuild.bat.
7) After successful build, copy lua.h, lauxlib.h, luaconf.h, lua51.lib from LuaJIT/src to lovemidi/src
8) Open lovemidi/lovemidi.vcxproj, in luamidi.cpp replace luaL_reg with luaL_Reg (or add #define luaL_reg luaL_Reg)
9) In solution properties set Release x64 for both projects, add x64 platform if missing.
10) Build solution. Get lovemidi\x64\Release\luamidi.dll
