---
title: "Tổng hợp một số kiến thức khi học Golang"
date: 2018-10-19T00:00:00+07:00
draft: true
tags: ["golang"]
---
Tính toàn vẹn intergrity
- Productivity quan trọng hơn performance
- Correctness quan trọng hơn performance -> optimize for correctness instead of optimize for performance
- Readability: not hide the cost.
- Simplicity: hide the complexity.
- Word size
- Var để khởi tạo rỗng, := để khởi tạo vào gán, consistentcy
- Struct: Khai báo struct trong Go cần quan tâm đến padding & alignment, khai báo từ lớn xuống nhỏ
- Pointer:
+ Stack memory in go (goroutine stack) starts out as 2K -> chứa các stack frame, cái gì biết tính toàn đc size trong compile time thì sẽ lưu trong stack frame. Stack is self cleaning, vì vậy cố gắng sử dụng stack tối đa có thể.
+ Những gì có thể share thì không thể stayOnStack mà escapeToHeap
+ Khi nào thì dùng pointer (Khi nào thì copy khi nào thì share)
+ Nhiều goroutine chạy trên 1 thread, 1 thread dung lượng 1MB
+ Stack memory cannot be share between goroutine
+ Giữ heap ở mức 4 meg -> cần biết khi nào dùng value semantic, khi nào dùng pointer semantic
- Const 256 bit, kind (untype) hoặc type
- Khai báo biến sau đó mới sử dụng pointer, đừng start with pointer semantic -> hide the cost -> not readability

map, slice, hàm vô danh, con trỏ => tham chiếu giá trị

# 2.4 Constant

- Hằng số có 2 loại là untype (kind) và type. Kind thì linh hoạt và chính xác hơn do tận dụng tài nguyên trong compile time. Nhớ rằng constant chỉ tồn tại trong compile time.

- iota

# 2.5 Function

- & có nghĩa là share, * có nghĩa là nhận giá trị share, nên ghi nhớ ngữ nghĩa để dùng cho thích hợp, đừng thông minh mà hide the cost.

- Biến sẽ bị shadow bởi các scope nhỏ hơn, ko cần thiết phải khởi tạo biến sớm, khởi tạo biến khi dùng, để nó trong scope nhỏ nhất có thể, để đỡ tốn tài nguyên, vì sau kết thúc mỗi block code sẽ được giải phóng memory. Đồng thời khai báo biến như vậy giúp tăng khả năng readability

- Blank indentify in go _

- Coi dòng dữ liệu là trôi chảy, và khi có lỗi thì return, không cần thiết dùng else khi đúng. Chỉ cần dùng if khi sai và báo lỗi. Coi như mặc định dòng dữ liệu là đúng.

# 3.1 Data-Oriented Design

- Sử dụng array, slice, map trong Go

- Hướng dữ liệu, performance tốt là khi xác định đc dữ liệu, dữ liệu vào dữ liệu ra để tổ chức cho hợp lý => sử dụng memory hợp lý

- Row trarvese > Linked list > column => vì theo cấu trúc của cache

- Array length tạo ra type riêng ví dụ type [4]int != type [5]int

# 3.2 Array

- Dữ liệu mặc định gắn với hardware -> sử dụng tốt cho performance, tuy nhiên độ dài array ko thay đổi, đc xác định trong compile time => sinh ra slice, map (dựa trên array) đc sử dụng nhiều nhất.

- Array, string... đều trỏ đến 1 backing array qua pointer. Vì vậy khi thao tác trên array, string thì sử dụng value semantic để tạo copy là đủ, mà ko cần dùng pointer semantic, vì ở bên trong cấu trúc array, string... đã là pointer semantic trỏ đến một backing array ở phía sau rồi.

# 3.3.1 Slice 

- Cấu trúc tương tự string

- Base on backing array *,l,c

- array, slice, map, channel, interface => reference type => sử dụng make. String ko phải

