<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>毛玻璃工具箱</title>
<style>
*{margin:0;padding:0;box-sizing:border-box}
body{min-height:100vh;background:linear-gradient(135deg,#1a1a2e,#16213e,#0f3460);font-family:system-ui,sans-serif;display:flex;align-items:center;justify-content:center;padding:20px}

/* 中央搜索框 */
.s{position:fixed;top:50%;left:50%;transform:translate(-50%,-50%);width:460px;max-width:90vw;z-index:1000;background:rgba(10,15,30,.45);backdrop-filter:blur(18px);border-radius:60px;border:1px solid rgba(255,255,255,.08);padding:6px 6px 6px 22px;display:flex;align-items:center}
.s input{flex:1;padding:14px 0;border:none;background:0 0;color:#f0f4ff;font-size:1rem;outline:none}
.s input::placeholder{color:rgba(200,215,255,.4)}
.s button{background:rgba(255,255,255,.08);border:1px solid rgba(255,255,255,.12);color:#c8d6f0;padding:8px 20px;border-radius:40px;cursor:pointer}
.s button:hover{background:rgba(0,200,255,.15);border-color:rgba(0,200,255,.3)}

/* 控制中心（主窗口） */
#w{position:fixed;width:520px;background:rgba(10,15,30,.55);backdrop-filter:blur(16px);border-radius:24px;border:1px solid rgba(255,255,255,.08);top:60px;left:60px;overflow:hidden;z-index:999;user-select:none;max-height:90vh;display:flex;flex-direction:column}
#b{height:46px;background:rgba(255,255,255,.04);color:#d0e0ff;line-height:46px;padding:0 20px;cursor:grab;display:flex;justify-content:space-between;border-bottom:1px solid rgba(255,255,255,.05);flex-shrink:0}
#b:active{cursor:grabbing}
#c{flex:1;overflow-y:auto;padding:16px 18px 20px;display:flex;gap:20px}
.l{flex:2}
.r{flex:1;border-left:1px solid rgba(255,255,255,.05);padding-left:18px;display:flex;flex-direction:column;justify-content:center}

/* 拖拽图片 */
.dz{display:block;width:100%;padding:16px 12px;margin-bottom:16px;text-align:center;background:rgba(255,255,255,.04);border:2px dashed rgba(255,255,255,.15);border-radius:16px;color:rgba(200,215,255,.5);font-size:.85rem;transition:.3s}
.dz.dragover{border-color:rgba(0,200,255,.6);background:rgba(0,200,255,.08);color:#c8d6f0}
.dz span{display:block;font-size:1.8rem;margin-bottom:4px}

/* 日历 */
#cal{width:100%;max-width:280px;margin:0 auto;text-align:center}
#cal .head{display:flex;justify-content:space-between;align-items:center;font-weight:600;font-size:1rem;color:#b6ceff;margin-bottom:12px}
#cal .head button{background:rgba(255,255,255,.06);border:1px solid rgba(255,255,255,.08);width:32px;height:32px;border-radius:30px;font-size:1.2rem;color:#a0c0ff;cursor:pointer}
#cal .head button:hover{background:rgba(0,255,255,.12)}
#cal table{width:100%;border-collapse:collapse;table-layout:fixed}
#cal th{font-size:.65rem;color:rgba(180,200,255,.4);padding:6px 0;font-weight:400}
#cal td{padding:7px 0;font-size:.9rem;text-align:center;cursor:pointer;border-radius:30px;color:#c8d6f0}
#cal td:hover:not(.o){background:rgba(255,255,255,.06)}
#cal td.t{background:rgba(0,200,255,.15);color:#fff;font-weight:600}
#cal td.s{background:linear-gradient(135deg,#00d4ff,#a855f7);color:#fff;font-weight:600}
#cal td.o{color:rgba(255,255,255,.12);pointer-events:none}
#cal .info{text-align:center;margin-top:14px;font-size:.75rem;color:rgba(180,200,255,.35);border-top:1px solid rgba(255,255,255,.04);padding-top:12px}

/* 计时器（右侧） */
.timer{display:flex;flex-direction:column;align-items:center;gap:12px}
.timer .disp{font-size:2.2rem;font-family:monospace;color:#f0f4ff;letter-spacing:3px;background:rgba(0,0,0,.2);padding:10px 16px;border-radius:16px;border:1px solid rgba(255,255,255,.05);width:100%;text-align:center}
.timer .btns{display:flex;gap:8px;flex-wrap:wrap;justify-content:center}
.timer .btns button{background:rgba(255,255,255,.06);border:1px solid rgba(255,255,255,.08);color:#c8d6f0;padding:6px 16px;border-radius:30px;cursor:pointer;font-size:.85rem;transition:.15s}
.timer .btns button:hover{background:rgba(255,255,255,.12)}
.timer .btns .start{background:rgba(0,200,255,.15);border-color:rgba(0,200,255,.2)}
.timer .btns .start:hover{background:rgba(0,200,255,.25)}
.timer .btns .stop{background:rgba(255,100,100,.15);border-color:rgba(255,100,100,.2)}
.timer .btns .stop:hover{background:rgba(255,100,100,.25)}
.timer .btns .reset{background:rgba(255,200,50,.1);border-color:rgba(255,200,50,.15)}
.timer .btns .reset:hover{background:rgba(255,200,50,.2)}

/* 右下角时钟 */
#cl{position:fixed;bottom:28px;right:28px;background:rgba(0,0,0,.5);backdrop-filter:blur(8px);color:#9effb0;padding:8px 20px;border-radius:40px;font:16px monospace;border:1px solid rgba(0,255,200,.08);z-index:999}

/* ========== 骰子窗口（独立可拖拽） ========== */
#diceWin{
  position:fixed;
  width:240px;
  background:rgba(10,15,30,.55);
  backdrop-filter:blur(16px);
  border-radius:24px;
  border:1px solid rgba(255,255,255,.08);
  bottom:100px;
  right:60px;
  overflow:hidden;
  z-index:998;
  user-select:none;
  box-shadow:0 20px 60px rgba(0,0,0,.6);
}
#diceBar{
  height:40px;
  background:rgba(255,255,255,.04);
  color:#d0e0ff;
  line-height:40px;
  padding:0 16px;
  cursor:grab;
  display:flex;
  justify-content:space-between;
  border-bottom:1px solid rgba(255,255,255,.05);
  font-size:.9rem;
}
#diceBar:active{cursor:grabbing}
#diceContent{
  padding:16px;
  text-align:center;
  color:#c8d6f0;
}
#diceValue{
  font-size:4rem;
  font-weight:300;
  margin:8px 0;
  transition:.1s;
}
#diceValue.roll{
  animation: shake .3s ease-in-out;
}
@keyframes shake{
  0%{transform:scale(1) rotate(0deg)}
  25%{transform:scale(1.2) rotate(-10deg)}
  50%{transform:scale(1) rotate(10deg)}
  75%{transform:scale(1.1) rotate(-5deg)}
  100%{transform:scale(1) rotate(0deg)}
}
#diceBtn{
  background:rgba(255,255,255,.08);
  border:1px solid rgba(255,255,255,.12);
  color:#c8d6f0;
  padding:8px 20px;
  border-radius:30px;
  cursor:pointer;
  font-size:1rem;
  transition:.15s;
}
#diceBtn:hover{background:rgba(0,200,255,.15);border-color:rgba(0,200,255,.3)}
#diceHistory{
  margin-top:12px;
  font-size:.8rem;
  color:rgba(200,215,255,.5);
  display:flex;
  gap:6px;
  justify-content:center;
  flex-wrap:wrap;
}
#diceHistory span{
  background:rgba(255,255,255,.05);
  padding:2px 8px;
  border-radius:12px;
}
</style>
</head>
<body>

