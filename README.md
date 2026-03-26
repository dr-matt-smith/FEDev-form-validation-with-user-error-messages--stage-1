# FEDev-form-validation-with-user-error-messages

Stage 1
|
[Stage 2](https://github.com/dr-matt-smith/FEDev-form-validation-with-user-error-messages--stage-2)
|
[Stage 3](https://github.com/dr-matt-smith/FEDev-form-validation-with-user-error-messages--stage-3)
|
[Stage 4](https://github.com/dr-matt-smith/FEDev-form-validation-with-user-error-messages--stage-4)
|
[Stage 5](https://github.com/dr-matt-smith/FEDev-form-validation-with-user-error-messages--stage-5)



# stage 1 - basic project setup

This project works through one of the SvelteKit tutorials, to illustrate how to validate form input, and feedback a error message to the user if there are issues with the form data.

The SvelteKit tutorial followed here can be found at:
- https://svelte.dev/tutorial/kit/form-validation

NOTE:
- although not a real database, the in-memory data for the Todos is stored in a dynamic 'map' ([JavaScript map objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map))
- see `/lib/server/database.js`
- you could adapt this sample code if you wish to manage CRUD actions on data for your project ....

## Setup for the tutorial (before any changes)

The project looks as follows:

![project folder structure](/screenshots/1_project_structure.png)

When we run this project as is starts, we'll see the following:

![form for new TODO](/screenshots/2_form_for_new_todo.png)

The form comes from the root Svelte page, which:
- gets `data` from the `propos` provided by its server page JavaScript `load()` function 
  - ```javascript
    <script>
        let { data } = $props();
    </script>
    ```

- presents the form, submitted using the HTTP POST method and form action `create`
  - ```javascript
    <div class="centered">
        <h1>todos</h1>
    
        <form method="POST" action="?/create">
            <label>
                add a todo:
                <input
                    name="description"
                    autocomplete="off"
                />
            </label>
        </form>
    ```

- uses a Svelte `each` loop to create an unordered (`<ul>`) list of all the todo objects received in the props `data` object:
  - ```javascript
          <ul class="todos">
              {#each data.todos as todo (todo.id)}
              <li>
                  <form method="POST" action="?/delete">
                      <input type="hidden" name="id" value={todo.id} />
                      <span>{todo.description}</span>
                      <button aria-label="Mark as complete"></button>
                  </form>
              </li>
              {/each}
          </ul>
      </div>
      ```
    
Altogether the full code for this page looks as follows:

`/routes/+page.svelte`

```javascript
<script>
    let { data } = $props();
</script>

<div class="centered">
    <h1>todos</h1>

    <form method="POST" action="?/create">
        <label>
            add a todo:
            <input
                name="description"
                autocomplete="off"
            />
        </label>
    </form>

    <ul class="todos">
        {#each data.todos as todo (todo.id)}
        <li>
            <form method="POST" action="?/delete">
                <input type="hidden" name="id" value={todo.id} />
                <span>{todo.description}</span>
                <button aria-label="Mark as complete"></button>
            </form>
        </li>
        {/each}
    </ul>
</div>

    <style>
        ... lots of style rules to make the page look nice
    </style>
```

The simulated database of todo objects provided by the function `getTodos()` created a single todo for `Learn SvelteKit` when it's called the first time, and no simulated map-database collection of todo objects yet exists:

`/lib/server/database.js`


```javascript
    ... other code here ...

    export function getTodos(userid) {
        if (!db.get(userid)) {
            db.set(userid, [{
                id: crypto.randomUUID(),
                description: 'Learn SvelteKit',
                done: false
            }]);
        }
    
        return db.get(userid);
    }
```


## Some issues with this project

We can submit an empty string todo, by clicking the text cursor into the text box and pressing <RETURN> without typing any characters:

![todo with no text content](/screenshots/3_empty_todo.png)

We can also create duplicate TODOs, with the text. For example, type `shopping` then press <RETURN>, and repeat this. You'll see 2 identical `shopping` todos listed:

![identical todos](/screenshots/4_identical_todos.png)

we'll fix these issues, and also display meaningful validation messages to the user in the next steps ...
