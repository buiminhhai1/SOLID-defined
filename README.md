# SOLID-defined
Basic SOLID for dev

## Single Responsibility Principle - Nguyên lý đơn trách nhiệm.
### Nội dung nguyên lý: 
```python
Một class chỉ nên giữ 1 trách nhiệm duy nhất
```
### Giải thích nguyên lý: 
Ta có thể tạm hiểu "Trách nhiệm" ở đây là tương đương với chức năng. Tại sao một class nên chỉ giữ một chức năng duy nhất? để hiểu điều này, hãy nhìn ở dưới.
Hãy xem con dao trong hình như một class với rất nhiều chức năng. Con dao này có vẻ khá là tiện dụng, nhưng lại cồng kềnh và nặng nề. Đặc biệt, khi có một bộ phần hư hỏng, ta phải tháo nguyên con dao để sửa. Việc sửa chữa và cải tiến rất phức tạp, có thể ảnh hưởng tới nhiều bộ phận khác nhau.

Một class có quá nhiều chức năng cũng sẽ trở nên cồng kềnh và phức tạp. Trong ngành IT, requirement rất hay thay đổi, dẫn tới sự thay đổi code. Nếu một class có quá nhiều chức năng, quá cồng kềnh, việc thay đổi sẽ rất khó khăn, mất nhiều thời gian, còn dễ gây ra ảnh hưởng tói các module đang hoạt động khác.

Áp dụng SRP vào con dao phía trên, ta có thể tách nó ra làm kéo, dao, mở nút chai, ... riêng biệt là xong. Cái gì hư thì t chỉ cần sửa cái đấy. Với code cũng vậy, a chỉ cần thiết kế các module sao cho đơn giản, một module chỉ có 1 chức năng duy nhất là xong (Nói vậy chứ việc xác định, gom nhóm chức năng không hề dễ đâu).

Ví dụ minh họa: đoan code dưới đây vi phạm SRP. Class Student có quá nhiều chức năng.

```python
public class Student {
  public string Name {get; set;}
  public int Age {get; set;}
   // Format class này dưới dạng text, html, json để in ra
  public string GetStudentInfoText() {
    return "Name: " + Name + ". Age: " + Age;
  }
  public string GetStudentInfoHTML() {
    return "<span>" + Name + " " + Age + "</span>";
  }
  public string GetStudentInfoJson() {
    return Json.Serialize(this);
  }
  // Lưu trữ xuống database, xuống file
  public void SaveToDatabase() {
    dbContext.Save(this);
  }
  public void SaveToFile() {
    Files.Save(this, "fileName.txt");
  }
}
```

Code như thế thì có làm sao không? Hiện tại thì không sao cả, nhưng khi code lớn dần, thêm chức năng nhiều hơn, class Student sẽ bị phìn to ra. Chưa kể, nếu như có thêm các class khác như Person, Teacher vvv đoạn code hiển thị/ lưu trữ thông tin sẽ nằm rải rác ở nhiều class, rất khó sửa chửa và nâng cấp

Để giải quyết ta chỉ cần tách ra làm nhiều class, mỗi class có một chức năng riêng là xong. Khi cần nâng cấp, sửa chữa, sẽ diễn ra ở từng class, không ảnh hưởng tói các class còn lại.

```python
public class Student {
  public string Name {get; set;}
  public int Age {get; set;}
}

// Class này chỉ format thông tin hiển thị student
public class Formatter {
  public string FormatStudentText(Student std) {
    return "Name: " + std.Name + ". Age: " + std.Age;
  }
  public string FormatStudentHtml(Student std) {
    return "<span>" + std.Name + " " + std.Age + "</span>";
  }
  public string FormatStudentJson(Student std) {
    return Json.Serialize(std);
  }
}

// Class này chỉ lo việc lưu trữ
public class Store {
  public void SaveToDatabase(Student std) {
    dbContext.Save(std);
  }
  public void SaveToFile(Student std) {
    Files.Save(std, "fileName.txt");
  }
}
```
### Note: Không phải lúc nào cũng nên áp dụng nguyên lý nào vào code. 
Một trường hợp hay gặp là các class dạng Helper hay Utilities - các class naỳ vi phạm SRP 1 cách trắng trợn, Neues số lượng hàm ít, ta vấn có thể cho tất cả các hàm này vào chung 1 class, xét cho cùng, toàn boọ các hàm trong helper đề có chức năng xử lý các tác vụ nho nhỏ.
Tuy nhiên, khi Helper có thêm nhiều chức năng, nó trở nên phức tạp và cồng kềnh hơn. Lúc naỳ chúng ta nên áp dụng nguyên tác SRP để chia nó thành các module nhỏ để dễ quản lý.

