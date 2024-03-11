# Xây dựng giao thức CoAP trên Ubuntu 22.04.3 LTS



**1. Tạo 1 folder trong Document của Ubuntu của bạn tên là Test**
**2. Mở Terminal tại folder vừa tạo**
**3. Tải git trên Ubuntu**
```c
sudo apt-get install git
```
**4. Cài đặt môi trường : libcoap**
```c
git clone https://github.com/obgm/libcoap.git
ls 
cd libcoap/
sudo apt-get install libtool 
sudo apt-get install libtool-bin
sudo apt-get install pkg-config 
./autogen.sh
ls
./configure --disable-documentation --disable-dtls
make
sudo make install

```
**6. File tiêu đề của libcoap**
```c
/usr/local/include/coap3
```
Nếu không biết trong các thư mục của mình có gì thì cta vào thư muc đó bằng ```cd + thư mục/``` dùng ``` ls ```
**7. Thư viện chia sẻ các đối tượng**
```c
/usr/local/lib
```
**8. Sau đó chuyển tới thư mục chúng ta vừa tạo, chọn phần **examples****
```c
/Document/Test/libcoap/examples
```
**10. Mở terminal và thêm**
```c
export LD_LIBRARY_PATH=/usr/local/lib
gcc -I /usr/local/include/ coap-server.c -o coap-server -lcoap-2-openssl -DWITH_POSIX
gcc -I /usr/local/include/ client-server.c -o client-server -lcoap-2-openssl -DWITH_POSIX

```
**11. Test và bắt bản tin bằng Wireshark với card localhost-lo**
```c
./coap-server -v9
./coap-client
./coap-client -m get coap://[::1]/time
```