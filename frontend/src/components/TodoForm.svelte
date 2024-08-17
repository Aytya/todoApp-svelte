<script>
    export let addTodo;

    let title = '';
    let date = '';
    let time = '';
    let priority = '';


    const handleSubmit = (e) => {
        e.preventDefault();

        const dateTime = new Date(`${date}T${time}:00`).toISOString();
        addTodo(title, dateTime, priority);

        title = '';
        date = '';
        time = '';
        priority = '';
    };
</script>

<form class="TodoForm" on:submit={handleSubmit}>
    <input
            type="text"
            class="todo-input"
            bind:value={title}
            placeholder="Add a new task"
    />
    <input
            type="date"
            class="todo-input-data"
            bind:value={date}
    />
    <input
            type="time"
            class="todo-input-time"
            bind:value={time}
    />
    <select
            class="todo-input-priority"
            bind:value={priority}>
        <option value="">Select Priority</option>
        <option class="priority-high" value="High">High</option>
        <option class="priority-medium" value="Medium">Medium</option>
        <option class="priority-low" value="Low">Low</option>
    </select>
    <button type="submit" class="todo-btn">
        Add Task
    </button>
</form>

<style>
</style>
