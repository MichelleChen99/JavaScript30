### Goal
當使用者敲擊特定的一組按鍵，會播放對應的鼓聲，且被敲擊的按鍵圖案閃現光圈。

### Flow (What)
1. 找到 audio 與 key
2. 若不存在則退出程式
3. 重設並播放 audio
4. 加入 playing class
5. CSS transition 結束
6. 移除 playing 樣式

---
1. 透過keyCode，分別將按鍵圖案與鼓聲連結起來。
2. 當按鍵被敲擊的時候，先將相對應的鼓聲音檔指定為從0秒開始，再進行播放。
3. 透過CSS class selector，選取所有按鍵圖案並加入光圈樣式。
4. 當光圈樣式已渲染完畢，將該樣式移除回到原鍵盤圖案樣式。

### Mechanism (How)


### Decision (Why)
為什麼每次都要把音檔的時間設為0秒？
為什麼要用transitionend，而不是setTimeout？

### Syntax notes
getElementsByClassName()和querySelector()有何不同？
NodeList是什麼樣的資料結構？如何產生？為何需要用forEach()來遍歷？
為何removeTransition()裡面的this能讀到key的值？ (closure)

9/16
window、document是什麼？attribute、property、method又是什麼？
HTML的Element和React的Component有什麼不同？
為什麼要叫querySelector而非elementSelector？

addEventListener只是先「註冊事件」，等待發生時機才執行，因此屬於「非同步」，callback不能加小括號

CSS屬性選擇器(attribute selector)的語法為什麼是用方括號？
