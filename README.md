# The_Art_of_Unit_Testing
單元測試的藝術讀後心得

- [單元測試的藝術第一章](https://hackmd.io/ioqU9RJIRzeGt-sedOV5aw?view)
- [單元測試的藝術第二章](https://hackmd.io/IXPCBdUAQMibiD3XbqHsvg?view)



# 疑惑
- 什麼時候使用參數化(例如 TestCase ) , 有沒有一定可以使用 , 或是一定不能使用的準則

例如 輸入參數為 
```C# 
Mid_Face_Data = null 以及
Mid_Face_Data = {
  Threshold = null
}
```
這兩種情況的時候 , 我程式的處理方式都是一樣的.

我是否可以使用餐數化設計?  還是應該拆開成兩個測試方法 !?
```C# 
public void Method_ParamIsNull_ReturnError([Values] bool hasMidData){
  var faceData = hasMidData ? new MidFaceData(){
      Thresold = null,
  }: null;  
  
  Call Method(faceData);
  
  略
}
```

# 疑惑2
- 甚麼時候完成單元測試 ?
假設一個方法有兩個參數
```C# 
//  資料來源皆為 FromForm
// JsonBinder.cs 把字串轉成 JsonObj
// ImageBinder.cs 把檔案(IFormFile)轉成對應類別
// CreateRequest.cs Model (儲存對應的資料 - 由 JsonObj 轉成)
// ImageSomething.cs 檔案轉成對應類別的儲存實體
public IActionResult Create(
        [ModelBinder(BinderType = typeof(JsonBinder), Name = "jsonStr")] CreateRequest createRequest,
        [ModelBinder(BinderType = typeof(ImageBinder), Name = "oneImage")] ImageSomething thumbnail
        )
```

我再完成 Create 的期間 , 可能因為亙本還沒有很全面的了解 , 所以會頻繁修改原本 ImageSomething 或是其他類別的實作.
這個時候如果我選擇的是先完成 JsonBinder ImageBinder CreateRequest ImageSomething 的單元測試 , 就也需要頻繁的修改這些單元測試

# 疑惑 3
假設我有多個單元測試都需要設定多個相依物件 , 是否應該替每一個相依物件建立一個產生實例的方法 , 還是直接使用一個方法產生多個實例
```C# 
[Test]
public void MyTest(){
var fakeNameChecker = new Mock<INameChecker>
fakeNameChecker.Setup(...)
略...

var fakeDominService = new Mock<IDominService>
fakeDominService.Setup(...)
略...

var fakeMathService = new Mock<IMathService>
fakeMathService.Setup(...)
略...

var controller = new MyController(fakeNameChecker.Object , fakeDominService.Object , fakeMathService.Object)

...

}
```
```C#
方案1
public class NameCheckerUtility{
  public static INameChecker GetNameChecker(一些參數用來初始化NameChecker){
     略...
  }
}
public class fakeDominServiceUtility{
  public static IDominService GetNameChecker(一些參數用來初始化fakeDominService){
     略...
  }
}
public class fakeMathServiceUtility{
  public static IMathService GetNameChecker(一些參數用來初始化fakeMathService){
     略...
  }
}
```
```C#
方案2
public (INameChecker , IDominService , IMathService) GetControllerParam(){
    初始化 INameChecker , IDominService , IMathService , 然後用 Tuple 回傳 
} 
```

# 疑惑4
假設我限制使用者只能傳 jpeg 圖片給我 , 若不是 jpeg 圖片 , 我會回傳 ErrorResponse 給它. 以及不能將這張圖片存入 DB.
所以我的測試就可能要寫成下列這樣嗎...?
```C#
[Test]
public void MethodName_WhenImageNotJpeg_ReturnWrongImageFormatError{
    # Arrange
    # Act
    # Assert
    1. 驗證 Response Code
    2. 驗證 Response Json Str
    3. 驗證 Db 是否沒有新增圖片
} 
```

