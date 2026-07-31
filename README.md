<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>可拖拽窗口 + 日历</title>
  <style>
    /* ===== 可拖动日历窗口样式 ===== */
    #win {
      position: fixed;
      width: 340px;
      background: #fff;
      border-radius: 12px;
      box-shadow: 0 12px 40px rgba(0, 0, 0, 0.25);
      top: 60px;
      left: 60px;
      font-family: system-ui, -apple-system, sans-serif;
      overflow: hidden;
      z-index: 999;
      user-select: none;
    }
    #bar {
      height: 44px;
      background: linear-gradient(135deg, #667eea, #764ba2);
      color: #fff;
      line-height: 44px;
      padding: 0 20px;
      cursor: grab;
      font-weight: 600;
      letter-spacing: 0.5px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    #bar:active {
      cursor: grabbing;
    }
    #content {
      padding: 20px 18px 24px;
      background: #fafafa;
      border-top: 1px solid #eee;
    }
    #calendar {
      width: 100%;
      max-width: 280px;
      margin: 0 auto;
      font-family: inherit;
      text-align: center;
    }
    .cal-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-weight: 600;
      font-size: 1.1rem;
      color: #333;
      margin-bottom: 10px;
    }
    .cal-header button {
      background: #eee;
      border: none;
      width: 32px;
      height: 32px;
      border-radius: 50%;
      font-size: 1.2rem;
      cursor: pointer;
      transition: background 0.2s;
      display: inline-flex;
      align-items: center;
      justify-content: center;
    }
    .cal-header button:hover {
      background: #ddd;
    }
    #calendar table {
      width: 100%;
      border-collapse: collapse;
      table-layout: fixed;
    }
    #calendar th {
      font-size: 0.75rem;
      color: #888;
      font-weight: 500;
      padding: 6px 0;
    }
    #calendar td {
      padding: 6px 0;
      font-size: 0.9rem;
      text-align: center;
      cursor: pointer;
      border-radius: 6px;
      transition: background 0.15s;
    }
    #calendar td:hover {
      background: #f0f0f0;
    }
    #calendar td.today {
      background: #667eea;
      color: #fff;
      font-weight: 600;
    }
    #calendar td.selected {
      background: #764ba2;
      color: #fff;
    }
    #calendar td.other-month {
      color: #ccc;
      pointer-events: none;
    }
    #cal-info {
      text-align: center;
      margin-top: 12px;
      font-size: 0.8rem;
      color: #888;
    }

    /* ===== 相册样式 ===== */
    .album {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 15px;
      padding: 20px;
    }
    .album img {
      width: 100%;
      height: 180px;
      object-fit: cover;
      border-radius: 8px;
      cursor: pointer;
      transition: 0.2s;
    }
    .album img:hover {
      transform: scale(1.03);
    }
    .mask {
      position: fixed;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      background: rgba(0, 0, 0, 0.85);
      display: none;
      justify-content: center;
      align-items: center;
      z-index: 2000;
    }
    .mask img {
      max-width: 90%;
      max-height: 90vh;
      border-radius: 8px;
    }
    .close {
      position: absolute;
      top: 20px;
      right: 30px;
      color: white;
      font-size: 40px;
      cursor: pointer;
    }
  </style>
