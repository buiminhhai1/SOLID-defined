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
