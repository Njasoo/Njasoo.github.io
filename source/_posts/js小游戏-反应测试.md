---
title: js小游戏-反应测试
date: 2025-11-01 20:04:53
tags: ["js"]
---
挺好玩的，蛮神奇的，事件触发改成用鼠标的话，反应时间测出来是270ms左右，但是用keydown事件直接变成180ms左右，可能是鼠标按键抬起来太费时间了
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Reaction Time Test</title>
</head>
<body class="waiting">
    <div class="flex-container">
        <h1 class="mytext">按下空格进行测试</h1>
        <h1 class="mytext"></h1>
    </div>
</body>
<script>
const body = document.body
const text = document.getElementsByClassName("mytext")[0];
const avgTimeText = document.getElementsByClassName("mytext")[1];
let startTime = null;
let cnt = 0;
let avg = 0;
let timerId = null;

body.addEventListener("keydown", e => {
    if (e.code == "Space") { // e.code == "Space"也行
        avgTimeText.textContent = "";
        if (body.className == "testing") {
            body.className = "waiting";
            text.textContent = "不要提前按";
            clearTimeout(timerId);
        } else if (body.className == "starting") {
            body.className = "waiting";
            const timeSpend = performance.now() - startTime;
            text.textContent = timeSpend.toString() + "ms";
            cnt++;
            avg += timeSpend;
            if (cnt == 5) {
                cnt = 0;
                avg /= 5;
                avg = Number(Math.floor(avg));
                avgTimeText.textContent = "平均时间：" + avg.toString() + "ms, 按下空格再次测试";
                avg = 0;
            } else {
                avgTimeText.textContent = cnt.toString() + "/5";
            }
        } else if (body.className == "waiting") {
            body.className = "testing";
            text.textContent = "等待变绿...";
            const delay = Math.random() * 1000 + 2000;
            timerId = setTimeout(function() {
                requestAnimationFrame(() => {
                    body.className = "starting";
                    text.textContent = "按下空格";
                    startTime = performance.now();
                })
            }, delay);
        }
        console.log("点击事件触发")
    }
})
</script>
<style>
body {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

.flex-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

body:hover {
    cursor: pointer;
}

.waiting {
    background: rgb(0, 0, 255);
}

.testing {
    background-color: red;
}

.starting {
    background-color: rgb(0, 255, 0);
}

.mytext {
    color: white;
    font-size: 50px;
}
</style>
</html>
```