## 2 Open - closed Principle
### Nội dung nguyên lý.
Có thể thoải mái mở rộng một module, nhưng hạn chế sửa đổi bên trong module đó 

Giải thích nguyên lý: 
* dễ mở rộng: có thể dễ dàng nâng cấp, mở rộng, thêm tính năng mới cho một module khi có yêu cầu.
* Khó sửa đổi: Hạn chế hoặc cấm việc sửa đổi source code của module sẵn có.

Hai điều kiện thoạt nghe có vẻ mâu thuẫn. Theo lẽ thường, khi muốn thêm chức năng thì ta phải viết thêm code hoặc sữa code đã có. Đằng naỳ, nguyên lý lại hạn chế việc sửa đổi source code. Vậy làm thế naò để ta có thể thiết kế một module dễ mở rộng, nhưng lại khó sửa đổi? 

Trước khi nói về lập trình, hãy cùng phân tích một vật dụng được thiết kế theo nguyên lý đóng mở. Khẩu súng. Để bắn được xa hơn, ta có thế gắn thê ống ngắm; để không gây tiếng động, ta có thể gắn nồng giảm thanh; khi cần cận chiến ta có thể găns thêm lưỡi lê. 

Dễ thấy khẩu súngn được thiết kế dễ dàng mở rộng tính năng mà không cần phải mổ xé tháo lắp các bộ phân bên trong (source code) của nos. Một module phù hợp OCP cũng nên được thiết kế như vậy

// Ví dụ minh họa.
```python
public class Shape {
}

public class Square : Shape {
  public double Height {get; set;}
}

public class Triangle : Shape{
  public double FirstSide {get;set;}
  public double SecondSide {get; set;}
  public double ThirdSide {get;set;}
}

// module in ra dien tich cac hinh 

public class AreaDisplay {
  public double ShowArea(List<Shape> shapes){
    foreach(var shape in shapes) {
      Square square (Square)shape;
      var area += Math.sqrt(square.Height);
    }
  }
}
```

Ta có 3 class là Square, Circle, Rectangle. Class AreaDisplay tính diện tích các hinhf này và in ra. Theo code cũ, để tính diện tích ta dùng hàm ì để check và ép kiểu object đưa vào, sau đó bắt đầu tính. Đễ thấy neeus trong tương  lai ta thêm nhiều class nữa, ta phải sửa class AreaDisplay, viết thêm chừng đó hàm if nưã. Sau khi chỉnh sửa, ta phải compile và deploy lại class AreaDisplay, dài dòng và dễ lỗi.

Ta chuyển module tính diện tích vào mỗi class. Class AreaDisplay chỉ việc in ra, Trong tương lai, khi thêm class mới ta chỉ việc cho class này kế thừa class Shape ban đàu. Class AreaDisplay có thể in ra diện tích của các class thêm vào mà không cần sửa gì tới source code của nó cả.

### Lưu ý và kết luận. 
Nguyên lý OCP xuất hiện mọi ngóc ngách của ngành lập trình. Một ứng dung của nguyên lý này là hệ thôngs plugin cho Elcipse VStudio, add-on Chrome. Để thêm các tính năng này, không cần can thiệp gì đế source code sẵn có.

Nguyên lý ày cunxg được áp dụng chặc chẽ khi viết các thư viện framework. Ví dụ khi  sử dụng MCV< ta không được xem hay chỉnh sửa code của các class Router, controller có sănx, nhưng có thể viết các controller mới, router mới để thêm tính năng