<!-- 搜索框 -->
<form class="s" action="https://www.baidu.com/s" target="_blank" method="get">
  <input type="text" name="wd" placeholder="🔍 搜索...">
  <button>搜索</button>
</form>

<!-- 控制中心 -->
<div id="w">
  <div id="b" onmousedown="dragStart(event, 'w')"><span>✧ 工具箱</span><span>🪟</span></div>
  <div id="c">
    <div class="l">
      <div class="dz" id="dropZone"><span>📁</span>拖拽图片<br><span style="font-size:.7rem;opacity:.6">新窗口打开</span></div>
      <div id="cal"></div>
    </div>
    <div class="r">
      <div class="timer">
        <div class="disp" id="timerDisplay">00:00</div>
        <div class="btns">
          <button class="start" id="timerStart">▶ 开始</button>
          <button class="stop" id="timerStop">⏸ 暂停</button>
          <button class="reset" id="timerReset">⟳ 重置</button>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- 骰子窗口（独立可拖拽） -->
<div id="diceWin">
  <div id="diceBar" onmousedown="dragStart(event, 'diceWin')">
    <span>🎲 骰子</span>
    <span style="opacity:.5;">🎯</span>
  </div>
  <div id="diceContent">
    <div id="diceValue">⚀</div>
    <button id="diceBtn">掷骰子</button>
    <div id="diceHistory"></div>
  </div>
