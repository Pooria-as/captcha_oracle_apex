<div align="center">
  <img width="516" height="705" alt="pooria_a_captcha" src="https://github.com/user-attachments/assets/12e2e0ec-15bd-41fe-bb7a-39fd5d1d6954" />
</div>
# CAPTCHA Implementation in Oracle APEX

A complete guide to implementing a dynamic CAPTCHA in Oracle APEX, including server-side generation, client-side rendering, and validation.

---

## 1. Server-side CAPTCHA Generation

Add this **Before Header Process** in your page to generate a random CAPTCHA code:

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
```

## 2. Add Two Page Items

| Item Name          | Type        | Notes                                |
| ------------------ | ----------- | ------------------------------------ |
| P9999_USER_CAPTCHA | Text Input  | Visible - User enters CAPTCHA here   |
| P9999_CAPTCHA_CODE | Hidden Item | Stores server-generated CAPTCHA code |
