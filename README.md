# The_Art_of_Unit_Testing
單元測試的藝術讀後心得

[單元測試的藝術第一章](https://hackmd.io/ioqU9RJIRzeGt-sedOV5aw?view)

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
