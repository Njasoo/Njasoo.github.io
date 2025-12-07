---
title: js贪吃蛇
date: 2025-11-01 10:02:03
tags: ["js"]
---
贪吃蛇的关键逻辑在于，移动蛇的时候，其实是生成一个新的头，然后把尾巴去掉，而不是遍历蛇的身体每个坐标，然后移动

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>贪吃蛇游戏</title>
</head>
<body>
    <h2>贪吃蛇 🐍</h2>
    <div id="game"></div>
</body>
<script>
const size = 20;
const game = document.getElementById("game");
const cells = [];

for (let i = 0; i < size * size; i++) {
    const cell = document.createElement("div");
    cell.classList.add("cell");
    game.appendChild(cell);
    cells.push(cell);
}

function index(x, y) {
    return y * size + x; // 列优先吗
}

let snake = [{x: 5, y: 5}, {x: 4, y: 5}, {x: 3, y: 5}];
let direction = "right";
let food = {x: 10, y: 10};

function render() {
    cells.forEach(c => c.className = "cell");
    for (let s of snake) {
        cells[index(s.x, s.y)].classList.add("snake")
    }
    cells[index(food.x, food.y)].classList.add("food");
}

function move() {
    const head = {...snake[0]}; // 展开，作用是深拷贝，直接复制会传引用
    if (direction == "right") head.x++;
    if (direction == "left") head.x--;
    if (direction == "up") head.y--;
    if (direction == "down") head.y++;

    if (head.x < 0 || head.x >= size || head.y < 0 || head.y >= size) {
        alert("游戏结束");
        clearInterval(timer);
        return;
    }

    for (let s of snake) {
        if (s.x === head.x && s.y === head.y) {
            alert("游戏结束");
            clearInterval(timer);
            return;
        }
    }

    snake.unshift(head); // 头部插入元素

    if (head.x === food.x && head.y === food.y) {
        food = {x: Math.floor(Math.random() * size), y: Math.floor(Math.random() * size)};
    } else {
        snake.pop();
    }
    render();
}

document.addEventListener("keydown", e => {
    if (e.key === "ArrowUp" && direction !== "down") direction = "up";
    if (e.key === "ArrowDown" && direction !== "up") direction = "down";
    if (e.key === "ArrowLeft" && direction !== "right") direction = "left";
    if (e.key === "ArrowRight" && direction !== "left") direction = "right";
})

render();
const timer = setInterval(move, 200);
</script>
<style>
body {
    display: flex;
    flex-direction: column;
    align-items: center;
    background: #fafafa;
}

#game {
    display: grid;
    grid-template-columns: repeat(20, 20px);
    grid-template-rows: repeat(20, 20px);
    gap: 1px;
    background: #ccc;
    margin-top: 20px;
}

.cell {
    width: 20px;
    height: 20px;
    background: white;
}

.snake {
    background: green;
}

.food {
    background: red;
}
</style>
</html>
```