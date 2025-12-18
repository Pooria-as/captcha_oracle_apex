
## ⚡ Setup Instructions

### Add Before Header Process

Add the following PL/SQL code **before the header process**:

<div align="center">
  <img width="516" height="705" alt="pooria_a_captcha" src="https://github.com/user-attachments/assets/12e2e0ec-15bd-41fe-bb7a-39fd5d1d6954" />
</div>

# CAPTCHA Implementation in Oracle APEX

A simple CAPTCHA solution for Oracle APEX forms.  
Developed by **Pooria A**.

---

## 📝 Overview

This project provides a CAPTCHA mechanism to prevent automated submissions in Oracle APEX.  
It includes:

- Server-side CAPTCHA generation using PL/SQL
- User input validation
- Custom CAPTCHA rendering with HTML5 Canvas and JavaScript

---

## ⚡ Setup Instructions

### Add Before Header Process

Add the following PL/SQL code **before the header process**:

```plsql
declare
    l_chars   varchar2(62) := 'ABCDEFGHJKLMNPQRSTUVWXYZ23456789abcdefghijklmnpqrstuvwxyz';
    l_captcha varchar2(10) := '';  -- initialize to empty
begin
    for i in 1 .. 6 loop
        l_captcha := l_captcha ||
                     substr(
                         l_chars,
                         trunc(dbms_random.value(1, length(l_chars)+1)), -- +1 to include last char
                         1
                     );
    end loop;

    -- store captcha in hidden item
    :P9999_CAPTCHA_CODE := l_captcha;
end;





declare
    l_chars   varchar2(62) := 'ABCDEFGHJKLMNPQRSTUVWXYZ23456789abcdefghijklmnpqrstuvwxyz';
    l_captcha varchar2(10) := '';  -- initialize to empty
begin
    for i in 1 .. 6 loop
        l_captcha := l_captcha ||
                     substr(
                         l_chars,
                         trunc(dbms_random.value(1, length(l_chars)+1)), -- +1 to include last char
                         1
                     );
    end loop;

    -- store captcha in hidden item
    :P9999_CAPTCHA_CODE := l_captcha;
end;




