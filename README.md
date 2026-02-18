<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DTU CSE Study Dashboard</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg: #0f172a;
            --glass: rgba(255, 255, 255, 0.05);
            --accent: #8b5cf6;
            --text: #f8fafc;
            --free-slot: rgba(139, 92, 246, 0.1);
            --class-slot: rgba(59, 130, 246, 0.3);
            --success: #10b981;
        }

        body {
            background: radial-gradient(circle at top left, #1e1b4b, #0f172a);
            color: var(--text);
            font-family: 'Inter', sans-serif;
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .container {
            max-width: 1100px;
            width: 100%;
        }

        header {
            text-align: center;
            margin-bottom: 30px;
        }

        h1 { font-weight: 600; letter-spacing: -1px; margin-bottom: 5px; }
        p { opacity: 0.6; font-size: 0.9rem; }

        /* Timer Section */
        .top-widgets {
            display: grid;
            grid-template-columns: 1fr 1.5fr;
            gap: 20px;
            margin-bottom: 30px;
        }

        .card {
            background: var(--glass);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 20px;
            padding: 20px;
        }

        .pomodoro {
            text-align: center;
        }

        .timer-display {
            font-size: 3rem;
            font-weight: 600;
            margin: 10px 0;
        }

        .btn {
            background: var(--accent);
            border: none;
            color: white;
            padding: 8px 20px;
            border-radius: 10px;
            cursor: pointer;
            transition: 0.3s;
        }

        .btn:hover { opacity: 0.8; transform: translateY(-2px); }

        /* Timetable Grid */
        .timetable-container {
            overflow-x: auto;
        }

        table {
            width: 100%;
            border-collapse: separate;
            border-spacing: 8px;
            table-layout: fixed;
        }

        th {
            font-size: 0.8rem;
            opacity: 0.7;
            padding: 10px;
            font-weight: 400;
        }

        td {
            height: 70px;
            border-radius: 12px;
            font-size: 0.75rem;
            text-align: center;
            padding: 5px;
            cursor: pointer;
            transition: 0.2s;
            border: 1px solid rgba(255, 255, 255, 0.05);
            position: relative;
        }

        .slot-class { background: var(--class-slot); border-left: 4px solid #3b82f6; }
        .slot-free { background: var(--free-slot); border-left: 4px solid rgba(139, 92, 246, 0.3); }

        td.completed {
            background: var(--success) !important;
            border-left: 4px solid #059669;
        }

        td.completed::after {
            content: '✓';
            position: absolute;
            top: 5px;
            right: 5px;
            font-weight: bold;
        }

        /* Weekend Tracker */
        .weekend-card {
            margin-top: 30px;
        }

        .progress-bar {
            height: 12px;
            background: rgba(255,255,255,0.1);
            border-radius: 10px;
            margin: 15px 0;
            overflow: hidden;
        }

        .progress-fill {
            height: 100%;
            width: 0%;
            background: linear-gradient(90deg, #8b5cf6, #d946ef);
            transition: 0.5s ease;
        }

        .weekend-inputs {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }

        .hour-chip {
            padding: 5px 12px;
            background: var(--glass);
            border-radius: 20px;
            font-size: 0.8rem;
            cursor: pointer;
        }

        .hour-chip.active { background: var(--accent); }

    </style>
</head>
<body>

    <div class="container">
        <header>
            <h1>DTU SEM-II Tracker</h1>
            <p>Computer Science & Engineering | SEC-4</p>

            <div style="text-align:center; margin-bottom:20px;">
    <h3 id="secretCountdownTitle">Personal Countdown</h3>
    <p id="secretCountdown" style="font-size:1.2rem; opacity:0.8;"></p>
</div>

        </header>

        <div class="top-widgets">
            <div class="card pomodoro">
                <h3>Study Timer</h3>
                <div style="margin-top:15px;">
    <h4>Daily Study Log</h4>

    <label>College Study Hours</label>
    <input type="number" id="collegeHours" min="0" max="10">

    <label>JEE Study Hours</label>
    <input type="number" id="jeeHours" min="0" max="12">

    <button class="btn" onclick="saveStudyHours()">Save Today</button>

    <p id="streakDisplay">🔥 Streak: 0 days</p>
</div>

                <div class="timer-display" id="timer">25:00</div>
                <button class="btn" onclick="toggleTimer()" id="timerBtn">Start Session</button>
            </div>
            <div class="card">
                <h3>Daily Goal</h3>
                <p>Complete your classes and fill free slots with revision.</p>
                <div class="weekend-inputs" id="dailyGoal">
                    Click slots in the timetable to track productivity.
                </div>
            </div>
        </div>

        <div class="card timetable-container">
            <table>
                <thead>
                    <tr>
                        <th>Time</th>
                        <th>MON</th>
                        <th>TUE</th>
                        <th>WED</th>
                        <th>THU</th>
                        <th>FRI</th>
                    </tr>
                </thead>
                <tbody id="timetableBody">
                    <tr>
                        <th>08-09</th>
                        <td class="slot-class" title="CS104 Lab">CS104 Lab</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-free">Self-Study</td>
                    </tr>
                    <tr>
                        <th>09-10</th>
                        <td class="slot-class">CS104 Lab</td>
                        <td class="slot-class">AEC/VAC</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-class">AEC/VAC</td>
                        <td class="slot-free">Self-Study</td>
                    </tr>
                    <tr>
                        <th>10-11</th>
                        <td class="slot-free">Study Break</td>
                        <td class="slot-class">CS104(L)</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-class">SEC-1</td>
                        <td class="slot-free">Self-Study</td>
                    </tr>
                    <tr>
                        <th>11-12</th>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-class">AP102(L)</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-free">Self-Study</td>
                    </tr>
                    <tr>
                        <th>12-01</th>
                        <td class="slot-free">Lunch</td>
                        <td class="slot-free">Lunch</td>
                        <td class="slot-free">Lunch</td>
                        <td class="slot-class">CS102(L+T)</td>
                        <td class="slot-free">Lunch</td>
                    </tr>
                    <tr>
                        <th>01-02</th>
                        <td class="slot-class">CS102(L)</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-class">CS102(L+T)</td>
                        <td class="slot-class">CS102(L)</td>
                    </tr>
                    <tr>
                        <th>02-03</th>
                        <td class="slot-class">AM102(L)</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-class">AM102(L)</td>
                        <td class="slot-class">CS104(L)</td>
                        <td class="slot-free">Self-Study</td>
                    </tr>
                    <tr>
                        <th>03-04</th>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-class">AM102(T)</td>
                        <td class="slot-class">AM102(L)</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-free">Self-Study</td>
                    </tr>
                    <tr>
                        <th>04-05</th>
                        <td class="slot-class">AP102(L)</td>
                        <td class="slot-class">CS106 Lab</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-class">AP102(P) Lab</td>
                    </tr>
                    <tr>
                        <th>05-06</th>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-class">CS106 Lab</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-free">Self-Study</td>
                        <td class="slot-class">AP102(P) Lab</td>
                    </tr>
                </tbody>
            </table>
        </div>

        <div class="card weekend-card">
            <h3>Weekend Deep Work Tracker (14 Hour Goal)</h3>
            <p>Sat & Sun: Click the hour chips as you complete them.</p>
            <div class="progress-bar">
                
                <div class="progress-fill" id="weekendProgress"></div>
            </div>
            <div class="weekend-inputs" id="weekendChips">
                </div>
                <div style="margin-top:15px;">
    <h4>Weekly Analytics</h4>

    <div class="progress-bar">
        <div class="progress-fill" id="weeklyProgress"></div>
    </div>
    <p id="weekendSummary"></p>
    <p>College Hours: <span id="collegeTotal">0</span></p>
    <p>JEE Hours: <span id="jeeTotal">0</span></p>
</div>

        </div>
    </div>

    <script>
    


/* ================= DATE + WEEK RESET ================= */

function getWeekStart(date = new Date()) {
    const d = new Date(date);
    const day = d.getDay();
    const diff = d.getDate() - day + (day === 0 ? -6 : 1);
    return new Date(d.setDate(diff)).toDateString();
}

const today = new Date().toDateString();
const currentWeek = getWeekStart();

if (localStorage.getItem("weekStart") !== currentWeek) {
    localStorage.clear();
    localStorage.setItem("weekStart", currentWeek);
}

/* ================= TIMETABLE LOCK + SAVE ================= */

let timetableData = JSON.parse(localStorage.getItem("timetableData") || "{}");

document.querySelectorAll('#timetableBody td').forEach((slot, index) => {

    const key = "slot_" + index;

    if (timetableData[key]?.done) slot.classList.add("completed");

    if (timetableData[key] && timetableData[key].date !== today) {
        slot.style.pointerEvents = "none";
        slot.style.opacity = "0.6";
    }

    slot.addEventListener("click", () => {

        if (timetableData[key] && timetableData[key].date !== today) return;

        slot.classList.toggle("completed");

        timetableData[key] = {
            done: slot.classList.contains("completed"),
            date: today
        };

        localStorage.setItem("timetableData", JSON.stringify(timetableData));
    });
});

/* ================= STUDY HOURS TRACKER ================= */

let hoursData = JSON.parse(localStorage.getItem("hoursData") || "{}");

function saveStudyHours() {

    const college = Number(document.getElementById("collegeHours").value || 0);
    const jee = Number(document.getElementById("jeeHours").value || 0);

    hoursData[today] = { college, jee };

    localStorage.setItem("hoursData", JSON.stringify(hoursData));

    updateAnalytics();
    updateStreak();
}

/* ================= STREAK ================= */

function updateStreak() {

    let streak = 0;
    let d = new Date();

    while (true) {
        let key = d.toDateString();

        if (hoursData[key]) streak++;
        else break;

        d.setDate(d.getDate() - 1);
    }

    document.getElementById("streakDisplay").innerText = `🔥 Streak: ${streak} days`;
}

updateStreak();

/* ================= ANALYTICS ================= */

function updateAnalytics() {

    let collegeTotal = 0;
    let jeeTotal = 0;

    Object.values(hoursData).forEach(day => {
        collegeTotal += day.college;
        jeeTotal += day.jee;
    });

    document.getElementById("collegeTotal").innerText = collegeTotal;
    document.getElementById("jeeTotal").innerText = jeeTotal;

    let percent = Math.min((collegeTotal + jeeTotal) / 50 * 100, 100);
    document.getElementById("weeklyProgress").style.width = percent + "%";
}

updateAnalytics();

/* ================= POMODORO TIMER (Auto Adds JEE Time) ================= */

let timerDisplay = document.getElementById('timer');
let timerBtn = document.getElementById('timerBtn');

let timeLeft = 25 * 60;
let timerId = null;

function toggleTimer() {

    if (timerId) {
        clearInterval(timerId);
        timerId = null;
        timerBtn.innerText = "Resume Session";
    } else {

        timerBtn.innerText = "Focusing...";

        timerId = setInterval(() => {

            timeLeft--;

            let mins = Math.floor(timeLeft / 60);
            let secs = timeLeft % 60;

            timerDisplay.innerText = `${mins}:${secs < 10 ? '0' : ''}${secs}`;

            if (timeLeft <= 0) {

                clearInterval(timerId);
                alert("Session Complete!");

                // Auto add 25 min to JEE hours
                if (!hoursData[today]) hoursData[today] = { college: 0, jee: 0 };

                hoursData[today].jee += 0.42;

                localStorage.setItem("hoursData", JSON.stringify(hoursData));

                updateAnalytics();
                updateStreak();

                timeLeft = 25 * 60;
            }

        }, 1000);
    }
}

/* ================= WEEKEND TRACKER ================= */

/* ================= ADVANCED WEEKEND JEE TRACKER ================= */

document.addEventListener("DOMContentLoaded", () => {

    const chipContainer = document.getElementById('weekendChips');
    if (!chipContainer) return;

    chipContainer.innerHTML = "";

    let weekendData = JSON.parse(localStorage.getItem("weekendData") || "{}");
    let hoursData = JSON.parse(localStorage.getItem("hoursData") || "{}");

    const totalChips = 14;
    const today = new Date().toDateString();
    const dayName = new Date().getDay(); // 6 = Sat, 0 = Sun

    for (let i = 1; i <= totalChips; i++) {

        const chip = document.createElement('div');
        chip.className = 'hour-chip';

        const isSaturday = i <= 7;
        chip.innerText = isSaturday ? `Sat Hr ${i}` : `Sun Hr ${i - 7}`;

        const key = "chip_" + i;

        /* Restore saved state */
        if (weekendData[key]?.active) chip.classList.add("active");

        /* LOCK based on day */
        const isWrongDay =
            (isSaturday && dayName !== 6) ||
            (!isSaturday && dayName !== 0);

        if (weekendData[key] && weekendData[key].date !== today || isWrongDay) {
            chip.style.pointerEvents = "none";
            chip.style.opacity = "0.5";
        }

        chip.onclick = () => {

            chip.classList.toggle("active");

            weekendData[key] = {
                active: chip.classList.contains("active"),
                date: today
            };

            /* Auto add/remove JEE hours */
            if (!hoursData[today]) hoursData[today] = { college: 0, jee: 0 };

            if (chip.classList.contains("active")) {
                hoursData[today].jee += 1;
            } else {
                hoursData[today].jee = Math.max(0, hoursData[today].jee - 1);
            }

            localStorage.setItem("weekendData", JSON.stringify(weekendData));
            localStorage.setItem("hoursData", JSON.stringify(hoursData));

            updateWeekendProgress();
            updateWeekendStats();
        };

        chipContainer.appendChild(chip);
    }

    updateWeekendProgress();
    updateWeekendStats();
});

/* ================= PROGRESS BARS ================= */

function updateWeekendProgress() {

    const total = 14;
    const active = document.querySelectorAll('.hour-chip.active').length;

    document.getElementById('weekendProgress').style.width =
        (active / total) * 100 + "%";
}

/* ================= SAT / SUN BREAKDOWN ================= */

function updateWeekendStats() {

    const sat = [...document.querySelectorAll('.hour-chip.active')]
        .filter(chip => chip.innerText.includes("Sat")).length;

    const sun = [...document.querySelectorAll('.hour-chip.active')]
        .filter(chip => chip.innerText.includes("Sun")).length;

    let message = "";

    if (sat + sun === 14) message = "🔥 Full Weekend Destroyed. Elite Consistency.";
    else if (sat + sun >= 10) message = "💪 Strong Work. Keep Pushing.";
    else if (sat + sun >= 5) message = "⚡ Decent Momentum. Don't Slow Down.";
    else message = "🚀 Weekend Just Started.";

    console.log(`Sat: ${sat}h | Sun: ${sun}h`);
   document.getElementById("weekendSummary").innerText =
`Saturday: ${sat}h | Sunday: ${sun}h → ${message}`;
/* ================= 80 DAY PRIVATE COUNTDOWN ================= */

const COUNTDOWN_DAYS = 80;

// Save start date only once
if (!localStorage.getItem("countdownStart")) {
    localStorage.setItem("countdownStart", new Date().toISOString());
}

function updateSecretCountdown() {

    const startDate = new Date(localStorage.getItem("countdownStart"));
    const endDate = new Date(startDate);
    endDate.setDate(startDate.getDate() + COUNTDOWN_DAYS);

    const now = new Date();

    const diff = endDate - now;

    if (diff <= 0) {
        document.getElementById("secretCountdown").innerText =
            "Countdown Complete.";
        return;
    }

    const days = Math.floor(diff / (1000 * 60 * 60 * 24));
    const hours = Math.floor((diff / (1000 * 60 * 60)) % 24);
    const mins = Math.floor((diff / (1000 * 60)) % 60);
    const secs = Math.floor((diff / 1000) % 60);

    document.getElementById("secretCountdown").innerText =
        `${days} Days ${hours} Hours ${mins} Minutes ${secs} Seconds`;
}

setInterval(updateSecretCountdown, 1000);
updateSecretCountdown();

}




</script>
</body>
</html>
