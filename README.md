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

Hai điều kiện thoạt nghe có vẻ mâu thuẫn. Theo lẽ thường, khi muốn thêm chức năng thì ta phải viết thêm code hoặc sữa code đã có. Đằng naỳ, nguyên lý lại hạn chế việc sửa đổi source code. Vậy làm thế naò để ta có thể
