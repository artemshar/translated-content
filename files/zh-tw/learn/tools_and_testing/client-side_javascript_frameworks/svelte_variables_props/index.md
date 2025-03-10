---
title: Svelte 中的動態行為：變數及屬性
slug: >-
  Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_variables_props
---

{{LearnSidebar}}
{{PreviousMenuNext("Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_Todo_list_beginning","Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_components", "Learn/Tools_and_testing/Client-side_JavaScript_frameworks")}}

現在我們已經準備好了標記語言及樣式，我們可以開始為 Svelte 待辦清單應用程式開發所需的功能。在此篇文章中，我們將使用變數及屬性使我們的應用程式動態化，允許我們新增及刪除待辦事項，標記它們為完成及藉由狀態過濾它們。

<table>
  <tbody>
    <tr>
      <th scope="row">預備知識：</th>
      <td>
        <p>
          推薦你至少需熟悉基本的
          <a href="/zh-TW/docs/Learn/HTML">HTML</a>、
          <a href="/zh-TW/docs/Learn/CSS">CSS</a> 與
          <a href="/zh-TW/docs/Learn/JavaScript">JavaScript</a>
          等程式語言且具備<a
            href="/zh-TW/docs/Learn/Tools_and_testing/Understanding_client-side_tools/Command_line"
            >終端機/命令列環境</a
          >基本知識。
        </p>
        <p>
          你將會需要一個安裝了 node 和 npm 的終端機來編譯和建置你的應用程式。
        </p>
      </td>
    </tr>
    <tr>
      <th scope="row">學習目標：</th>
      <td>
        學習並實踐一些基本的 Svelte 概念，像是建立元件、使用屬性傳遞資料、將 JavaScript 表達式渲染到我們的標記語言、修改元件的狀態以及迭代清單。
      </td>
    </tr>
  </tbody>
</table>

## 和我們一起撰寫程式碼

### Git

複製 Github 儲存庫（假如你還沒完成）如下：

```bash
git clone https://github.com/opensas/mdn-svelte-tutorial.git
```

接著取得當前應用程式狀態，執行如下：

```bash
cd mdn-svelte-tutorial/03-adding-dynamic-behavior
```

或直接下載資料夾內容如下：

```bash
npx degit opensas/mdn-svelte-tutorial/03-adding-dynamic-behavior
```

記得執行 `npm install && npm run dev` 以開發模式來運行你的應用程式。

### REPL

為了使用 REPL 和我們一起撰寫程式碼，點擊如下連結：

<https://svelte.dev/repl/c862d964d48d473ca63ab91709a0a5a0?version=3.23.2>

## 處理待辦事項

目前我們的 `Todos.svelte` 元件只有顯示靜態標記語言，讓我們開始讓它變得更加動態。我們將從標記語言中取得任務資訊並將其儲存在 `todos` 陣列中。另外我們將建立兩個變數來持續追蹤任務總數及已完成的任務。

我們元件的狀態將由這三個頂層變數代表。

1. 在 `src/components/Todos.svelte` 的頂端建立一個 `<script>` 區塊並給它一些內容，如下所示：

   ```html
   <script>
     let todos = [
       { id: 1, name: "Create a Svelte starter app", completed: true },
       { id: 2, name: "Create your first component", completed: true },
       { id: 3, name: "Complete the rest of the tutorial", completed: false }
     ];
     let totalTodos = todos.length;
     let completedTodos = todos.filter((todo) => todo.completed).length;
   </script>
   ```

   現在讓我們用這些資訊做點什麼。

2. 讓我們從顯示狀態訊息開始。找到 `id` 為 `list-heading` 的 `<h2>` 標頭並用動態表達式替換已經寫死的現行任務及已完成任務數量：

   ```html
   <h2 id="list-heading">{completedTodos} out of {totalTodos} items completed</h2>
   ```

3. 回到應用程式，你應該會看到和以前一樣的「2 out of 3 items completed」訊息，但這次的資訊來自 `todos` 陣列。
4. 為了證明這一點，回到陣列並嘗試改變一些待辦事項物件的已完成屬性值，甚至是新增一個新的待辦事項物件。觀察訊息中的數字是如何適當地被更新。

