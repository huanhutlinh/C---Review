## Volatile là gì?

- ngăn compiler optimize code (tức là nó sẽ lược bỏ những phần nó nghĩ là không cần thiết).

Cụ thể là nó sẽ bảo compiler rằng: “Cái biến này mặc dù không thấy chỗ nào trong đoạn code này tác động vào nó nhưng hãy nhớ rằng nó có thể thay đổi giá trị và đừng có optimize nó nha”.

Vậy là có những biến có thể thay đổi mà compiler không biết ư? Nghe có vẻ gì đó cao siêu nhưng thực ra có thể bạn gặp nó hằng ngày.

Các trường hợp đó là:

- Biến được map vào vùng nhớ ngoại vi (phần cứng nào đó chẳng hạn).
- Biến global mà được update giá trị thông qua ISR (interrupt service routine).
- Biến global được dùng chung giữa một chương trình multi-thread.

Cú pháp:

```
volatile int foo;
int volatile foo;
```

Tham khảo:

[https://www.keil.com/support/man/docs/armclang\_intro/armclang\_intro\_chr1385110934192.htm](https://www.keil.com/support/man/docs/armclang_intro/armclang_intro_chr1385110934192.htm)

## So sánh struct và union

Cả struct lẫn union đều là các user define type (kiểu dữ liệu do người dung đề ra). Ta đều có thể dung dấu “.” để truy cập các phần tử. Tuy vậy việc sử dụng chúng lại có đôi chút khác biệt.

<table><tbody><tr><td>&nbsp;</td><td>Struct</td><td>Union</td></tr><tr><td>Keyword</td><td>Dùng keyword “struct” để define struct.</td><td>Dùng keyword “union” để define union</td></tr><tr><td>Size</td><td>Lớn hơn hoặc bằng tổng size các member (tùy vào data alignment)</td><td>Lấy theo size của của member lớn nhất.</td></tr><tr><td>Memory</td><td>Mỗi member có vùng nhớ riêng biệt.</td><td>Các member dung chung 1 vùng nhớ.</td></tr><tr><td>Modify giá trị</td><td>Modify một member không ảnh hưởng member khác.</td><td>Việc modify bất kỳ member nào cũng ảnh hưởng đám còn lại.</td></tr><tr><td>Truy cập member</td><td>Truy cập bất kỳ thành viên nào ở thời điển nào cũng được.</td><td>Trong một lúc chỉ truy cập được một thành viên mà thui.</td></tr><tr><td>Init giá trị đầu</td><td>Member nào cũng init được</td><td>Chỉ member đầu tiên là có thể init</td></tr></tbody></table>

Đọc sơ qua thì thấy struct có quá nhiều ưu điểm phải không nào? Nhưng trong một số trường hợp người ta lại thích dùng union hơn.

Ví dụ để tiết kiệm bộ nhớ, khi cần access một vùng nhớ cố định cho 2-3 members (như trong trường hợp bạn cần truy cập toàn bộ thanh ghi hoặc chỉ các phần tử bit nhỏ hơn).

Tham khảo:

[https://www.geeksforgeeks.org/difference-structure-union-c/](https://www.geeksforgeeks.org/difference-structure-union-c/)

[https://www.guru99.com/difference-structure-union-c.html](https://www.guru99.com/difference-structure-union-c.html)

## ISR là gì? Có return type hay pass value vào được không?

A: KHONG

ISR là Interrupt Service Routine. Hiểu đơn giản là chương trình được gọi khi có một sự kiện (ngắt) nào đó xảy ra.

Không giống như các function bình thường, việc gọi các ISR thường không đoán trước được.

Ví dụ như khi nhấn nút thì đèn sáng, bạn không biết được khi nào xảy ra việc nhấn nút.

Do có tính đặc thù vậy nên ISR nào riêng khỏi main flow của chương trình, không có return type hoặc cho phép pass bất kỳ value nào vào.

Các ISR thường chỉ dùng để xử lý các tác vụ nhanh (vd như bạn thấy xe quên gạt chống thì la lên, còn người lái xe sẽ lo việc còn lại).

Quyền ưu tiên thực thi của ISR thường rất cao nên nếu không xong nhanh rồi out ra thì có thể ảnh hưởng performance hệ thống. Vậy nên ta cần tránh xử lý quá nhiều trong ISR.

Các lỗi thường gặp khi viết ISR:

- Dùng hàm printf: hàm này khi chạy cần alloc mem nên tốn thời gian.
- Đặt breakpoint: về cơ bản không sai nhưng hệ thống có thể hoạt động không sát thực tế, từ đó việc debug cũng không mang nhiều ý nghĩa.

## Little Endian và Big Endian là gì? Viết chương trình kiểm tra

Khi lưu dữ liệu vào bộ nhớ, LE tức là bit thấp sẽ lưu vào vùng nhớ thấp còn BE thì ngược lại.

Ta có thể kiểm tra hệ thống là LE hay BE dựa vào chương trình đơn giản sau

```
int p=0x2; 
if(* (char *) &p == 0x2) printf (“little endian”); 
else printf (“big endian”);
```

Giải thích

int là kiểu dữ liệu gồm 4 bytes còn char thì chỉ có 1 byte.

Khi ta lưu data vào một biến int rồi dùng pointer to char dereference ra thì sẽ biết được value đang nằm trong phần char “cao” hay “thấp”.

**LE**

<table><tbody><tr><td>Byte 3</td><td>Byte 2</td><td>Byte 1</td><td>Byte 0</td></tr><tr><td>00000000</td><td>00000000</td><td>00000000</td><td><span>00000010</span></td></tr></tbody></table>

**BE**

<table><tbody><tr><td>Byte 3</td><td>Byte 2</td><td>Byte 1</td><td>Byte 0</td></tr><tr><td><span>00000010</span></td><td>00000000</td><td>00000000</td><td>00000000</td></tr></tbody></table>

## Watchdog Timer là gì?

WDT là một bộ đếm phần cứng có thể đếm cho tới khi đạt giá trị nào đó. Khi ấy, nó sẽ sinh ra tín hiệu reset nhằm đưa hệ thống quay về trạng thái ban đầu.

Ta sử dụng WDT để tránh cho system không bị mắc kẹt, treo hoài trong một lỗi nào đó.

## Pointer là gì? Size của pointer? Dùng pointer truy cập vào vùng nhớ cho trước?

Pointer là một biến đặc biệt dùng để lưu địa chỉ. Vì địa chỉ các ô nhớ là cố định theo kiểu int nên với system 32bit thì size của pointer sẽ là 4 bytes. 64bit size = 8.

Khi làm việc với hệ thống nhúng, ta thường có yêu cầu phải truy xuất một vùng nhớ được nêu giá trị địa chỉ cụ thể. Ví dụ như bạn cần đọc data hay thay đổi giá trị tại vùng nhớ có địa chỉ 0x67a9. Để thực hiện điều đó chúng ta có thể dùng pointer và làm như sau.

```
int *ptr;
ptr = (int *)0x67a9;
*ptr = 0xaa55;
```

## Đọc tên các kiểu khai báo pointer sau

int a;  
\=> Biến kiểu int.

int \*a;  
\=> Pointer trỏ đến biến kiểu int.

int \*\*a;  
\=> Pointer trỏ đến một pointer khác, mà pointer đó lại trỏ đến một biến kiểu int.

int a\[10\];  
\=> Mảng 10 phần tử kiểu int.

int \*a\[10\];  
\=> Mảng10 phần tử là pointer trỏ tới biến kiểu int (dĩ nhiên mỗi thằng trỏ độc lập nhau).

int (\*a)\[10\];  
\=> Pointer trỏ tới 1 mảng có 10 phần tử kiểu int.

int (\*a)(int);  
\=> Pointer trỏ tới 1 function (function pointer) mà function đó có 1 argument kiểu int và return type là int.

int (\*a\[10\])(int);  
\=> Mảng gồm 10 thằng function pointer có đặc điểm như trên.

## C memory layout

Một chương trình C cơ bản sau khi compile thì sẽ có memory layout gồm các phần chính sau:

1\. Text segment
2\. Initialized data segment
3\. Uninitialized data segment
4\. Stack
5\. Heap

![](https://laptrinhmai.com/wp-content/uploads/2021/07/image.png)

**Text segmen**t

Còn được gọi là code segment, dùng để lưu các đoạn code thực thi được. Phần này thường nằm cuối, tránh xa ra Stack hay Heap nhằm tránh việc bị các vùng này overwrite.

Text segment thường được đặt là vùng nhớ read-only.

**Initialized data segment**

Chứa các biến global và static (không phân biệt static local hay static global) đã được init giá trị rồi.

 Vì các biến có thể thay đổi được nên nó sẽ không phải là vùng read-only.

**Uninitialized data segment**

Hay còn gọi là BSS segment, dùng để chứa các biến global và static chưa được init giá trị. Trước khi thực thi chương trình, kernel sẽ init các biến này về giá 0.

**Stack**

Sử dụng cấu trúc LIFO (Last In First Out), Stack dùng để lưu biến local và những thông tin cần cho việc thực thi function (như argument chẳng hạn).

Những thông tin như vậy nằm thành từng block và lần lược được nhét vào Stack, khi dùng Stack sẽ lấy từ block được nhét vào sau cùng (top block). Các block như vậy được gọi là stack frame.

Nếu học về kiến trúc máy tính sẽ có thanh ghi SP (Stack Pointer) để track lại top frame.

**Heap**

Ngược hướng với chiều tăng của vùng Stack, Heap được dùng để tạo vùng nhớ dynamic (qua các hàm malloc etc) Nếu Heap đụng tới vùng Stack thì ta sẽ hết free mem.

Tương tự nếu Stack quá lớn cũng sẽ gặp lỗi “Stackoverflow” (thường gặp nếu khai báo mảng quá khổ hoặc các hàm đệ quy được lặp quá nhiều).

Tham khảo thêm:

[https://www.geeksforgeeks.org/memory-layout-of-c-program/](https://www.geeksforgeeks.org/memory-layout-of-c-program/)

[https://www.hackerearth.com/practice/notes/memory-layout-of-c-program/](https://www.hackerearth.com/practice/notes/memory-layout-of-c-program/)
