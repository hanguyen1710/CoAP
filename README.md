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

**12. Phân tích bản tin trên WireShark**
###### Định dạng bản tin CoAP
> **1. Ver** (Version) : 2 bits, thường là 01
> **2. T** (Type) : 2 bits, có thể là CON (00 - 0), NON(01 - 1), ACK (10 - 2), RST (11 - 3)
> **3. TKL** (Token Length) : 4 bits, độ dài của Token
> **4. Code** : 8 bits, mã code của bản tin, chia thành 2 phần: *3 bits cho class và 5 bits cho detail*. **c.dd** trong đó c là class, dd là detail
> |Code|Name|Reference|
> |:----:|:----:|:---------:|
> |0.01|GET|RFC7252|
> |0.02|POST|RFC7252|
> |0.03|PUT|RFC7252|
> |0.04|DELETE|RFC7252|
>
> **Note** : *0.00* là empty message
> |Code class|Description|
> |:----:|:----:|
> |0|Request|
> |2|Success Response|
> |4|Client Error Response|
> |5|Server Error Response|
>
> **Ví dụ**
> ###### Success 2.xx : Mô tả rằng client request đã được server xử lý thành công
> |Code|Name|Description|
> |:----:|:----:|:----:|
> |2.01|Created|Request đã được xử lý thành công và **tài nguyên mới đã được tạo**|
> |2.02|Deleted|Request đã được xử lý thành công và **tài nguyên đã bị xóa**|
> |2.03|Valid|Request đã được xử lý thành công và **tài nguyên vẫn còn hiệu lực**|
> |2.04|Changed|Request đã được xử lý thành công và **tài nguyên đã được thay đổi**|
> |2.05|Content|Request đã được xử lý thành công và **tài nguyên đã được trả về**|
>
> ###### Client Error 4.xx : Mô tả rằng client dường như đã gửi một request không hợp lệ
> |Code|Name|Description|
> |:----:|:----:|:----:|
> |**4.00**|**Bad Request**|**Request không hợp lệ**|
> |4.01|Unauthorized|Client cần phải xác thực|
> |**4.02**|**Bad Option**|**Option không hợp lệ**|
> |4.03|Forbidden|Client không có quyền truy cập|
> |**4.04**|**Not Found**|**Tài nguyên không tồn tại**|
> |4.05|Method Not Allowed|Phương thức không được hỗ trợ|
> |4.06|Not Acceptable|Tài nguyên không thể trả về theo định dạng yêu cầu|
> |4.12|Precondition Failed|Điều kiện tiên quyết không đúng|
> |4.13|Request Entity Too Large|Request quá lớn|
> |4.15|Unsupported Content-Format|Định dạng không được hỗ trợ|
>
> ###### Server Error 5.xx : Mô tả rằng server không thể xử lý request của client
> |Code|Name|Description|
> |:----:|:----:|:----:|
> |5.00|Internal Server Error|Lỗi nội bộ của server|
> |5.01|Not Implemented|Server không hỗ trợ phương thức|
> |5.02|Bad Gateway|Server không thể truy cập tài nguyên|
> 
> **5. Message ID**: 16 bits, ID của bản tin dùng để nhận dạng bản tin
>
> **6. Token**: 0-8 bytes, dùng để ánh xạ bản tin request và response
> **7. Options**: 0-8 bytes, dùng để chứa thông tin mở rộng
> Số option được chứa trong bản tin được xác định bởi *Option Delta* và *Option Length*
> **Option Delta** : 4 bits, độ lệch giữa option hiện tại và option trước đó
> **Option Length** : 4 bits, độ dài của option hiện tại
> ###### Bảng Option 
> |No|Name|
> |:----:|:----:|
> |1|If-Match|
> |3|Uri-Host|
> |4|ETag|
> |5|If-None-Match|
> |7|Uri-Port|
> |8|Location-Path|
> |11|Uri-Path|
> |12|Content-Format|
> |14|Max-Age|
> |15|Uri-Query|
> |17|Accept|
> |20|Location-Query|
> |35|Proxy-Uri|
> |39|Proxy-Scheme|
> |60|Size1|
>
> **8. Payload**: 0- 2^8-1 bytes, dữ liệu của bản tin

###### Các mô hình của giao thức CoAP
> **1. Mô hình Piggy-Back**
> - Client gửi 1 yêu cầu sử dụng bản tin CON
> - Server phản hồi ngay lập tức kể có thành công hay không
> 
> 





> 

