<script>
    import { onMount } from 'svelte';
    import TodoForm from './TodoForm.svelte';
    import Todo from './Todo.svelte';
    import EditTodoForm from './EditTodoForm.svelte';
    import Modal from './Modal.svelte';

    import { CreateTodo, CheckTodo, UpdateTodo, GetAllTodos, DeleteTodo } from '../../wailsjs/go/handler/App';
    import { domain } from '../../wailsjs/go/models';

    let todos = [];
    let modalVisible = false;
    let modalMessage = '';

    const fetchTodos = async () => {
        try {
            const fetchedTodos = await GetAllTodos();
            todos = fetchedTodos.map(todo => {
                return domain.Todo.createFrom(todo);
            });
        } catch (error) {
            console.error('Error fetching todos:', error);
            modalMessage = `Error fetching todos: ${error.message}`;
            modalVisible = true;
        }
    };

    onMount(fetchTodos);

    const addTodo = async (title, dateTime, priority) => {
        if (typeof title !== 'string' || !title.trim() || !dateTime || !['High', 'Medium', 'Low'].includes(priority)) {
            modalMessage = 'Validation failed: fields cannot be empty!';
            modalVisible = true;
            return;
        }

        try {
            const createdTodo = await CreateTodo(title.trim(), priority, dateTime);
            todos = [...todos, createdTodo];
        } catch (error) {
            console.error('Error creating todo:', error);
            modalMessage = `Error creating todo: ${error.message}`;
            modalVisible = true;
        }
    };

    const toggleComplete = async id => {
        try {
            await CheckTodo(id);
            todos = todos.map(todo => todo.id === id ? { ...todo, status: !todo.status } : todo);
        } catch (error) {
            console.error('Error toggling complete status:', error);
            modalMessage = `Error toggling complete status: ${error.message}`;
            modalVisible = true;
        }
    };

    const deleteTodo = async id => {
        try {
            await DeleteTodo(id);
            todos = todos.filter(todo => todo.id !== id);
            modalMessage = "Well done! You're one step closer to becoming better 🎉";
            modalVisible = true;
        } catch (error) {
            console.error('Error deleting todo:', error);
            modalMessage = `Error deleting todo: ${error.message}`;
            modalVisible = true;
        }
    };

    const updateTodo = async (id, title, datetime, priority) => {
        if (typeof title !== 'string' || !title.trim() || !datetime || !['High', 'Medium', 'Low'].includes(priority)) {
            modalMessage = 'Validation failed: fields cannot be empty!';
            modalVisible = true;
            return;
        }

        try {
            console.log('Updating Todo with data:', { id, title, priority, datetime });
            await UpdateTodo(id, title, priority, datetime);
            const updatedTodo = domain.Todo.createFrom({
                id,
                title: title.trim(),
                priority,
                datetime,
                status: false,
            });
            todos = todos.map(todo => todo.id === id ? updatedTodo : todo);
        } catch (error) {
            console.error('Error updating todo:', error);
            modalMessage = `Error updating todo: ${error.message}`;
            modalVisible = true;
        }
    };

    const sortTodosByPriority = todos => {
        const priorityOrder = { High: 1, Medium: 2, Low: 3 };
        return todos.sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
    };

    let activeTodos = [];
    let completedTodos = [];

    $: activeTodos = sortTodosByPriority(todos.filter(todo => !todo.status));
    $: completedTodos = sortTodosByPriority(todos.filter(todo => todo.status));
</script>

<div class="TodoWrapper">
    <h1>TODO LIST</h1>
    <TodoForm {addTodo} />
    <h2 class="active-task">Active Tasks</h2>
    {#each activeTodos as todo}
        {#if todo.isEditing}
            <EditTodoForm {updateTodo} task={todo}/>
        {:else}
            <Todo
                    task={todo}
                    toggleComplete={() => toggleComplete(todo.id)}
                    deleteTodo={() => deleteTodo(todo.id)}
                    updateTodo={() => todos = todos.map(t => t.id === todo.id ? { ...t, isEditing: true } : t)}
            />
        {/if}
    {/each}
    <h2 class="complete-task">Completed Tasks</h2>
    {#each completedTodos as todo}
        {#if todo.isEditing}
            <EditTodoForm {updateTodo} task={todo} />
        {:else}
            <Todo
                    task={todo}
            toggleComplete={() => toggleComplete(todo.id)}
            deleteTodo={() => deleteTodo(todo.id)}
            updateTodo={() => todos = todos.map(t => t.id === todo.id ? { ...t, isEditing: true } : t)}
            isCompleted={true}
            />
        {/if}
    {/each}
    {#if modalVisible}
        <Modal message={modalMessage} onClose={() => modalVisible = false} />
    {/if}
</div>

<style>
</style>
