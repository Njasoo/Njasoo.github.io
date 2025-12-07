---
title: 学习day18
date: 2025-12-01 16:23:32
tags: ["vue"]
---

# 简单 reactive 的实现

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div>
      <input type="text" id="input_box" />
    </div>
    <div>
      <label for="input_box" id="input_box_label">hello</label>
    </div>
  </body>
  <script>
    const input_box = document.getElementById("input_box");
    const input_box_label = document.getElementById("input_box_label");

    const reactive = (obj, callback) => {
      return new Proxy(obj, {
        set(target, prop, value) {
          target[prop] = value;
          callback(prop, value);
          return true;
        },
      });
    };

    const state = reactive({ inputValue: "" }, (key, value) => {
      if (key == "inputValue") {
        input_box_label.textContent = value;
      }
    });

    input_box.addEventListener("input", (e) => {
      state.inputValue = e.target.value;
    });
  </script>
</html>
```
