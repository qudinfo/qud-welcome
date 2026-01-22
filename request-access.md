---
layout: default
title: Request Access
nav_exclude: true
permalink: /request-access/
---

# Запрос доступа

Заполните данные для доступа.

<form id="qudForm" class="qud-form">
  <label>Email</label><br>
  <input type="email" id="email" required placeholder="name@example.com" style="width:340px; padding:10px;"><br><br>

  <label>Telegram @username</label><br>
  <input type="text" id="tg" required placeholder="@username" style="width:340px; padding:10px;"><br><br>

  <button type="submit" style="padding:10px 18px; cursor:pointer;">
    Отправить
  </button>

  <p id="msg" style="margin-top:14px;"></p>
</form>

<script>
  // ✅ сюда потом вставим URL твоего Apps Script Web App
  const WEB_APP_URL = "https://script.google.com/macros/s/AKfycbwrb7OC1E4ymEYP5rcBMnt--YSvyOxODrESkn5DtjefYSOYlwi87JrCGB2T4vLU4hqa_w/exec";

  function normalizeTg(v){
    v = (v || "").trim();
    if (!v) return v;
    if (!v.startsWith("@")) v = "@"+v;
    return v;
  }

  document.getElementById("qudForm").addEventListener("submit", async (e) => {
    e.preventDefault();
    const msg = document.getElementById("msg");
    msg.textContent = "Отправка...";

    const payload = {
      email: document.getElementById("email").value.trim(),
      telegram: normalizeTg(document.getElementById("tg").value),
      source: "qud-welcome",
      page: location.href,
      ts: new Date().toISOString()
    };

    try{
      const res = await fetch(WEB_APP_URL, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(payload),
        mode: "cors"
      });

      const data = await res.json().catch(()=>({}));
      if (!res.ok || data.ok !== true) {
        throw new Error(data.error || "Request failed");
      }

      msg.textContent = "✅ Заявка отправлена. На вашу почту от qatequd@proton.me придёт доступ к тесту.";
      e.target.reset();
    } catch(err){
      msg.textContent = "⚠️ Не удалось отправить. Попробуйте ещё раз через минуту.";
      console.error(err);
    }
  });
</script>
