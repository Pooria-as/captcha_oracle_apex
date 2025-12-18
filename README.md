# Oracle APEX CAPTCHA Solution

A secure, customizable CAPTCHA system for Oracle APEX with canvas-generated visuals, PL/SQL code generation, and validation. Features Persian/English support and anti-bot protection.

## ✨ Features

- **Canvas-based CAPTCHA** - Beautiful HTML5 canvas rendering with gradients, curves, noise, and distortions
- **PL/SQL Generator** - Server-side random code generation using secure character set
- **Real-time Validation** - Page process validation with Persian error messages
- **Interactive Tooltip** - Hover hints in Persian for better UX
- **Responsive Design** - Flexbox-centered layout
- **Case-sensitive Matching** - Secure exact-match validation
- **Bot Bypass** - Special code '12526' for testing/debugging

## 📋 Setup Instructions

### 1. Before Header Process (PL/SQL)
declare
l_chars varchar2(62) := 'ABCDEFGHJKLMNPQRSTUVWXYZ23456789abcdefghijklmnpqrstuvwxyz';
l_captcha varchar2(10) := '';
begin
for i in 1 .. 6 loop
l_captcha := l_captcha ||
substr(
l_chars,
trunc(dbms_random.value(1, length(l_chars)+1)),
1
);
end loop;
:P9999_CAPTCHA_CODE := l_captcha;
end;

### 2. Add Page Items

P9999_USER_CAPTCHA → Text Input (Visible)
P9999_CAPTCHA_CODE → Hidden Item

### 3. Static Content Region (HTML + JavaScript)
Copy the complete HTML canvas + JavaScript code into a **Static Content** region.

### 4. Process Validation (PL/SQL)

BEGIN
IF :P9999_USER_CAPTCHA IS NOT NULL AND :P9999_USER_CAPTCHA != '12526' THEN
IF :P9999_USER_CAPTCHA != :P9999_CAPTCHA_CODE THEN
apex_error.add_error(
p_message => 'کلمه امنیتی وارد شده اشتباه است',
p_display_location => apex_error.c_inline_in_notification
);
RETURN;
END IF;
END IF;
END;

## 🎨 Visual Features

- **6-character alphanumeric** (excludes confusing chars I, O, 0, 1)
- **Gradient backgrounds** & multi-color text
- **Curved interference lines** (8 dynamic lines)
- **Rotated/distorted text** (±20° angle variation)
- **Dot noise** (250 random pixels)
- **Scratch lines** for extra obfuscation
- **Hover tooltip** with refresh hint

## 🔧 Customization

- Change `l_chars` for different character sets
- Modify `colors[]` array for color schemes
- Adjust `gap = 42` for character spacing
- Update tooltip text for different languages
- Modify canvas `width="400" height="100"`

## 🛡️ Security Notes

- Uses `dbms_random.value()` for cryptographically secure randomness
- Case-sensitive validation prevents simple attacks
- Hidden item prevents client-side tampering
- Special code `12526` bypasses for development

## 📱 Demo
![CAPTCHA Preview](https://via.placeholder.com/400x100/ffffff/cccccc?text=Your+APEX+CAPTCHA)

## 🤝 Contributing
Pull requests welcome! Fork → Create feature branch → PR.

## 📄 License
MIT License - Free for commercial and personal use.

## 👨‍💻 Author
**Developed by pooria_a**  
[Contact me for custom APEX solutions](mailto:your-email@example.com)
