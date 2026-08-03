<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <!--
    ============================================================
    My OS - Interactive Desktop v2.0
    ============================================================
    Description: A multi-functional interactive web page featuring
                 a draggable calendar window, photo gallery, and
                 real-time clock display.
    Features:
        1. Draggable Window - Click and drag the title bar to move
        2. Interactive Calendar - Month navigation, date selection
        3. Photo Gallery - Click thumbnails to view full-size images
        4. Real-time Clock - Live date and time display
        5. Hover Effects - Smooth animations on interactive elements
    Dependencies: None (pure HTML, CSS, JavaScript)
    ============================================================
    -->
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Draggable Window + Calendar</title>
    
    <style>
        /* ================================================
           Draggable Window Styles
           ================================================ */
        
        /* Main draggable window container */
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
            user-select: none;  /* Prevent text selection during drag */
        }

        /* Window title bar (drag handle) */
        #bar {
            height: 44px;
            background: linear-gradient(135deg, #667eea, #764ba2);  /* Purple gradient */
            color: #fff;
            line-height: 44px;
            padding: 0 20px;
            cursor: grab;         /* Grab cursor when hovering */
            font-weight: 600;
            letter-spacing: 0.5px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        /* Cursor changes to grabbing while dragging */
        #bar:active {
            cursor: grabbing;
        }

        /* Window content area */
        #content {
            padding: 20px 18px 24px;
            background: #fafafa;
            border-top: 1px solid #eee;
        }

        /* ================================================
           Calendar Styles
           ================================================ */
        
        /* Calendar container */
        #calendar {
            width: 100%;
            max-width: 280px;
            margin: 0 auto;
            font-family: inherit;
            text-align: center;
        }

        /* Calendar header (month + navigation buttons) */
        .cal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-weight: 600;
            font-size: 1.1rem;
            color: #333;
            margin-bottom: 10px;
        }

        /* Prev/Next month navigation buttons */
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

        /* Calendar table */
        #calendar table {
            width: 100%;
            border-collapse: collapse;
            table-layout: fixed;
        }

        /* Day name headers (Sun, Mon, etc.) */
        #calendar th {
            font-size: 0.75rem;
            color: #888;
            font-weight: 500;
            padding: 6px 0;
        }

        /* Date cells */
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

        /* Today's date highlight */
        #calendar td.today {
            background: #667eea;
            color: #fff;
            font-weight: 600;
        }

        /* Selected date highlight */
        #calendar td.selected {
            background: #764ba2;
            color: #fff;
        }

        /* Dates from other months (greyed out) */
        #calendar td.other-month {
            color: #ccc;
            pointer-events: none;  /* Non-clickable */
        }

        /* Calendar info text below calendar */
        #cal-info {
            text-align: center;
            margin-top: 12px;
            font-size: 0.8rem;
            color: #888;
        }

        /* ================================================
           Photo Gallery Styles
           ================================================ */
        
        /* Photo gallery grid container */
        .album {
            display: grid;
            grid-template-columns: repeat(3, 1fr);  /* 3 equal columns */
            gap: 15px;
            padding: 20px;
        }

        /* Gallery thumbnail images */
        .album img {
            width: 100%;
            height: 180px;
            object-fit: cover;
            border-radius: 8px;
            cursor: pointer;
            transition: 0.2s;
        }

        /* Hover scale effect */
        .album img:hover {
            transform: scale(1.03);
        }

        /* ================================================
           Lightbox / Modal Styles
           ================================================ */
        
        /* Fullscreen image overlay */
        .mask {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            background: rgba(0, 0, 0, 0.85);  /* 85% black */
            display: none;                      /* Hidden by default */
            justify-content: center;
            align-items: center;
        }

        /* Enlarged image in modal */
        .mask img {
            max-width: 90%;
            max-height: 90vh;
            border-radius: 8px;
        }

        /* Close button in modal */
        .close {
            position: absolute;
            top: 20px;
            right: 30px;
            color: white;
            font-size: 40px;
            cursor: pointer;
        }

        /* ================================================
           Real-Time Clock Styles
           ================================================ */
        
        /* Clock display */
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
            box-shadow: 0 4px 12px rgba(0, 0, 0, .3);
        }
    </style>