## .Liskov Substitution Principle
### Nội dung nguyên lý: 
trong một chương trình, các object của class con có thể thay thế class cha mà không làm thay đổi tính đúng đắn của chuong trình.

### Giải thích 


## 3. Liskov Substituation Principle 
Nội dung nguyên lý: 
```python
  Trong một chương trình, các object của class con có thể thay thế class cha mà không làm thay đổi tính đúng đắn của chương trình.
```

Giải thích nguyên lý: 
Xin cảnh báo trước một chút nguyên lý này hơi trừu tượng và khó hiểu (các bác developer nước ngoài cũng tranh cãi khá nhiều về nó), do đó mình sẽ cố gắn giải thích một cách đơn giản nhất có thể. Nếu đọc lần đầu không hiểu, các bạn cố gắn đọc kỹ lại vài lânf. 

Để giữ tính đúng đắn của chương trình, class con phải thay thế được class cha. Nói dễ hiểu là: ngày xửa ngày xưa, hẳn bạn nào cũng có một máy chơi game "Xếp hình" thầnh thánh. 

Ví dụ minh họa.
ví dụ thường gặp về việc vi phạm LSP: 

### Ví dụ thứ nhất, class con quăng exception khi gọi hàm.
Giả sử, ta muốn viết một chương trình để mô tả các loài chim bay. Đại bàng chim sẻ, vịt bay được, nhưng chim cánh cụt không bay được. Do chim cánh cụt là chim, ta cho nó kế thừa từ lớp Bird. Tuy nhiên, vì cánh cụt nên không biết bay, khi gọi hàm bay của chim cánh cụt, ta quăng NoFlyException.

```python
public class Bird {
  public virtual void Fly() { Console.Write("Fly"); }
}  

public class Eagle : Bird {
  public override void Fly() { Console.Write("Eagle Fly"); }
}

public class Duck : Bird {
  public override void Fly() { Console.Write("Duck Fly"); }
}

public class Penguin : Bird {
  public override void Fly() { throw new NoFlyException(); }
}

var birds = new List { new Bird(), new Eagle(), new Duck(), new Penguin() };
foeach(var bird in birds) bird.Fly();
// Tới pengiun thì lỗi vì cánh cụt quăng Exception
```

Ta tạo 1 mảng chứa các loài chim rồi duyệt các phần tử. Khi gọi hàm Fly của class Penguin, hàm này sẽ quăng lỗi. Class Penguin gây lỗi khi chạy, không thay thế được class cha của nó là Bird, do đó nó đã vi phạm LSP.

### Ví dụ thứ 2, class con thay đổi hành vi class châ. 
Đây là ví dụ kinh điển về hình vuông và hình chữ nhật mà mọi người thường dung để giải thích LSP, mình chỉ viết và giải thích lại đôi chút. 

Đầu tiên, hãy cùng đọc đoạn code dưới đây. Ta có 2 class cho hình vuông và hình chữ nhật. Ai cũng biết hình vuông là hình chữ nhật có 2 cạnh bằng nhau, do đó ta có thể cho class Square kế thừa class Rectangle để tái sử dụng code.

```python
public class Rectangle{
  public int Height {get; set;}
  public int Width {get; set;}
  
  public virtual void SetHeight(int height){
    this.Height = height;
  }
  
  public virtual void SetWidth(int width){
    this.Width = width;
  }
  
  public virtual int CalculateArea(){
    return this.Height * this.Width;
  }
}

public class Square : Rectangle {
  public override void SetHeight(int height){
    this.Height = height;
    this.Width = height;
  }
  public override void SetWidth(int width){
    this.Width = width;
    this.Height = width;
  }
}

Rectangle rect = new Rectangle();
rect.SetHeight(5);
rect.SetWidth(15);
Console.WriteLine(rect.CalculateArea()); // Ket qua la 5 * 15

Rectangle rect1 = new Square();
rect1.SetHeight(15);
rect2.SetWidth(5);
Console.WriteLine(rect1.CalculateArea()); // Ket qua la 5 * 5
```

