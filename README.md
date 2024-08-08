# Rod Hartzell 

### About me
- 👋 Hi, I’m @rhartzell-tyler
- 👀 I’m interested in all things computer science
- 🌱 I’m currently deploying a new pod to a kubernetes cluster
- 💞️ Here to collaborate on Tyler projects
- 📫 Reach me on teams: rodney.hartzell@tylertech.com
- ⚡ Fun fact: I reside in the city renowned for the Kansas City Chiefs, the champions of the NFL.
- ☢ Here's something interesting [https://rhartzell-tyler.github.io/](https://rhartzell-tyler.github.io/)

<!---
rhartzell-tyler/rhartzell-tyler is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->

### Now for a code snippet

```sql
declare
    cursor noroles is select ud.userid, email, customerid, cr.rolename, r.roleid
                        from user_data ud
                            join user_profile up on up.userid = ud.userid
                            join psp_customer c on c.cdbcustomerid = up.customerid
                            join psp_customer_type_roles ctr on ctr.typeid = c.typeid
                            join psp_customer_roles cr on ctr.roleid = cr.roleid
                            join user_roles r on r.rolename = cr.rolename
                        where ud.userid not in (select userid from user_usersinroles)
                        and c.cdbprimarycontactemail = loweredemail
                        and ud.isapproved = 1
                    order by ud.userid, customerid, rolename;
    custRoleid varchar2(36);
    vSql varchar(1024);
begin
   FOR rec IN noroles LOOP
        --dbms_output.put_line(rec.loweredemail);
        
        select roleid into custRoleid from user_roles where rolename = rec.customerid;
        
        -- show dynamic sql
        dbms_output.put_line(rec.email || ', ' || rec.rolename);
        vSql := 'insert into user_usersinroles (userid, roleid) values (''' || rec.userid || ''', ''' || rec.roleid || ''')';
        dbms_output.put_line(vSql);
        
        
   END LOOP;
END;
```
