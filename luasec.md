1) git clone https://github.com/brunoos/luasec
2) replace "MD32" in rockspec with ""
3) install https://slproweb.com/products/Win32OpenSSL.html
4) %LUAROCKS% build OPENSSL_DIR="C:\Program Files\OpenSSL-Win64" OPENSSL_LIBDIR="C:\Program Files\OpenSSL-Win64\lib"
