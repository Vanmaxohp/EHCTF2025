Qua đề bài, ta thu được một số dữ kiện sau:

Flag nằm trong tin nhắn của Alice

Gọi lần lười 3 tin nhắn và bản mã tương ứng là m1, m2, m3 và c1, c2, c3; khoá của Alice là ka, khoá của Bob là kb; ta có:

c1 = m1 ^ ka

c2 = m2 ^ kb ^ c1 =  m2 ^ kb ^ m1 ^ ka
 
c3 = m3 ^ ka ^ c2 = m3 ^ ka ^ m2 ^ kb ^ m1 ^ ka = m1 ^ m2 ^ m3 ^ kb

Suy ra:

c2 ^ c3 = m3 ^ ka


Từ đó ta quay về dạng OTP sử dụng 1 khoá (ka) nhiều lần. Giải bằng trick Crib Drag.

Minh hoạ cách giải bằng web http://cribdrag.com/:

Nhập c1 vào ô cipher 1, nhập c2 ^ c3 vào ô cipher 2.

Ở ô Crib word, nhập `EHCTF{` (phần đã biết). Ta có được `How co`

Copy từ `How co` lên phần Crib word thay cho `EHCTF{`

Dưa vào kiến thức tiếng anh, hoặc mò, hoặc bruteforce, ta sẽ tìm được từ đầy đủ là `How could `. Kết quả bên dưới là `EHCTF{Man`

Tiếp tục thay `EHCTF{Man` lên phần Crib word và làm tương tự ta sẽ từ từ tìm ra flag như sau:

`EHCTF{Many_` => `How could I`

`How could I ` => `EHCTF{Many_T`

`EHCTF{Many_Time_` => `How could I join`

`How could I join ` => `EHCTF{Many_Time_P`

`EHCTF{Many_Time_Padding_` => `How could I join the org`
 
`How could I join the organization` => `EHCTF{Many_Time_Padding_Attack!!!`

Ký tự cuối hiển nhiên là dấu "}", ta có flag: `EHCTF{Many_Time_Padding_Attack!!!}` và tin nhắn: "How could I join the organization?"