</div>

<!-- 右下角时钟 -->
<div id="cl"></div>

<script>
// ============================================
// 通用窗口拖拽（支持任意窗口）
// ============================================
(function() {
  let currentWin = null;
  let offsetX, offsetY;

  window.dragStart = function(e, winId) {
    e.preventDefault();
    currentWin = document.getElementById(winId);
    offsetX = e.clientX - currentWin.offsetLeft;
    offsetY = e.clientY - currentWin.offsetTop;
    document.onmousemove = function(ev) {
      ev.preventDefault();
      currentWin.style.left = (ev.clientX - offsetX) + 'px';
      currentWin.style.top = (ev.clientY - offsetY) + 'px';
    };
    document.onmouseup = function() {
      document.onmousemove = null;
      document.onmouseup = null;
      currentWin = null;
    };
  };
})();

// ============================================
// 日历
// ============================================
(function() {
  const cal = document.getElementById('cal');
  let year, month, selected = null;

  function render(y, m) {
    year = y; month = m;
    const firstDay = new Date(y, m, 1).getDay();
    const daysInMonth = new Date(y, m+1, 0).getDate();
    const prevMonthDays = new Date(y, m, 0).getDate();
    const today = new Date();
    const td = today.getDate(), tm = today.getMonth(), ty = today.getFullYear();

    let html = `<div class="head">
      <button onclick="prevMonth()">‹</button>
      <span>${y}年 ${m+1}月</span>
      <button onclick="nextMonth()">›</button>
    </div><table><thead><tr><th>日</th><th>一</th><th>二</th><th>三</th><th>四</th><th>五</th><th>六</th></tr></thead><tbody><tr>`;

    for (let i = 0; i < firstDay; i++) {
      const d = prevMonthDays - firstDay + i + 1;
      html += `<td class="o">${d}</td>`;
    }

    for (let d = 1; d <= daysInMonth; d++) {
      const isToday = (d === td && m === tm && y === ty);
      const isSelected = (selected && d === selected.getDate() && m === selected.getMonth() && y === selected.getFullYear());
      const cls = (isToday ? ' t' : '') + (isSelected ? ' s' : '');
      html += `<td class="${cls}" onclick="selectDate(${y},${m},${d})">${d}</td>`;
      if ((firstDay + d - 1) % 7 === 6 && d < daysInMonth) {
        html += `</tr><tr>`;
      }
    }

    const total = firstDay + daysInMonth;
    const rem = (7 - total % 7) % 7;
    for (let i = 1; i <= rem; i++) {
      html += `<td class="o">${i}</td>`;
    }
    html += `</tr></tbody></table>`;
    html += `<div class="info">${selected ? '已选：'+selected.getFullYear()+'/'+(selected.getMonth()+1)+'/'+selected.getDate() : '点击日期选中'}</div>`;
    cal.innerHTML = html;
  }

  window.prevMonth = function() {
    if (month === 0) render(year-1, 11);
    else render(year, month-1);
  };
  window.nextMonth = function() {
    if (month === 11) render(year+1, 0);
    else render(year, month+1);
  };
  window.selectDate = function(y, m, d) {
    selected = new Date(y, m, d);
    render(y, m);
  };

  const now = new Date();
  render(now.getFullYear(), now.getMonth());
})();