Do hình vuông có 2 cạnh bằng nhau, mỗi khi set độ dài 1 cạnh thì ta set luôn độ dài cạnh còn lại. Tuy nhiên, khi chạy thử, hành động này đã thay đổi hành vi của class Rectangle, dẫn đến LSP.

Trong trường hợp này, để code không vi phạm LSP, ta phải tạo 1 class cha là class Shape, sau đó cho Square và Rectangle kế thừa class Shape này.

### Lưu ý và kết luận: 
Đây là nguyên lý dễ bị vi phạm nhất, nguyên nhân chủ yếu là do sự thiếu kinh nghiệm khi thiết kế class. Thông thường, design các class dựa theo đời thật: hình vuông là hình chữ nhật, chim là chim cánh cụt. Tuy nhiên, không thể bê nguyên văn mối quan hệ này vào code.

## 4. Dependency Inversion Principle
Nội dung nguyên lý:
```python
1.  Các module cấp cao không nên phụ thuộc vào các module cấp thấp. Cả 2 nên phụ thuộc và abstraction.
2. Interface (abstraction) không nên phụ thuộc vào chi tiết, mà người lại. (Các class giao tiếp thông qua interface, không phải thông qua implementation)
```
### Giải thích nguyên lý: 
Trong bài, mình hay dùng từ module. Trong thực tế, module này có thể là 1 project, 1 file dll, hoặc một server. Để dễ hiểu, chỉ trong bài này bạn xem 1 module là 1 class. 

Vói cách node thông thường, các module cấp cao sẽ gọi các module cấp thấp. Module cấp cao sẽ phụ thuộc và module cấp thấp, điều đó ta ra các dependency. Khi module cấp thấp thay đổi, module cấp cao phải thay đổi theo. Một thay đổi sẽ kéo theo hàng loạt thay đổi, giảm năng bảo trì code.

Nếu tuân theo DIP, các module cấp thấp lẫn cấp cao đều phụ thuộc vào một interface không đổi. Ta có thể dễ dàng thay thế, sửa đổi module cấp thấp mà không ảnh hưởng gì tới module cấp cao.

Để dễ hiểu, bạn hãy nhìn vào cái mấy cái đền trong nhà mình. Ở đây, module cấp cao chính là ổ điện, interface chính là đuôi đèn tròn, 2 module cấp thấp là bóng đèn tròn và bóng đèn huỳnh quang.
Hai module này đều kế thừa interface đuôi tròn, ta có thể dễ dàng thay đổi 2 loại bóng vì module cấp cao (ổ điện) chỉ quan tâm tói interface (đuôi tròn), không quan tâm tới implementation.

### Ví dụ minh họa: 
Code khi chưa áp dụng DIP: 
```python
public class Cart 
{
  public void Checkout(int orderId, int userId){
    // Database, Logger, EmailSender la module cap thap
    Database db = new Database();
    db.Save(orderId);
    
    Logger log = new Logger();
    log.LogInfo("order has been checkout");
    
    EmailSender es = new EmailSender();
    es.SendEmail(userId);
  }
}

```

Code sau khi da thiet ke lai, Ap dung DIP 
```python
// Interface 
public interface IDatabase {
  void Save(int orderId);
}
public interface ILogger {
  void LogInfo(string info);
}

public interface IEmailSender{
  void SendEmail(int userId);
}

// Cac module implement cac interface 
public class Logger : ILogger {
  public void LogInfo(string info){}
}

public Database : IDatabase {
  public void Save(int orderId){}
}

public EmailSender : IEmailSender {
  public void SendEmail(int userId){}
}

// Ham checkout moi se nhu sau
public void Checkout(int orderId, int userId){
  IDatabase db = new Database();
  db.Save(orderId);
  
  ILogger log = new Logger();
  log.LogInfo(Order has been checkout!");
  
  IEmailSender es = new EmailSender();
  es.SendEmail(userId);
}
```
