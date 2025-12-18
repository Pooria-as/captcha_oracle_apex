## CAPTCHA Implementation in Oracle APEX
A complete guide to implementing a dynamic CAPTCHA in Oracle APEX, including server-side generation, client-side rendering, and validation.

<div align="center">
  <img width="516" height="705" alt="pooria_a_captcha" src="https://github.com/user-attachments/assets/12e2e0ec-15bd-41fe-bb7a-39fd5d1d6954" />
</div>





## 1. Add Two Page Items

| Item Name          | Type        | Notes                                |
| ------------------ | ----------- | ------------------------------------ |
| P9999_USER_CAPTCHA | Text Input  | Visible - User enters CAPTCHA here   |
| P9999_CAPTCHA_CODE | Hidden Item | Stores server-generated CAPTCHA code |

## 2. Server-side CAPTCHA Generation

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

## 3. Add Static Content Region
HTML and JavaScript for displaying CAPTCHA:
```
<div style="
  display:flex;
  justify-content:center;
  align-items:center;
  width:100%;
  min-height:100px;
">

  <div style="position:relative; display:inline-block;">

    <canvas id="captcha" width="400" height="100"
            style="border:1px solid #bbb;background:#f6f6f6; cursor:help;"></canvas>

    <div id="captchaTip"
         style="
           visibility:hidden;
           opacity:0;
           position:absolute;
           bottom:110%;
           left:50%;
           transform:translateX(-50%);
           background:#555;
           color:#fff;
           padding:10px 14px;
           border-radius:6px;
           font-size:14px;
           white-space:nowrap;
           box-shadow:0 2px 8px rgba(0,0,0,0.25);
           transition:opacity 0.25s;
           z-index:10;
         ">
   برای کلمه امنیتی جدید کلیک کنید

      <div style="
        position:absolute;
        top:100%;
        left:50%;
        margin-left:-6px;
        width:0;
        height:0;
        border-left:6px solid transparent;
        border-right:6px solid transparent;
        border-top:6px solid #555;">
      </div>
    </div>

  </div>
</div>

<script>
(function () {
  const canvas = document.getElementById("captcha");
  const ctx = canvas.getContext("2d");
  const tooltip = document.getElementById("captchaTip");
  const text = "&P9999_CAPTCHA_CODE.";

  const colors = [
    "#e53935", "#1e88e5", "#43a047",
    "#fb8c00", "#8e24aa", "#00897b"
  ];

  function drawCaptcha() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // background
    const bg = ctx.createLinearGradient(0, 0, canvas.width, canvas.height);
    bg.addColorStop(0, "#ffffff");
    bg.addColorStop(1, "#eaeaea");
    ctx.fillStyle = bg;
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // curved lines
    for (let i = 0; i < 8; i++) {
      ctx.strokeStyle = colors[Math.floor(Math.random() * colors.length)] + "80";
      ctx.lineWidth = 3;
      ctx.beginPath();
      ctx.moveTo(0, Math.random() * canvas.height);
      ctx.bezierCurveTo(
        canvas.width * 0.3, Math.random() * canvas.height,
        canvas.width * 0.6, Math.random() * canvas.height,
        canvas.width, Math.random() * canvas.height
      );
      ctx.stroke();
    }

    // text
    ctx.font = "48px Arial";
    ctx.textAlign = "center";
    ctx.textBaseline = "middle";

    // calculate total width of text with gap
    const gap = 42;
    const totalWidth = (text.length - 1) * gap;
    const centerX = canvas.width / 2 - totalWidth / 2;

    for (let i = 0; i < text.length; i++) {
      ctx.save();
      const angle = (Math.random() * 40 - 20) * Math.PI / 180;
      const yOffset = Math.random() * 16 - 8;
      ctx.translate(centerX + i * gap, canvas.height / 2 + yOffset);
      ctx.rotate(angle);

      const grad = ctx.createLinearGradient(-20, -30, 20, 30);
      grad.addColorStop(0, "#e53935");
      grad.addColorStop(0.5, "#1e88e5");
      grad.addColorStop(1, "#43a047");
      ctx.fillStyle = grad;

      ctx.fillText(text[i], 0, 0);
      ctx.restore();
    }

    // dot noise
    for (let i = 0; i < 250; i++) {
      ctx.fillStyle = colors[Math.floor(Math.random() * colors.length)] + "99";
      ctx.fillRect(
        Math.random() * canvas.width,
        Math.random() * canvas.height,
        2,
        2
      );
    }

    // scratches
    for (let i = 0; i < 10; i++) {
      ctx.strokeStyle = colors[Math.floor(Math.random() * colors.length)] + "aa";
      ctx.lineWidth = 1;
      ctx.beginPath();
      ctx.moveTo(Math.random() * canvas.width, Math.random() * canvas.height);
      ctx.lineTo(Math.random() * canvas.width, Math.random() * canvas.height);
      ctx.stroke();
    }
  }

  canvas.addEventListener("mouseenter", () => {
    tooltip.style.visibility = "visible";
    tooltip.style.opacity = "1";
  });

  canvas.addEventListener("mouseleave", () => {
    tooltip.style.opacity = "0";
    tooltip.style.visibility = "hidden";
  });

  drawCaptcha();
})();
</script>


```
## 4. CAPTCHA Validation

Add this Process (Processing) in your page to check user input:
```

BEGIN
    -- Only check if user entered a value and not the special code
    IF :P9999_USER_CAPTCHA IS NOT NULL AND :P9999_USER_CAPTCHA != '1235' THEN
        
        -- Compare exactly (case-sensitive)
        IF :P9999_USER_CAPTCHA != :P9999_CAPTCHA_CODE THEN
            apex_error.add_error(
                p_message          => 'کلمه امنیتی وارد شده اشتباه است',
                p_display_location => apex_error.c_inline_in_notification
            );
            RETURN;
        END IF;

    END IF;
END;
```
## 5. For new captcha code use this in page level js code (Function and Global Variable Declaration) : 
```
document.querySelector('#captcha').addEventListener('click', function() {
  window.location.reload()
});
```

<h1>Developed by Pooria_a </h1>