// ============================================
// 右下角时钟
// ============================================
(function() {
  const cl = document.getElementById('cl');
  function update() {
    cl.textContent = new Date().toLocaleString('zh-CN', {hour12: false});
  }
  update();
  setInterval(update, 1000);
})();

// ============================================
// 图片拖拽上传
// ============================================
(function() {
  const dz = document.getElementById('dropZone');
  ['dragenter','dragover','dragleave','drop'].forEach(evt => {
    document.addEventListener(evt, e => { e.preventDefault(); e.stopPropagation(); });
  });
  dz.addEventListener('dragover', () => dz.classList.add('dragover'));
  dz.addEventListener('dragleave', () => dz.classList.remove('dragover'));
  dz.addEventListener('drop', function(e) {
    this.classList.remove('dragover');
    const file = e.dataTransfer.files[0];
    if (file && file.type.startsWith('image/')) {
      const reader = new FileReader();
      reader.onload = function(ev) {
        const win = window.open('', '_blank', 'width=800,height=600,menubar=no,toolbar=no,location=no,status=no,scrollbars=yes');
        if (!win) { alert('请允许弹出窗口'); return; }
        win.document.write(`
          <!DOCTYPE html>
          <html><head><title>图片预览</title>
          <style>*{margin:0;padding:0}body{background:#0a0a0f;display:flex;align-items:center;justify-content:center;min-height:100vh}img{max-width:95vw;max-height:95vh;border-radius:12px;box-shadow:0 20px 60px rgba(0,0,0,.8)}</style>
          </head><body>< img src="${ev.target.result}"></body></html>
        `);
        win.document.close();
      };
      reader.readAsDataURL(file);
    } else {
      alert('请拖入图片文件');
    }
  });
})();

// ============================================
// 计时器（分:秒）
// ============================================
(function() {
  const display = document.getElementById('timerDisplay');
  let timer = null, seconds = 0;

  function format(sec) {
    const h = Math.floor(sec / 3600);
    const m = Math.floor((sec % 3600) / 60);
    const s = sec % 60;
    if (h > 0) {
      return `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
    } else {
      return `${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
    }
  }

  function updateDisplay() { display.textContent = format(seconds); }

  function start() { if (timer) return; timer = setInterval(() => { seconds++; updateDisplay(); }, 1000); }
  function stop() { clearInterval(timer); timer = null; }
  function reset() { stop(); seconds = 0; updateDisplay(); }

  document.getElementById('timerStart').addEventListener('click', start);
  document.getElementById('timerStop').addEventListener('click', stop);
  document.getElementById('timerReset').addEventListener('click', reset);
  updateDisplay();
})();

// ============================================
// 骰子游戏
// ============================================
(function() {
  const valueEl = document.getElementById('diceValue');
  const historyEl = document.getElementById('diceHistory');
  const btn = document.getElementById('diceBtn');
  let history = [];

  // 骰子字符映射
  const diceFaces = ['⚀', '⚁', '⚂', '⚃', '⚄', '⚅'];

  function roll() {
    // 动画
    valueEl.classList.remove('roll');
    void valueEl.offsetWidth; // 触发重绘
    valueEl.classList.add('roll');

    const result = Math.floor(Math.random() * 6) + 1;
    valueEl.textContent = diceFaces[result - 1];

    // 更新历史（保留最近5次）
    history.push(result);
    if (history.length > 5) history.shift();
    renderHistory();
  }

  function renderHistory() {
    historyEl.innerHTML = history.map(num => `<span>${diceFaces[num-1]}</span>`).join('');
  }

  btn.addEventListener('click', roll);
  // 初始化显示
  valueEl.textContent = diceFaces[0];
})();
</script>
</body>
</html>
