<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Interactive Web OS</title>
  <style>
    /* ============================================
       MODULE 1: Draggable Calendar Window
       ============================================ */
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
      user-select: none; /* Prevent text selection during drag */
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
      pointer-events: none; /* Non-current month dates are not clickable */
    }
    #cal-info {
      text-align: center;
      margin-top: 12px;
      font-size: 0.8rem;
      color: #888;
    }

    /* ============================================
       MODULE 2: Photo Gallery
       ============================================ */
    .album {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 15px;
      padding: 20px;
      max-width: 600px;
      margin: 0 auto;
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
      z-index: 1000;
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

    /* ============================================
       MODULE 3: Live Clock
       ============================================ */
    #clock {
      position: fixed;
      top: 10px;
      right: 10px;
      background: #000;
      color: #fff;
      padding: 8px 16px;
      border-radius: 20px;
      font: 18px monospace;
      z-index: 999;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
    }

    /* Page layout */
    body {
      margin: 0;
      padding: 0;
      background: #f5f5f5;
      font-family: system-ui, -apple-system, sans-serif;
    }
    h1 {
      text-align: center;
      padding: 20px;
      color: #333;
    }
  </style>
</head>
<body>

  <!-- ============================================
       MODULE 1: Draggable Calendar Window
       A floating window that can be dragged around
       the screen. Contains an interactive calendar
       with month navigation and date selection.
       ============================================ -->
  <div id="win">
    <!-- Title bar — acts as the drag handle -->
    <div id="bar" onmousedown="startDrag(event)">
      <span>✧ Drag Me</span>
      <span style="font-size:0.8rem;opacity:0.8;">📅</span>
    </div>
    <!-- Content area — holds the calendar -->
    <div id="content">
      <div id="calendar"></div>
      <div id="cal-info">Click a date to select</div>
    </div>
  </div>

  <!-- ============================================
       MODULE 2: Photo Gallery
       A responsive grid of photos. Clicking any
       thumbnail opens a full-screen lightbox view.
       ============================================ -->
  <h1>Photo Gallery</h1>
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

  <!-- ============================================
       MODULE 3: Live Clock
       A real-time digital clock displayed in the
       top-right corner, updated every second.
       ============================================ -->
  <div id="clock"></div>

  <!-- ============================================
       JavaScript
       ============================================ -->
  <script>
    /* ---------- Draggable Window Logic ---------- */
    (function() {
      const win = document.getElementById('win');
      let offsetX = 0, offsetY = 0;

      // Start dragging — record the offset between cursor and window top-left
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

    /* ---------- Calendar Logic ---------- */
    (function() {
      const calDiv = document.getElementById('calendar');
      const infoDiv = document.getElementById('cal-info');
      const monthNames = ['January', 'February', 'March', 'April', 'May', 'June',
                          'July', 'August', 'September', 'October', 'November', 'December'];
      let currentYear, currentMonth;
      let selectedDate = null; // Currently selected date

      // Render the calendar for a given year and month
      function renderCalendar(year, month) {
        currentYear = year;
        currentMonth = month;
        const firstDay = new Date(year, month, 1).getDay(); // 0 = Sunday
        const daysInMonth = new Date(year, month + 1, 0).getDate();
        const daysInPrevMonth = new Date(year, month, 0).getDate();
        const today = new Date();
        const todayDate = today.getDate();
        const todayMonth = today.getMonth();
        const todayYear = today.getFullYear();

        let html = `
          <div class="cal-header">
            <button onclick="window.calPrev()">‹</button>
            <span>${monthNames[month]} ${year}</span>
            <button onclick="window.calNext()">›</button>
          </div>
          <table>
            <thead><tr>
              <th>Sun</th><th>Mon</th><th>Tue</th><th>Wed</th><th>Thu</th><th>Fri</th><th>Sat</th>
            </tr></thead>
            <tbody><tr>
        `;

        // Fill leading cells with previous month's dates (gray)
        for (let i = 0; i < firstDay; i++) {
          const day = daysInPrevMonth - firstDay + i + 1;
          html += `<td class="other-month">${day}</td>`;
        }

        // Render current month dates
        for (let d = 1; d <= daysInMonth; d++) {
          const isToday = (d === todayDate && month === todayMonth && year === todayYear);
          const isSelected = selectedDate &&
                            d === selectedDate.getDate() &&
                            month === selectedDate.getMonth() &&
                            year === selectedDate.getFullYear();
          const cls = (isToday ? 'today' : '') + (isSelected ? ' selected' : '');
          html += `<td class="${cls}" onclick="window.calSelect(${year}, ${month}, ${d})">${d}</td>`;
          // New row every 7 days
          if ((firstDay + d - 1) % 7 === 6 && d < daysInMonth) {
            html += `</tr><tr>`;
          }
        }

        // Fill trailing cells with next month's dates (gray)
        const totalCells = firstDay + daysInMonth;
        const remain = (7 - totalCells % 7) % 7;
        for (let i = 1; i <= remain; i++) {
          html += `<td class="other-month">${i}</td>`;
        }

        html += `</tr></tbody></table>`;
        calDiv.innerHTML = html;

        // Update info bar text
        if (selectedDate) {
          infoDiv.textContent = `Selected: ${monthNames[selectedDate.getMonth()]} ${selectedDate.getDate()}, ${selectedDate.getFullYear()}`;
        } else {
          infoDiv.textContent = 'Click a date to select';
        }
      }

      // Navigate to previous month
      window.calPrev = function() {
        if (currentMonth === 0) {
          renderCalendar(currentYear - 1, 11);
        } else {
          renderCalendar(currentYear, currentMonth - 1);
        }
      };

      // Navigate to next month
      window.calNext = function() {
        if (currentMonth === 11) {
          renderCalendar(currentYear + 1, 0);
        } else {
          renderCalendar(currentYear, currentMonth + 1);
        }
      };

      // Select a specific date
      window.calSelect = function(year, month, day) {
        selectedDate = new Date(year, month, day);
        renderCalendar(currentYear, currentMonth); // Re-render to show highlight
      };

      // Initialize with current month
      const now = new Date();
      renderCalendar(now.getFullYear(), now.getMonth());
    })();

    /* ---------- Photo Gallery Logic ---------- */
    function showBig(src) {
      document.getElementById("bigImg").src = src;
      document.getElementById("mask").style.display = "flex";
    }
    function closeBig() {
      document.getElementById("mask").style.display = "none";
    }

    /* ---------- Live Clock Logic ---------- */
    const clockEl = document.getElementById('clock');
    clockEl.innerText = new Date().toLocaleString('en-US', { hour12: false });
    setInterval(() => {
      clockEl.innerText = new Date().toLocaleString('en-US', { hour12: false });
    }, 1000);
  </script>
</body>
</html>
