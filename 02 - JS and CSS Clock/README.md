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

JS計算出來的旋轉角度，會直接在DOM裡面inline產生動態變化，
也就是會覆蓋掉style sheet原本已經設定好的旋轉90度，所以才需要再加回去。
```javascript
const secondHand = document.querySelector(".second-hand");
const minuteHand = document.querySelector(".min-hand");
const hourHand = document.querySelector(".hour-hand");

secondHand.style.transform = `rotate(${secondDegrees}deg)`;
minuteHand.style.transform = `rotate(${minuteDegrees}deg)`;
hourHand.style.transform = `rotate(${hourDegrees}deg)`;
```

用JS補償90度是其中的一種解法。另一種解法是：
一開始就畫縱向指針，配合CSS將指針依照自身寬度水平置中。
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
2. 為何走到12點鐘當下會出現指針逆轉的問題？
```css
.hand {
    transform-origin: right center;
    transform: rotate(90deg);
    transition: transform 0.5s cubic-bezier(0.1, 2.7, 0.58, 1);
}
```
指針「前進」是每次計算的角度動態變化，上一次角度小於下一次角度產生的效果。
譬如10秒走到11秒，其實就是指針從150度走到156度：
```js
((10 / 60) * 360) + 90 = 150; // 10秒
((11 / 60) * 360) + 90 = 156; // 11秒
```

當秒針要走到12點鐘方向時，59秒到0秒的角度變化則是：
```js
((59 / 60) * 360) + 90 = 444; // 59秒
((0 / 60) * 360) + 90 = 90; // 0秒
```
從444度走到90度的時候，由於transform套用了transition，
瀏覽器會在前後兩個rotate()角度之間產生「中間值」，做出漸進式的變化，
概念上類似：
444 => 355.5 => 267 => 178.5 => 90

視覺上就會變成「逆向旋轉」一圈，才到達12點鐘方向。
這個問題在時、分、秒針都會發生。

3. 如何解決指針逆轉的問題？
當指針即將走到12點鐘的時候，讓指針的前後兩個角度「瞬間切換」，直接從444切換至90，中間無插值，就不會看到指針逆轉的現象。

因此，我們要阻止瀏覽器從444度轉到90度的時候，產生transition的漸進變化效果。
具體來說，就是將transition設為`none`，移除漸變效果。

```js
const isSecondReset = seconds === 0;
const isMinuteReset = minutes === 0 && seconds === 0;
const isHourReset = hours % 12 === 0 && minutes === 0;

function setTransition(hand, shouldReset) {
    hand.style.transition = shouldReset ? "none" : "";
} // 條件式地切換inline transition

setTransition(secondHand, isSecondReset);
setTransition(minuteHand, isMinuteReset);
setTransition(hourHand, isHourReset);
```

注意DOM style更新的順序：
必須先設定此次角度變化是否需要transition，再更新transform，執行旋轉動作。
如果不小心順序弄反，先旋轉完、再取消transition，就失去移除漸變的意義了。
```js
setTransition(secondHand, isSecondReset);
secondHand.style.transform = `rotate(${secondDegrees}deg)`;
```
