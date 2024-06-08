---
layout: post
title: Create-and-import-database-oracle
author: chungna
# subtitle: Product team
# gh-repo: chungpj
gh-badge: [follow]
tags: [knowledge, oracle]
comments: true
#date: '2024-05-26 14:35:23 +0530'
category: knowledge
#thumbnail: /assets/img/posts/code.jpg
keywords: create oracle db, import oracle db
permalink: /blog/Create-and-import-database-oracle/
usemathjax: true
summary: Tạo và import một oracle database
---

### Description:

#### Các nội dung sẽ thực hiện:

- Tạo datafiles
- Tạo `User`, `TableSpace`
- Thực hiện `import dump`

#### Chi tiết:

- Mở Sql Plus

  - Mở trực tiếp sqlplus.exe

    ```sql
    Nhập username, password để sử dụng <user>/<pass>
    Hoặc dùng sysdba với command ./sys as sysdba
    ```

  - Thông qua cmd administrator

    ```sql
    sqlplus / AS SYSDBA
    ---- chú ý thay đổi pass và Alias
    Hoặc sqlplus sys/<pass>@localhost/<YOUR_ALIAS> as sysdba
    ```

- CREATE `DUMP_DIR` (Folder chứa các file .dump):

  ```sql
  ---- CREATE DUMP_DIR (Folder chứa các file .dump) (Run sqlplus ./sys sysdba) ----------
  CREATE OR REPLACE DIRECTORY DUMP_DIR AS 'E:\PW_DUMP';
  ```

- Create `User` & `TableSpace`:

  ```sql
  ---- CREATE USER & TABLESPACE (Run sqlplus ./sys sysdba) ----------
  // USER = PASS = XXX, chỉnh size khởi tạo phù hợp để tránh lãng phí ổ cứng
  CREATE TABLESPACE XXX DATAFILE 'E:\Databases\XXX.dbf' SIZE 500M AUTOEXTEND ON NEXT 50M;
  CREATE TEMPORARY TABLESPACE TMPXXX TEMPFILE 'E:\Databases\TMPXXX.dbf' SIZE 100M AUTOEXTEND ON NEXT 10M;
  ```

- `GRANT` quyền cho các `User` vừa tạo và gắn vào `TableSpace`:

  ```sql
  ---- GRANT QUYỀN ----------
  alter session set "_ORACLE_SCRIPT"=true;
  CREATE USER XXX IDENTIFIED BY XXX DEFAULT TABLESPACE XXX TEMPORARY TABLESPACE TMPXXX;
  GRANT CONNECT,RESOURCE TO XXX;
  GRANT EXECUTE on dbms_crypto to XXX;
  ALTER USER XXX QUOTA UNLIMITED ON XXX;
  GRANT CREATE ANY DIRECTORY TO XXX;
  GRANT READ,WRITE ON DIRECTORY DUMP_DIR TO XXX;GRANT import full database to XXX;
  GRANT imp_full_database to XXX;
  ```

- Một số commands khác (có thể bạn cần):

  ```sql
  ---- Xoá user, tablespace ----
  DROP USER XXX CASCADE;
  DROP TABLESPACE XXX INCLUDING CONTENTS CASCADE CONSTRAINTS;
  DROP TABLESPACE TMPXXX INCLUDING CONTENTS CASCADE CONSTRAINTS;
  ---- Grant nhiều quyền hơn để đọc vào user/dabase khác
  GRANT all privileges to XXX;
  ---- Tăng datafile size với trường hợp data lớn (Mỗi datafile có thể phình tối đa đến 60Gb): 1G = 1000M
  ALTER TABLESPACE XXX ADD DATAFILE 'E:\Databases\XXX_01.dbf' SIZE 1G AUTOEXTEND ON MAXSIZE UNLIMITED;
  ---- Grant quyền cho phép user trỏ vào db, bảng cụ thể
  ALTER USER XXX quota unlimited on <ANOTHER_DATABSE>;
  GRANT SELECT ON <ANOTHER_DATABSE>.<TABLE1> TO XXX WITH GRANT OPTION;
  GRANT SELECT ON <ANOTHER_DATABSE>.<TABLE2> TO XXX WITH GRANT OPTION;
  ```

- Export dump (bạn có db và muốn export ra file .dump):

  ```sql
  ---- EXPORT DB (Run sqlplus ./sys sysdba) ----------
  GRANT DATAPUMP_EXP_FULL_DATABASE to XXX;
  expdp XXX/XXX@localhost:1521/<YOUR_ALIAS> directory=DUMP_DIR dumpfile=exp_XXX.dmp;
  expdp XXX/XXX@localhost:1523/<YOUR_ALIAS> full=y directory=DUMP_DIR dumpfile=exp_xxx.dmp;
  ```

- Import .dump file (Run `cmd` administrator or impdp.exe): (lựa chọn một trong số các commands dưới đây)

  ```sql
  ---- normal ----
  impdp XXX/XXX directory=DUMP_DIR dumpfile=XXX.DMP nologfile=YES;
  ---- case có thay đổi user so với dump user ----
  impdp XXX/XXX directory=DUMP_DIR dumpfile=xxx.DMP logfile=imp_xxx.log FROMUSER=NXPWBU TOUSER=XXX
  ----- case thay đổi tablespace và user ----
  impdp XXX/XXX directory=DUMP_DIR dumpfile=xxx.DMP logfile=imp_xxx.log REMAP_TABLESPACE=<DUMP_TABLESPACE_NAME>:XXX REMAP_SCHEMA=<DUMP_USER>:XXX
  ----- case
  --------- chú ý thay đổi pass, Alias, đường dẫn chứa file dump, các tên bảng cần import
  imp SYSTEM/<password>@localhost/<YOUR_ALIAS> file=E:\\DumpDir\\xxx.dmp tables='<TABLE1>','<TABLE2>','<TABLE3>','<TABLEn>' FROMUSER=<DUMP_USER> TOUSER=XXX
  ```

#### Một số thuật từ khoá cần nắm:

- `Alias`: định danh cái vùng db khi bạn cài oracle. VD: ORCL, XE
- `User`: tên cái database của bạn
- `TableSpace`: Tên vùng chứa data của database
- `Temporary`: Cũng là vùng chứa data nhưng là data temp khi bạn thực hiện query, order, group..
- `Datafile`: File vật lý của database

**Note:** Copy toàn bộ command của tôi vào notepad++ rồi thực hiện Replace XXX-><Tên DB của bạn>
