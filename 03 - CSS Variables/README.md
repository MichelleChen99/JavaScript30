### Goal
利用CSS variables與JavaScript動態調整CSS的值，以改變圖片樣式。

### Flow
1. 定義CSS custom properties(CSS variables)的預設值
2. 將圖片的CSS properties設為引用上述custom properties
3. 選取所有控制圖片樣式的<input>
4. 在每個input上註冊監聽器，監聽input value的變化
5. 將新值寫入<html>的inline custom property，更新畫面

### Decision
1. CSS variable是什麼？
```css
:root {
    --spacing: 10px;
}
img {
    padding: var(--spacing);
}
p {
    margin: var(--spacing);
}
```
把`10px`存在`--spacing`，需要`10px`的地方都可以寫`var(--spacing)`，即可讀取該值。
從本質來看，`--spacing`稱呼為custom property比較妥當；從應用方式來看，可稱為CSS variable。

倘若未來需修改spacing，只要改`:root`裡面的`--spacing`，依賴這個值的<img>內距、<p>外距都會連動修改，方便集中管理。

更重要的是：CSS variable會保留到瀏覽器執行階段，因此可以用JavaScript來調整它。
常見的CSS預處理器Sass，它的變數(語法寫成`$spacing`)只存在於編譯階段，因此只能在編譯階段讀取值，統一修改有用到該變數的地方；
而到瀏覽器執行階段，值就固定了，沒辦法動態調整。

CSS variable

2. 為何suffix要加fallback？