## 從資料動態地產生待辦事項

目前我們顯示的待辦事項都是靜態的。而我們想要迭代 `todos` 陣列中的每個項目並為每個任務渲染其標記，讓我們馬上動手吧。

HTML 沒有表達邏輯的方式——像是條件和迴圈。但 Svelte 做到了。在這種情況下，我們使用 [`{#each}`](https://svelte.dev/docs#each) 指令來迭代 `todos` 陣列。假如有提供第二個參數，將包含當前項目的索引。此外，可以提供一個關鍵表達式，它將唯一識別每個項目。Svelte 將在資料改變時使用它來差異清單，而不是在最後新增或刪除項目，所以始終指定一個是一種好的做法。最後，可以提供 `:else` 區塊，它將在清單為空時呈現。

試一試吧。

1. 將現有的 `<ul>` 元素替換為以下的簡化版本，以了解其工作原理：

   ```html
   <ul>
   {#each todos as todo, index (todo.id)}
     <li>
       <input type="checkbox" checked={todo.completed}/> {index}. {todo.name} (id: {todo.id})
     </li>
   {:else}
     Nothing to do here!
   {/each}
   </ul>
   ```

2. 回到應用程式，你會看到這樣的畫面：

   ![very simple to-do list output created using an each block](01-each-block.png)

3. 現在我們已經看到這是有效的，接著讓我們使用 `{#each}` 指令的每個循環產生一個完整的待辦事項並在其中嵌入來自 `todos` 陣列中的資訊：`id`、`name` 和 `completed`。將現有的 `<ul>` 區塊替換為以下內容：

   ```html
   <!-- To-dos -->
   <ul role="list" class="todo-list stack-large" aria-labelledby="list-heading">
     {#each todos as todo (todo.id)}
     <li class="todo">
       <div class="stack-small">
         <div class="c-cb">
           <input
             type="checkbox"
             id="todo-{todo.id}"
             checked={todo.completed} />
           <label for="todo-{todo.id}" class="todo-label"> {todo.name} </label>
         </div>
         <div class="btn-group">
           <button type="button" class="btn">
             Edit <span class="visually-hidden">{todo.name}</span>
           </button>
           <button type="button" class="btn btn__danger">
             Delete <span class="visually-hidden">{todo.name}</span>
           </button>
         </div>
       </div>
     </li>
     {:else}
     <li>Nothing to do here!</li>
     {/each}
   </ul>
   ```

   請注意我們如何使用大括號將 JavaScript 表達式嵌入 HTML 屬性中，就像我們對勾選框的 `checked` 和 `id` 屬性所做的那樣。

我們已經將靜態標記轉為動態範本並從元件的狀態來顯示這些任務。做得好！

## 處理屬性

寫死的待辦事項清單，使我們的 `Todos` 元件不是那麼有用。為了將我們的元件變為一個通用的待辦事項編輯器，我們應該允許它的上層元件可以傳遞待辦事項清單以進行編輯。這將允許我們將它們儲存至網路服務或局部儲存區，便於之後將它們取回以進行更新。所以讓我們將陣列變為一個 `prop`。

1. 在 `Todos.svelte` 中，將現有的 `let todos = …` 區塊替換為 `export let todos = []`。

   ```js
   export let todos = []
   ```

   起初這可能會讓你覺得有點奇怪。這不像在 JavaScript 模組中使用 `export` 的常見方式！但這就是 Svelte 透過採用有效語法並賦予其新用途藉此來「擴展」JavaScript 的作法。在這種情況下，Svelte 使用 `export` 關鍵字將變數宣告標記為屬性，這意味著元件的消費者可以存取它。

   你也可以為屬性指定預設初始值。如果元件的消費者沒有在元件上指定屬性時，則將使用此預設初始值——或者如果它的初始值未定義——在實例化元件時。

   因此，透過 `export let todos = []` 我們告訴 Svelte 我們的 `Todos.svelte` 元件將可接受一個 `todos` 屬性，而當省略該屬性時，它將被初始化為一個空陣列。

2. 回頭看看應用程式，你將會看到「Nothing to do here!」訊息。這是因為我們目前沒有從 `App.svelte` 向它傳遞任何數值，所以它使用預設值。
3. 現在讓我們將待辦事項移動到 `App.svelte` 並將它們作為屬性傳遞給 `Todos.svelte` 元件。更新 `src/App.svelte` 如下：

   ```html
   <script>
     import Todos from "./components/Todos.svelte";

     let todos = [
       { id: 1, name: "Create a Svelte starter app", completed: true },
       { id: 2, name: "Create your first component", completed: true },
       { id: 3, name: "Complete the rest of the tutorial", completed: false }
     ];
   </script>

   <Todos todos={todos} />
   ```

4. 當屬性和變數具有相同名稱時，Svelte 允許你僅指定變數作為便利的捷徑，因此我們可以像這樣重寫最後一行。現在試試吧。

   ```html
   <Todos {todos} />
   ```

此時，你的待辦事項呈現應該如同以往，除了現在我們是從 `App.svelte` 元件來傳遞它們之外。

## 切換和刪除​​待辦事項

讓我們新增一些功能性來切換任務狀態。Svelte 具有用於監聽 DOM 事件的 `on:eventname` 指令。讓我們為勾選框的 `on:click` 事件新增一個處理器來切換已完成的數值。

1. 更新 `src/components/Todos.svelte` 中的 `<input type="checkbox">` 元素，如下：

   ```html
   <input type="checkbox" id="todo-{todo.id}"
     on:click={() => todo.completed = !todo.completed}
     checked={todo.completed}
   />
   ```

2. 接下來我們將新增一個函式來從我們的 `todos` 陣列中刪除一個待辦事項。在 `Todos.svelte` 中 `<script>` 區塊的底部，新增 `removeTodo()` 函式，如下：

   ```js
   function removeTodo(todo) {
     todos = todos.filter((t) => t.id !== todo.id)
   }
   ```

3. 我們將透過 _Delete_ 按鈕呼叫它。更新 `click` 事件，如下：

   ```html
   <button type="button" class="btn btn__danger"
     on:click={() => removeTodo(todo)}
   >
     Delete <span class="visually-hidden">{todo.name}</span>
   </button>
   ```

   Svelte 中處理器的一個常見錯誤是將執行函式的結果作為處理器傳遞，而不是傳遞函式。舉例來說，假如你指定 `on:click={removeTodo(todo)}`，它會執行 `removeTodo(todo)` 並把結果作為處理器傳遞，但這不是我們要的。

   在這種情況下，您必須指定 `on:click={() => removeTodo(todo)}` 作為處理器。如果 `removeTodo()` 沒有接收參數，你可以使用 `on:event={removeTodo}`，但不能使用 `on:event={removeTodo()}`。這並不是 Svelte 中的特殊語法——這裡我們只是使用常規的 JavaScript [箭頭函式](/zh-TW/docs/Web/JavaScript/Reference/Functions/Arrow_functions)。

這是不錯的進展——此時，我們現在能刪除任務了。當一個待辦事項的 _Delete_ 按鈕被按下時，相關的待辦事項會從 `todos` 陣列中被移除並且更新使用者介面讓它不再顯示。此外，我們現在能勾選勾選框，相關待辦事項的已完成狀態將在 `todos` 陣列中被更新。

然而，「x out of y items completed」標頭並未被更新。繼續閱讀以了解為什麼會發生這種情況以及我們如何解決它。

## 待辦事項反應性

正如我們所看到的，每次元件頂層變數的數值被修改時，Svelte 都知道如何更新使用者介面。在我們的應用程式中，每次切換或刪除待辦事項時都會直接地更新 `todos` 陣列數值，所以 Svelte 會自動地更新 DOM。

然而，對於 `totalTodos` 和 `completedTodos` 來說情況並非如此。在下面的程式碼中，當元件被實例化且腳本被執行時，它們會被指定一個數值，但是在那之後，它們的數值不會被改變：

```js
let totalTodos = todos.length
let completedTodos = todos.filter((todo) => todo.completed).length
```

我們可以在切換和刪除​​待辦事項後重新計算它們，但有一種更簡單的方式可以做到。

可以告訴 Svelte 我們想要 `totalTodos` 和 `completedTodos` 等變數藉由前綴 `$:`，使其具有反應性。Svelte 將產生程式碼以在它們相依的資料發生變化時自動更新它們。

> **備註：** Svelte 使用 `$:` [JavaScript 標記陳述語法](/zh-TW/docs/Web/JavaScript/Reference/Statements/label)來標記反應性陳述。像是用於宣告屬性的 `export` 關鍵字一樣，這可能看起來會有點陌生。但這是 Svelte 利用有效 JavaScript 語法並賦予其新用途的另一個例子——在這種情況下，意味著「每當任何參考的數值有變化時，則會重新執行此程式碼」。一旦習慣了，就無法回頭了。

更新 `src/components/Todos.svelte` 中的 `totalTodos` 和 `completedTodos` 變數定義，如下：

```js
$: totalTodos = todos.length
$: completedTodos = todos.filter((todo) => todo.completed).length
```

如果你現在檢查你的應用程式，當待辦事項完成或被刪除時，你將會看到標頭的數字被更新。做得好！

Svelte 編譯器在背後會解析和分析我們的程式碼以產生相依樹，接著它會產生 JavaScript 程式碼以在其中一個相依項目被更新時，來重新評估每個反應性陳述。Svelte 的反應性以非常輕量和高性能的方式來實現，無須使用監聽器（listener）、設定器（setter）、取得器（getter）或任何其它複雜的機制。

## 加入新的待辦事項

目前此文章的下一個主要任務——讓我們新增一些功能性來加入新的待辦事項。

1. 首先，我們將建立一個變數來保存新待辦事項的文字。將此宣告新增到 `Todos.svelte` 檔案的 `<script>` 區塊中：

   ```js
   let newTodoName = ''
   ```

2. 我們將使用 `<input>` 中的數值來加入新任務。為此我們需要將 `newTodoName` 變數綁定到 `todo-0` 輸入框，以便 `newTodoName` 變數數值與輸入框的 `value` 屬性保持同步。我們可以這樣做：

   ```html
   <input value={newTodoName} on:keydown={(e) => newTodoName = e.target.value} />
   ```

   每當變數 `newTodoName` 的數值發生變化時，它將被反映在 `value` 輸入框的屬性中且每當在輸入框按下某個鍵時，我們將會更新變數 `newTodoName` 的內容。

   這是對輸入框雙向資料綁定的手動實作。但是我們不需要這樣做—— Svelte 提供了一種更簡單的方式來將任何屬性綁定到變數，使用 [`bind:property`](https://svelte.dev/docs#bind_element_property) 指令：

   ```html
   <input bind:value={newTodoName} />
   ```

   所以，讓我們來實作它。更新 `todo-0` 輸入框如下：

   ```html
   <input
     bind:value={newTodoName}
     type="text"
     id="todo-0"
     autocomplete="off"
     class="input input__lg" />
   ```

3. 測試是否有效的一個簡單方式是新增一個反應性陳述來記錄 `newTodoName` 的內容。在 `<script>` 區塊的末端新增此程式碼片段：

   ```js
   $: console.log('newTodoName: ', newTodoName)
   ```

   > **備註：** 你可能已經注意到了，反應性陳述不僅限於變數宣告。你可以在 `$:` 符號之後放上 _任何_ JavaScript 陳述。

4. 現在嘗試回到 `localhost:5042`，按下 <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>K</kbd> 來打開瀏覽器控制台並在輸入框中輸入一些內容。你應該就會看到你的輸入被記錄了。此時，你可以視情況刪除反應性 `console.log()`。
5. 接下來，我們將建立一個函式來加入新的待辦事項—— `addTodo()` ——它會將一個新的 `todo` 物件塞入到 `todos` 陣列中。將此函式新增到 `src/components/Todos.svelte` 中 `<script>` 區塊的底部：

   ```js
   function addTodo() {
     todos.push({ id: 999, name: newTodoName, completed: false })
     newTodoName = ''
   }
   ```

   > **備註：** 目前我們為每個待辦事項都指定相同的 `id`，別擔心，我們會盡快解決這個問題。

6. 現在我們要更新我們的 HTML，以便每當表單被提交時來呼叫 `addTodo()`。更新新增待辦事項（NewTodo）表單的起始標籤如下：

   ```html
   <form on:submit|preventDefault={addTodo}>
   ```

   [`on:eventname`](https://svelte.dev/docs#on_element_event) 指令支援使用 `|` 字元向 DOM 事件加入修飾詞（modifier）。在這種情況下，`preventDefault` 修飾詞告訴 Svelte 在運行處理器之前，產生呼叫 `event.preventDefault()` 的程式碼。瀏覽前一個連結以查看其它可以用的修飾詞有哪些。

7. 假如你現在嘗試加入新的待辦事項，新的待辦事項會被加入到待辦事項陣列中，但我們的使用者介面不會被更新。記住在 Svelte 中[反應性是由指定來觸發](https://svelte.dev/docs#2_Assignments_are_reactive)。這意味著 `addTodo()` 函式被執行，元素被新增到 `todos` 陣列中，但 Svelte 不會檢測到推入（push）方法修改了陣列，所以它也不會刷新任務 `<ul>`。

   只要將 `todos = todos` 新增到 `addTodo()` 函式的末端就能解決這個問題，但是必須在函式末端包含它似乎很奇怪。取而代之，我們將拿掉 `push()` 方法並使用[展開語法](/zh-TW/docs/Web/JavaScript/Reference/Operators/Spread_syntax)來達到相同的結果：我們將為 `todos` 陣列指定一個等於 `todos` 陣列加上新物件的數值。

   > **備註：** 陣列（`Array`）有幾個可變的操作：[`push()`](/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/push)、[`pop()`](/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)、[`splice()`](/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)、[`shift()`](/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/shift)、[`unshift()`](/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift)、[`reverse()`](/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse) 和 [`sort()`](/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)。使用它們常會導致難以追蹤的副作用和錯誤。透過使用展開語法而不是 `push()`，我們可以避免改變陣列本身，這被認為是一種好的做法。

   更新你的 `addTodo()` 函式如下：

   ```js
   function addTodo() {
     todos = [...todos, { id: 999, name: newTodoName, completed: false }]
     newTodoName = ''
   }
   ```

## Giving each to-do a unique ID

If you try to add new to-dos in your app now, you'll be able to add a new to-do and have it appear in the UI — once. If you try it a second time, it won't work, and you'll get a console message saying "Error: Cannot have duplicate keys in a keyed each". We need unique IDs for our to-dos.

1. Let's declare a `newTodoId` variable calculated from the number of to-dos plus 1, and make it reactive. Add the following snippet to the `<script>` section:

   ```js
   let newTodoId
     $: {
       if (totalTodos === 0) {
         newTodoId = 1;
       } else {
         newTodoId = Math.max(...todos.map((t) => t.id)) + 1;
       }
     }
   ```

   > **Note:** As you can see, reactive statements are not limited to one-liners. The following would work too, but it is a little less readable: `$: newTodoId = totalTodos ? Math.max(...todos.map((t) => t.id)) + 1 : 1`

2. How does Svelte achieve this? The compiler parses the whole reactive statement, and detects that it depends on the `totalTodos` variable and the `todos` array. So whenever either of them is modified, this code is re-evaluated, updating `newTodoId` accordingly.

   Let's use this in our `addTodo()` function. Update it like so:

   ```js
   function addTodo() {
     todos = [...todos, { id: newTodoId, name: newTodoName, completed: false }]
     newTodoName = ''
   }
   ```

## Filtering to-dos by status

Finally for this article, let's implement the ability to filter our to-dos by status. We'll create a variable to hold the current filter, and a helper function that will return the filtered to-dos.

1. At the bottom of our `<script>` section add the following:

   ```js
   let filter = 'all'
   const filterTodos = (filter, todos) =>
     filter === 'active' ? todos.filter((t) => !t.completed) :
     filter === 'completed' ? todos.filter((t) => t.completed) :
     todos
   ```

   We use the `filter` variable to control the active filter: _all_, _active_, or _completed_. Just assigning one of these values to the filter variable will activate the filter and update the list of to-dos. Let's see how to achieve this.

   The `filterTodos()` function will receive the current filter and the list of to-dos, and return a new array of to-dos filtered accordingly.

2. Let's update the filter button markup to make it dynamic and update the current filter when the user presses one of the filter buttons. Update it like this:

   ```html
   <div class="filters btn-group stack-exception">
     <button class="btn toggle-btn" class:btn__primary={filter === 'all'} aria-pressed={filter === 'all'} on:click={() => filter = 'all'} >
       <span class="visually-hidden">Show</span>
       <span>All</span>
       <span class="visually-hidden">tasks</span>
     </button>
     <button class="btn toggle-btn" class:btn__primary={filter === 'active'} aria-pressed={filter === 'active'} on:click={() => filter = 'active'} >
       <span class="visually-hidden">Show</span>
       <span>Active</span>
       <span class="visually-hidden">tasks</span>
     </button>
     <button class="btn toggle-btn" class:btn__primary={filter === 'completed'} aria-pressed={filter === 'completed'} on:click={() => filter = 'completed'} >
       <span class="visually-hidden">Show</span>
       <span>Completed</span>
       <span class="visually-hidden">tasks</span>
     </button>
   </div>
   ```

   There are a couple of things going on in this markup.

   We will show the current filter by applying the `btn__primary` class to the active filter button. To conditionally apply style classes to an element we use the `class:name={value}` directive. If the value expression evaluates to truthy, the class name will be applied. You can add many of these directives, with different conditions, to the same element. So when we issue `class:btn__primary={filter === 'all'}`, Svelte will apply the `btn__primary` class if filter equals all.

   > **Note:** Svelte provides a shortcut which allows us to shorten `<div class:active={active}>` to `<div class:active>` when the class matches the variable name.

   Something similar happens with `aria-pressed={filter === 'all'}`: when the JavaScript expression passed between curly braces evaluates to a truthy value, the `aria-pressed` attribute will be added to the button.

   Whenever we click on a button, we update the filter variable by issuing `on:click={() => filter = 'all'}`. Read on to find out how Svelte reactivity will take care of the rest.

3. Now we just need to use the helper function in the `{#each}` loop; update it like this:

   ```html
   …
     <ul role="list" class="todo-list stack-large" aria-labelledby="list-heading">
     {#each filterTodos(filter, todos) as todo (todo.id)}
   …
   ```

   After analyzing our code, Svelte detects that our `filterTodos()` function depends on the variables `filter` and `todos`. And, just like with any other dynamic expression embedded in the markup, whenever any of these dependencies changes, the DOM will be updated accordingly. So whenever `filter` or `todos` changes, the `filterTodos()` function will be re-evaluated and the items inside the loop will be updated.

> **Note:** Reactivity can be tricky sometimes. Svelte recognizes `filter` as a dependency because we are referencing it in the `filterTodos(filter, todo)` expression. `filter` is a top-level variable, so we might be tempted to remove it from the helper function params, and just call it like this: `filterTodos(todo)`. This would work, but now Svelte has no way to find out that `{#each filterTodos(todos) }` depends on `filter`, and the list of filtered to-dos won't be updated when the filter changes. Always remember that Svelte analyzes our code to find out dependencies, so it's better to be explicit about it and not rely on the visibility of top-level variables. Besides, it's a good practice to make our code clear and explicit about what information it is using.

## The code so far

### Git

To see the state of the code as it should be at the end of this article, access your copy of our repo like this:

```bash
cd mdn-svelte-tutorial/04-componentizing-our-app
```

Or directly download the folder's content:

```bash
npx degit opensas/mdn-svelte-tutorial/04-componentizing-our-app
```

Remember to run `npm install && npm run dev` to start your app in development mode.

### REPL

To see the current state of the code in a REPL, visit:

<https://svelte.dev/repl/99b9eb228b404a2f8c8959b22c0a40d3?version=3.23.2>

## Summary

That will do for now! In this article we already implemented most of our desired functionality. Our app can display, add, and delete to-dos, toggle their completed status, show how many of them are completed, and apply filters.

To recap, we covered the following topics:

- Creating and using components
- Turning static markup into a live template
- Embedding JavaScript expressions in our markup
- Iterating over lists using the `{#each}` directive
- Passing information between components with props
- Listening to DOM events
- Declaring reactive statements
- Basic debugging with `console.log()` and reactive statements
- Binding HTML properties with the `bind:property` directive
- Triggering reactivity with assignments
- Using reactive expressions to filter data
- Explicitly defining our reactive dependencies

In the next article we will add further functionality, which will allow users to edit to-dos.

{{PreviousMenuNext("Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_Todo_list_beginning","Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_components", "Learn/Tools_and_testing/Client-side_JavaScript_frameworks")}}

## 於此模組中

- [前端框架介紹](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Introduction)
- [框架主要功能](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Main_features)
- React

  - [React 入門](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_getting_started)
  - [建立我們的 React 待辦清單](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_todo_list_beginning)
  - [元件化我們的 React 應用程式](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_components)
  - [React 互動性：事件與狀態](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_interactivity_events_state)
  - [React 互動性：編輯、過濾、條件式渲染](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_interactivity_filtering_conditional_rendering)
  - [React 無障礙](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_accessibility)
  - [React 資源](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_resources)

- Ember

  - [Ember 入門](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Ember_getting_started)
  - [Ember 應用程式結構及元件化](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Ember_structure_componentization)
  - [Ember 互動性：事件、類別、狀態](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Ember_interactivity_events_state)
  - [Ember 互動性：Footer 功能、條件式渲染](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Ember_conditional_footer)
  - [Ember 路由](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Ember_routing)
  - [Ember 資源及偵錯](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Ember_resources)

- Vue

  - [Vue 入門](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Vue_getting_started)
  - [建立第一個 Vue 元件](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Vue_first_component)
  - [渲染 Vue 清單](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Vue_rendering_lists)
  - [新增待辦表單：Vue 事件、方法、模型](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Vue_methods_events_models)
  - [透過 CSS 樣式化 Vue 元件](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Vue_styling)
  - [使用 Vue 計算屬性](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Vue_computed_properties)
  - [Vue 條件式渲染：編輯已存在的待辦表單](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Vue_conditional_rendering)
  - [聚焦 Vue refs](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Vue_refs_focus_management)
  - [Vue 資源](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Vue_resources)

- Svelte

  - [Svelte 入門](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_getting_started)
  - [開始寫我們的 Svelte 待辦清單應用程式](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_Todo_list_beginning)
  - [Svelte 中的動態行為：變數及屬性](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_variables_props)
  - [元件化我們的 Svelte 應用程式](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_components)
  - [進階 Svelte：反應性、生命週期、無障礙](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_reactivity_lifecycle_accessibility)
  - [和 Svelte stores 共舞](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_stores)
  - [Svelte 中的 TypeScript](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_TypeScript)
  - [部署和下一步](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Svelte_deployment_next)

- Angular

  - [Angular 入門](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Angular_getting_started)
  - [開始我們的 Angular 待辦清單應用程式](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Angular_todo_list_beginning)
  - [樣式化我們的 Angular 應用程式](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Angular_styling)
  - [建立（待辦清單）項目元件](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Angular_item_component)
  - [過濾我們的待辦項目](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Angular_filtering)
  - [建置 Angular 應用程式及更多資源](/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Angular_building)
