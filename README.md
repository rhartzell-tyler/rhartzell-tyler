# Rod Hartzell 

## About me
- 👋 Hi, I’m @rhartzell-tyler
- 👀 I’m interested in all things computer science
- 🌱 I’m currently deploying a new pod to a kubernetes cluster, and migrating the stack to AWS.
- 💞️ Here to collaborate on Tyler projects.
- 📫 Reach me on teams: rodney.hartzell@tylertech.com
- ⚡ Fun fact: I reside in the city of the current NFL champions.
- ☢ Fluid Simulation using WebGL [(click here)](https://rhartzell-tyler.github.io/)

[Shortcut to PSP Repositories](https://github.com/orgs/tyler-technologies-dsd/teams/pre-employment-screening-program/repositories)
<!---
rhartzell-tyler/rhartzell-tyler is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
### Oracle to PostgreSQL Conversion Notes
| Oracle | PostrgeSQL | C# | TypeScript |
--------|-------------|----|-------------
|![logo](https://www.siebelhub.com/main/wp-content/uploads/2015/09/oracle-logo.png)|![logo](https://raw.githubusercontent.com/docker-library/docs/01c12653951b2fe592c1f93a13b4e289ada0e3a1/postgres/logo.png)|![logo](https://serengetijade.github.io/img/technologies/csharp.jpg)|![logo](https://cdn.thenewstack.io/media/2022/01/10b88c68-typescript-logo.png)|

1. Differences in SQL Syntax and Functionality
Oracle and PostgreSQL SQL syntax and functionality aren't always a one-to-one match. This can lead to issues during the migration, especially with complex queries and stored procedures.
    - __Example__: In Oracle, I used to rely heavily on the `CONNECT BY` clause for hierarchical queries. PostgreSQL doesn't support `CONNECT BY`. I had to rewrite those queries using recursive common table expressions (CTEs), which are the PostgreSQL way of handling hierarchical data.

3. Transaction Behavior
Oracle and PostgreSQL handle transactions differently. In Oracle, DDL statements are treated as autonomous transactions and are immediately committed. In contrast, PostgreSQL treats DDL statements as regular transactions.
    - __Example__: I once tried to execute a series of DDL and DML statements in a single transaction in PostgreSQL, expecting them to behave like in Oracle. This resulted in some unexpected behavior because the changes weren't immediately committed.

3. Case Sensitivity
By default, Oracle is case-insensitive for column and table names, while PostgreSQL is case-sensitive. This can lead to issues if your Oracle schema uses mixed-case identifiers.
    - __Example__: I had a table in Oracle with mixed-case columns. When I migrated it to PostgreSQL, I ran into issues because PostgreSQL was treating `myColumn` and `mycolumn` as two different columns.
4. Sequences and Auto-Incrementing
In Oracle, you create a sequence and then use a trigger to auto-increment a column. PostgreSQL has a built-in feature for auto-incrementing columns using `SERIAL` or `IDENTITY`, but migrating Oracle sequences and triggers to this new paradigm can be tricky.
    - __Example__: In Oracle, I had a table with a sequence and a trigger for auto-incrementing the primary key. When migrating to PostgreSQL, I had to replace these with a `SERIAL` primary key.
5. Data Types
Oracle and PostgreSQL have different sets of data types. While many of them map directly, some Oracle data types have no equivalent in PostgreSQL.
    - __Example__: I had a table in Oracle that used the `NUMBER` data type. PostgreSQL doesn't have a `NUMBER` type, so I had to carefully map it to an appropriate numeric type in PostgreSQL.
6. NULLs and Empty Strings
Oracle treats NULLs and empty strings the same way, which is different from PostgreSQL where NULL and an empty string are distinct. If your Oracle database or applications rely on this behavior, you'll need to carefully handle this during the migration.
    - __Example__: I once had a bug in our application after migration because it was relying on Oracle's treatment of empty strings as NULL. I had to update the application code to treat NULL and empty strings separately in PostgreSQL.
7. Date and Time Types
Oracle has a single DATE type that includes both date and time, unlike PostgreSQL, which separates date and time into DATE, TIME, and TIMESTAMP types. This can lead to confusion if your Oracle database uses the DATE type to store times.
    - __Example__: We had a table in Oracle with a DATE column that was being used to store both date and time. When I migrated it to PostgreSQL using the DATE type, we lost the time information. I had to go back and change it to the TIMESTAMP type in PostgreSQL.

### SQL Coding Challenge
Can you convert the following cursor based procedure into a single `insert` statement?

```sql
declare
    cursor noroles is select ud.userid, email, customerid, cr.rolename, r.roleid,c.cdbprimarycontactemail
                        from user_data ud
                            join user_profile up on up.userid = ud.userid
                            join psp_customer c on c.cdbcustomerid = up.customerid
                            join psp_customer_type_roles ctr on ctr.typeid = c.typeid
                            join psp_customer_roles cr on ctr.roleid = cr.roleid
                            join user_roles r on r.rolename = cr.rolename
                        where ud.userid not in (select userid from user_usersinroles)
                        and ud.isapproved = 1
                    order by ud.userid, customerid, rolename;
    vCustRoleid varchar2(36);
    vSql varchar2(1024);
    vCustRoleSql varchar2(1024);
    vCurrCustId varchar2(20) := ' ';
    vPrevCustId varchar2(20) := ' ';
    vUserCount number := 0;
    vRowsAdded number := 0;
begin
   FOR rec IN noroles LOOP
        vCurrCustId := rec.customerid;
        select roleid into vCustRoleid from user_roles where rolename = rec.customerid;
        
        -- only add admin role if is primary
        if (rec.rolename = 'admin' AND rec.email = rec.cdbprimarycontactemail) then
            vSql := 'insert into user_usersinroles (userid, roleid) values (''' || rec.userid || ''', ''' || rec.roleid || ''')';
            execute immediate vSql;
            vRowsAdded := vRowsAdded + 1;
        end if;
        
        if (rec.rolename != 'mc_useradmin') then
            vSql := 'insert into user_usersinroles (userid, roleid) values (''' || rec.userid || ''', ''' || rec.roleid || ''')';
            execute immediate vSql;
            vRowsAdded := vRowsAdded + 1;
        end if;
        
        if (vCurrCustId != vPrevCustId) then
            vUserCount := vUserCount + 1;
            vCustRoleSql := 'insert into user_usersinroles (userid, roleid) values (''' || rec.userid || ''', ''' || vCustRoleId || ''')';
            execute immediate vSql;
            vRowsAdded := vRowsAdded + 1;
            vPrevCustId := vCurrCustId;
        end if;
        
   END LOOP;
   dbms_output.put_line('Total users: ' || vUserCount);
   dbms_output.put_line('Total rows added: ' || vRowsAdded);
END;
```
Bragging rights to any who conquer the Challenge. __Good Luck!__
