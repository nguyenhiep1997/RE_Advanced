## break poin.

> tài liệu tham khảo: [internet](https://google.com)

> Người thực hiện: Nguyễn Văn Hiệp 

> cập nhật lần cuối: 24/5//2017

>---

### Mục Lục:

 - [1. tổng quan về break point](#1)

 - [2. Common Breakpoint or BPX](#2)

 - [3. Breakpoints on Memory (Memory Breakpoint)](#3)

 - [4. Hardware Breakpoints](#4)

 - [5. Conditional Breakpoints](#5)

 - [6. Conditional Log Breakpoints](#6)

 - [7. Message Breakpoints](#7)

-----------------------------------

#### 1. tổng quan về break poin<a name="1"></a>

 - đầu tiên khi nói về break point ta phải hiểu rằng : tất cả các loại break point bao gồn hardware break point (hwbp) hay conditional break point thì mục đích của việc thiết lập preak poin đó củng là để ngưng sự thực thi của chương trình và trả quyền kiểm soát về lại với olly 
 
 - break point giữ vị trí rất quan trọng trong olly, nó làm cho việc debug trở nên dễ dàng hơn. Ta hãy thử tưởng tượng khi 1 chương trình viết bằng ngôn ngữ bậc cao vô cùng phức tạp mà lại được dịch lại bằng ngôn ngữ asm với số lượng mã lệnh lên đến hàng triệu dòng code thì việc chạy qua từng dòng lệnh là điều vô cùng khó khăn và đôi khi còn gây ra sự nhàm chán ( nếu không tin bạn có thể thử load 1 crackme bất kì nào đó vào và nhấn ctrl + F7 xem . cá là bạn sẽ đổ mồ hôi hột đấy). bởi vậy ta cần đựt những break point tại những điểm quan trọng của chương trình ( thông thường là các hàm quan trọng mà cụ thể sẽ được nói đến sau). và sau đó cho thực thi để đến được điểm ta cần chỉ trong giây lát .

#### 2.  Common Breakpoint<a name="2"></a>

 - đây là phương pháp đặt break poin phổ biến nhất khi debug bằng olly hay bất kì trình gỡ lỗi nào khác , ở đâu tôi sẽ chỉ nói đến olly . khi bạn tìm đến 1 dòng lệnh và bạn muốn đặt break point ở đó, bạn đơn giản chỉ nhấn F2 vào vị trí con trỏ đang chỉ và dòng code đó sẽ được đặt làm điểm dừng. khi bạn nhấn F2 thêm 1 lần nữa thì bạn đã bỏ break point ở dòng code đó đi.

 - điều hay ho ở break point này là bạn có thể đặt vô số break point tùy ý . đối với win 98 và win xp thì việc đặt break point này được chia làm hai loại : break point address và break point API , hay có thể goi là BP và BPX . Nhưng qua thời gian thì các nhà phát triển đã thay đổi để break point có thể đặt cho cả API và address.

 - tìm hiểu chuyên sâu về loại break point này . ở trên trang [ollydbg](http://www.ollydbg.de/Help/i_Breakpoints.htm) thì break point này được gọi là INT3 break point , Để đặt điểm ngắt INT3, OllyDbg thay thế byte đầu tiên của lệnh 80x86 bằng một mã đặc biệt 0xCC (ngắt một byte với một vector 3, còn được gọi là "trap to debugger"). Khi CPU thực hiện INT3, nó sẽ gọi trình xử lý ngắt của hệ điều hành, lần lượt nó báo cáo nó như là một ngoại lệ của loại EXCEPTION_BREAKPOINT để OllyDbg.

 - Chúng chỉ hoạt động trên mã. Điểm dừng INT3 bị đặt sai (đặt vào dữ liệu hoặc ở giữa lệnh) có thể dễ dàng dẫn đến sự sụp đổ của ứng dụng gỡ lỗi. Nếu OllyDbg không chắc chắn liệu điểm ngắt INT3 được cho phép, nó sẽ yêu cầu xác nhận. 

 - trong trường hợp khi ta đặt quá nhiều break point hoặc muốn biết ta đã đặt break point ở những vị trí nào thì ta có 1 công cụ để kiểm soát đó là cửa sổ brak point ký hiệu B trong olly .

 - ngoài cách đặt break truyền thống là  tìm đến tận dòng mã đó để đặt thì ta có thể thông qua command bar để đặt với cú pháp `bp tên hàm` nó sẽ đặt break point tại dòng lệnh đầu tiên của hàm đó nhưng cần lưu ý một điều : tên hàm phải được viết đúng đến từng ký tự kể cả chữ hoa và chữ thường. 

 - 1 lưu ý nữa khi đặt break point bằng command bar thì khi dùng lệnh `bpx tên hàm ` nó sẽ đặt break point cho tất cả các lệnh tham chiếu đến hàm đó .

 - ngoài ra ta củng có thể đặt break point bằng cách nhấn đúp chuột vào cửa sổ CPU trên dòng mã hex.

 - khi bạn đặt 1 break point nào đó , có khi nào bạn gặp trường hợp này chưa :
 ![1](http://i.imgur.com/unk2IRC.png)

 khi mà những trạng thái trên startus hiện là one-shot thay vì allway như thường lệ , khi đó break point ở vị trí đó sẽ bị bỏ qua , và nếu ta nhấn vào nó nó sẽ tự động xóa .

#### 3.  Breakpoints on Memory<a name="3"></a>

 - ở phần trên khi nói về break point thông thường ta nghĩ đến đặt break point trên các dòng lệnh (opcode) nhưng trong 1 số trường hợp khi bạn có quá nhiều sự lựa chọn về dòng lệnh hoặc chẳng có manh mối nào để đặt break point cho đúng cả thì phải làm thế nào . Lúc đó Break point memory (bpm) sẽ cứu cánh cho bạn.

 - giả sử 1 crackme khi chạy yêu cầu bạn nhập vào 1 mật khẩu , sau đó mật khẩu được tính toán ; nếu như mật khẩu ( hoặc serial) đó đúng thì sẽ xuất hiện 1 messege box là good boy hoặc ngược lại sẽ là bab boy . lúc đó bạn sẽ làm cách nào để có mật khẩu. đó là lúc bpm ra tay ....... hãy sử dụng 1 ít kiến thức của mình về các hàm trong windown bạn sẽ nhận ra hàm để nhận vào mật khẩu thông thường sẽ là GetDigItentext nó nhận những gì ta nhập vào và lưu tại 1 vị trí trên buffer , việc của ta là tìm nó và bôi đen tất cả các mã biêt thị đoạn ta vừa nhập vào chọn `break point => memory ` thế là ổn . trace đến đoạn code nó so sánh cái mã mình nhập vào với key đúng để tìm ra key hoặc tìm cách gen key vô cùng dễ dàng .

 - cách thức hoạt động của loại break point này như sau khi chương trình thực thi đến 1 dòng lệnh mà nó tham chiếu đến địa chỉ mà ta đặt break point (ghi hoặc đọc thông tin trên đó ) thì olly sẽ tạo ra 1 exceptions làm ngưng sự thực thi và trả lại quyền điều khiển cho olly, tùy thuộc vào điều kiện ngừng của chương trình mà ta có 2 loại break point memory:
<ul>
<li> 1) **break point memory on access** : khi ta đặt 1 break point memory on access tại 1 điểm thì khi chương trình cố ghi hoặc đọc thông tin từ vùng đó sẽ làm ngắt chương trình và trả quyền điều khiển về olly</li> 
<li> 2) **break point memory on write** : 2 loại break point này nhìn bề ngoài thì khác nhau nhưng bản chất lại giống nhau , hay nói cách khác break pont memory on access là trường hợp tổng quát còn write là trưởng hợp riêng , nó thực hiện ngắt chương trình khi có lệnh ghi vào vùng mà ta đặt break point .</li>
</ul>

 - thông tin từ nhà cung cấp ollydbg cho biết
```
- OllyDbg can set memory breakpoints on read, write and/or on instruction execution. Number of memory breakpoints is unlimited. Note that 80x86 CPU can set memory attributes only on the complete memory pages, 4096 bytes long. Therefore memory breakpoints produce many false breaks and in some cases may be very slow.

- Memory breakpoints are user-controlled. They may have associated condition and may protocol the values of expressions.

- If kernel attempts to access protected memory page, this may result in the crash of the debugged application. Memory breakpoint on the stack is an absolute no-go.
```
 - qua đó chúng ta biết rằng loại break này chỉ cho phép ta thiết lập tại 1 điểm duy nhất trong chương trình , nếu như ta thiết lập tại điểm mới thì điểm cũ tự động bị bỏ đi nên cần nhớ vị trí đã đặt break point mà bỏ đi .

 - cần lưu ý , break point memory sẽ bị mất đi nếu ta restart lại chương trình mà không để lại dấu vết , điều này khác so với break point thường khi sau khi restart các điểm đặt break point vẫn được giữ nguyên.

 - break point memory on access củng có thể được dùng để đặt cho các section ví dụ như `kenel32`, `code` hay 1 thư viện mà ta rất hay gặp đó là `user32.dll` . cách thực hiện củng tương tự như trên chỉ khác là ta thực hiện nó trong cửa sổ **map**

 ![2](http://i.imgur.com/XZsQ2ZP.png)

 - khác với break point thường sử dụng ngắt INT3 (0xcc) . memory break point không làm thay đổi code hay nói cách khác nó chỉ đơn giản là làm dừng thực thi mà không ảnh hưởng đến chương trình vì vậy nó sẽ rất hữu dụng khi có 1 chương trình bằng cách nào đó phát hiện cc trong code làm dừng thực thi để tiến hành kill olly của chúng ta. điều cuối cùng về memory break point đó là nó không trực tiếp được quản lí tại 1 cửa sổ nào nhất định nên việc ghi nhớ xem ta đã đặt break point ở đâu củng là 1 vấn đề khá khó khăn.

#### 4.  Hardware Breakpoints <a name="4"></a>

 - đầu tiên khi nói về hardware break poin (HWBP) là chúng sẽ không thể thực hiện được trên windown 98, 2000, NT hoặc XP, nó tương thích với bộ vi xử lý 8086 và cho phép bạn đặt tối đa 4 break point thay vì 1 break point như với memory break point . ưu điểm của HWBP là nó không làm chậm quá trình thực thi của chương trình và điểm giống với memory break point là HWBP củng không sử dụng INT3 ( cc) để làm ngừng thực thi chương trình mà nó dùng INT1 

 - hardware break point được hổ trợ trực tiếp từ CPU , chúng sử dụng 4 thanh ghi gọi là register debug **DR0, DR1, DR2, DR3** . đến đây chắc các bạn củng hiểu được lý do tại sao HWBP có thể đặt được 4 điểm rồi chứ . vì chỉ có 4 thanh ghi thôi và nó dùng để lưu trữ địa chỉ mà ta thiết lập break point . điều kiện để các break point thực thi việc dừng hoạt động lại được lưu ở thanh ghi khác đó là **DR7** . khi gặp điều kiện thỏa mãn thì chương trình lại tạo ra 1 exception để trả quyền điều khiển về cho trình debug.

 - có 4 kịch bản để một chương trình được dừng thực thi khi set HWBP 
 <ul>
 <li>1. Khi một câu lệnh được thực thi.</li>
 <li>2. Khi nội dung của memory có thay đổi (modified).</li>
 <li>3. Khi một ví trí memory được đọc ra hoặc được cập nhật(updated).</li>
 <li>4. Khi một input-output port được tham chiếu tới. </li>
 </ul>
 - việc đặt 1 HWBP củng tương tự với đặt các loại break point tại 1 câu lệnh thông thường nhưng vì đặc điểm của HWBP là không làm thay đổi code nên việc phát hiện nó là khá khó khăn song trong thực tế vẫn có một số tric có thể làm được điều này ......... quá siêu .

 - cách thức để đặt hardware break point là nhấn vào vị trí mà ta muốn set break point sau đó nhấn chuột phải chọn `break point => harware on execution` hoặc còn một cách khác là thông qua thanh command bar và gõ ` HE [địa chỉ lệnh]` hoặc `HE [tên hàm]` như vậy ta đã set break point thành công

 - nhưng có một điều đặc biệt đó là khi ta thiết lập xong lại chẳng thấy gì thay đổi cả, nếu trong trường hợp ta muốn tìm hiểu xem các break point đã đặt ở đâu và ra sao thì phải làm thế nào ....... thật may mắn là ta có 1 công cụ giúp thực hiện điều này , bạn chọn `debug => hardware break point ` 

 ![3](http://i.imgur.com/Pp7zDcZ.png)

 - từng phần trên thanh đó chắc củng dễ hiểu thôi , đầu tiên là điều kiện dừng ở trên ta chọn *execution* nghĩa là sẽ dừng nếu thực thi qua dòng lệnh đó . dòng thứ 2 là kích cỡ dữ liệu , đối với trên mỗi dòng lệnh thì mặc định nó là byte còn khi ta đặt HWBP tại các đơn vị của cửa sổ dump thì sẽ có những tùy chọn khác như `word` hoặc `DWORD` . còng dòng cuối cùng là để hiển thị địa chỉ ta đặt break point và tình trạng của nó .

 - khi restart lại chương trình thì các điểm đặt của ta vẫn được giữ nguyên không đổi nên ta không cần phải lo lắng .

> đó là đối với **HardWare Breakpoint on Execution** còn bây giờ hãy cùng tìm hiểu về **HWBP on Write và HWBP on Access.**

 - về cơ bản thì cả 3 loại break point này đều tương tự nhau , đặc biệt là HWBP on Write và HWBP on Access. chúng giống như anh trai và em gái vậy.

 - đối với HWBP on Access việc set break point , ta đơn giản chỉ là tìm đến những bit cần đặt BP trên cửa sổ dump và bôi đen chúng sau đó nhấp phải chọn `break point => hard ware, on Access` sau đó tùy vào điều kiện mà ta có thể chọn byte , word hoặc dword . như vậy ta đã có thể giám sát vùng nhớ này , mỗi khi có lệnh nào thực hiện đọc hoặc ghi vào vùng này thì sẽ sinh ra 1 ngoại lệ và đưa quyền điều khiển về lại với olly .

 - tất nhiên vì là HWBP nên tất cả đều sử dụng chung 1 cửa sổ để quản lý y như đối với HardWare Breakpoint on Execution , hoàn toàn không có gì thay đổi cả.

 - điểm khác biệt lớn nhất khi ta dùng HWBP khác với memory break point đó là đối với BP memory thì ngay khi lệnh bắt đầu đọc hoặc ghi dữ liệu thì chương trình sẽ break và trả quyền thực thi về cho olly , còn khi sử dụng HWBP on access thì khác , phải sau khi thực thi xong chương trình mới bị ngắt.

 - đối với HWBP on Write thì tất nhiên là làm tương tự rồi . chỉ khác 1 tí tị ti là khi ta đặt HWBP on write thì chương trình sẽ chỉ ngắt khi có lệnh viết lên những byte mà ta lựa chọn thiết lập chứ không bao gồm cả viết và đọc như on access nữa . qua đây ta củng nhận thấy một sự tương đồng trong cấu trúc của break point memory và hard ware break point 

#### 5. Conditional Breakpoints<a name="5"></a>

 - Conditional Breakpoints (phím tắt Shift + F2) là một điểm ngắt INT3 thông thường có biểu thức hoặc các điều kiện liên quan. Mỗi lần gỡ lỗi gặp điểm dừng này, nó tính toán các biểu thức, và nếu kết quả là bằng hoặc biểu thức hợp lệ,  thì chương trình dừng gỡ lỗi. Tất nhiên,sai khác  gây ra bởi breakpoint điều kiện sai là rất cao (chủ yếu là do độ trễ của hệ thống hoạt động). Trên PII / 450 trong Windows NT OllyDbg xử lý tới 2500 điểm ngắt có điều kiện sai trong mỗi giây. Một trường hợp quan trọng của điểm dừng có điều kiện là phá vỡ tin nhắn của Windows (như WM_PAINT). Với mục đích này, bạn có thể sử dụng giả mạo MSG giả cùng với việc giải thích hợp lý các đối số. Nếu cửa sổ đang hoạt động, hãy xem xét breakpoint thông báo được mô tả dưới đây.

 - Về mặt khách quan thì nó cũng giống như bp thông thường, tức là nó cũng dùng lệnh Int3 để dừng sự thực thi của chương trình, thêm nữa Condtional bp cũng được quản lý thông qua cửa sổ Breakpoints(B). Tuy nhiên, điểm khác biệt nằm ở chỗ điều kiện dừng phải thỏa một điều kiện đã được thiết lập từ trước.

 - Điểm quan trọng khác của conditional bp là nó hay được sử dụng để “tóm” các Windows Message vậy thì Windows Message là gì ??? . nếu đi sâu vào tìm hiểu thì nó khá phức tạp và dài dòng nhưng ta có thể hiểu 1 cách đơn giản là các API do windown tạo ra và được người lập trình sử dụng . ví dụ như WM_CLOSE, WM_PAINT v..v... nhiều lắm 

 - tất nhiên tất cả các conditional breakpoint đều được quản lý ở cử sổ break point.

 - việc thiết lập loại breakpoint này củng khá đơn giản , ta chọn vào đoạn code muốn set và nhấn chuột phải chọn `breakpoint => conditional ` hoặc nhấn tổ hợp phím shift +F2 để thiết lập nhanh .

#### 6. Conditional Log Breakpoints<a name="6"></a>

 - Conditional Log Breakpoints (Shift + F4) là một điểm dừng có điều kiện với tùy chọn để ghi lại giá trị của một số biểu thức hoặc các đối số của hàm đã biết mỗi khi gặp điểm dừng hoặc khi điều kiện được đáp ứng. Ví dụ, bạn có thể thiết lập breakpoint log cho một số thủ tục cửa sổ và liệt kê tất cả các lệnh gọi đến thủ tục này, hoặc chỉ định danh nhận được message WM_COMMAND, hoặc đặt nó vào lệnh gọi đến CreateFile và log tên của các tập tin được mở cho truy cập chỉ đọc vv log  các điểm ngắt được nhanh như có Conditional, và dĩ nhiên nó dễ dàng hơn nhiều để xem qua vài trăm thư trong cửa sổ log hơn là nhấn F9 vài trăm lần. Bạn có thể chọn một trong một số phiên bản được xác định trước cho biểu thức của bạn.

 - Tổng quan về hoạt động
Khi một breakpoint bị trúng, x64dbg sẽ làm những việc sau:

>Tăng counter truy cập ;
 Đặt biến hệ thống `$breakpointcounter`với giá trị của bộ đếm lần truy cập ;
 Nếu điều kiện phá vỡ được thiết lập, đánh giá biểu thức (mặc định là `1`);
 Nếu khôi phục lại nhanh được thiết lập và đánh giá tình trạng phá vỡ để `0`:
 Tiếp tục thực hiện *debuggee* (bỏ qua các bước tiếp theo). Điều này cũng sẽ bỏ qua việc thực hiện các cuộc gọi lại plugin và cập nhật GUI.
 Nếu điều kiện đăng nhập được thiết lập, đánh giá biểu thức (mặc định là `1`);
 Nếu điều kiện lệnh được thiết lập, đánh giá biểu thức (mặc định để phá vỡ điều kiện );
 Nếu tình trạng lỗi được đánh giá `1`(hoặc bất kỳ giá trị nào khác với '0'):
 In thông điệp tường trình chuẩn;
 Thực hiện gọi lại plugin.
 Nếu văn bản ghi được đặt và điều kiện đăng nhập được đánh giá để `1`(hoặc bất kỳ giá trị nào khác với '0'):
 Định dạng và in bản ghi văn bản (xem String Formatting ).
 Nếu văn bản lệnh được thiết lập và điều kiện lệnh được đánh giá là` 1`:
 Đặt biến hệ thống` $breakpointcondition`thành điều kiện ngắt ;
 Đặt biến hệ thống` $breakpointlogcondition`cho điều kiện đăng nhập ;
 Thực hiện lệnh trong văn bản lệnh ;
 Các điều kiện break sẽ được thiết lập để giá trị` $breakpointcondition`. Vì vậy, nếu bạn sửa đổi biến hệ thống này trong kịch bản, bạn sẽ có thể kiểm soát xem  *debuggee* có bị hỏng hay không.
 Nếu tình trạng lỗi được đánh giá `1`(hoặc bất kỳ giá trị nào khác với '0'):
 Break debuggee và đợi người dùng tiếp tục.

 - Bp này cũng là một dạng conditional bp tuy nhiên cao cấp hơn một chút. Nó có thêm tùy chọn cho phép ta lưu “vết” giá trị của biểu thức hoặc các tham sổ của function mỗi khi xảy ra bp hoặc khi thỏa mãn điều kiện mà ta đặt ra. Những thông tin này được lưu lại trong cửa sổ Log(L) của Olly.

 - khi đặt Conditional Log Breakpoints ta sẽ có 1 cửa sổ dạng như thế này .
 ![4](http://i.imgur.com/SN5fgIO.png)

 - với các tùy chọn:
 <ul>
 <li> 1) dòng đầu tiên ứng với conditional , nó là điều kiện để olly ngừng thực thi khi điều kiện đúng , các dòng explanation và expression củng là điều kiện , cụ thể thế nào thì tùy vào mỗi hoàn cảnh ta có cách cách đặt khác nhau .</li>
 <li> 2) các tùy chọn pause program tức là có dừng chương trình không , có 3 phương án cho bạn lựa chọn always , never hoặc on condition là tương đương với luôn luôn , không bao giờ hay thỏa mãn điều kiện mới dừng . log value of expression là có ghi lại giá trị hay không và log fuction arguments là lưu lại cấu trúc không . </li>
 </ul>

#### 7.  Message Breakpoints<a name="7"></a>

 - theo như olly định nghĩa :

 `Message breakpoint is same as conditional logging except that OllyDbg automatically generates condition allowing to break on some message (like WM_PAINT) on the ep to w procedure. You can set it in the Windows window`

 - breakpoint message  cũng giống như log breakpoint , ngoại trừ OllyDbg tự động tạo ra điều kiện cho phép phá vỡ một message (như WM_PAINT) trên thủ tục entry point của cử sổ  . Bạn có thể đặt nó trong cửa sổ Windows

 - Các thông báo trong Windows được sử dụng để truyền đạt hầu hết các sự kiện, ít nhất ở các cấp cơ bản. Nếu bạn muốn một cửa sổ hoặc kiểm soát (đó là một cửa sổ chuyên biệt) làm một cái gì đó, bạn phải gửi một tin nhắn cho nó. Nếu một cửa sổ khác muốn bạn làm điều gì đó, thì nó sẽ gửi một thông báo cho bạn. Nếu một sự kiện xảy ra, chẳng hạn như khi người dùng di chuyển con chuột, nhấn bàn phím, vv .. hệ thống sẽ gửi một thông báo đến cửa sổ bị ảnh hưởng. Cửa sổ này nhận được thông điệp và hành động phù hợp. "
 
 - ta đi tìm hiểu sâu vào xem thử messge windown là cái gì mà lại ghê gớm vậy . trên net cho ta một vài thông tin hữu ích.

 - Message là một trong những phương tiện giao tiếp quan trọng nhất trong môi trường Windows. Lập trình trong Windows chủ yếu là đáp ứng lại những sự kiện. Message có thể báo hiệu nhiều sự kiện gây ra bởi người sử dụng, hệ điều hành, hoặc chương trình khác.

 - Có hai lọai message: window message và thread message:

 - **Window Message:** Tất cả các message đều được trữ trong một Message Queue(một nơi trong bộ nhớ). Những message này sau đó sẽ được luân chuyển giữa các ứng dụng.

 - **Message Loop:** -Bạn có thể gọi những message bằng cách tạo ra một Message Queue. Message Loop là một vòng lặp kiểm tra những message trong Message Queue. Khi một message được nhận, Message Loop giải quyết nó bằng cách gọi Message Handler (một hàm được thiết kế để giúp Message Loop xử lý message) *hầu hết công việc lập trình của mình sẽ tập trung vào đây*

 - Message Loop sẽ kết thúc khi nhận được message WM_QUIT (lúc người dùng chọn File/Exit hoặc click vào nút Close hoặc bấm Alt+F4)

 - Khi bạn tạo cửa sổ (ứng dụng) Windows sẽ tạo cho bạn một Message Handler mặc định. Bạn sẽ vào đây để sửa chữa giúp ứng dụng phản hồi lại những sự kiện theo ý bạn muốn để tạo ra chương trình của bạn.

 - Tất cả những control chuẩn đều như thế. Lấy một button làm ví dụ: khi nó nhận WM_PAINT message nó sẽ vẽ button; khi bạn click chuột trái lên nó sẽ nhận WM_LBUTTONDOWN message và nó sẽ vẽ hình button bị nhấn xuống, khi buông chuột ra nó nhận WM_LBUTTONUP message và sẽ vẽ lại button bình thường. điểm đặc biệt ở đây là Tên của window message thường có dạng WM_ và hàm để xử lý message đó thường có dạng On. Ví dụ hàm xử lý WM_SIZE message là OnSize.

 - Message thường có hai tham số lưu trữ thông tin về sự kiện(32 bit): lParam kiểu LONG và wParam kiểu WORD. Ví dụ: WM_MOUSEMOVE sẽ trữ tọa độ chuột trong một tham số còn tham số kia sẽ có cờ hiệu để ghi nhận trạng thái của phím ATL, Shift, CTRL và những nút trên con chuột. Message có thể trả về một giá trị giúp bạn gửi dữ liệu ngược trở về chương trình gửi nó. Ví dụ: WM_CTLCOLOR message chờ bạn trả về một HBRUSH (khi dùng AppWizard để tạo nhanh ứng dụng bạn chú ý những hàm có dạng On... và nhận xét những kiểu trả về của nó, bạn sẽ hiểu hơn về cơ chế liên kết giữa các thành phần ứng dụng với nhau)

 - ứng dụng của loại breakpoint này là gì : giả sử khi chạy 1 chương trình mà nó yêu cầu nhập 1 mật khẩu sau đó chương trình sẽ tiến hành làm 1 việc gì đó với mật khẩu để so sánh với mật khẩu đúng . Nhiệm vụ của ta lúc này là tìm mật khẩu đúng để nhập vào nên ta sẽ bắt đầu từ cái lúc ta nhập face serial vào . ta có thể đặt breakpoint tại hàm GetDigItemTextAvà tìm đến địa chỉ buffer chứa đoạn face serial và đặt breakpoint memory on access . nhưng cách đó cũ rồi bây giờ ta có cách hay hơn đó là đặt 1 Message breakpoint khi ta nhấn vào nút `OK` để nhập vào thì chương trình break luôn và sẽ lần mò từ đó . 

 - Không giống như đặt BP tại các hàm API là ta có thể đặt BP tại đầu hàm thì để đặt Message BP chúng ta phải làm việc với cửa sổ Windows.khi chương trình của chúng ta đang thực thi, chúng ta chuyển qua cửa sổ Window bằng cách nhấn vào nút W .Sau khi bạn nhấn vào nút W thì cửa sổ Windows sẽ hiện ra. Nếu như bạn thấy nó trống trơn không có gì cả thì nhấn chuột phải và chọn` Actualize` .

 - như ta đã nói , mục đích của ta là khi ta nhấn vào nút `OK` thì chương trình sẽ break nên ta thấy tại phần `class` ta thấy `button` và trên phần `title` ta lại để ý có `OK` vậy ta chọn vào đây và nhấn chuột phải chọn `Message Breakpoint on ClassProc` nó sẽ xổ ra 1 đống thứ cho bạn lựa chọn , ở đây vì chỉ muốn bắt tín hiệu button nên ta chọn `button` . và nhấn ok 

 - suy nghĩ thêm ta củng có thể chọn điều kiện khác . để ý khi ta nhấn nút `ok` thì chương trình sẽ gửi 1 thông điệp **WM_LBUTTONDOWN** nghĩa là ta nhấn chuột trái xuống . Lúc ta nhả chuột thì chương trình cũng sẽ gửi một thông điệp là **WM_LBUTTONUP**. vậy ta sẽ đặt điều kiện để olly bắt lấy thông điệp **WM_LBUTTONUP** và dừng chương trình lại mã số của nó trong thanh cuộn message là `202 *WM_LBUTTONUP`.

 - Qua đây ta thấy rằng không cần sử dụng đến phương pháp đặt bp tại hàm API ta cũng có thể thông qua Messages BP để lần tới những điểm quan trọng. Tuy nhiên, giả sử trong một trường hợp nào đó mà chương trình không sử dụng hàm API để lấy text do ta nhập vào thì ta làm thế nào, lúc đó ta cần sử dụng đến Messages BP để tiếp cận mục tiêu

 - các message breakpoint củng được quản lý ở cửa sổ breakpoint (B) . ta thử vào cửa sổ breakpoint để xem khi ta nhấn vào button `ok` thì chính xác điều gì đã xảy ra và tại sao olly lại có thể bắt được thông điệp đó , nhấn chuột phải và chọn `edit condition` ta sẽ thấy thì ra bản chất của Message BP thực ra là một Conditional Log BP, trong đó điều kiện để dừng sự thực thì của chương trình là [ESP+8]==WM_LBUTTONUP (tức là [ESP+8]==202). vậy thì các message khác củng củng tương tự thôi đúng không nhỉ ^^ . 

 - củng cần nói thêm Ngoài việc để kiểm soát toàn bộ các Message cho tất cả các chương trình chúng ta có thể đặt một BP conditional log tại các hàm APIs chuyên kiểm soát các Messages. Hai hàm API đó là `TranslateMessage` và `DefWindowProcA` .


hết
============
