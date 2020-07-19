---
tags: Unit Test, 單元測試的藝術筆記
---

# 單元測試的藝術第二章 - 第一個單元測試

## 前言
本章會介紹如何使用 NUnit 這個測試框架來撰寫一個簡單的單元測試.

## 單元測試框架
假設你不使用任何的單元測試框架 , 那麼你可能每次新增一個功能就必須為其額外撰寫一個主控台應用程式或是測試專用 UI (不論是桌面應用或是網頁). 這額外的負擔 , 會降低你撰寫單元測試的意願. 甚至即使你願意撰寫 , 也可能影響你撰寫的單元測試的品質. 例如 : 測試程式沒有覆蓋產品程式所有重要的邏輯.

所以我們可以考慮使用測試框架來幫助我們
- 易於撰寫單元測試
- 易於執行單元測試程式以及確認其結果


### 單元測試框架提供的東西
![](https://i.imgur.com/5muSARR.jpg)

![](https://i.imgur.com/ifGJtz0.png)

### xUnit 框架
通常單元測試框架都以它們所支援的程式語言開頭加上 Unit 作為名字 , 
它們統稱為 xUnit 框架. 例如本書中使用的 .Net 單元測試框架 NUnit.
Nunit 最初是從流行的 Java 單元測試框架 JUnit 移植過來的 , 之後 Nunit 經過極大的改進 , 
已經與 JUnit 有很大的區別  

### NUnit
#### NUnit GUI (不建議使用)
![](https://i.imgur.com/lI6AOKU.jpg)
#### 安裝 NUnit
作者建議使用 NuGet 安裝
##### 不過現在使用 VS2019 , 超貼心 ~ , 直接選擇對應的專案就好
![](https://i.imgur.com/HWpfUou.png)
##### 會幫你安裝好這三個單元測試的 NuGet
![](https://i.imgur.com/dTCW3F7.png)
##### 假設你自己開一個空白專案像底下這個 , 自己安裝上述的單元測試 NuGet
![](https://i.imgur.com/Hjvn3SD.png)
##### 遇到測試專案沒有跑的問題 - 有底下的錯誤訊息
![](https://i.imgur.com/FR3kSyQ.png)
##### 記得在專案右點屬性 -> Application -> Output Type -> 選擇 Class Library
![](https://i.imgur.com/TkIWJFy.png)

## 撰寫第一個測試專案
### 測試專案的命名
![](https://i.imgur.com/dmYtHl5.png)
- 測試專案名稱 : [預測試專案名稱]+[UnitTests]  
- 測試類別名稱 : [預測試類別名稱]+[Tests]
- 測試方法名稱 : [預測試方法名稱]+[測試的情境]+[在此情境下,期望此方法執行的動作]
    - UnitOfWorkName：被測試的方法、一組方法或一組類別
    - ScenarioUnderTest：測試進行的假設條件 , 例如登入失敗、無效的使用者、密碼正確
        - 你能使用測試情境描述傳給公開方法的參數 , 或是單元測試執行時 , 程式初始化的狀態. 
        - 例如 : 「無此使用者」 、「該使用者已經存在」
    - ExpectedBehavior：在測試情境的假設條件下 , 預期方法執行的行為.
        1. 回傳一個結果值（一個真實的值或是一個 Exception）
        2. 系統狀態的改變（在系統中新增一個使用者 , 導致下次登入時系統的行為發生改變）
        3. 呼叫外部第三方系統提供的服務（與一個外部的 Web 服務進行互動）
    
### NUnit 特性：TestFixture 與 Test
- Attribute [TestFixture] 標記一個包含自動化 NUnit 測試的類別。
- Attribute [Test] 可以加在一個方法上，用來標記這個方法是一個需要被執行的自動化測試
- 詳細請參考 [NUnit 幾個參數化測試的方式](https://blog.yowko.com/nunit-parameterized-test/) 的介紹

### 單元測試程式
#### 一個單元測試通常包含三種行為
- 準備 (Arrange) 物件、建立物件、進行必要的設定
- 操作 (Act) 物件
- 驗證 (Assert) 某件事符合預期
```C# 
[Test]
public void IsValidLogFileName_BadExtension_ReturnsFalse()
{
    // 必要的設定
    LogAnalyzer analyzer = new LogAnalyzer();
    // 執行動作
    bool result = analyzer.IsValidLogFileName("filewithbadextension.foo");
    // 驗證執行動作後 , 結果是否符合預期
    Assert.False(result);
}
```
#### Assert 類別
Assert 類別屬於 NUnit.Framework 的命名空間裡 , 提供簡單易使用的靜態方法 , 幫助我們驗證結果

例如 
- Assert.IsTrue(result) // 驗證 input 是否為 true
- Assert.IsFalse(result) // 驗證 input 是否為 false
- Assert.AreEqual(expectedValue,result)  // 驗證結果值是否相等
- ...

相比 Assert , 我個人比較喜歡使用 Fluent Assertions 這個測試輔助套件 XD

#### 範例
```C# 
// 被測試的程式
// 判斷檔案名稱是否有效(以 .SLF 結尾) , 若有效 , 回傳 True
public class LogAnalyzer
{
     public bool IsValidLogFileName(string fileName)
     {
         if (fileName.EndsWith(".SLF")) // INCORRECT HERE , Missing !
         {
             return false;
         }
         return true;
     }
}
```
##### 若是有寫測試 , 就能發現寫錯了 !!!
```C# 
[Test]
public void IsValidLogFileName_BadExtension_ReturnsFalse()
{
    LogAnalyzer analyzer = new LogAnalyzer();
    bool result = analyzer.IsValidLogFileName("filewithbadextension.foo");
    Assert.False(result); // 上述程式會回傳 True , 與期望結果不同 , 因此 NUnit 會報錯
}
```
![](https://i.imgur.com/PjTu8QZ.png)

##### 把 ! 補回去後 , 會發現變綠燈了.
代表目前程式可以辨認錯誤的副檔名.
![](https://i.imgur.com/d3pj7Uz.png)

但不代表程式也可以辨認正確的副檔名.
因此要再補上兩個測試驗證
```C#
[Test]
public void IsValidLogFileName_GoodExtensionLowercase_ReturnsTrue()
{
    LogAnalyzer analyzer = new LogAnalyzer();
    bool result = analyzer.IsValidLogFileName("filewithgoodextension.slf");
    Assert.True(result);
}

[Test]
public void IsValidLogFileName_GoodExtensionUppercase_ReturnsTrue()
{
    LogAnalyzer analyzer = new LogAnalyzer();
    bool result = analyzer.IsValidLogFileName("filewithgoodextension.SLF");
    Assert.True(result);
}
```
##### 發現還有錯誤 , 程式沒辦法正確判斷小寫的副檔名
![](https://i.imgur.com/7g9BrJK.png)

改動程式碼 , 改用不分大小寫的字串比對
```C#
public bool IsValidLogFileName(string fileName)
{
    return fileName.EndsWith(".SLF", StringComparison.OrdinalIgnoreCase);
}
```
##### 測試成功
![](https://i.imgur.com/rsNlLp7.png)

上述執行的動作為 TDD 流行的紅綠燈的概念
- 紅燈(有測試失敗)
    - 若是有未預期的例外拋出 , 測試也算是失敗
    - 若是有預期的例外未拋出 , 測試也算是失敗
- 綠燈(測試全部通過)
- 重構
從失敗的測試開始 , 逐步修改程式碼 , 使其通過測試 , 再修改程式碼使其可讀或是更容易維護

#### 測試程式風格
撰寫測試程式時 , 可讀性是最重要的考量之一 . 
就算是未看過這個測試的人 , 亦可用最短的時間精確地了解這個測試程式.

#### 使用參數重構測試
IsValidLogFileName_GoodExtensionUppercase_ReturnsTrue 以及
IsValidLogFileName_GoodExtensionLowercase_ReturnsTrue 這兩個測試程式測試的概念其實差不多 ,
兩者唯一的差異是輸入的參數不同. 因此可以使用參數的方式合併為一隻.
- 未來當 LogAnalyzer 要修改時 , 例如: 增加一個建構子參數.
    - 你需要修改的測試程式也會由兩支變為一支.
    - 假設你從不試圖減少測試的方法 , 想像一下 , 若你有一百支測試程式要修改 , 你會.........
##### 步驟
- 把特性 [Test] 換成 [TestCase]
- 把測試中所寫死的值 , 替換持這個測試方法的參數
- 把被替換掉的名字放到特性中的括號裡，如 [TestCase(param1, param2, ...)]
- 用一個比較共用的名字
- 把這個測試方法上 , 對每個需要合併的測試方法 , 用其測試值新增一個 [TestCase(...)] 特性
- 移除其他測試 , 只保留這一個帶有多個 [TestCase] 特性的測試方法
##### 成果
```C# 
// this is a refactoring of the previous two tests
[TestCase("filewithgoodextension.SLF")] // TestCase 會將 () 內的 result 作為參數 file 傳給測試方法
[TestCase("filewithgoodextension.slf")] // 有兩個 TestCase , 代表此測試方法會執行兩次 (.SLF & .slf 各傳入一次)
public void IsValidLogFileName_ValidExtensions_ReturnsTrue(string file)
{
    LogAnalyzer analyzer = new LogAnalyzer();
    bool result = analyzer.IsValidLogFileName(file);
    Assert.True(result);
}
```
##### 繼續加入負面的測試
```C#
// this is a refactoring of all the "regular" tests
[TestCase("filewithgoodextension.SLF",true)] // 傳入 file & expected
[TestCase("filewithgoodextension.slf",true)]
[TestCase("filewithbadextension.foo",false)]
public void IsValidLogFileName_VariousExtensions_ChecksThem(string file, bool expected)
{
    LogAnalyzer analyzer = new LogAnalyzer();
    bool result = analyzer.IsValidLogFileName(file);
    Assert.AreEqual(expected, result);
}
```
有了這個測試 , 原本的三個測試方法就能夠移除了. 因為已經被包含在這支測試方法的範圍內了.

需要注意的是 , 使用這個方法會使得測試方法名稱變得通用.
- 所以若是合併後無法依據測試名稱去判斷哪種情況是有效的或是無效的 , 就不建議使用這個方法合併測試.
- 傳入的參數需要簡單明瞭

### Setup & Teardown
- [Setup] 可以像 [Test] 一樣 , 加在一個方法上 , NUnit 每次在執行測試類別裡的任何一個測試方法之前 , 都會先呼叫標記 [Setup] 的方法
- [TearDown] 標記這個方法應該在每個測試執行完畢之後被呼叫

![](https://i.imgur.com/JoDOsPv.png)
- 可以參考 [单元测试与Nunit的基本使用](https://www.cnblogs.com/zwt-blog/p/5788222.html) 的介紹
##### 作者不建議使用 , 若用到 , 則可能寫的是整合測試. 作者建議使用工廠模式。（詳情作法會在第七章 - 測試階層和組織 ）


```C#
private LogAnalyzer m_analyzer = null;
[SetUp]
public void SetUp() // setup 特性
{
    m_analyzer = new LogAnalyzer();
}
[Test]
public void IsValidFileName_validFileLowerCased_ReturnsTrue()
{
    bool result = m_analyzer.IsValidLogFileName("whatever.slf");
    Assert.IsTrue(result, "filename should be valid");
}
[Test]
public void IsValidFileName_validFileUpperCased_ReturnsTrue()
{
    bool result = m_analyzer.IsValidLogFileName("whatever.SLF");
    Assert.IsTrue(result, "filename should be valid");
}
[TearDown]
public void TearDown() // TearDown 特性
{
    // 反模式，不要在實作上這樣做
    m_analyzer = null;
}
```
#### TestFixtureSetUp & TestFixtureTearDown
- NUnit 還包含其他輔助設定和清理系統狀態的特性. 例如 [TestFixtureSetUp] 和 [TestFixtureTearDown] , 可以在該測試類別裡的測試執行前/後 , 進行狀態的設定.

### 驗證預期的例外
程式會拋出例外 , 如何驗證 ?!
```C#
if (string.IsNullOrEmpty(fileName)
{
    throw new ArgumentException("filename has to be provided");
}
```
##### [ExpectedException] 過去流行使用 , 現在已不支援使用. 
- ExpectedException 會將整個方法用 try-catch 包起來 , 因此若有錯誤發生 , 會無法判斷是方法內的哪一行導致的.
```C#
[Test]
[ExpectedException(typeof(ArgumentException),
      ExpectedMessage = "filename has to be provided")]
public void IsValidLogFileName_EmptyFileName_ThrowsException()
{
    LogAnalyzer la = MakeAnalyzer();
    la.IsValidLogFileName(string.Empty);
}
```

##### NUnit 提供了一個更新的 API：Assert.Throws<T>(delegate)
```C#
[Test]
public void IsValidLogFileName_EmptyFileName_Throws()
{
    LogAnalyzer la = MakeAnalyzer();
    var ex = Assert.Throws<ArgumentException>(() => la.IsValidLogFileName(""));
    StringAssert.Contains("filename has to be provided", ex.Message);
}

// NUnit 流利語法 - Assert.That 開頭
[Test]
public void IsValidLogFileName_EmptyFileName_ThrowsFluent()
{
    LogAnalyzer la = MakeAnalyzer();
    var ex = Assert.Throws<ArgumentException>(() => la.IsValidLogFileName(""));
    Assert.That(ex.Message, Is.StringContaining("filename has to be provided"));
}
```
1. 如果 Lambda 中的程式碼拋出了一個符合預期的例外 , 測試就會通過. 反之 , 就會失敗。
2. Assert.Throws 函數會回傳 Lambda 內所拋出的例外的物件 , 你可以後續對這個例外進行驗證.
3. 使用 StringAssert(NUnit 提供 , 簡化字串驗證的輔助函數) 增加可讀性
4. 沒有用 Asser.AreEqual 來進行完整字串驗證 , 而是使用 StringAssert.Contains , 因為隨著程式碼功能越來越多 , 字串內容經常發生變化 , 使用 StringAssert.Contains 更容易維護

- 書上程式碼範例是寫 Catch , 但作者提供的程式碼範例卻是用 Throws = =
- 請參考 [When to use Assert.Catch versus Assert.Throws in Unit Testing](https://stackoverflow.com/questions/31971918/when-to-use-assert-catch-versus-assert-throws-in-unit-testing)

### 忽略此測試
有時候測試有問題 , 但又希望盡快進版. 可以使用 Ignore Attribute
```C#
[Ignore]
[Test]
public void IsValidLogFileName_EmptyFileName_Throws()
{
    LogAnalyzer la = MakeAnalyzer();
    var ex = Assert.Throws<ArgumentException>(() => la.IsValidLogFileName(""));
    StringAssert.Contains("filename has to be provided", ex.Message);
}
```
![](https://i.imgur.com/nfHvXWR.png)

### 設定測試分類
你可以將測試 , 按照指定的測試分類來執行. 例如：執行快慢. 
使用 [Category] 特性可以完成此功能
```C#
 [Category("Fast Tests")]
[Test]
public void IsValidLogFileName_EmptyFileName_Throws()
{
    // ...
}

[Category("Fast Tests"),]
[Test]
public void IsValidLogFileName_EmptyFileName_ThrowsFluent()
{
    // ...
}
```
##### Reshpaer 的測試專案分類 , 出現了 Fast Tests .
![](https://i.imgur.com/QPZipCj.png)

### 測試系統狀態的改變 , 而非驗證回傳值
依據上一章[工作單元的定義](https://hackmd.io/ioqU9RJIRzeGt-sedOV5aw?view#%E5%B7%A5%E4%BD%9C%E5%96%AE%E5%85%83%E7%9A%84%E5%AE%9A%E7%BE%A9%E5%A6%82%E4%B8%8B)
工作單元的三種最終結果：
- 回傳值 (在上一章有介紹如何驗證)
- 系統狀態的改變
- 第三方系統

接下來我們將會討論第二種驗證方式：系統狀態的改變 — 驗證被測試系統 , 在執行某個動作的前後 , 其行為所發生的變化是否符合預期.

> 基於狀態的測試(狀態驗證) : 透過檢查被測試系統及與之協作的事物(依賴的物件或第三方系統). 在被測試方法執行後 , 其行為或是狀態的改變 ,來驗證測試方法是否正常運作

##### 透過呼叫 IsValidLogFileName 來測試 WasLastFileNameValid 屬性的值
```C#
// LogAnalyzer 會透過屬性 WasLastFileNameValid 記住最後一次的執行結果
public class LogAnalyzer
{
    public bool WasLastFileNameValid { get; set; }
    public bool IsValidLogFileName(string fileName)
    {
        WasLastFileNameValid = false;
        if (string.IsNullOrEmpty(fileName))
        {
            throw new ArgumentException("filename has to be provided");
        }
        if (!fileName.EndsWith(".SLF",StringComparison.CurrentCultureIgnoreCase))
        {
            return false;
        }
        WasLastFileNameValid = true;
        return true;
    }
}
```
- 因為 WasLastFileNameValid 的值是基於 IsValidLogFileName() 被呼叫的執行情況. 所以無法透過 IsValidLogFileName() 的回傳值來驗證 , 關於 WasLastFileNameValid 是否有被正確設定. 所以你需要用另外的方法來驗證這段邏輯是否正常.

##### 測試程式
```C#
[TestCase("badfile.foo", false)]
[TestCase("goodfile.slf", true)]
public void IsValidLogFileName_WhenCalled_ChangesWasLastFileNameValid(string file,
                                                                    bool expected)
{
    LogAnalyzer la = MakeAnalyzer();
    la.IsValidLogFileName(file);
    Assert.AreEqual(expected, la.WasLastFileNameValid);
}
```
- 驗證執行 IsValidLogFileName() 時 , WasLastFileNameValid 是否有被正確設定.

### 命名的延伸
- 如果沒有前置動作而有預期的回傳值 , 可以用 ByDefault , 如 Sum_ByDefault_ReturnsZero
- 針對第二種或第三種結果（改變狀態或呼叫第三方系統） , 如果不需要進行前置設定 , 可以用 WhenCall 或 Always。如 Sum_WhenCalled_CallsTheLogger 或 Sum_Always_CallsTheLogger


## 總結
- 使用 [TestCase] 重構測試. - 移除已被包含的測試.
- 利用 [SetUp] 和 [TearDown] 來確保測試前後的狀態是乾淨的
- 使用工廠模式來讓測試更好維護
- 使用 [Ignore] 來略過有問題的測試
- 使用 [Category] 對測試方法分類. 如：Fast Tests
- Assert.Throws() 確保程式在預期時候拋出例外
- 如果測試的邏輯 , 無法透過其方法的回傳值驗證 , 你可以考慮測試物件互動後的系統狀態
- 建立測試類別、專案和方法的慣例 ： 
    1. 對每個待測試類別建立對應的測試類別
    2. 對每個帶測試的專案建立一個測試專案
    3. 對每個工作單元建立至少一個測試方法
- 使用 [UnitOfWorkName]_[ScenarioUnderTest]_[ExpectedBehavior] 讓測試命名更好懂
- 測試方法間皆會使用到的物件 , 可考慮使用工廠模式 , 讓測試方法能共同使用. 減少重複的設定.
    - 例如 : 初始化或建立所有測試都要使用到的物件
- 盡量不要使用到 [SetUp] 和 [TearDown]. 它們會讓可讀性變差 , 多加利用工廠模式.

---

## 參考
[When to use Assert.Catch versus Assert.Throws in Unit Testing](https://stackoverflow.com/questions/31971918/when-to-use-assert-catch-versus-assert-throws-in-unit-testing)     
[单元测试与Nunit的基本使用](https://www.cnblogs.com/zwt-blog/p/5788222.html)         
[NUnit - 測試案例生命週期 Life Cycle](https://blog.johnwu.cc/article/nunit-life-cycle.html)         
[NUnit 幾個參數化測試的方式](https://blog.yowko.com/nunit-parameterized-test/)         
[編寫單元測試時的好用輔助套件 - Fluent Assertions](https://kevintsengtw.blogspot.com/2015/11/fluent-assertions.html)         
[[UnitTest] 使用 Fluent Assertions 增加單元測試碼可讀性](https://marcus116.blogspot.com/2019/04/unittest-fluent-assertions.html)         
[[C#.NET] 用了 Fluent Assertions，我的測試程式碼也會說話](https://dotblogs.com.tw/yc421206/2015/06/20/151606)         
### Thank you!     

You can find me on

- [GitHub](https://github.com/s0920832252)
- [Facebook](https://www.facebook.com/fourtune.chen)

若有謬誤 , 煩請告知 , 新手發帖請多包涵

# :100: :muscle: :tada: :sheep: 
