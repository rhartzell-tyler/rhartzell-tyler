# Rod Hartzell 

### About me
- 👋 Hi, I’m @rhartzell-tyler
- 👀 I’m interested in all things computer science
- 🌱 I’m currently deploying a new pod to a kubernetes cluster
- 💞️ Here to collaborate on Tyler projects
- 📫 Reach me on teams: rodney.hartzell@tylertech.com
- ⚡ Fun fact: I reside in the city renowned for the Kansas City Chiefs, the champions of the NFL.
- ☢ Fluid Sim. in WebGL [https://rhartzell-tyler.github.io/](https://rhartzell-tyler.github.io/)

<!---
rhartzell-tyler/rhartzell-tyler is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->

### Now for a code snippet
Can you convert the following cursor based procedure into a single statement?

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
