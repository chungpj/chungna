---
layout: post
title: Import dump file in case of multiple oracle versions
author: chungna
# subtitle: Product team
# gh-repo: chungpj
gh-badge: [follow]
tags: [knowledge, oracle]
comments: true
#date: '2024-05-26 14:35:23 +0530'
category: knowledge
#thumbnail: /assets/img/posts/code.jpg
keywords: knowledge, oracle
permalink: /blog/Import-dump-in-case-of-multiple-oracle-versions/
usemathjax: true
summary: Nếu trên máy server của bạn có nhiều hơn 1 phiên bản oracle (case study là 12c và 18c). Khi import dump file thì làm sao để chỉ định đúng database, đúng phiên bản?
---
### Description:
- Như ta đã biết, `import dump` sẽ thực hiện bằng `cmd` với câu lệnh cơ bản như sau:
```csharp
impdp <user>/<pass> 
```
- Trường hợp này, cách an toàn nhất là sử dụng file dump của từng phiên bản theo đúng đường dẫn, nên cần phải nắm được chính xác vị trí cài đặt từng phiên bản oracle đã cài.
- Các bước thực hiện:
	- B1: Tìm folder chứa file `impdp.exe` hay `imp.exe`, thông thường folder cấp nhỏ nhất sẽ là `app`. Ví dụ:
		```csharp
		C:\app\virtual\product\12.2.0\dbhome_1\bin
		```
	- B2: Có thể làm 2 cách:
		```csharp
		Mở cmd với quyền Administrator
		sử dụng lệnh cd vào C:\app\virtual\product\12.2.0\dbhome_1\bin
		```
		```csharp
		Nhấn [Shift] + chuột phải -> chọn Open PowerShell window here
		```
	- B3: Nhập câu lệnh import và run




