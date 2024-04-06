<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pomodoro Study Timer</title>
<style>
  body {
    font-family: Arial, sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    flex-direction: column;
    z-index: 2;
    overflow: hidden;
    margin: 0;
  }
  #studyForm {
    position: relative;
    z-index: 10;
  }

  #clock {
    width: 200px;
    height: 200px;
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 20px;
    z-index: 10;
  }

  .timer {
    font-size: 2em;
    color: black;
  }

  .lava {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 0%;
    background: #ff4500;
    z-index: 1;
    overflow: hidden;
  }

  .lava::before, .lava::after {
    content: '';
    position: absolute;
    width: 100%;
    height: 20%;
    bottom: 10%;
    background: rgba(255, 69, 0, 0.6);
    border-radius: 100%;
  }

  .lava::before {
    left: 50%;
    transform: translateX(-50%) scale(1.5);
    animation: bubble 6s ease-in-out infinite;
  }

  .lava::after {
    left: 10%;
    transform: scale(1.2);
    animation: bubble 8s ease-in-out infinite;
  }

  @keyframes rise {
    from {
      height: 0%;
    }
    to {
      height: 100%;
    }
  }

  .pause {
    background: #0044ff;
  }
</style>
</head>
<body>

<form id="studyForm">
  <label for="studyTime">Enter study time in minutes:</label>
  <input type="number" id="studyTime" name="studyTime" min="1" required>
  <label for="breakTime">Enter break time in minutes:</label>
  <input type="number" id="breakTime" name="breakTime" min="1" required>
  <label for="cycles">Enter number of cycles:</label>
  <input type="number" id="cycles" name="cycles" min="1" required>
  <button type="submit">Start Timer</button>
</form>

<div id="clock" class="lava">
  <div class="timer" id="timerDisplay">00:00</div>
</div>

<script>
document.getElementById('studyForm').addEventListener('submit', function(event) {
  event.preventDefault();
  let studyTimeInSeconds = parseInt(document.getElementById('studyTime').value, 10) * 60;
  let breakTimeInSeconds = parseInt(document.getElementById('breakTime').value, 10) * 60;
  let cycles = parseInt(document.getElementById('cycles').value, 10);
  let isStudyPeriod = true;
  const lavaElement = document.querySelector('.lava');

  const resetAnimation = () => {
    lavaElement.style.animation = 'none';
    lavaElement.offsetHeight; // trigger reflow
  };

  const startAnimation = (duration, color) => {
    resetAnimation();
    lavaElement.style.background = color;
    lavaElement.style.animation = `rise ${duration}s linear forwards`;
  };

  const updateTimer = () => {
    let currentTime = isStudyPeriod ? studyTimeInSeconds : breakTimeInSeconds;
    const minutes = Math.floor(currentTime / 60);
    const seconds = currentTime % 60;
    document.getElementById('timerDisplay').textContent = `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;

    if (currentTime <= 0) {
      if (isStudyPeriod) {
        isStudyPeriod = false;
        startAnimation(breakTimeInSeconds, '#0044ff'); // Blue for break
      } else if (cycles > 1) {
        cycles--;
        isStudyPeriod = true;
        studyTimeInSeconds = parseInt(document.getElementById('studyTime').value, 10) * 60;
        breakTimeInSeconds = parseInt(document.getElementById('breakTime').value, 10) * 60;
        startAnimation(studyTimeInSeconds, '#ff4500'); // Red for study
      } else {
        clearInterval(interval);
        document.getElementById('timerDisplay').textContent = "All cycles completed!";
        return;
      }
    }

    isStudyPeriod ? studyTimeInSeconds-- : breakTimeInSeconds--;
  };

  startAnimation(studyTimeInSeconds, '#ff4500'); // Start with study period
  const interval = setInterval(updateTimer, 1000);
});
</script>
    
</body>
</html>
