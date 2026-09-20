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
    /* 指針定位在時鐘上方、右方各50%的地方，也就是9點鐘方向 */
    transform: rotate(90deg);
    /* 然後再旋轉90度到12點鐘方向 */
}
```

JS計算出來的旋轉角度，會直接在DOM裡面inline產生動態變化，也就是會覆蓋掉style sheet原本已經設定好的旋轉90度，所以前面才需要再加回去。
```javascript
const secondHand = document.querySelector(".second-hand");
const minuteHand = document.querySelector(".min-hand");
const hourHand = document.querySelector(".hour-hand");

secondHand.style.transform = `rotate(${secondDegrees}deg)`;
minuteHand.style.transform = `rotate(${minuteDegrees}deg)`;
hourHand.style.transform = `rotate(${hourDegrees}deg)`;
```
另外有一處CSS的細節：橫向指針比較容易將旋轉端點固定在時鐘的正中央。
若寫成縱向指針，固然不必再用JS補償90度，但這時指針粗細就會影響指針指向的精確度，會產生1-2px的偏差。
