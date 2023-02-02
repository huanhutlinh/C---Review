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