- Khai báo empty slice nên sử dụng var => nil, 0, 0. Nếu dụng := []string{} sẽ tạo ra *,0,0

- Khi append quá sức chứa, thì sẽ tạo 1 slice mới với sức chứa gấp đôi nếu nhỏ hơn 1024, tăng khoảng 25% nếu > 1024. Cẩn thận khi append quá sức chứa sẽ tạo 1 backing array mới, mà các pointer trỏ đến giá trị cũ của slice sẽ trỏ về backing array cũ và ko nhận được giá trị ở backing array mới.

- UTF 8, 1 ký tự UTF 8 có thể mất tối đã 4 bytes

# 3.4 Map


# 4.1.1 Method

- Các primitive var như number, string, bool thì sử dụng value semantic. Nên chứa trong stack thay vì heap -> copy & copy -> not share

- Các reference type như slice, map, channel, interface cũng sử dụng value semantic. Slice chỉ sử dụng address (pointer) trong trường hợp marshall & unmarshall khi parse json.

- Struct khi không chắc chắn semantic nào thì sử dụng pointer semantic

- Các build-in func sử dụng value hay pointer semantic thì dùng theo, ví dụ hàm open file trả lại pointer *File thì các hàm sử dụng File cần sử dụng pointer semantic *File

- Function > method

# 4.2 Interface

- Struct type, Interface type

- Interface is a two word data structure. String is two, slice is three. Two pointer *,*: 1 -> type of data (có method gì), 2 -> data => call method với interface, interface sẽ tìm method của type và call với data đc lưu. Interface chỉ có một và thay đổi giá trị liên tục khi gọi đến các kiểu khớp với interface.

- Concrete type là type có thể có cho mình phương thức riêng ví dụ struct

- Khi method của data là pointer semantic thì sử dụng interface để gọi method cũng phỉa pointer semantic

- Khi method của data là value semantic thì sử dụng interface để gọi method có thể là value semantic và pointer semantic. Tuy nhiên chỉ nên sử dụng value semantic để readable, trường hợp pointer semantic chỉ dùng trong marshall và unmarshall.

- Share on heap, copy on stack

# 4.3 Embedding

- Kế thừa: kế thừa method ảnh hưởng cả interface

# 4.4 Exporting

- Đặt tên package trùng tên thư mục chứa

- Export by lower and upper case

- Export & Unexport ko hoàn toàn giống với public và private ở nn khác

# 5.1 Grouping Types

- Không design theo OOP: group theo một tiêu chí nào đấy ví dụ Dog, Cat are Animals -> subtyping. Grouping by state.

- Should Grouping by behavior

- Khai báo type cho đối tượng mới hoặc unique, không khai báo type chung chung, khi đó thì dùng interface

- embed khi cần behavior, không phải state

# 5.2 Decoupling

- Giải quyết vấn đề để chạy trước sau đó refactor. Work 2days (today) + Finish 2weeks (tomorrow, think about change) => done. Correctness => Performance

- Tách rời từng phần nhỏ trong vấn đề cần giải quyết, và xử lý nó, không cần quan tâm đến các cái khác. Solve one problem at a time.

# 5.3 Conversion & Assertion

- Type Assertions don't happen in compile time. Concrete value => interface, interface --type assertion--> concrete value. This process may fail -> panic, log, exit... Nên giảm tối thiểu việc này trong code.

# 5.4 Interface Pollution

- Đa số mọi ng xây dựng ngược: đi từ interface -> concrete value

- Khi interface và struct type có cùng tên thì khả năng có gì đó ko đúng, hoặc interface ko cần thiết ở đây

# 6.2 Error variables

- Switch error bằng cách đặt variable name cho error

# 6.5 Find the bug

- Luôn phải trả lại error interface khi muốn lấy lỗi ở biến thứ 2, ko thể dùng bất kì concrete type nào khác

reference types: slice, array, map, channel, interface