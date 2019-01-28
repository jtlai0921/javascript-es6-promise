---
id: ch12_other_libs
title: Async 函式
sidebar_label: Async 函式
---

## 前言

處理 JavaScript 中的異步程序總是需要面臨到各種不同應用情況的挑戰，在 ES6(ES2015)中所加入的 Promise 以及 Generator，讓新版本的 JavaScript 多了一些工具和語法，可以更方便的來作這些異步的程序的流程控制。但這些新的工具或結構，或許又讓開發者有了一些新的問題，例如下面幾項:

- 語法複雜，需要進一步學習與大幅度修改原有的程式碼
- 除錯不容易
- 錯誤處理不容易
- 對於情況控制(Conditionals)與迴圈/迭代處理仍然不是很理想

async 函式在 ECMAScript 2017(ES8) 後加入了標準之中，其目的非常的明確，就是為了要解決上述的問題，它可以更進一步的精簡整個語法。

async/await 的語法十分容易學習與使用，相較於 Promise 或 Generator 而言，開發者可以在很短的時間內理解用法，以及開始使用它們，甚至不需要對 Promise 或 Generator 有太深入的知識。

不過，async 函式的基礎仍然是 Promise 與 Generator，雖然它提供了便捷的語法，能夠很輕易的處理這些異步程序的流程控制，不過我們仍然需要對 Promise 有一定的理解，才能真正靈活地應用到各種情況中。

## async 函式

一開始我們先以一個最簡單的 Promise 的範例來說明，我們使用 Fetch API 向伺服器要求待辦事項的資料，然後更新目前應用中的列表，程式碼範例如下:

```js
fetch('http://example.com/items')
  .then(response => response.json())
  .then(data => {
    updateView(data)
  })
  .catch(error => {
    console.log('Update failed', error)
  })
```

Promise 的語法結構的涵意是 "我想要進行這個操作，然後(then)在下一步對操作得到的資料再進行處理" 這是一種連鎖語法的結構。

使用 async/await 來修改上面的範例，會變為下面這樣的程式碼，你可以注意到 then 已經不存在這個程式碼中:

```js
const response = await fetch('http://example.com/items')
const data = await response.json()
updateView(data)
```

await 的語法涵意會是 "我想要得到這個操作的結果(值)"，這會感覺像是在撰寫同步的語句。

由於 await 運算子是被設計來等待 Promise 的，它只能在 async 函式 內使用。所以我們需要把它放在一個 async 函式 之中，像下面這樣的程式碼:

```js
async function updateMyView() {
  const response = await fetch('http://example.com/items')
  const data = await response.json()
  updateView(data)
}
```

至於最一開始 Promise 裡的 catch 方法，可以用來作錯誤處理，在 async/await 語法裡，要使用 try/catch 來取代它，如以下的程式碼:

```js
async function updateMyView() {
  try {
    const response = await fetch('http://example.com/items')
    const data = await response.json()
    updateView(data)
  } catch (error) {
    console.log('Update failed', error)
  }
}
```

當然，你也可以用 IIFE 或是箭頭函式的語法來搭配 async 函式，這可以因應不同的使用情況，以及讓語法更為簡化，像下面的程式碼:

```js
;(async () => {
  try {
    const response = await fetch('http://example.com/items')
    const data = await response.json()
    updateView(data)
  } catch (error) {
    console.log('Update failed', error)
  }
})()
```

當然，由於  async 函式呼叫後會返回一個 Promise，你也可以使用原本的 catch 方法來作錯誤處理，像下面這樣的程式碼:

```js
async function updateMyView() {
  const response = await fetch('http://example.com/items')
  const data = await response.json()
  updateView(data)
}

updateMyView().catch(error => {
  console.log('Update failed', error)
})
```

> 註: 上面的例子可以使用 catch 方法，當然也可以使用 then 方法

由上面的改寫範例，你可以看到我們雖然是在撰寫異步的程序，但寫出來的程式碼卻是像是在撰寫同步的程序。有被加上 await 的語句，會等待到該語句執行的結果得到後，才會接著處理下一步的程序語句。這使得整體的程式碼可閱讀性提高了，而且也變得更容易除錯。

> 註: 目前 await 不能在全域(頂級作用域)直接使用，它一定要在 async 函式內才能使用

## async 函式的語法說明

根據 MDN 中有關於[async 函式](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Statements/async_function)的說明:

> 當 async 函式被呼叫時，它會回傳一個 Promise，這與有沒有使用 await 無關

async 函式被呼叫後，如果回傳一個值，就會被視為帶有該回傳值的實現(resolved)狀態的 Promise，反之如果拋出例外，就會被視為帶有被拋出值的拒絕(rejected)狀態的 Promise。所以這 async 函式呼叫的結果，與 Promise 兩者之間有互相對應的關係。

> 註: 在 JavaScript 中的函式主體內，最後沒有寫 return 回傳值，相當於 return undefined，也算有回傳值。

async 函式的語法即是使用 async 作為函式的修飾關鍵字詞，所宣告的函式，語法如下,，出自 MDN:

```js
async function name([param[, param[, ... param]]]) {
   statements
}
```

這函式會轉變為一個異步的函式，但它與原本的 JavaScript 中函式裡的建構式不同，是使用新的 AsyncFunction 作為建構式來建立函式物件，也就是說它與原本的函式物件的結構並不相同，太深究裡面的底層設計似乎沒那麼重要。

async 函式的語法可以用於各種函式宣告的語法，沒有什麼限制或問題，例如以下幾個:

- 函式定義(FD)
- 函式表達式(FE)
- 箭頭函式
- 物件字面定義內的物件方法
- 類別定義中的方法

### await 運算子

await 是一個運算子，使用這個關鍵字在表達式前作為類似修飾字詞，會讓表達式變為"等待 Promise 解析的表達式"，await 只能在 async 函式內使用，不能在一般的函式內使用。

await 的語法如下，出自 [MDN 這裡](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Operators/await):

```
[rv] = await expression
```

> [rv] 回傳 Promise 物件的 resolved 值，如果當值不是 Promise 物件時，則會回傳該值本身。

await 會讓 JavaScript 程式進行等待(暫停)，等到後面接著的 Promise 的狀態已經固定了(settled)，然後回傳它的值，才會再進行下一個語句。所以多個 await 表達式組合，是一種"順序操作(sequential)"的執行程序，就像是同步語句組合的語法，但實際上是非阻塞的、異步執行的語句組合。

await 表達式也是一個表達式，它會回傳 Promise 的 resolved 值，因為這裡要得到的是值，而不是像 Promise 結構中一個可以往下傳的另一個 Promise 物件。

## 瀏覽器相容議題

根據[Async functions - Can I Use](https://caniuse.com/#feat=async-functions)在 2019/1 的資料，目前市場上大約有 85%的瀏覽器是原生支援 async 函式功能。

Google 瀏覽器的 V8 引擎在很早之前就開始支援 async 函式功能，這也讓 Node 在

## 參考資源

- [14.6async Function Definitions - ECMAScript 2017](https://www.ecma-international.org/ecma-262/8.0/#sec-async-function-definitions)