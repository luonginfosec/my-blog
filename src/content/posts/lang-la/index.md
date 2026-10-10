---
title: "LangLaTool-v355-Key.exe"
published: 2026-10-10
description: 'Chỉ dành cho mục đích học tập và nghiên cứu, không sử dụng vào mục đích thương mại hoặc vi phạm pháp luật.'
image: ''
tags: [Cracking]
category: 'Cracking'
draft: false 
lang: 'vi'
---

## Cái nhìn ban đầu

![alt text](images/0.png)

Trước khi đi vào crack một phần mềm nào đó, rõ ràng chúng ta cần quan tâm nó code bằng gì. Có pack bủng gì không. Nó check license như nào, logic là gì. Có những entry nào check.

Chắc chắn đó là những điều quan trọng nhất khi nhận định 1 phần mềm có khả thi trong việc crack được hay không. Đây chỉ là quan điểm cá nhân của mình trước khi bước vào crack thực sự 1 cái gì đó (cái này chắc là tùy trình độ kkkk).

Khi anh em nhìn vô phần mềm `LangLaTool-v355-Key.exe` này anh em thấy. Rõ ràng đây là 1 file code bằng C#, dotnet.

![alt text](images/1.png)

Anh em thấy được rõ ràng đây là dạng .NET Single-File Bundle. Đại loại (.NET Single-File Bundle là một cơ chế của Microsoft .NET cho phép đóng gói nhiều thành phần của ứng dụng C#/.NET vào một file .exe duy nhất, thay vì phân phối hàng chục hoặc hàng trăm file DLL riêng lẻ.) theo nguồn ChatGPT.

## Đi vào chi tiết hơn

Từ file tôi mở là `LangLaTool.deps.json`. Đây là tệp mô tả các thư viện phụ thuộc (Dependency Manifest) của ứng dụng .NET. Nó cho .NET biết ứng dụng cần những DLL nào, phiên bản gì và môi trường nào để chạy.

```text
LangLaTool.exe
     │
     ▼
LangLaTool.dll        ← Logic chính của ứng dụng
     │
     ├── System.Net.Http.dll
     ├── System.Text.Json.dll
     ├── System.Security.Cryptography.dll
     ├── System.Windows.Forms.dll
     └── Nhiều DLL khác
```

Rõ rồi thế cái LangLaTool.dll kia chính là file quan trọng nhất rồi. Thế bây giờ tư duy logic bình thường sẽ là tìm cách sửa file đó sao cho bypass được phần license rồi đóng gói lại là ngon. Hoặc tìm logic để triển khai fake server là cũng được.

Sử dụng công cụ <https://www.nuget.org/packages/sfextract> để bung hết mớ file từ exe ra.

![alt text](images/2.png)

Ngon như vậy là thành công lấy được cái dll đấy ra rồi.

![alt text](images/3.png)

Mở dnSpy rồi `Go to Entry Point` thử.

![alt text](images/4.png)

Như vậy là nó mở tiếp MainForm từ entry.

Vấn đề tiếp là source quá lớn. Nhưng mà anh em để ý source rất sạch và dễ nhìn. Ở thời điểm AI ngon như hiện tại thì rõ ràng có thể extract hết các file code liên quan ra. Rồi ném AI bảo nó phân tích logic bảo mật. Dạng kiểu tóm tắt hướng check key như nào đang triển khai như nào. Như vậy chắc chắn không bị flag. Anh em cứ phang mấy con model trung quốc là đẹp.

![alt text](images/5.png)

Tiến hành export project ra thôi.

![alt text](images/6.png)

![alt text](images/7.png)

Như vậy rõ ràng 4 file chúng ta cần target vào là LicenseValidation, LicenseSecurity, LicenseTier và MainForm.

## Tiến hành bypass license

Tiến hành quăng tiếp 4 file lên thôi.

![alt text](images/8.png)

![alt text](images/9.png)

Rồi rồi, chính là hàm này đây.

![alt text](images/10.png)

Ấn Analyze thì nó ra 4 entry kia. Vậy là nó dùng ở hẳn 4 chỗ đó.
Mình sẽ sửa lần lượt 4 entry đó để tìm cách bypass như vậy là có thể vượt qua được license.

![alt text](images/11.png)

Đầu tiên là EnsureLicense. Anh em thấy đoạn này chỉ là bool. Thế chỉ cần luôn return về true là được.

![alt text](images/12.png)

Không thơm rồi. Sửa bằng Edit Method không được. Thế phải xoay qua Edit IL Instructions rồi.

![alt text](images/13.png)

![alt text](images/14.png)

Đoạn

```text
ldc.i4.0
ret
```

Anh em hiểu đoạn này nó return 0 mà 0 thì là false thôi. :3 sửa ngay thì ldc.i4.1 là done.

![alt text](images/15.png)

Done thành công 1/4 entry.

![alt text](images/16.png)

![alt text](images/17.png)

Tiếp tục là BuildLicenseTab. Ở hàm này anh em để ý có 2 biến vô cùng quan trọng là this.license.Valid và this.license.Tier. Rõ ràng 2 biến này liên quan đến cách nhánh thành công của 1 license. Cũng như quyết định phần mềm có sử dụng được full chức năng hay không.

![alt text](images/18.png)

Anh em thấy như vậy chúng ta sẽ sửa get_Valid() và get_Tier() để luôn trả về giá trị mà chúng ta mong muốn.

![alt text](images/19.png)

Đoạn này phải luôn trả về true. Tiến hành Edit IL Instructions.

![alt text](images/20.png)

![alt text](images/21.png)

Thành công rồi nha ae.

![alt text](images/22.png)

Cái LicenseTier này rõ ràng phải trả về Full thì mới mở được hết chức năng, không bị limit.

![alt text](images/23.png)

![alt text](images/24.png)

Sửa thành enum số 2 nha ae. Tức là full đó.

![alt text](images/25.png)

Done như vậy là xong 2/4.

![alt text](images/26.png)

Đoạn này cũng chỉ là check this.license.Valid thôi.

Done như vậy là xong 3/4.

![alt text](images/27.png)

Đoạn này cũng vậy như vậy là xong 4/4.

Chúng ta đã xử lý hết logic liên quan đến check license hay các thứ liên quan đến Validate.

## Tiến hành repack

Bây giờ sau khi đã sửa xong. Chúng ta sẽ thử save lại dll đã sửa rồi build lại exe hoàn chỉnh.

![alt text](images/28.png)

Save Module thôi.

Sử dụng <https://github.com/trup40/DotNetBundlePatcher>

![alt text](images/29.png)

Đặt các file như sơ đồ.

![alt text](images/30.png)

![alt text](images/31.png)

![alt text](images/32.png)

Thành công rồi nha ae.

## Kiểm tra

Ban đầu.

![alt text](images/33.png)

Sau.

![alt text](images/34.png)

Các chức năng hoạt động bình thường.


Chỉ dành cho mục đích học tập và nghiên cứu, không sử dụng vào mục đích thương mại hoặc vi phạm pháp luật.