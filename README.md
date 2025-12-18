Here's an improved, professional English README section for your CAPTCHA implementation:

🔐 Secure CAPTCHA Implementation
A robust CAPTCHA system combining PL/SQL code generation with HTML5 Canvas visual rendering for Oracle APEX applications.

1. Before Header Process: generate_captcha
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
Features:

6-character alphanumeric code excluding confusing characters (I, O, 0, 1)

Cryptographically secure DBMS_RANDOM generation
P9999_USER_CAPTCHA    → Text Input (Visible - User enters code here)
P9999_CAPTCHA_CODE    → Hidden Item (Stores server-generated code)
3. Static Content Region (CAPTCHA Canvas)
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
4. Page JavaScript (Function and Global Declaration)
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

Stores in hidden item P9999_CAPTCHA_CODE

2. Form Items Configuration
