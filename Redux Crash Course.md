# How to Setup Redux for React

CLI approach -
```shell
    npm i @redux/toolkit react-redux
```

Create react app -
```shell
    npm create vite@latest -y
```

# Steps for using RTK in a dummy Todo App

1. Create a file `./src/app/store.js` and initialize a store. In it, write -
```js
    import {configureStore} from '@reduxjs/toolkit'
    export const store = configureStore({})
``` 

2. We have to create reducers (a.k.a. 'slicers') now. Create `./src/features/todo/todoSlice.js`. In it, write -
```js 
    import { createSlice, nanoid } from "@reduxjs/toolkit"

    const initialState = {
        todos: [{id: 1, text: 'Hello world'}]
    }

    export const todoSlice = createSlice({
        name: 'todo', 
        initialState,
        reducers: {
            addTodo: (state, action) => {
                const todo = {
                    id: nanoid(),
                    text: action.payload
                }
                state.todos.push(todo)
            },
            removeTodo: (state, action) => {
                state.todos = state.todos.filter((todo) => todo.id !== action.payload)
            }
        }
    })

    export const {addTodo, removeTodo} = todoSlice.actions

    export default todoSlice.reducer
```

3. Update reducers list in the store as reducers must be registered inside the store itself. Edit -
```js
    export const store = configureStore({
        reducer: todoReducer
    })
```

4. Make use of `useSelector()` and `useDispatch()` hook to use the reducers.

    a. `useDispatch()`: Create `./src/components/AddTodo.jsx` and in it, write -
    ```jsx
        import { useState } from "react"
        import {useDispatch} from 'react-redux'
        import { addTodo } from "../features/todo/todoSlice"
        const AddTodo = () => {

            const [input, setInput] = useState('')
            const dispatch = useDispatch()

            const addTodoHandler = (e) => {
                e.preventDefault()
                dispatch(addTodo(input))
                setInput('')
            }

            return (
                <form onSubmit={addTodoHandler} className="space-x-3 mt-12">
                    <input 
                        type='text'
                        className="bg-gray-800 rounded border border-gray-700 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-900 text-base outline-none text-gray-100 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out"
                        placeholder="Enter a Todo..."
                        value={input}
                        onChange={(e) => setInput(e.target.value)}
                    />
                    <button 
                        type="submit"
                        className="text-white bg-indigo-500 border-0 py-2 px-6 focus:outline hover:bg-indigo-600 rounded text-lg"
                    >
                        Add Todo
                    </button>
                </form>
            )
        }

        export default AddTodo
    ``` 

    b. `useSelector()`: Create `./src/components/Todos.jsx` and in it, write -
    ```jsx
        import { useSelector, useDispatch } from "react-redux"
        import { removeTodo } from "../features/todo/todoSlice"

        const Todos = () => {

            const todoList = useSelector(state => state.todos)
            const dispatch = useDispatch()

            return (
                <>
                    <div>Todos</div>
                    <ul className="list-none">
                        {todoList.map((todo) => (
                            <li 
                                className="mt-4 flex justify-between items-center bg-zinc-800 px-4 py-2 rounded"
                                key={todo.id}
                            >
                                <div className="text-white">{todo.text}</div>
                                <button
                                    onClick={() => dispatch(removeTodo(todo.id))}
                                    className="text-white bg-red-500 border-0 py-1 px-4 focus:outline-none hover:bg-red-600 rounded text-md"
                                >
                                    X
                                </button>
                            </li>
                        ))}
                    </ul>
                </>
            )
        }

        export default Todos
    ```

5. Wrap the project with provider from `react-redux`, preferably at the `main.jsx` file.
```jsx
    import { Provider } from 'react-redux'
    import { store } from './app/store.js'

    ReactDOM.createRoot(document.getElementById('root')).render(
    <Provider store={store}>
        <App />
    </Provider>
    )
```

# NOTES

1. `useDispatch()` is a hook provided by React, not Redux and `dispatch` (function provided by `useDispatch()` hook) uses reducers to update store. 