# HOW TO SET UP

[Docker image link](https://container-registry.oracle.com/ords/f?p=113:4:80650608053:::4:P4_REPOSITORY,AI_REPOSITORY,AI_REPOSITORY_NAME,P4_REPOSITORY_NAME,P4_EULA_ID,P4_BUSINESS_AREA_ID:803,803,Oracle%20Database%20Express%20Edition,Oracle%20Database%20Express%20Edition,1,0&cs=3C-amBK1Pj9DV4I8A-Y9haIqn2IdN8I_FZL9DOh_omzm78vINENGmeeM-VNAgdP3oGXnNzCiuPlL1OjjKtLuWww)

## 0 - Build the container
```docker compose up -d```

After initialization you can run the script to change default passwords: ```docker exec -it oracledb ./setPassword.sh oracle123```
- enter container with: ```docker exec -it oracledb bash```
- connect to sql as SYSTEM: ```sqlplus system/oracle123@localhost:1521/XEPDB1```
- connect to sql as SYS sqlplus ```sys/oracle123@localhost:1521/XEPDB1 as sysdba```

> [!NOTE]
> SYSTEM vs SYS: https://dba.stackexchange.com/questions/405/what-is-the-difference-between-sys-and-system-accounts-in-oracle-databases


#

> [!NOTE]
> Reference:
> https://www.oracle.com/tools/technologies/quickstart-dotnet-for-oracle-database.html#second-option-tab

#
## 1 - Create new connection as admin:

IN ORACLE SQL DEVELOPER TOOL EXTENSION FOR VSCODE:

- Connection type = Basic
- Hostname = localhost
- Port number = 1521
- Service type = Service name
- Service name = XEPDB1
- Role = SYSDBA(Administrator)
- Username = SYS
- Password = same as the one used in docker command

(test connection first)
<img width="1247" height="523" alt="image" src="https://github.com/user-attachments/assets/29ffc075-b6b4-45c2-a703-993be96b899a" />

#
## 2 - Create a new user

After creating the connection right click the connection from the SQL Developer extension tool and create a new SQL file
Paste the following code:

```
CREATE USER testuser IDENTIFIED BY oracle123

   DEFAULT TABLESPACE USERS QUOTA UNLIMITED ON USERS;

GRANT CREATE SESSION, CREATE VIEW, CREATE SEQUENCE,
   CREATE PROCEDURE, CREATE TABLE, CREATE TRIGGER,
     CREATE TYPE, CREATE MATERIALIZED VIEW TO testuser;
```
     
Press F5 to run the script

Expected output:
- USER created
- Grant succeeded

This creates a new user called testuser with password oracle123 and grants it usage privileges on the database

#
## 3 - Create a new connection as testuser:
IN ORACLE SQL DEVELOPER TOOL EXTENSION FOR VSCODE:

- Connection type = Basic
- Role = default
- Hostname = localhost
- Port number = 1521
- Service type = Service name
- Service name = XEPDB1
- Username = testuser
- Password = oracle123

(test connection first)

#
## 4 - Create a new TABLE:
After creating the connection right click the connection from the SQL Developer extension tool and create a new SQL file
Paste the following code:

```
CREATE TABLE TODOITEM (id NUMBER GENERATED ALWAYS AS IDENTITY,

  DESCRIPTION VARCHAR2(4000), CREATION_TS TIMESTAMP WITH TIME ZONE
    DEFAULT CURRENT_TIMESTAMP, DONE NUMBER(1, 0), PRIMARY KEY(id));

INSERT INTO TODOITEM (DESCRIPTION, DONE) VALUES('Task 1', 0);
INSERT INTO TODOITEM (DESCRIPTION, DONE) VALUES('Task 2', 0);
INSERT INTO TODOITEM (DESCRIPTION, DONE) VALUES('Task 3', 1);

COMMIT;

SELECT DESCRIPTION, DONE FROM TODOITEM;
```

Press F5 to run the script.

Expected output inside he script output tab:
- Table TODOITEM created.
- 1 row inserted.
- 1 row inserted.
- 1 row inserted.
- Commit complete.

#
## 5 - Testing the user inside docker:

Enter container's console:
docker exec -it oracledb bash

Enter db manager with sqlplus:
sqlplus testuser/oracle123@localhost:1521/XEPDB1

Check table:
SELECT ID FROM TODOITEM;