</head>
<body style="background-color: #c9ffcf; margin: 0;">

    <!-- ================================================
         Draggable Window with Calendar
         ================================================ -->
    <div id="win">
        <!-- Title bar (drag handle) -->
        <div id="bar" onmousedown="startDrag(event)">
            <span>✧ Drag Me</span>
            <span style="font-size:0.8rem;opacity:0.8;">📅</span>
        </div>
        
        <!-- Window content: calendar -->
        <div id="content">
            <div id="calendar"></div>
            <div id="cal-info">Click a date to select</div>
        </div>
    </div>

    <!-- ================================================
         Photo Gallery
         ================================================ -->
    <h1 style="text-align: center; padding: 20px;">My Photo Album</h1>
    
    <div class="album">
        <img src="1.JPG" onclick="showBig(this.src)">
        <img src="2.JPG" onclick="showBig(this.src)">
        <img src="3.JPG" onclick="showBig(this.src)">
        <img src="4.JPG" onclick="showBig(this.src)">
        <img src="5.JPG" onclick="showBig(this.src)">
        <img src="6.JPG" onclick="showBig(this.src)">
    </div>

    <!-- Lightbox modal for enlarged images -->
    <div class="mask" id="mask">
        <span class="close" onclick="closeBig()">×</span>
        <img src="" id="bigImg">
    </div>

    <!-- ================================================
         Real-Time Clock
         ================================================ -->
    <div id="clock"></div>

    <script>
        /* ================================================
           Window Drag Logic
           IIFE (Immediately Invoked Function Expression)
           ================================================ */
        
        (function() {
            const win = document.getElementById('win');
            let offsetX = 0, offsetY = 0;

            /**
             * Start dragging the window
             * Calculates offset between mouse position and window position
             * @param {MouseEvent} e - Mouse down event
             */
            window.startDrag = function(e) {
                e.preventDefault();
                offsetX = e.clientX - win.offsetLeft;
                offsetY = e.clientY - win.offsetTop;

                // Track mouse movement
                document.onmousemove = function(e) {
                    e.preventDefault();
                    win.style.left = e.clientX - offsetX + 'px';
                    win.style.top = e.clientY - offsetY + 'px';
                };

                // Stop dragging on mouse release
                document.onmouseup = function() {
                    document.onmousemove = null;
                    document.onmouseup = null;
                };
            };
        })();

        /* ================================================
           Calendar Logic
           IIFE (Immediately Invoked Function Expression)
           ================================================ */
        
        (function() {
            const calDiv = document.getElementById('calendar');
            const infoDiv = document.getElementById('cal-info');
            let currentYear, currentMonth;
            let selectedDate = null;  // Stores the currently selected date

            /**
             * Render the calendar for a given month and year
             * Generates the full calendar HTML with navigation and date grid
             * @param {number} year - The year to display
             * @param {number} month - The month to display (0-11)
             */
            function renderCalendar(year, month) {
                currentYear = year;
                currentMonth = month;

                // Calculate calendar data
                const firstDay = new Date(year, month, 1).getDay();  // 0 = Sunday
                const daysInMonth = new Date(year, month + 1, 0).getDate();
                const daysInPrevMonth = new Date(year, month, 0).getDate();
                
                // Get today's date for highlighting
                const today = new Date();
                const todayDate = today.getDate();
                const todayMonth = today.getMonth();
                const todayYear = today.getFullYear();

                // Start building HTML
                let html = `
                    <div class="cal-header">
                        <button onclick="window.calPrev()">‹</button>
                        <span>${year}年 ${month + 1}月</span>
                        <button onclick="window.calNext()">›</button>
                    </div>
                    <table>
                        <thead><tr>
                            <th>Sun</th><th>Mon</th><th>Tue</th><th>Wed</th><th>Thu</th><th>Fri</th><th>Sat</th>
                        </tr></thead>
                        <tbody><tr>
                `;

                // Fill in days from previous month (greyed out)
                for (let i = 0; i < firstDay; i++) {
                    const day = daysInPrevMonth - firstDay + i + 1;
                    html += `<td class="other-month">${day}</td>`;
                }

                // Fill in current month days
                for (let d = 1; d <= daysInMonth; d++) {
                    // Check if this day is today
                    const isToday = (d === todayDate && month === todayMonth && year === todayYear);
                    // Check if this day is selected
                    const isSelected = selectedDate &&
                                      d === selectedDate.getDate() &&
                                      month === selectedDate.getMonth() &&
                                      year === selectedDate.getFullYear();
                    
                    // Apply appropriate CSS classes
                    const cls = (isToday ? 'today' : '') + (isSelected ? ' selected' : '');
                    html += `<td class="${cls}" onclick="window.calSelect(${year}, ${month}, ${d})">${d}</td>`;
                    
                    // New row every 7 days
                    if ((firstDay + d - 1) % 7 === 6 && d < daysInMonth) {
                        html += `</tr><tr>`;
                    }
                }

                // Fill in remaining cells with next month's days
                const totalCells = firstDay + daysInMonth;
                const remain = (7 - totalCells % 7) % 7;
                for (let i = 1; i <= remain; i++) {
                    html += `<td class="other-month">${i}</td>`;
                }

                html += `</tr></tbody></table>`;
                calDiv.innerHTML = html;

                // Update info text
                if (selectedDate) {
                    infoDiv.textContent = `Selected: ${selectedDate.getFullYear()}/${selectedDate.getMonth()+1}/${selectedDate.getDate()}`;
                } else {
                    infoDiv.textContent = 'Click a date to select';
                }
            }

            /**
             * Navigate to previous month
             * Wraps to December of previous year if in January
             */
            window.calPrev = function() {
                if (currentMonth === 0) {
                    renderCalendar(currentYear - 1, 11);
                } else {
                    renderCalendar(currentYear, currentMonth - 1);
                }
            };

            /**
             * Navigate to next month
             * Wraps to January of next year if in December
             */
            window.calNext = function() {
                if (currentMonth === 11) {
                    renderCalendar(currentYear + 1, 0);
                } else {
                    renderCalendar(currentYear, currentMonth + 1);
                }
            };

            /**
             * Select a date and re-render to show selection
             * @param {number} year - Year of selected date
             * @param {number} month - Month of selected date
             * @param {number} day - Day of selected date
             */
            window.calSelect = function(year, month, day) {
                selectedDate = new Date(year, month, day);
                renderCalendar(currentYear, currentMonth);  // Re-render to update highlight
            };

            // Initialize calendar with current month
            const now = new Date();
            renderCalendar(now.getFullYear(), now.getMonth());
        })();

        /* ================================================
           Photo Gallery Lightbox Functions
           ================================================ */
        
        /**
         * Display clicked image in fullscreen lightbox
         * @param {string} src - Source URL of the image to display
         */
        function showBig(src) {
            document.getElementById("bigImg").src = src;
            document.getElementById("mask").style.display = "flex";
        }

        /**
         * Close the lightbox modal
         */
        function closeBig() {
            document.getElementById("mask").style.display = "none";
        }

        /* ================================================
           Real-Time Clock
           ================================================ */
        
        // Get clock element reference
        const clock = document.getElementById("clock");

        // Set initial time immediately
        clock.innerText = new Date().toLocaleString('zh-CN', { hour12: false });

        // Update clock every second
        setInterval(() => {
            clock.innerText = new Date().toLocaleString('zh-CN', { hour12: false });
        }, 1000);
    </script>

</body>
</html>
