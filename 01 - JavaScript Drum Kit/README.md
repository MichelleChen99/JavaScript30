### Goal
當使用者按下按鍵，會播放對應的聲音，且該按鍵圖案出現短暫的特效。

### Flow (What)
1. 找到按鍵對應的 audio 與 key
2. 若不存在則退出程式
3. 重設並播放 audio
4. 加入 playing class
5. CSS transition 結束
6. 移除 playing 樣式

### Decision (Why)
1. 為什麼每次都要把audio的時間重設為0秒？
- 因為如果沒有重設audio時間，當連續按下一個按鍵的時候，要等前面的audio播完，後面的才能開始播。
- 當audio秒數大於兩次按下按鍵的間隔時間，就會變成第二次按鍵按下去了，卻沒聽到聲音，延遲幾秒後才聽見。

2. 為什麼移除CSS特效是用transitionend，而不是setTimeout()？
```css
.key {
    transition: all 0.07s ease;
}
```
- 如果用setTimeout()，指定倒數幾秒後移除CSS特效，而這個秒數必須對應CSS特效的持續時間長度。
```javascript
setTimeout(() => {
    key.remove("playing");
}, 70)
```
- 這樣一來，倘若日後要修改CSS特效持續時間，就必須同步修改setTimeout()的秒數，屆時很可能會忘記任何一邊而產生bug。