</head>
<body style="background-color: #c9ffcf">

  <!-- ========== 可拖拽日历窗口 ========== -->
  <div id="win">
    <div id="bar" onmousedown="startDrag(event)">
      <span>✧ 拖动我</span>
      <span style="font-size:0.8rem;opacity:0.8;">📅</span>
    </div>
    <div id="content">
      <div id="calendar"></div>
      <div id="cal-info">点击日期可选中</div>
    </div>
  </div>

  <!-- ========== 页面内容 ========== -->
  <h1>Welcome to my OS</h1>
  <h2>Introduction</h2>
  <p>Hello world</p>

  (rest of your content)
  <a href="https://stardance.hackclub.com/amd">label</a>

  <!-- your content -->

  <h1>我的相册</h1>
  <div class="album">
    <img src="1.JPG" onclick="showBig(this.src)">
    <img src="2.JPG" onclick="showBig(this.src)">
    <img src="3.JPG" onclick="showBig(this.src)">
    <img src="4.JPG" onclick="showBig(this.src)">
    <img src="5.JPG" onclick="showBig(this.src)">
    <img src="6.JPG" onclick="showBig(this.src)">
  </div>
  <div class="mask" id="mask">
    <span class="close" onclick="closeBig()">×</span>
    <img src="" id="bigImg">
  </div>

  <!-- ========== 右上角时钟 ========== -->
  <div id="c" style="position:fixed;top:10px;right:10px;background:#000;color:#fff;padding:8px 16px;border-radius:20px;font:18px monospace;z-index:999;box-shadow:0 4px 12px rgba(0,0,0,.3)"></div>

  <!-- ========== JavaScript ========== -->
  <script>
    // ---------- 拖拽逻辑 ----------
    (function() {
      const win = document.getElementById('win');
      let offsetX = 0, offsetY = 0;
      window.startDrag = function(e) {
        e.preventDefault();
        offsetX = e.clientX - win.offsetLeft;
        offsetY = e.clientY - win.offsetTop;
        document.onmousemove = function(e) {
          e.preventDefault();
          win.style.left = e.clientX - offsetX + 'px';
          win.style.top = e.clientY - offsetY + 'px';
        };
        document.onmouseup = function() {
          document.onmousemove = null;
          document.onmouseup = null;
        };
      };
    })();

    // ---------- 日历逻辑 ----------
    (function() {
      const calDiv = document.getElementById('calendar');
      const infoDiv = document.getElementById('cal-info');
      let currentYear, currentMonth;
      let selectedDate = null;

      function renderCalendar(year, month) {
        currentYear = year;
        currentMonth = month;
        const firstDay = new Date(year, month, 1).getDay();
        const daysInMonth = new Date(year, month + 1, 0).getDate();
        const daysInPrevMonth = new Date(year, month, 0).getDate();

        const today = new Date();
        const todayDate = today.getDate();
        const todayMonth = today.getMonth();
        const todayYear = today.getFullYear();

        let html = `
          <div class="cal-header">
            <button onclick="window.calPrev()">‹</button>
            <span>${year}年 ${month + 1}月</span>
            <button onclick="window.calNext()">›</button>
          </div>
          <table>
            <thead><tr>
              <th>日</th><th>一</th><th>二</th><th>三</th><th>四</th><th>五</th><th>六</th>
            </tr></thead>
            <tbody><tr>
        `;

        // 上月填充
        for (let i = 0; i < firstDay; i++) {
          const day = daysInPrevMonth - firstDay + i + 1;
          html += `<td class="other-month">${day}</td>`;
        }

        // 当月日期
        for (let d = 1; d <= daysInMonth; d++) {
          const isToday = (d === todayDate && month === todayMonth && year === todayYear);
          const isSelected = selectedDate &&
                            d === selectedDate.getDate() &&
                            month === selectedDate.getMonth() &&
                            year === selectedDate.getFullYear();
          const cls = (isToday ? 'today' : '') + (isSelected ? ' selected' : '');
          html += `<td class="${cls}" onclick="window.calSelect(${year}, ${month}, ${d})">${d}</td>`;

          if ((firstDay + d - 1) % 7 === 6 && d < daysInMonth) {
            html += `</tr><tr>`;
          }
        }

        // 下月填充
        const totalCells = firstDay + daysInMonth;
        const remain = (7 - totalCells % 7) % 7;
        for (let i = 1; i <= remain; i++) {
          html += `<td class="other-month">${i}</td>`;
        }

        html += `</tr></tbody></table>`;
        calDiv.innerHTML = html;

        if (selectedDate) {
          infoDiv.textContent = `已选：${selectedDate.getFullYear()}年${selectedDate.getMonth()+1}月${selectedDate.getDate()}日`;
        } else {
          infoDiv.textContent = '点击日期可选中';
        }
      }

      window.calPrev = function() {
        if (currentMonth === 0) {
          renderCalendar(currentYear - 1, 11);
        } else {
          renderCalendar(currentYear, currentMonth - 1);
        }
      };

      window.calNext = function() {
        if (currentMonth === 11) {
          renderCalendar(currentYear + 1, 0);
        } else {
          renderCalendar(currentYear, currentMonth + 1);
        }
      };

      window.calSelect = function(year, month, day) {
        selectedDate = new Date(year, month, day);
        renderCalendar(currentYear, currentMonth);
      };

      const now = new Date();
      renderCalendar(now.getFullYear(), now.getMonth());
    })();

    // ---------- 相册功能 ----------
    function showBig(src) {
      document.getElementById("bigImg").src = src;
      document.getElementById("mask").style.display = "flex";
    }
    function closeBig() {
      document.getElementById("mask").style.display = "none";
    }

    // ---------- 时钟功能 ----------
    const c = document.getElementById('c');
    c.innerText = new Date().toLocaleString('zh-CN', { hour12: false });
    setInterval(() => {
      c.innerText = new Date().toLocaleString('zh-CN', { hour12: false });
    }, 1000);
  </script>

</body>
</html>
