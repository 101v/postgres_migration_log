### 09-Sep-2019

1.  To support Integrated Security in the connection string for postgreSQL needs to be setup as per [this](https://wiki.postgresql.org/wiki/Configuring_for_single_sign-on_using_SSPI_on_Windows) document.
2. pg_hba.conf file can be found under the postgreSQL data directory. Usually it is in <InstallationFolder>\PostgreSQL\10\Data
3. How to set pg_hba.conf file https://dankuida.com/sspi-connection-to-postgres-34e288ea82f3

### 30-Sep-2019
1. Update sql script block as follow

    **Replace**

    ```
    DECLARE @creatorId UNIQUEIDENTIFIER
    DECLARE @creatorName NVARCHAR(100)
    DECLARE @createdDate DATETIMEOFFSET(7)

    SET @creatorId = CAST(CAST(0 AS BINARY) AS UNIQUEIDENTIFIER)
    SET @creatorName = 'Administrator .'
    SET @createdDate = GETDATE()

    **OTHER SQL SCRIPT**
    ```

    **with**
    ```
    DO $$
    DECLARE
        creatorId uuid := '00000000-0000-0000-0000-000000000000'; 
        creatorName varchar(100) := 'Administrator .';
        createdDate timestamptz := NOW();
    BEGIN
        **OTHER SQL SCRIPT**
    END $$;
    ```
2. Some time in the **INSERT** SQL statement, we do not tend to use **INTO** keywork. SQL Sever still accepts it as a valid statement. However postgreSQL does not, make sure **INTO** is used in the **INSERT** statement

3. Make sure to add **semicolon (;)** at the end of each statement to ternmate it. It is mandatory in postgres.

4. For the scenario where we need to insert data in the indentity column use following
    ```
    --SET IDENTITY_INSERT [Admin].[LanguageMaster] ON 
    -- In posgres there is no need to do this, rather adjust the sequence value at the end of the script.

    -- At the bottom of this script just reset the sequence id to reflect the lastest max id in the table.

    PERFORM setval(pg_get_serial_sequence('admin.languagemaster', 'id'), (select max(id) from Admin.LanguageMaster));
    ```
5. PostgreSQL supports **boolean** type in the database, hence some of our column does gets created as boolean. Remeber to repalce bit datat with boolean TRUE/FALSE value

6. Remove [ and ] characters from the script. Use double quotation instead if needed.

7. Reserverd keyword used for column names should always be wrapped in double quotation.

8. Parameters are decleared without @ symbol. Remember to replce them in the script.

9. Replaced **TOP n** WITH **LIMIT n** as shown below
    **Replace**
    ```
    SELECT TOP 10 column FROM table
    ```
    **with**
    ```
    SELECT column FROM table LIMIT 10
    ```

10. Replace IF statement with IF THEN END IF;

11. Remember postgres treat every name as a lowercase, hence be aware that name ModiferDate and modiferDate are the same. If you have a column Name ModifierDate in the database it will be modifierdate, hehce if you define a variable named modifierName it will conflict in an **UPDATE** statement and in **WHERE** clause. As a result you may get following error
    ```
    The error was 42702: column reference "modifiername" is ambiguous
    ```
