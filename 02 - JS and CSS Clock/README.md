### Goal
透過CSS、JavaScript打造出一個實時的擬真機械時鐘。

### Flow
1. 取得當前的hour、minute、second
2. 計算當前的時、分、秒針相應的旋轉角度
3. 當指針指向12點鐘位置時，移除transition動畫
4. 依step 2計算結果，轉動時、分、秒針
5. 每隔1秒重複執行上述步驟

### Decision
1. 計算旋轉角度時，為何時、分、秒針都要多加 90 度？
```javascript
const secondDegrees = ((seconds / 60) * 360) + 90;

const minuteDegrees = ((minutes / 60) * 360) + ((seconds / 60) * 6) + 90;

const hourDegrees = (((hours % 12) / 12) * 360) + ((minutes / 60) * 30) + 90; 
```

因為CSS(style sheet)的畫法，指針是先指向9點鐘方向，再旋轉90度到12點鐘方向：
```css
.clock {
    position: relative;
}
.clock-face {
    position: relative;
}

.hand {
    width: 50%;
    height: 6px;
    /* 指針寬度佔時鐘的50%，且高度很低，因此畫出來是橫的 */
    background: black;
    position: absolute;
    top: 50%;
    right: 50%;
    /* 指針定位在鐘面的垂直、水平中線；配合上面的寬、高設定，就會畫出指向9點鐘方向的指針 */
    transform-origin: right center;
    /* 以指針右端為支點旋轉 */
    transform: rotate(90deg);
    /* 從9點鐘方向旋轉90度到12點鐘方向 */
}
```

JS計算出來的旋轉角度，會直接在DOM裡面inline產生動態變化，也就是會覆蓋掉style sheet原本已經設定好的旋轉90度，所以才需要再加回去。
```javascript
const secondHand = document.querySelector(".second-hand");
const minuteHand = document.querySelector(".min-hand");
const hourHand = document.querySelector(".hour-hand");

secondHand.style.transform = `rotate(${secondDegrees}deg)`;
minuteHand.style.transform = `rotate(${minuteDegrees}deg)`;
hourHand.style.transform = `rotate(${hourDegrees}deg)`;
```

用JS補償90度是其中的一種解法。另一種解法是：一開始就畫縱向指針，配合CSS將指針依照自身寬度水平置中。
相對的，指針的整個座標定位都需要修改，例如：
```css
.hand {
  bottom: 50%;
  left: 50%;
  translate: -50% 0;
  transform-origin: bottom center;
}
```
---
2. 為何會出現指針反彈的問題？

3. 如何解決指針反彈的問題？